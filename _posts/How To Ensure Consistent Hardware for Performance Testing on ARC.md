Ben Skubi, 2025-4-22

**Purpose:** This guide explains how to run computational jobs on specific, consistent hardware within the OHSU ARC high-performance computing cluster. This is crucial for reliable performance testing and benchmarking.

**Contents:**

1.  **The Challenge:** Hardware Heterogeneity on ARC
2.  **Background:** ARC Architecture (SLURM, Nodes, Partitions)
3.  **Controlling Job Placement:** Using SLURM's `--exclude` Option
4.  **ARC-Specific Solution:** Environment Variables for Simplified Node Selection
5.  **Implementation:** Setting Up and Using the Environment Variables
6.  **Verifying Hardware and Partitions**

---

## 1. The Challenge: Hardware Heterogeneity on ARC

For accurate performance testing or benchmarking, it's essential that comparisons are run on identical hardware. However, the ARC cluster comprises compute nodes with different CPU models and potentially other varying characteristics. By default, the ARC job scheduler (SLURM) may assign your job tasks to any available node within a partition that meets your resource request, potentially leading to inconsistent hardware usage across different runs. This guide provides a method to control job placement onto specific groups of nodes.

---

## 2. Background: ARC Architecture (SLURM, Nodes, Partitions)

ARC utilizes the **SLURM Workload Manager** ([SLURM Overview](https://slurm.schedmd.com/overview.html)) to manage resources and schedule user jobs.

* **Job:** A request submitted by a user for a specific allocation of computing resources (e.g., CPU cores, memory, GPUs) for a defined maximum wall-clock time.
* **Node:** A physical compute server within the cluster. Most ARC compute nodes follow the naming convention `cnode-[A]-[B]`, where `[A]` and `[B]` are integers. Note that single-digit `[A]` values are zero-padded (e.g., `07`), but single-digit `[B]` values are not (e.g., `7`). An example node name is `cnode-07-8`. Nodes themselves are another example of a resource that SLURM manages.
* **Partition:** A logical grouping of nodes. Every node on ARC belongs to exactly one partition. Partitions define policies, such as maximum job runtime, CPU count, or memory limits. When you submit a job (using commands like `sbatch`, `srun`, or `salloc`), it targets a specific partition, either explicitly (`--partition <partition_name>`) or implicitly (using the default partition). SLURM then assigns the job to run on one or more nodes within that partition.

**Important:** Requesting more resources than a partition allows may cause your job to remain indefinitely in the `PENDING` state. Always ensure your job's resource requests comply with the limits of the target partition. (Partition details as of 2025-04-22 were originally listed here - refer to current ARC documentation or use `sinfo` for up-to-date limits).

---

## 3. Controlling Job Placement: Using SLURM's `--exclude` Option

SLURM's primary mechanism for *preventing* jobs from running on specific nodes is the `--exclude` option, available for `sbatch`, `salloc`, and `srun`.

* **Functionality:** `--exclude <nodelist>` tells SLURM *not* to schedule the job (or specific tasks within the job) on the nodes specified in the `<nodelist>`.
* **Syntax:** The `<nodelist>` uses a powerful bracket notation:
    * **Ranges:** `[start-end]` (e.g., `cnode-07-[1-5]`)
    * **Ranges with Step:** `[start-end:step]` (e.g., `cnode-08-[0-10:2]` expands to nodes 0, 2, 4, 6, 8, 10 in block 08)
    * **Comma-separated Lists:** Can combine individual numbers and ranges (e.g., `cnode-09-[1,3,5-8,12]`)
    * **Multiple Node Blocks:** You can list multiple specifications separated by commas (e.g., `--exclude cnode-07-8,cnode-09-[1-5],cnode-10-1`)
* **Limitations:**
    * There are **no wildcards**.
    * Specifying a node name that doesn't exist will result in an error: `error: Unable to allocate resources: Invalid node name specified`.
    * There is also a `--nodelist` option, but this does **not** "whitelist" certain nodes. Instead, it enforces that every specified node is reserved as a resource available to the job, which is useful for MPI-based workloads that are distributed across multiple nodes, but is not helpful for reserving a specific node within a subset of acceptable node options. 

---

## 4. ARC-Specific Solution: Environment Variables for Simplified Node Selection

Directly using `--exclude` to target a *specific* set of desired nodes on ARC can be cumbersome. This is because you need to exclude *all other* nodes, and:

* The `[B]` indices within `cnode-[A]-[B]` are often not contiguous for a given `[A]`.
* Nodes belonging to a specific partition are not always grouped neatly within consecutive `[B]` indices or across `[A]` blocks.

To overcome this, we can define environment variables in your `~/.bashrc` file. Each variable will store the complex `--exclude` string needed to isolate a specific block of `cnode`s (e.g., all `cnode-11-*` nodes).

For example, instead of manually constructing a long `--exclude=cnode-07-[...],cnode-08-[...],...` string each time you want to run *only* on `cnode-07-*` nodes, you can define a variable like `USE_CNODE_7` and use it like this:

```
bash srun $USE_CNODE_7 --partition batch [your_command]
```

Or for an interactive session:

```
salloc $USE_CNODE_7 --partition interactive
# Once allocated, run commands or use:
# srun [your_command]
```

## 5. Implementation: Setting Up and Using the Environment Variables

1. **Edit your `~/.bashrc` file:** Add the environment variable definitions provided below to the end of your `~/.bashrc` file (located in your home directory on ARC).
    
    Bash
    
```
export CNODE_7_LEGACYGPU="cnode-07-[5-12]"
export CNODE_7_INTERACTIVE="cnode-07-[13-20]"
export CNODE_7_BATCH="cnode-07-[21-29]"
export CNODE_7="${CNODE_7_LEGACYGPU},${CNODE_7_INTERACTIVE},${CNODE_7_BATCH}"
export CNODE_8_BASIC="cnode-08-[0-3]"
export CNODE_8_INTERACTIVE="cnode-08-[4-15]"
export CNODE_8_LEGACYGPU="cnode-08-[16-23]"
export CNODE_8_BATCH="cnode-08-[24-35]"
export CNODE_8="${CNODE_8_BASIC},${CNODE_8_INTERACTIVE},${CNODE_8_LEGACYGPU},${CNODE_8_BATCH}"
export CNODE_9_BATCH="cnode-09-[5-6,9-13,15-23,25,27,32-35,37-47]"
export CNODE_9="${CNODE_9_BATCH}"
export CNODE_10_GPU="cnode-10-[0,2]"
export CNODE_10_BATCH="cnode-10-[4-27]"
export CNODE_10_INTERACTIVE="cnode-10-[28-31]"
export CNODE_10="${CNODE_10_GPU},${CNODE_10_BATCH},${CNODE_10_INTERACTIVE}"
export CNODE_11_A100="cnode-11-0"
export CNODE_11_GPU="cnode-11-[2,16]"
export CNODE_11_BATCH="cnode-11-[4-15]"
export CNODE_11="${CNODE_11_A100},${CNODE_11_GPU},${CNODE_11_BATCH}"
export CNODE_12_A100="cnode-12-0"
export CNODE_12_GPU="cnode-12-[2,28,29]"
export CNODE_12_BATCH="cnode-12-[4-27]"
export CNODE_12="${CNODE_12_A100},${CNODE_12_GPU},${CNODE_12_BATCH}"
export CNODE_13_GPU="cnode-13-[0-2,28,29]"
export CNODE_13_BATCH="cnode-13-[4-27]"
export CNODE_13="${CNODE_13_GPU},${CNODE_13_BATCH}"
export CONDO="condo-08-[36,37],condo-10-[1,32,33],condo-11-[48,49]"
export USE_XEON_GOLD_6542Y="--exclude ${CNODE_7},${CNODE_8},${CNODE_9},${CNODE_10},${CONDO}"
export USE_XEON_GOLD_6326="--exclude ${CNODE_7},${CNODE_8},${CNODE_9},${CNODE_11},${CNODE_12},${CNODE_13},${CONDO}"
export USE_XEON_E5_2699="--exclude ${CNODE_7},${CNODE_8},${CNODE_10},${CNODE_11},${CNODE_12},${CNODE_13},${CONDO}"
export USE_XEON_GOLD_6132="--exclude ${CNODE_8},${CNODE_9},${CNODE_10},${CNODE_11},${CNODE_12},${CNODE_13},${CONDO}"
export USE_CNODE_7="--exclude ${CNODE_8},${CNODE_9},${CNODE_10},${CNODE_11},${CNODE_12},${CNODE_13},${CONDO}"
export USE_CNODE_8="--exclude ${CNODE_7},${CNODE_9},${CNODE_10},${CNODE_11},${CNODE_12},${CNODE_13},${CONDO}"
export USE_CNODE_9="--exclude ${CNODE_7},${CNODE_8},${CNODE_10},${CNODE_11},${CNODE_12},${CNODE_13},${CONDO}"
export USE_CNODE_10="--exclude ${CNODE_7},${CNODE_8},${CNODE_9},${CNODE_11},${CNODE_12},${CNODE_13},${CONDO}"
export USE_CNODE_11="--exclude ${CNODE_7},${CNODE_8},${CNODE_9},${CNODE_10},${CNODE_12},${CNODE_13},${CONDO}"
export USE_CNODE_12="--exclude ${CNODE_7},${CNODE_8},${CNODE_9},${CNODE_10},${CNODE_11},${CNODE_13},${CONDO}"
export USE_CNODE_13="--exclude ${CNODE_7},${CNODE_8},${CNODE_9},${CNODE_10},${CNODE_11},${CNODE_12},${CONDO}"
export USE_CONDO="--exclude ${CNODE_7},${CNODE_8},${CNODE_9},${CNODE_10},${CNODE_11},${CNODE_12},${CNODE_13}"
```
     
2. **Load the variables:** After saving the changes to `~/.bashrc`, run the following command in your terminal to apply them to your current session:
    
    Bash
    
    ```
    source ~/.bashrc
    ```
    
    (You only need to run `source ~/.bashrc` once after modifying the file. New login sessions will load it automatically).
    
3. **Use the variables:** Include the appropriate variable when submitting your job:
    
    Bash
    
    ```
    # Batch job requesting only nodes in cnode block 11, using the 'batch' partition
    sbatch $USE_CNODE_11 --partition batch my_job_script.sh
    
    # Interactive job requesting only nodes in cnode block 08, using the 'interactive' partition
    salloc $USE_CNODE_8 --partition interactive
    ```

---
## 6. Verifying Hardware and Partitions

- **Check Node Partitions:** Before submitting, ensure the node block you intend to use (e.g., `cnode-11-*`) contains nodes that are actually part of the partition you want to submit to (e.g., `batch` or `interactive`). Use `sinfo` for this:
    
    Bash
    
    ```
    sinfo -N -o "%N %P" | grep cnode-11 # Lists partitions for nodes starting with cnode-11
    sinfo -N # Lists all nodes and their assigned partition & state
    ```
    
- **Specify Partition:** It's crucial to either select a node block variable corresponding to nodes available in your desired partition OR explicitly specify the partition using `--partition <partition_name>`. If the nodes you are targeting are not in the default partition or the partition you specify, your job will not run.
- **Inspect Node Hardware:** To confirm the hardware specifications (like the exact CPU model) of the node your job landed on, run the `lscpu` command from _within_ an active job on that node:
    
    Bash
    
    ```
    srun $USE_CNODE_11 --partition batch lscpu
    ```
    
    Or within an interactive `salloc` session:
    
    Bash
    
    ```
    lscpu
    ```
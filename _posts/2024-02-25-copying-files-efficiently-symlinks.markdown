# Efficient File Copying: The Power of Symbolic Links
Author: Emily Chao (chaoe@ohsu.edu)

With the increasing amount of data and collaboration, copying files is a necessity. Instead of directly duplicating them, symbolic links are a better alternative because they are more efficient, space-saving, and preserve important file permissions!

## Use Cases:

- **Copying files for the same user:**  
  Example: Using the same reference genome and index files across multiple analysis runs in separate folders.

- **Copying files between users:**  
  Example: Transferring data collected by one user to another for further analysis.

## Why Use Symbolic Links?

Traditionally, you would duplicate a file entirely (hard copy). Instead, symbolic links (symlinks) allow you to create a reference to the original file (soft copy).

This method has several key benefits:

1. **Permissions are retained:**  
   For example, if the original file is set to ‘read-only’, your symlinked copy will be as well. This is especially useful when files need to be accessed by different users. In this example, it can maintain the integrity of the file’s contents and prevent any accidental edits.

2. **Link to the original:**  
   A symlink is a direct reference to the original file, and so the original can be easily accessed.  
   To find the absolute path to the original, use:  
` ls -l filepath `. 

3. **Save storage space:**  
A symlink takes up minimal storage space, compared to creating a hard copy. This is especially useful when working with large files or directories, as it reduces the risk of overloading the storage space.  
In our shared storage system, RDS, minimizing storage usage ensures that everyone can access the resources smoothly.

## How to Create Symbolic Links

For **individual** files, do:
``` 
ln -s absfilepath newfilepath
```
**Example:** 
To create a symlink of 'test.txt’ from the chaoe directory to the projects directory: 
```
ln -s /home/groups/CEDAR/chaoe/test.txt /home/groups/CEDAR/projects/test.txt
```

For an **entire directory**, do: 
```
ln -s absdirpath newdirpath
```
**Example:**
To symlink all the files in the chronocell subdirectory from the chaoe directory to the projects directory:  
```
ln -s /home/groups/CEDAR/chaoe/chronocell /home/groups/CEDAR/chaoe/projects/chronocell
```

## Other Resources 
Have more questions about symlinks and their usefulness? 
Refer to these links: 
https://www.linode.com/docs/guides/linux-symlinks/

https://www.freecodecamp.org/news/linux-ln-how-to-create-a-symbolic-link-in-linux-example-bash-command/




You can also email me: chaoe@ohsu.edu 
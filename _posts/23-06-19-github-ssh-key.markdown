To use SSH Key access to Github   
Note: a "public" key is sharable - you will give it to github.
The "private" key should be protected...

Source: https://www.youtube.com/watch?v=s6KTbytdNgs

1. On your local machine (eg, exacloud or laptop) - generate a key pair
$ ssh-keygen -o -t rsa -C "your.email@ohsu.edu"
- If you're inclined to a different protocol, there are other protocols available...

Go to ~/.ssh - you should see (at least) two files - 
private key:  id_rsa
PUBLIC key: id_rsa.pub
Copy the key in id_rsa.pub - it's very long. It will look something like this
ssh-rsa AAA..............@ohsu.edu

2. Log into github
* go to your settings/ssh & GPG keys
 Add a "new SSH Key"
 Paste in the public key

 You're done! Clone the repository using git clone [ssh repo from github repo Code button]

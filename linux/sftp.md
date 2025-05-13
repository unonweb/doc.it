# sftp

**SSH secure file transfer protocol**

SFTP is more or less a direct successor to SCP. The data transfer is encrypted and uses existing SSH infrastructure. In practice this means that hosts’ remote file systems can be accessed with STFP if SSH access is allowed from the local system. The SFTP protocol offers a **significantly broader range of features than SCP**. Transferred data is automatically checked for correctness and interrupted transfers can be resumed.
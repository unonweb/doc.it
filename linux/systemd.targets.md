# Targets

0. <u>system shutdown</u>
   - **halt.target**
   - **poweroff.target**
   - runlevel0.target
1. <u>single-user mode</u>
   - **rescue.target**
   - runlevel1.target
2. <u>local multiuser</u> (no network)
   - **multi-user.target**
   - runlevel2.target
3. <u>full multiuser</u> (with network)
   - **multi-user.target** 
   - runlevel3.target
4. <u>user-defined</u>
   - runlevel4.target
5. <u>full multiuser with network and display manager</u>
   - **graphical.target**
   - runlevel5.target
6. <u>system reboot</u>
   - **reboot.target **
   - runlevel6.target

# list targets

```sh
systemctl list-units --type target --all # list all loaded units regardless of their state
systemctl list-units --type target # list all currently loaded target units
```

* **multi-user.target:**

  often used as the default target a system starts in

  starts everything that is needed for full system functionality and is commonly used on servers

* **graphical.target:**

  also is commonly used

  starts all units that are needed for full functionality, as well as a graphical interface
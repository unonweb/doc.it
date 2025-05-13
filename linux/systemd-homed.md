- FOSDEM: Lennart Poettering: Reinventing Home Directories Let's bring the UNIX concept of Home Directories into the 21st century 
  https://www.youtube.com/watch?v=ZYOHvzv2T64
- supports FIDO2 and PKCS11 for unlocking the home directories

you cryptographically unlock your home dir at the moment of login, and unless you logged in your home directory is entirely inaccessible because there's no decryption key available the system could decrypt it with.

# systemd-homed & cron

per-user cronjobs don't really work anymore as long as the user hasn't logged in, because they cannot access the user's home directory
# systemd-homed & SSH

SSH logins into locked home directories so far wasn't supported: ssh authentication works differently from traditional PAM based authentication after all: it entirely bypasses the Linux PAM auth stack (it does run the account and service stacks however) and instead does authentication via asymmetric key pairs, i.e. those things you traditionally maintain in your .ssh/ directory in the home directory.

And that's a problem for systemd: if systemd-homed cannot hook into the authentication mechanism, ask the user for a LUKS passphrase, or ask them to touch the FIDO2 key or enter its PIN, then it cannot automatically unlock your home directory when you log in via SSH. (If you already logged in before locally, then SSH would work).

So, what's new regarding this problem in v256? Well, it works now: you finally can log in via SSH directly into your super secure systemd-homed account.

How did we make this happen? Roughly like this: first, we let SSH do its authentication. If that succeeded then we permit the log in to go though, but temporarily provide a slightly different user record for the user, where the login shell in the record is redirected to a small stub tool provided by systemd.

This stub tool has only one job: ask the user interactively for the unlock password/PIN/touch/… and then execve() the actual user's shell.

Or in other words: one ssh allows a login to go through you'll end up in something that behaves a lot like a shell, but ensures the home directory is unlocked first, before it passes control to the actual shell.

This little stub hence runs without any home dir accessible, but under the user's identity. It's job is to make the home dir accessible, and then get out of the way, and let the real shell take over.

How does this feel from the client side? Basically if you "ssh" into a host like that, the regular SSH authentication takes place. If this succeeds there are now two options:

1. if the home dir alreads has been unlocked, you get the shell directly as on classic UNIX systems.

2. if the home dir as not been unlocked yet, you get a quick prompt to do so, and once that succeeded you get your shell.
# CLI

```sh
sudo apt install systemd-homed
sudo systemctl enable --now systemd-homed
```

```sh
sudo homectl create test --storage=luks --disk-size=50G -c "Test User"
sudo homectl list
sudo homectl inspect <username>
sudo homectl remove <username>
# remove the users home folder
# remove any storage it had claimed
# remove user from display manager entries
sudo homectl update test --email-address="test@example.com"
```

If you don't define the space used, it will use 85% of free space for a LUKS storage user!

```bash
# add homed user to sudo
echo "test ALL=(ALL) ALL" | sudo tee /etc/sudoers.d/90-homectl
```
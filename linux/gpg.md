# FILES

```sh
~/.gnupg/pubring.kbx
/home/frida/.gnupg/pubring.kbx
```

# How To

## add repository key

Es gibt zwei Formate: Der Schlüssel kann rein binär sein (und dann  kann er sofort genutzt werden) oder aber im Ascii-Format vorliegen. In zweiten Fall muss er zunächst konvertiert werden. Leider können Sie sich nicht darauf verlassen, dass der Betreiber des Repositorys dies über die Dateiendung kenntlich macht. Haben Sie den Schlüssel geladen, hilft eine kleine Probe:

```sh
file <repository-schluessel>.gpg
```

Handelt es sich um einen Ascii-Schlüssel muss dieser konvertiert werden. Das erfolgt über eine Option, die sich `dearmor` nennt.

**Schlüssel von Website**

```sh
wget -O- https://[Webadresse]/apt/keys.asc | sudo -H gpg --dearmor -o sudo tee /etc/apt/keyrings/xyz.gpg
```

**Schlüssel vom Keyserver**

```sh
sudo gpg --no-default-keyring --keyring /etc/apt/keyrings/[Repository]-archive-keyring.gpg --keyserver --recv-keys [Fingerprint]
```

**Alte Schlüssel loswerden**

Wenn Sie einen Schlüssel neu hinzugefügt und die entsprechende  Paketquelle angepasst haben, können Sie den alten Schlüssel entfernen. 

```
apt-key list
```

Hier finden Sie jeweils die Schlüssel-ID, die aus einem zehnstelligen Code besteht. 

Nehmen Sie die letzten vier Ziffern und nutzen Sie dann dieses Kommando (Beispiel):

```
sudo apt-key del C33A7AFF
```

Diese Löschmöglichkeit soll nach Intention der Entwicklergemeinde die einzige Option für „apt-key“ bleiben, die weiterhin funktioniert.

### repository key location

```sh
ll /usr/share/keyrings
ll /etc/apt/keyrings/
```

However, I think it's logical to put `apt` keyrings in `/etc/apt/keyrings` folder, whereas keyrings for other purposes of an application could be put under `/usr/share/keyrings`. In addition, `apt` has another folder called `/etc/apt/trusted.gpg.d/`, where the trusted keyrings for Ubuntu are stored.

### examples

```sh
sudo gpg --keyserver hkp://keyserver.ubuntu.com:80 --no-default-keyring --keyring /usr/share/keyrings/<ppa-name>.gpg --recv-keys 00000000
```



```sh
wget -O- https://updates.signal.org/desktop/apt/keys.asc | gpg --dearmor > signal-desktop-keyring.gpg

cat signal-desktop-keyring.gpg | sudo tee -a /usr/share/keyrings/signal-desktop-keyring.gpg > /dev/null
# downloads the key and puts it in /usr/share/keyrings.
```



# BASICS

a unique identifier for a key pair, it is independent of any protocol, so that the same key can be used with different protocols. 

* No information coming from GnuPG (e.g. the packet version) nor SSH (e.g. the typename) is used to build them
* As it works with both GnuPG and SSH protocols, this identifier is mainly used by gpg-agent.

## protecting your private key

* store the public key's revocation certificate and a backup of your private key on write-protected media in a safe place.

### passphrase

GnuPG does not store your raw private key on disk. Instead it encrypts it using a symmetric encryption algorithm. That is why you need a passphrase to access the key. Thus there are two barriers an attacker must cross to access your private key: (1) he must actually acquire the key, and (2) he must get past the encryption.

## validating other keys

a correspondent's key is validated by personally checking his key's fingerprint and then signing his public key with your private key. Unfortunately, this procedure is awkward when either you must validate a large number of keys or communicate with people whom you do not know personally.

### web of trust

GnuPG addresses this problem with a mechanism popularly known as the web of trust. In this model, responsibility for validating public keys is delegated to people you trust.

* Alice has signed Bob's key, and
* Bob has signed Joe's key and Carla's key.
* If Alice trusts Bob to properly validate keys that he signs, then Alice can infer that Joes's and Carlas's keys are valid without having to personally check them. She simply uses her validated copy of Bob's public key to check that Blake's signatures on Joes's and Carla's are good.

### levels of trust

There are four trust levels:

* *unknown* # Nothing is known about the owner's judgement in key signing. Keys on your public keyring that you do not own initially have this trust level.
* *none* # The owner is known to improperly sign other keys.
* *marginal* # The owner understands the implications of key signing and properly validates keys before signing them.
* *full* # The owner has an excellent understanding of key signing, and his signature on a key would be as good as your own.


# CLI

```sh
gpg --gen-key
```

## armorq

```sh
gpg -a
gpg --armor # create ascii armored output

gpg --dearmor # Pack or unpack an arbitrary input into/from an OpenPGP ASCII armor.
```

## list keys

```sh
gpg --list-keys
gpg --list-public-keys # List the public keys.  
gpg --list-secret-keys # List the secret keys.  

gpg --with-keygrip # Include the keygrip in the key listings
```

```sh
# list-options
gpg --list-options <options> 
# a space or comma delimited string that gives options used when listing keys
gpg --list-keys --list-options show-keyring 
# Display the keyring name at the head of key listings to show which keyring a given key resides on.
```

Usage information for keys and subkeys in the standard key listing (a list of letters indicating the allowed usage for a key): *E=encryption*, *S=signing*, *C=certification*, *A=authentication*

## export keys

```sh
gpg --export > allpublic.key        # export all keys from all keyrings to the specified file
gpg --export <key> > mypublic.key   # export specific key
gpg --export -a apato > apato_public.key
gpg --export-secret-key -a apato > apato_private.key
gpg --export-secret-key <key> > private.key
```

## keyserver

```sh
gpg --search-keys <names> # Search the keyserver for the given names. Multiple names given here will be joined together to create the search string for the keyserver. Note that keyservers search for names in a different and simpler way than gpg does.  The best choice is to use a  mail  address. Due to data privacy reasons keyservers may even not even allow searching by user id or mail address and thus may only return results when being used with `--recv-key` to search by key fingerprint or keyid.

gpg --fetch-keys <uri> # Retrieve keys located at the specified URIs. Note that different installations of GnuPG may support different protocols (HTTP, FTP, LDAP). When using HTTPS the system provided root certificates are used by this command.
```

```sh
gpg --keyserver pool.sks-keyservers.net --recv-keys FBF1FC87DAD95197
# import keys matching FBF1FC87DAD95197
```


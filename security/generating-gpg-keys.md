# Generating GPG Keys

## Prerequisites

Check that GnuPG is correctly installed

```text
gpg --version
```

If its not installed, which unlikely for any Linux distributions, you can run the following:

```text
sudo apt update
sudo apt install gnupg
```

Or for those on Mac OS X \(assuming you have Homebrew installed\):

```text
brew install gpg
```

## Initialising configuration files

GnuPG can be optionally configured using the `gpg.conf` file, which is to be created on `~/.gnupg/`. The motivation behind these set of configurations are a result of adopting best practices relevant to using GnuPG. For more information, you can explore this [site](https://riseup.net/en/security/message-security/openpgp/best-practices) to learn more about the best practices. As for the configuration files, they will follow [this configuration](https://raw.githubusercontent.com/ioerror/duraconf/master/configs/gnupg/gpg.conf) \(do note that some of these options have already been deprecated\).

Download the configuration file and save it as `gpg.conf`

```text
curl https://raw.githubusercontent.com/ioerror/duraconf/master/configs/gnupg/gpg.conf > gpg.conf
```

Note: Do this step on another computer before you move on to the next step, where you will generate the keys on an airgapped computer. Once the file is generated, transfer the file securely to the airgapped computer's `~/.gnupg/` directory

Refresh with new configuration

```text
gpg-connect-agent reloadagent /bye
```

## Generating Master Key

The informative overview of GnuPG and the recommended master-sub key structure can be found here on [Debian's wiki](https://wiki.debian.org/Subkeys). To be absolutely safe, please make this is being done on an airgapped machine, e.g. Debian Live image without internet access.

To generate a set of master keys:

```text
gpg --expert --full-gen-key
```

Toggle `S`, `E`, and make sure that the key is only able to `Certify`  Choose `(8) RSA (set your own capabilities)`  Choose `4096`  Choose `0`

Note: The GPG keys are set not to expire because key renewals are very irritating to me, and it doesn't add much security. It is more important to secure the master.key at the get-go, and more even more important to \(separately\) backup the revocation.key to ensure a compromised key can be invalidated. For more information regarding this, refer to this [answer](https://security.stackexchange.com/questions/14718/does-openpgp-key-expiration-add-to-security/79386#79386) on StackExchange

To show the public key generated:

```text
gpg -k
```

And to show private key:

```text
gpg -K
```

## Generating Sub Keys

Next, 3 subkeys will be generated, each with their own specific function of `Sign`, `Encrypt`, and `Authenticate`. Repeat the following steps for each of the there keys

Access the gpg interface

```text
gpg --expert --edit-key $KEYID
```

Invoke the `addkey` command

```text
addkey
```

Toggle `S`, `E`, `A` options accordingly to achieve 3 keys with separate functions  Choose `(8) RSA (set your own capabilities)`  Choose `4096`  Choose `0`

Verify the 3 keys have been correctly created

```text
list
```

Save changes

```text
save
```

## Backing up keys

Save the following keys to an encrypted USB flash drive, to be used ONLY on an airgapped computer.

Change directory to the USB drive or backup path

```text
cd /path/to/backup
```

Backup public key

```text
gpg --armor --export > public.asc
```

Backup secret key

```text
gpg --armor --export-secret-keys > secret.asc
```

Backup 'laptop' key

```text
gpg --armor --export-secret-subkeys > laptop.asc
```

Backup trust

```text
gpg --armor --export-ownertrust > trust.asc
```

Generate & backup revocation certificate

```text
gpg --armor --gen-revoke > revoke.asc
```

Backup pre-generated revocation certificate

```text
cp ~/.gnupg/openpgp-revocs.d/*.rev
```

\(Optional\) If you want, you may want to compress these into a file.

```text
cd ..
tar cvf gpg.conf /path/to/backup
```

Saves these files securely

## Uploading public key

If you are ready, and you deem that uploading your key to a public keyserver is appropriate, you may run the following command. Do take note that any information pushed to a public SKS/PGP keyserver is strictly uneditable/unremovable.

Upload to `pgp.mit.edu` keyserver

```text
gpg --keyserver hkps://pgp.mit.edu --send-key $KEYID
```

## Transfer keys to OpenPGP/Yubikey

This step requires first setting up the key, then transferring the keys to the yubikey. Make sure this step is also done on an airgapped machine

Install `scdaemon`

```text
sudo apt update
sudo apt install scdaemon
```

Show card status

```text
gpg --card-status
```

Import secret keys if its not currently on the airgapped machine

```text
gpg --import secret.asc
gpg --import-ownertrust trust.asc
```

### Initialisation of OpenPGP Card

Setup card

```text
gpg --edit-card
```

Set custom passwords

```text
admin
passwd

1
123456
<DESIRED PASSWORD>

3
12345678
<DESIRED ADMIN PIN>

q
```

Fill in other information

```text
name
<FULL NAME>

url
hkps://pgp.mit.edu

login
<LOGIN NAME>

list

q
```

### Transfer Signing, Encrypting, and Authentication Keys to card

Access the gpg interface

```text
gpg --expert --edit-key $KEYID
```

```text
toggle
```

Transfer `S` key

```text
key 1
keytocard
1
```

Transfer `E` key

```text
key 1
key 2
keytocard
1
```

Transfer `A` key

```text
key 2
key 3
keytocard
1
```

Save changes

```text
save
```

## Delete all keys

Once you are done, you make remove the keys and destroy the live image.

Delete secret keys

```text
gpg --delete-secret-keys $KEYID
```

Delete public keys

```text
gpg --delete-keys $KEYID
```

## Using OpenPGP/Yubikey on a new computer

Verify that no keys exist on the machine

```text
gpg -k
gpg -K
```

Import public key from SKS/PGP Keyserver

```text
gpg --keyserver hkps://pgp.mit.edu --recv-key $KEYID
```

\[Optional\] You can also import the key from `public.asc`

```text
gpg --import public.asc
```

Copy key stubs to local machine

```text
gpg --card-status
```

Set trust level of key

```text
gpg --edit-key $KEYID
trust
5
Y
save
```

## Use OpenPGP/Yubikey to encrypt and decrypt files

To encrypt

```text
echo 'Hello World!' > test
gpg -s -e test
```

To decrypt

```text
gpg -d test.gpg
```


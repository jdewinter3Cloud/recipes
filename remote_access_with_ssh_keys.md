# Setting Up Remote Access Using SSH Keys

- this is a summary of [this page](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-22-04)

## Estimated Duration

- 5-10 minutes

## Steps to Follow

- enable access to SSH on [remote system](https://linuxize.com/post/how-to-enable-ssh-on-ubuntu-18-04/)
  - install through the linux package manager
    ```
    sudo apt update
    sudo apt install openssh-server    
    ```
  - for most distros, the service will start automatically
  - validate that it is up and running by entering:
    ```
    sudo systemctl status ssh
    ```
- create a new [key pair](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-22-04#step-1-creating-the-key-pair)
  using `ssh-keygen`
  - unless you are creating a keypair for your own access, do not use the default filename of `id_rsa`
  - select a file name that reflects what you are doing and do not assign a passphrase
  - the generated pair includes a key file (no extension) and a public file (`.pub` extension)
- [copy the public key](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-22-04#copying-the-public-key-using-ssh)
  to the target system
  - use `cat ~/.ssh/id_rsa.pub | ssh username@remote_host "mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys && chmod -R go= ~/.ssh && cat >> ~/.ssh/authorized_keys"`
    - replace `id_rsa` with the name of the key pair that was generated
    - replace `username` usually with `root`
    - replace `remote_host` with the remote host name to install the creds on
  - there are more "elegant" ways to do it, but this action clearly spells out each step and allows you to verify it
- [authenticate](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-22-04#step-3-authenticating-to-your-ubuntu-server-using-ssh-keys)
  to the remote server
  - using the same `username` and `remote_host`, perform `ssh username@remote_host`
  - if the key-pair is not on your "keychain", may need to do `ssh username@remote_host -i ~/.ssh/private.key` to explicitly provide the key
  - if it is the first time hitting a host, will see a message like:
    ```
    The authenticity of host '203.0.113.1 (203.0.113.1)' can't be established.
    ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
    Are you sure you want to continue connecting (yes/no)? yes
    ```
    - this message just confirms that the identity of the SSH server at the other end remains constant
- [disable non-key access](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-22-04#step-4-disabling-password-authentication-on-your-server)
  - edit the config file on the server by `sudo nano /etc/ssh/sshd_config`
  - make sure password access is off
    ```
    PasswordAuthentication no
    ```
  - save and `sudo systemctl restart ssh` to restart the SSH service
  - verify that no passwords are prompted for

## Notes

- information provided for Ubuntu, one of the more popular distros
  - installation for other distros is similar

## Examples

- generating key-pair `bob`
  - should see output like this
    ```
    >ssh-keygen
    Generating public/private rsa key pair.
    Enter file in which to save the key (C:\Users\Me/.ssh/id_rsa): bob
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in bob
    Your public key has been saved in bob.pub
    The key fingerprint is:
    SHA256:8eww8rm9JHZ9seGgUQnWxUmXYPrHyBWv7JH6BLYnvuU azuread\Me@DESKTOP-QDKV3OQ
    The key's randomart image is:
    +---[RSA 3072]----+
    |          o. *+oo|
    |         . .+.ooo|
    |        .  .o  ..|
    |         + .o.+o |
    |      . S + ++Bo |
    |       o = = B.= |
    |        = = = O  |
    |       . * . O   |
    |        . o.o.E  |
    +----[SHA256]-----+
    ```


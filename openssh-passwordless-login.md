# OpenSSH Passwordless Login


The power of ssh without the hassel of typing in your password everytime

* tutorial video: [Link](https://www.youtube.com/watch?v=LlGL-uBSe6M)
* offical website: [Link](http://www.openssh.com/)

### install requirements
    openssh


### generate a public key 
- this will create id_rsa and id_rsa.pub files in ~/.ssh folder
- press **enter** for all the prompt questions


    ssh-keygen

    
    # example prompt
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/heoyea-ant/.ssh/id_rsa):[Press Enter key]
    Enter passphrase (empty for no passphrase): [Press Enter key]
    Enter same passphrase again: [Pess Enter key]
    Your identification has been saved in /home/heoyea-ant/.ssh/id_rsa.
    Your public key has been saved in /home/heoyea-ant/.ssh/id_rsa.pub.


### send public key
- this store public key to ~/.ssh/authorized_keys file on server machine
- Note: ssh-copy-id command will append keys, not overwrite it ( good for multiple keys)
 
		ssh-copy-id -i ~/.ssh/id_rsa.pub heoyea-core@192.168.1.100
		
![alt text](http://i.imgur.com/wwZmOLf.png)


### send public key (method 2)
    # send keys to server
    scp ~/.ssh/id_rsa.pub heoyea-core@192.168.1.100:/tmp/
    
    # login to server machine then append keys manually
    ssh heoyea-core@192.168.1.100
    cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys
    rm -f /tmp/id_rsa.pub

![alt text](http://i.imgur.com/Mml6JpU.png)

----
## ANROID Section
### generate a public key ( Android )

[ConnectBot](https://play.google.com/store/apps/details?id=org.connectbot&hl=en) > Settings Button > Manage Pubkeys > Generate
    
    Nickname: Samsung_Note2
    Type: RSA
    Bits: 1024
    [X] Load key on start


### send public key from android to server

- [ConnectBot](https://play.google.com/store/apps/details?id=org.connectbot&hl=en) > Settings Button > Manage Pubkeys
- Copy public key
- Connect to Server


    echo "PASTEKEYHERE" >> ~/.ssh/authorized_keys


![alt text](http://i.imgur.com/7r01HJq.png)

### references
http://michaelchelen.net/0f3e/android-connectbot-ssh-key-auth-howto/

### contact

                 _   _     _      _         
      __ _  ___ | |_| |__ | | ___| |_ _   _ 
     / _` |/ _ \| __| '_ \| |/ _ \ __| | | |
    | (_| | (_) | |_| |_) | |  __/ |_| |_| |
     \__, |\___/ \__|_.__/|_|\___|\__|\__,_|
     |___/                                  

- http://www.youtube.com/user/gotbletu
- https://twitter.com/gotbletu
- https://www.facebook.com/gotbletu
- https://plus.google.com/+gotbletu
- https://github.com/gotbletu
- gotbletu@gmail.com



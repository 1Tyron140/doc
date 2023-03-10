# Quelques mesures de sécurité

## Chroot

Chroot permet de cloisonner les users dans un répertoire afin de rendre inaccessible certaines parties du serveur.

__Sans chroot__:

![Yast ftp sans chroot](https://raw.githubusercontent.com/1Tyron140/doc/main/images/ftp/ftp_general_yast.png)
chroot est désactivé

![terminal ftp sans chroot 1](https://raw.githubusercontent.com/1Tyron140/doc/main/images/ftp/sans_chroot_cli_1.png)
![terminal ftp sans chroot 2](https://raw.githubusercontent.com/1Tyron140/doc/main/images/ftp/sans_chroot_cli_2.png)

* (1) Je me connecte sur mon compte "master"
* (2) Mon répertoire de connexion est`/home/master`
* (3) Le répertoire racine est celiu de la machine
* (4) Je liste les fichiers de `/` et il s'agit bien de la racine de machine

Je ne souhaite pas que ces répertoires soient accessibles car ce n'est pas nécessaire voire risqué.

__Avec chroot__:

![yast ftp avec chroot](https://raw.githubusercontent.com/1Tyron140/doc/main/images/ftp/avec_chroot.png)

J'ai ajouté un fichier "test.txt" dans le nouveau répertoire racines choisi  `/srv/ftp` pour la démo.

![terminal ftp avec chroot](https://raw.githubusercontent.com/1Tyron140/doc/main/images/ftp/avec_chroot_cli.png)

* (1) Je me connecte sur mon compte "master"
* (2) Mon répertoire de connexion est différent car c'est la racine `/`
* (3) Je retrouve mon fichier test.txt que j'ai mis dans `/srv/ftp` donc le répertoire racine est ce dernier

## Passer en FTPS

### Création du certificat et de la clé

`openssl req -x509 -newkey rsa:4096 -x509 -days 3650 -nodes -keyout /etc/ssl/private/ftpkey.key -out /etc/ssl/certs/ftpcert.crt -nodes -days 3650`
* Autoriser la lecture clé à l'utilisateur ftp (il est dans le groupe ftp)

`chown root:ftp /etc/ssl/private/ftpkey.key`
`chmod 640 /etc/ssl/private/ftpkey.key`

* Autoriser la lecture du certificat à l'utilisateur ftp

`chown root:ftp /etc/ssl/certs/ftpcert.crt`
`chmod 640 /etc/ssl/certs/ftpcert.crt`

### Activer TLS

* Ouvrir YAST

* Serveur FTP

* Paramètres pour les experts 

* Autoriser SSL > autoriser TLS

* Sélectionner le certificat crée précédemment

![config tls sur yast](https://raw.githubusercontent.com/1Tyron140/doc/main/images/ftp/tls_ftp.png)

* Ajouter la clé dans /etc/vsftpd.conf

En dessous de rsa_cert_file, ajouter 

`rsa_private_key_file=/etc/ssl/certs/ftpcert.crt`

* Redémmarrer vsftpd*

![config tls sur yast](https://raw.githubusercontent.com/1Tyron140/doc/main/images/ftp/filezilla_ftp_tls.png)

* Maintenant lorsque je me connecte, je dois accepter le certificat

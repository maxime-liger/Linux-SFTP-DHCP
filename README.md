Configuration Linux pour SFTP et DHCP.

DHCP ↓


$ apt install sudo


$ apt-get install isc-dhcp-server < installé le service DHCP ( Si erreur = normal ) 


$ nano /etc/dhcp/dhcpd.conf < configurer la plage d'adresse IP

° En bas de ( # A slighty different configuration for an interanal subnet. ) 

1. Chosire le Numero de réseau qui va etre utilisée a
 ( # subnet 10.5.5.0 netmask 255.255.255.254 ) exemple ( 172.16.0.0 255.255.0.0 )
 
2.  Chosir la plage d'IP ou Range

3. Potentiellement ajouté l'IP du DNS  a la place de ( ns1.internal.exemple.org )

4. A ( option routeurs ) il faut mettre la Gateway
 
 
   $ nano /etc/network/interfaces < Mettre IP en statique
 
   1. EXemple de configuration en dessous de ( # The primary network interface )    
                                                                  ( auto eth 0 ) 
                                                                  ( allow-hotplug eth0 )                                                  
                                                                  ( iface eth0 inet dhcp )                                                    
                                                   
                                                                  ( address 192.168.0.5 )
                                                                  ( netmask 255.255.255.0 ) 
                                                                  ( gateway 192.168.0.254 )                       
   
   3. Et suprimée ( dhcp ) et écrire ( static ) a la place.
   
                                                                                                                      
  $  nano /etc/default/isc-dhcp-server < définir l'interface

° Mettre l'interface comme cela ( INTERFACESv4="eth0" )

° Faire ( ip addr ) pour voir l'interface utilisée comme ( eth0 )


$ systemctl restart isc-dhcp-server < censé redémarré le service DHCP voir
$ si erreur 


$  journalctl -xeu  isc-dhcp-server.service < Afficher les logs d'erreur

° Ne pas oublier les { }  et ;

° Bien fair conrespondre le ( .conf ) et ( interfaces )  


$ systemctl status isc-dhcp-server.service < Vérifier l’état du service DHCP ( doit être active )


$  cat /var/lib/dhcp/dhcpd.leases < voir les attribution IP 
 
 ° Chaque fois qu'un client DHCP obtient une IP , les détails de l'attribution IP sont enregistrés dans ce fichier
 
 il faut qu'il est 2 carte réseau en NAT pour pouvoir attribuer des IP 
 
 
 
                                      Client DHCP  
  
  
  $ sudo dhclient -r eth0 < Libérer l'adresse IP

$ sudo dhclient eth0< Renouveler l'adresse IP

$ sudo systemctl restart networking <  redémarrer le service réseau 



SFTP ↓ 
                                      
                                                                                                                              
                      
$ sudo apt install ufw < insatlle le paquet ufw 


$ sudo apt install openssh-server -y < Installer OpenSSH


$ sudo systemctl status ssh < Vérifiez que le service SSH est actif


$ sudo adduser laplateforme < Créez un utilisateur pour l'accès SFTP

° Definir le Mot de passe Marseile13!


$ sudo chown root:root /home/laplateforme < Change les permissions du répertoire home :


$ sudo chmod 755 /home/laplateforme


$ sudo mkdir /home/laplateforme/data < Crée un sous-dossier pour les fichiers et donne des permissions à l'utilisateur :


$ sudo chown laplateforme:laplateforme /home/laplateforme/data


$ sudo nano /etc/ssh/sshd_config < configurer OpenSSH Restreindre laplateforme à SFTP et chroot


°  Match User laplateforme
   
    ForceCommand internal-sftp <  l'utilisateur est limité à SFTP

    PasswordAuthentication yes

    ChrootDirectory /home/laplateforme <  l'utilisateur est "enfermé" dans ce répertoire

    AllowTCPForwarding no

    X11Forwarding no

    PermitTunnel no

    MaxSessions 1 <  limite le nombre de sessions à une seule à la fois.


$ sudo systemctl restart ssh < Redémarrer le service SSH


A faire sur les 2 machine 


$ sudo  ss -tnlp | grep 22 < Vérifie que le port 22 est bien ouvert

° Si SSH écoute bien sur 0.0.0.0:22 ou 192.168.1.100:22, alors il est accessible depuis une autre machine du réseau.


$ sudo ufw allow 22/tcp < Ouvre le port 22


$ sudo ufw reload


$ sudo ufw status < Voir si les règles sont active  

A faire sur la machine cliente 


$ sftp lapalteforme@192.168.0.5 < user-client@IP-SFTP


$  put fichier.txt < Copie un fichier de la machine local vers le serveur SFTP.


$ get fichier.txt < Copie un fichier du serveur SFTP vers la machine local.


$ rename /dossier1/fichier.txt  /dossier2/fichier.txt < Déplacer fichier. 


$ rm fichier.txt < Supprimer fichier.


$  rm dossier/* < Supprimer contenue d'un dossier.


$ rmdir dossier < Suppromer dossier vide.


° Avec SFTP nous pouvons, transférer, déplacer, supprimer et renommer des fichiers
  mais pas en crée. En revenche nous pouvont crée des dossier.

° Avec SFTP nous ne pouvons pas déplacer un fichier ou dossier entre le SRV SFTP et le client mais seulement le copier , nous pouvont uniquement déplacer
   fichier et dossier dans le serveur SFTP.   


                                                                                                    
                                                                                                                              
                                                                                                                              


# Born2BeRoot


### 1. Attribution des privilèges `sudo` à votre user
___
###### BUT: Installer le paquet `sudo`, se connecter en tant que `root`, attribuer votre user au groupe `sudoers` et lui attribuer les privilèges `sudo`. 
--- 
##### Vérifier si le paquet `sudo`a été installé
```
apt-cache policy sudo
```
***
Vous devez voir `(aucun)`ce qui veut dire que le paquet sudo n'est pas installé sur votre machine virtuelle. 

Commande générale `apt-cache policy [nom-paquet]`.

En effet lors de l'installation, nous avons choisi de n'installer aucun logiciel. Votre machine virtuelle à ce stade contient uniquement le noyau du système.
---
##### Se connecter en tant que `root` et insérer le mot de passe de `root`:
```
su -
```
***
Remarquez que lorsque vous avez saisi la commande, le prompt a été modifié. L'utilisateur root est suivi du signe `#`alors qu'un utilisateur normal est suivi du signe `$`. 

Exemple pour root:
```
root@yourlogin42:~# 
```

Exemple pour un utilisateur normal:
```
yourlogin@yourlogin42:~$
```
---

##### Mettre à jour la liste des fichiers disponibles dans les dépôts APT:

```
apt-get update
```
---
Les dépôts APT (*Advanced Packaging Tool*) sont des "sources de logiciels", concrètement des serveurs qui contiennent un ensemble de paquets. À l'aide d'un outil appelé gestionnaire de paquets, vous pouvez accéder à ces dépôts et, en quelques clics, vous trouvez, téléchargez et installez les logiciels de votre choix.


Il est recommandé de mettre à jour les dépôts APT avant l'installation d'un nouveau paquet (commande `update`).
***

##### Installer le paquet `sudo`
```
apt-get install [nom-paquet]
```
***
Ici on veut installer le paquet `sudo`-> donc `apt-get install sudo`

---

##### Attribuer votre user au groupe `sudoers`:

```
usermod -aG sudo [user-name]
```

##### Vérifier si les membres du groupe `sudo` ont les privilèges sudo:
```
visudo
```
---
`visudo` est un utilitaire de vérification qui ouvre le fichier de configuration du groupe sudo : `/etc/sudoers`. 
Ce fichier doit être modifié uniquement en mode `root`.
Il est recommandé de ne pas modifier directement ce fichier, mais d'ajouter un nouveau fichier de configuration dans le dossier `/etc/sudoer.d/`
***

##### Chercher la règle suivante:
```
# User privilege specification
root            ALL=(ALL:ALL) ALL
```
---
###### Cette règle veut dire que l'utilisateur `root` a des privilèges illimités et peut exécuter n'importe quelle commande sur n'importe quel system.
###### Vous n'avez rien à modifier ici. Le `#` sert à insérer des commentaires.

**`root`**  -> indique le nom d'utilisateur auquel la règle sera appliquée.

**`ALL`**`=(ALL:ALL) ALL`   -> le 1<sup>er</sup> `ALL` indique que la règle est appliquée à tous les **hosts**.

`ALL=(`**`ALL`**`:ALL) ALL`  -> le 2<sup>ème</sup> `ALL` indique que les membres du groupe sudo peuvent exécuter des commandes en tant que tous les **users**.

`ALL=(ALL:`**`ALL`**`) ALL`   -> le 3<sup>ème</sup> `ALL` indique que les membres du groupe sudo peuvent exécuter des commandes en tant que tous les **groupes**.

`ALL=(ALL:ALL)`**` ALL`**     -> le 4<sup>ème</sup> `ALL` indique que la règle s'applique pour toutes les **commandes**.
***

##### Avez-vous la ligne suivante?

```
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
```

---
Cette règle attribue des privilèges illimités à tous les membres du groupe `sudo` qui peuvent exécuter n'importe quelle commande sur n'importe quel system. Le `#` sert à insérer des commentaires.

Si vous avez cette règle, vous n'avez rien à modifier. 

Si vous n'avez pas cete règle, ajoutez-la.

**`%`**`sudo` -> le signe `%` indique un groupe, `sudo` est le nom du groupe. Donc c'est le groupe auquel la règle sera appliquée.

***

##### Revenir en mode `user`
```
exit
```

##### Vérifier que votre user appartient bien au groupe `sudoers`:

```
getent group [group-name]
```

---
Le groupe sudoers s'appelle `sudo`. Donc la commande sera: `getent group sudo`.

Cette commande est une manière parmi plein d'autres qui vous permet de vérifier si votre user appartient bien au groupe `sudoers`. Vous devez obtenir une résultat similaire:
```
sudo:x:27:[user-name]
```  
*** 


##### Vérifier que votre user a des privilèges sudo:

```
sudo -i
```
---
Cette commande vous renvoie en mode `root` parce que votre user a des privilèges sudo. Oubliez-pas de sortir de ce mode avec la commande:
```
exit
```
Pratiquement, vous pouvez écrire n'importe quelle commande commençant par le mot-clé `sudo`. Si la commande s'effectue sans message d'avertissement, vous avez bien les privilèges `sudo`. P.ex. la commande `sudo visudo` devrait ouvrir le fichier `/etc/sudoers` comme lorsqu'on l'a ouvert depuis le compte `root`.
***

### 2. Configuration SSH

---
### SSH - serveur shell sécurisé (SSH),  qui permet de se connecter à une machine distante et d'y exécuter des commandes sur shell. 

###### Les ordinateurs communiquent entre eux via des réseaux. Par conséquent, les chercheurs en réseau ont défini un ensemble de règles pour communiquer avec d'autres machines et ont commencé à développer des protocoles qui permettent qu'un utilisateur puisse prendre le contrôle d'un autre ordinateur.

###### Les commandes que l'utilisateur pourrait exécuter consistent à exécuter des programmes, créer des répertoires, créer/supprimer/transférer des fichiers, démarrer/arrêter des services, etc.

###### Mais si les protocoles ne sont pas sécurisés, alors n'importe qui au milieu du réseau peut intercepter et lire les données transférées. 

###### SSH est un protocole réseau sécurisé permettant d'**accéder à des ordinateurs distants dans un réseau**.

###### SSH utilise une **architecture client-serveur** pour une communication sécurisée sur le réseau en connectant un client ssh au serveur ssh. 

###### Il utilise une technique de **cryptographie à clé publique** pour **s'authentifier** entre le client et le serveur. De plus, le protocole utilise algorithmes de cryptage et de hashing pour l'échange de messages entre le client et le serveur afin d'assurer la confidentialité et l'intégrité des données.

###### La plupart des **sessions SSH** (période pendant laquelle nous accédons au serveur distant) n'auront que les deux opérations suivantes :

###### - Authentification
###### - Exécution de la commande

###### Le serveur authetifie le client grâce à un mot de passe et une paire de clés SSH.

###### Une fois que le serveur a authentifié le client avec succès, une connexion sécurisée est établie entre eux.

###### SSH crypte la session de connexion et empêche ainsi tout agresseur de recueillir des mots de passe non-cryptés.

###### Pour vous connecter à un serveur distant à l'aide de SSH, vous devez savoir au moins deux choses.

###### - hôte du serveur 
###### - nom d'utilisateur 
##### La syntaxe de la commande ssh de base est :
```
ssh [user-name]@[host]
ssh [user-name]@[host] -p [port-no]
```
###### La commande de la clé SSH indique à votre système que vous souhaitez ouvrir une connexion Secure Shell cryptée. 

###### `[user-name]` est la machine distante que nous essayons de connecter (et non l'utilisateur sur votre machine locale).

###### `[host]` fait référence à l’ordinateur auquel vous souhaitez accéder - soit une adresse IP, soit un nom de domaine.

###### L'option `-p [port-no]` est facultative. Elle permet de désigner un autre port de choix. Si rien n'est péecisé, le port 22 sera utilisé par défaut, parce que toutes les connexions SSH écoutent sur le port 22.

###### Lorsque vous appuyez sur Entrée, vous serez invité à entrer le mot de passe du compte demandé. Si votre mot de passe est correct, vous serez accueilli avec une fenêtre de terminal à distance.

##### Lorsque vous souhaitez mettre fin à la session ssh, tapez sortie commander:

```
exit
```

### Les ports

![Clipboard_2022-11-30-18-12-09](https://user-images.githubusercontent.com/109855801/206394359-6fd1c514-e514-4998-bcfa-7b1e32bb46a0.png)


###### Pour le client, un service est souvent désigné par un nom symbolique. Ce nom doit être converti en une adresse interprétable par les protocoles du réseau.

###### La conversion d’un nom symbolique (par ex. www.google.com) en une adresse IP (216.239.39.99) est à la charge du service DNS

###### En fait, l’adresse IP du serveur ne suffit pas, car le serveur (machine physique) peut comporter différents services; il faut préciser le service demandé au moyen d’un numéro de port, qui permet d’atteindre un processus particulier sur la machine serveur.

![Clipboard_2022-11-30-18-16-21](https://user-images.githubusercontent.com/109855801/206394406-4e47cc5f-0ec4-4ac7-ba99-afecebaa4d5e.png)


###### L'adresse IP sert à identifier de façon unique une machine sur le réseau tandis que le numéro de port indique le service auquel les données sont destinées.

###### Un numéro de port comprend 16 bits (0 à 65'535) et est associé à un protocole de transport donné

###### Le port est en quelque sorte une "porte" qui permet d'attribuer une information à un service du serveur.
***

##### Installation du paquet Openssh-server. Celui-ci installe le server SSH:

```
sudo apt-get update
sudo apt-get upgrade
sudo apt install openssh-server
```
---
Comme mentionné au point 1. il faut mettre à jour le **dépôt** APT **avant** chaque installation d'un nouveau paquet (commande `update`). 

La commande `upgrade` permet d'actualiser **tous** les **paquets** installés avant cette nouvelle actualisation du dépôt APT. 
***
##### Vérifier que le paquet a été bien installé
```
apt-cache policy openssh-server
```

##### Vérifier le status du server SSH
```
sudo systemctl status ssh
```
---
Vous devez voir en vert `Active (running)` et `Server listening on 0.0.0.0 port 22.`. Si ce n'est pas le cas, activez le server SSH avec la commande:
```
sudo systemctl start ssh
```
et vérifiez de nouveau le status.
***

##### Changer le port d'écoute d'SSH pour le port 4242

```
sudo nano /etc/ssh/sshd_config
```
---
Cette commande permet d'aller dans le dossier `/etc/ssh` et ouvrir le fichier `sshd_config`.

`/etc/ssh` - le répertoire du service OpenSSH

`sshd_config` - le fichier de configuration du serveur OpenSSH.

Pour information le "d" de `sshd` est une marque courante des configurations serveur car il se rapporte à "daemon" qui est la façon dont on désigne des applications qui tournent sur un serveur Linux.
***

##### Trouver la ligne `#Port 22` et la faire remplacer par `Port 4242` (sans `#`)

---
Les configuration par défaut sont mises en commentaires pour l'information du lecteur.

Par défaut, toutes les connexions SSH fonctionnent sur le port 22. Le port 22 fait maintenant partie des *"well known port"*, ces ports qui sont "réservés" par des applications connues. Cependant, une bonne pratique de sécurité consiste à changer le port d'écoute du serveur SSH.

Cela pour une raison simple - il existe sur internet une quantité importante de robots qui scannent les ports 22 de toutes les IP publiques pour y trouver un serveur SSH à exploiter, en changeant le port par défaut de votre service SSH, vous vous protégerez d'un grand nombre de robots qui automatisent scans et attaques.

***

##### Sauvegarder

##### Relancer le service SSH afin de valider les nouveaux paramètres

```
sudo systemctl restart ssh
```
---
Pensez à relancer ssh à chaque fois que vous apportez une modification dans le fichier `sshd_config`
***

##### Vérifier le status du server SSH
```
sudo systemctl status ssh
```
---
Vous devez voir en vert `Active (running)` et `Server listening on 0.0.0.0 port 4242.`.
***

### 3. Intaller et configurer UFW

---
###### UFW, ou Uncomplicated Firewall, est une interface de gestion de pare-feu simplifiée qui masque la complexité des technologies de filtrage de paquets. Si vous souhaitez commencer à sécuriser votre réseau, et vous n’êtes pas sûr de l’outil à utiliser, UFW peut être le bon choix pour vous parce que c'est facile à utiliser.
###### Un pare-feu (ou coupe-feu, barrière de sécurité ou firewall), dans le contexte d'un réseau informatique, est un composant essentiel de la sécurité des réseaux informatiques. Son but est de protéger un réseau informatique des intrusions indésirables en filtrant les communications autorisées ou non entre deux réseaux informatiques (généralement dans un contexte domestique : votre réseau privé domestique et le réseau Internet). 
###### Le pare-feu pourrait être comparé à un agent de sécurité à l'aéroport. Pour entrer dans votre pays, un visiteur étranger doit passer par un poste-frontière et être contrôlé par un douanier qui, selon des instructions qu'il doit suivre, le laissera passer ou lui fera rebrousser chemin. Pareillement, un habitant de votre pays doit passer un contrôle avant de monter dans un avion vers une destination extérieure ; suite à son contrôle, le voyageur pourra continuer ou non sa route. Le pare-feu joue ce rôle, au niveau informatique : il filtre les connexions qui arrivent dans votre ordinateur et celles qui sortent de votre ordinateur, et bloque celles qui sont indésirables selon ce que vous lui avez paramétré comme politique de sécurité.
###### Un pare-feu se présente essentiellement sous deux formes :
###### *Logicielle* : un programme qui fonctionne dans votre ordinateur personnel ou de bureau et assure le rôle de filtrage des connexions. Un pare-feu logiciel doit être installé dans chaque ordinateur ;
###### *Matérielle* : un composant physique de votre réseau domestique qui inclut un logiciel de pare-feu. Un pare-feu matériel doit être présent une seule fois dans un réseau informatique – au passage entre un réseau privé et un réseau public.
***

##### Installation du paquet UFW

```
sudo apt-get update
sudo apt-get upgrade
sudo apt install ufw
```

##### Autoriser les connexions SSH entrantes

```
sudo ufw allow 4242
```
---
Si nous activions notre pare-feu UFW maintenant, il refuserait toutes les connexions entrantes. Cela signifie que nous devrons créer des règles qui autorisent explicitement les connexions entrantes légitimes – connexions SSH ou HTTP, par exemple – si nous voulons que notre serveur réponde à ce type de demandes. 
Comme le démon SSH a été configurer pour utiliser un port différent que le port 22, le port approprié a été spécifié (ici 4242).
***

##### Activer UFW
```
sudo ufw enable
```
---
Maintenant que votre pare-feu est configuré pour autoriser les connexions SSH entrantes, nous pouvons l’activer.

Vous pouvez recevoir un avertissement qui indique que la commande peut perturber les connexions SSH existantes. Nous avons déjà mis en place une règle de pare-feu qui autorise les connexions SSH, donc nous pouvons continuer. Répondez à l’invite avec y et appuyez sur ENTER.
***

##### Afficher les règles UFW numérotées

```
sudo ufw status numbered
```

### 4. Redirection de port (Port Forwarding)
---
###### VirtualBox est un programme utilisé pour exécuter et passer facilement entre plusieurs systèmes d'exploitation sur votre système d'exploitation. Il est particulièrement utile pour établir des connexions sur des réseaux. Secure Shell est un protocole réseau cryptographique qui fonctionne en toute sécurité et connecte un client à un serveur sur un réseau non sécurisé. Les données doivent être sécurisées cryptographiquement avant de les envoyer sur le réseau pour éviter les attaques de l'homme du milieu. En outre, vous devrez activer SSH lors de l'interaction avec les machines virtuelles pour des raisons de sécurité. 
***

##### Configurer comme suit:

1) VirtualBox -> Settings 
<img width="1221" alt="Screen Shot 2022-12-08 at 11 54 34 AM" src="https://user-images.githubusercontent.com/109855801/206429070-0cfdd533-c079-4d22-8110-480284219322.png">

2) Network 

<img width="763" alt="Screen Shot 2022-12-08 at 11 55 10 AM" src="https://user-images.githubusercontent.com/109855801/206429273-b211cad7-a353-468c-bafa-78499a7f989d.png">

3) Port Forwarding -> Entrer les configuration suivantes grâce au bouton vert + sur le côté droite.

<img width="939" alt="Screen Shot 2022-12-08 at 11 56 04 AM" src="https://user-images.githubusercontent.com/109855801/206429436-872a0e78-84d2-466e-9e99-85601e25f995.png">

##### Relancez le serveur SSH:

```
sudo systemctl restart ssh
```
##### Ouvrez votre terminal de choix (iTerm, VisualStudio etc) et tapez la commande:

```
ssh [user-name]@127.0.0.1 -p 4242 
```

---
Vous êtes maintenaint connecté à votre machine et vous pouvez la contrôler à distance depuis votre terminal à l'aide de commandes.

Lorsque vous appelez l'adresse IP `127.0.0.1`, vous communiquez avec le localhost qui est en principe votre propre ordinateur (lorsque vous appelez le localhost, votre ordinateur se parle à lui-même)
Les développeurs utilisent l'hôte local pour tester des programmes et des applications Web.

***

### 5. Mettre une politique de mot de pass

---
###### En particulier, la sécurité des mots de passe doit être considérée comme la principale préoccupation pour tout système Linux sécurisé.

###### PAM: Pluggable Authentication Modules (Modules d'Authentification Enfichables):

###### Des programmes qui autorisent des utilisateurs à accéder à un système vérifient préalablement l'identité de chaque utilisateur au moyen d'un processus appelé authentification. Dans le passé, chaque programme de ce genre effectuait les opérations d'authentification d'une manière qui lui était propre. Sous Red Hat Enterprise Linux, un grand nombre de ces programmes sont configurés de telle sorte qu'ils utilisent un processus d'authentification centralisé appelé modules d'authentification enfichables (ou PAM de l'anglais Pluggable Authentication Modules).
###### PAM utilise une architecture modulaire enfichable, offrant à l'administrateur système une grande flexibilité quant à l'établissement d'une politique d'authentification pour le système.
###### Le répertoire /etc/pam.d/ contient les fichiers de configuration PAM pour chaque application utilisant PAM. Chacun de ces fichiers est nommé en fonction du service dont il contrôle l’accès.
###### Par exemple, le programme login attribue le nom ‘login’ à son service et installe le fichier de configuration PAM /etc/pam.d/login.
***

##### Installer le paquet libpam-pwquality (password quality)

```
sudo apt-get update
sudo apt-get upgrade
sudo apt install libpam-pwquality libpwquality-tools
```

---
Les outils libpam-pwquality permettent de paramétrer le refus des mots de passe trop faibles pour les sessions root et utilisateurs. Ils permettent aussi de les évaluer en fonction de leur caractère aléatoire apparent et de les comparer à un dictionnaire des mots de passe trop courants. 
Le paquet crée le module `pam_quality.so` dans le fichier de configuration PAM `/etc/pam.d/common-password`
***

##### Ouvrir le fichier de configuration PAM liée à l'authentification
```
sudo nano /etc/pam.d/common-password
```
##### Entrer les configurations suivantes à coté du terme `pam_pwquality.so`:

```
password        requisite                       pam_pwquality.so retry=3 dcredit=-1 difok=7 enforce_for_root maxrepeat=3 minclass=3 minlen=10 ucredit=-1 usercheck=1
```
---
`retry=3` -> Nombre maximum de tentatives ratées de connexion (option présente par défaut par défaut).

`minlen=10` -> Votre mot de passe sera de 10 caractères minimums.

`minclass=3`-> [facultatif] Votre mot de passe a au moins 3 classes de caractères: majuscule, chiffre et autres ou minuscule.

`maxrepeat=3` -> Votre mot de passe ne peut pas compter plus de 3 caractères consécutifs.

`dcredit=-1` -> Votre mot de passe contient au moins un chiffre.

`ucredit=-1` -> Votre mot de passe contient au moins une majuscule.

`enforce_for_root` -> Les règles s'appliquent à `root` aussi.

`difok=7` -> Votre nouveau mot de pass aura au moins 7 caractères différents de votre ancien mot de pass(ne s’applique pas au root).

`usercheck=1` -> Le mot de passe ne peut pas être identique au nom d'utilisateur (ne s'applique pas si le nom d'utilisateur est composé d'uniquement 3 caractères).

***

##### Visualiser les options ajoutées

```
cd /etc/pam.d/
grep 'pwquality' *
```

##### Pour la configuration de l’expiration du mot de passe ouvrir le fichiers suivants:

```
sudo nano /etc/login.defs
```

##### Entrez les modifications suivantes à la fin du paragrapge `# Password aging controls:`:

```
PASS_MAX_DAYS   30
PASS_MIN_DAYS   2
PASS_WARN_AGE   7
```
---
`PASS_MAX_DAYS` : Nombre maximum de jours de validité d'un mot de passe. Après cette durée, une modification du mot de passe est obligatoire. S'il n'est pas précisé, la valeur de -1 est utilisée (ce qui enlève toute restriction).

`PASS_MIN_DAYS` : Nombre minimum de jours autorisé avant la modification d'un mot de passe. Toute tentative de modification du mot de passe avant cette durée est rejetée. S'il n’est pas précisé, la valeur de -1 est utilisée (ce qui enlève toute restriction).

`PASS_WARN_AGE` : Nombre de jours durant lesquels l'utilisateur recevra un avertissement avant que son mot de passe n'arrive en fin de validité. Une valeur négative signifie qu’aucun avertissement n'est donné. S'il n'est pas précisé, aucun avertissement n'est donné.

Les paramètres `PASS_MAX_DAYS`, `PASS_MIN_DAYS` et `PASS_WARN_AGE` ne sont utilisés qu'au moment de la création d'un compte. **Les changements n'affecteront pas les comptes existants.**

Si vous créez un nouveau utilisateur, vous pouvez vérifier si cette configuration d'expiration du mot de passe est valable pour lui avec la commande:
```
sudo chage -l [user-name]
```
Si vous voulez vérifier la configuration de l'utilisateur avec lequel vous êtes actuellement connecté:
```
chage -l [user-name]
```
***

##### Changer le mot de passe selon la nouvelle politique de l'utilisateur avec lequel vous êtes actuellement connecté:
```
passwd [user-name]
```

##### Changer le mot de passe de `root` selon la nouvelle politique:
```
sudo passwd root
```
##### Relancer la machine afin de mettre à jour le système
```
sudo reboot
```

### 6. Configuration strict du groupe sudo

---
###### L'utilitaire sudo, par le jeu de paramètres dont il dispose, peut autoriser ou refuser à un utilisateur ou à un groupe d'utilisateur l'exécution de tâches privilégiées avec ou sans saisie d'un mot de passe. Cette gestion des droits accordés aux utilisateurs est consignée dans le fichier /etc/sudoers.

###### Tous les fichiers du répertoire /etc/sudoers.d/ ne finissant pas par ~ (tilde) ou ne contenant pas un .  (point) sont lus et analysés lorsque l'on utilise la commande sudo.

###### Pour modifier le fonctionnement de la commande sudo, l'administrateur du système ne modifie plus le fichier etc/sudoers mais positionne des fichiers de personnalisation dans le répertoire /etc/sudoers.d.

```
sudo visudo -f /etc/sudoers.d/[nom_de_fichier_a_créer]
```
***

##### Créer un fichier de configuration avec un nom de votre choix

```
sudo visudo -f /etc/sudoers.d/[nom_de_votre_fichier]
```
---
Cette commande permet d'aller dans le dossier ```/etc/sudoers.d``` et de créer un fichier `[nom-de-fichier]`. Ce fichier contiendra les futures configations des utilisateurs sudo pour ce projet.*
***

##### Noter dans le fichier les configurations suivantes:

```
# Password configuration for sudo
Defaults        passwd_tries=3
Defaults        env_reset,badpass_message="Votre mot de pass est incorrect. Veuillez essayer encore une fois."

# Optional Password configurations for sudo
Defaults        env_reset,pwfeedback
Defaults        env_reset,timestamp_timeout=60

# TTY
Defaults        requiretty

# Security
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

# Sudo Log Files
Defaults        syslog=local1
Defaults        log_input, log_output
#
```
---
`Defaults	passwd_tries=3` : L’authentification en utilisant sudo sera limitée à 3 essais en cas de mot de passe erroné.

`Defaults	env_reset,badpass_message=“Votre message”` : Un message de votre choix s’affichera en cas d’erreur suite à un mauvais mot de passe lors de l’utilisation de sudo.

`Defaults	env_reset,pwfeedback` : Afficher des astérisques lors de la saisie du mot de passe (utile pour mieux visualiser ce qu’on écrit).

`Defaults	env_reset,timestamp_timeout=X` : Fournir un mot de passe à chaque authentification (utile pour faire des tests si les paramètres précédents ont bien eu lieu). La valeur X doit être remplacée par la durée, en minutes, durant laquelle le mot de passe n'a pas à être fourni pour effectuer des actions d'administration dans le terminal ou pseudo-terminal courant. La valeur 0 désactive ce temps de grâce : un mot de passe doit être fourni à chaque action d’administration. Si cette option n'est pas précisée, le temps de grâce par défaut est 15 minutes.

`Defaults requiretty` Interdit l'exécution de la commande sudo à partir d'autre chose qu'une console ou un terminal, notamment à partir de scripts cron ou cgi-bin. 

`Defaults   secure_path=“/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"` : Restreindre les paths utilisables par sudo

`Defaults        syslog=local1` : pour l'archivage de chaque commande `sudo`

`Defaults        log_input, log_output` : archiver les inputs et outputs
***

##### Ouvrir le fichier `rsyslog.conf`
```
sudo nano /etc/rsyslog.conf
```
##### Ajouter cette ligne `local1.*                        /var/log/sudo/sudo.log` avant `auth,authpriv.*                 /var/log/auth.log`, comme montré sur la photo:

<img width="407" alt="Capture d’écran 2022-12-13 à 21 08 23" src="https://user-images.githubusercontent.com/109855801/207433716-994bbc9f-0565-426b-b5a5-92256af6f711.png">


---
La règle suivante comprend un sélecteur, qui sélectionne tous les messages syslog `local1` et une action qui les enregistre sans le fichier journal `/var/log/sudo/sudo.log`.

Par défaut, le fichier journal est synchronisé chaque fois qu'un message syslog est généré.
***

##### Relancer le service rsyslog afin mettre à jour les modifications avec la commande:
```
sudo systemctl restart rsyslog
```
##### Vérifier le résultat dans le fichier `sudo.log`
```
sudo nano /var/log/sudo/sudo.log
```
---
EXPLICATION:

###### En informatique, un log est un fichier qui enregistre des événements qui se produisent sur un système d'exploitation ou tout autre équipement informatique, routeur, switch, serveur. Les logs sont aussi appelés des fichiers journaux.
###### La gestion de votre collecte de logs consistera à mettre en place la journalisation et la centralisation. La journalisation est la mise en place d’un système permettant la remontée automatique de logs. L’envoi de vos logs à un point central décrit la centralisation, qui vous donnera une vision globale des événements du système d’information.
###### Cette gestion des logs a plusieurs objectifs :
###### - Obtenir un état général du SI et identifier des événements anormaux. 
###### - Détecter les intrusions 
###### - Retracer l’historique et les actions d’un attaquant dans le cadre d’une investigation forensic. 
###### - Visualiser les actions du SI, définir des statistiques et identifier les signaux faibles.  
###### Le démon rsyslogd a pour charge de collecter les messages de service provenant des applications et du noyau puis de les répartir dans des fichiers de logs (habituellement stockés dans le répertoire /var/log/). Il obéit au fichier de configuration /etc/rsyslog.conf.

### 7. Monitoring.sh

##### Créer le fichier `monitoring.sh` dans le dossier `/usr/local/bin/`:
```
cd /usr/local/bin/
sudo touch monitoring.sh
```

##### Ouvrir le fichier:
```
sudo nano monitoring.sh
```

##### Ecrire dedans le script suivant:
```
#!/bin/bash
wall $'#Architecture: ' `hostnamectl | grep "Operating System" | cut -d ' ' -f5- ` `awk -F':' '/^model name/ {print $2}' /proc/cpuinfo | uniq | sed -e 's/^[ \t]*//'` `arch` \
$'\n#CPU physical: '`cat /proc/cpuinfo | grep processor | wc -l` \
$'\n#vCPU:  '`cat /proc/cpuinfo | grep processor | wc -l` \
$'\n'`free -m | awk 'NR==2{printf "#Memory Usage: %s/%sMB (%.2f%%)", $3,$2,$3*100/$2 }'` \
$'\n'`df -h | awk '$NF=="/"{printf "#Disk Usage: %d/%dGB (%s)", $3,$2,$5}'` \
$'\n'`top -bn1 | grep load | awk '{printf "#CPU Load: %.2f\n", $(NF-2)}'` \
$'\n#Last boot: ' `who -b | awk '{print $3" "$4" "$5}'` \
$'\n#LVM use: ' `lsblk |grep lvm | awk '{if ($1) {print "yes";exit;} else {print "no"} }'` \
$'\n#Connection TCP:' `netstat -an | grep ESTABLISHED |  wc -l` \
$'\n#User log: ' `who | cut -d " " -f 1 | sort -u | wc -l` \
$'\nNetwork: IP ' `hostname -I`"("`ip a | grep link/ether | awk '{print $2}'`")" \
$'\n#Sudo:  ' `grep 'sudo ' /var/log/auth.log | wc -l`
```
---
Check the following commands to figure out how to write the script:
`wall`: commande qui affiche un message sur le terminal de tous les utilisateurs connectés.

`uname` : affiche le nom du système d'exploitation

`/proc/cpuinfo` : CPU information

`free` : RAM information

`df` : disk information

`top -bn1` : process information

`who` : boot and connected user information

`lsblk` : partition and LVM information

`/proc/net/sockstat` : TCP information

`hostname` : hostname and IP information

`ip link show / ip address` : IP and MAC information
***
##### Exécuter le script avec la commande `bash`afin de détecter des evéntuelles erreurs dans votre script:

Si vous êtes dans le dossier où se trouve le fichier .sh:
```
bash monitoring.sh
```

ou depuis n'importe quel dossier:

```
bash /usr/local/bin/monitoring.sh
```

#####  Vérifier si le fichier a des droit d'exécution `x`

```
ls -l
```

##### S'il n'y a pas des droits d'exécution, érire la commande suivante:

```
sudo chmod +x monitoring.sh
```

##### Vérifier si les droits d'exécution ont bien été activés:

```
ls -l
```
##### Comamndes pour vérifier que le fichier .sh est maintenant exécutable et que votre script ne contient pas des erreurs: (commande à utiliser uniquement lorsque vous êtes dans le dossier de l'exécutable, dans ce cas dans /usr/local/bin/`:

Depuis le dossier qui contient le fichier .sh (dans ce cas `/usr/local/bin/`:
```
./monitoring.sh 
```
***
Si le script s'exécute, votre fichier est exécutable. Notez qu'on n'a plus besoin d'utiliser `bash`.
---


##### Vérifier si le paquet cron est déjà installé:

```
apt-cache policy cron
```

---
Cron est un programme démon disponible sur les systèmes de type Unix (Linux, Mac Osx ...) permettant de planifier des taches régulières. Il est en effet intéressant que les tâches habituelles soient réalisées automatiquement par le système plutôt que d’avoir à les lancer manuellement en tant qu’utilisateur.

Si le paquet n'est pas installé:
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install cron
```
***

##### Passer en mode root afin de donner les instructions d'affichage du script

```
su -
systemctl enable cron
crontab -e
```
---
Les tâches peuvent être planifiées par heure, par jour, par semaine ou par mois. La commande `crontab` permet de définir planifier fréquence. 

Pour être autorisé à utiliser la commande crontab, il faut que l'utilisateur soit présent dans le groupe crontab (à vérifier avec la commande `getent group crontab`). 

***

##### Ajouter les instructions suivantes à la fin:
```
*/10 * * * * /usr/local/bin/monitoring.sh
#
```
<img width="507" alt="Capture d’écran 2022-12-13 à 21 32 56" src="https://user-images.githubusercontent.com/109855801/207437726-89877011-50a3-4159-b79b-59bf07a40e99.png">

---
La commande `crontab` permet de définir la fréquence.
 
La syntaxe des fichiers de Cron : `mm hh jj MMM JJJ tâche `

Une tâche planifiée dans un fichier de Cron est composée de 3 données différentes :
- Sa période de répétition définie par 5 données différentes :
    Les minutes ;
    Les heures ;
    Les jours dans le mois ;
    Les mois ;
    Les jours de la semaine ;
- La commande à réaliser ;

Dans un fichier de Cron pour un utilisateur en particulier (en utilisant crontab), le nom d’utilisateur ne doit pas figurer puisque les actions seront réalisées sous l’utilisateur auquel appartient ce fichier.


***

##### La commande suivante affichera la liste des tâches Cron de l’utilisateur en cours :
```
crontab -l
```
***
##### Sortir du mode `user`
```
exit
```

### 8. Ajouter votre user au groupe `user42`

##### Créer un nouveau groupe `user42`

```
sudo groupadd [group-name]
```
---
Crée un nouveau groupe avec le nom de votre choix et lui attribue automatique un numéro id. 
***

##### Vérifier que le groupe a bien été créé:
```
getent group [group-name]
```
---
Normalement vous devez voir qu'un numéro ID a bien été attribué au groupe, et qu'aucun user n'a été attribué à ce groupe.
***

##### Ajouter votre user dans le group `user42`
```
sudo usermod -aG [group-name] [user-name]
```
##### Vérifier que votre user a bien été ajouté au groupe `user42`
```
getent group [group-name]
```

### 9. Faire un snapshot de la machine

##### Virtualbox -> appuyer sur le carré à coté du nom de votre machine -> séléctionner snapshots

<img width="1221" alt="Screen Shot 2022-12-08 at 4 05 18 PM" src="https://user-images.githubusercontent.com/109855801/206481136-c18b4ceb-667f-413b-a08b-a61d463c8d5c.png">

##### Appuyer sur Take et attribuer un nom

<img width="1221" alt="Screen Shot 2022-12-08 at 4 06 36 PM" src="https://user-images.githubusercontent.com/109855801/206481435-4e18a320-08a1-4576-83b1-a50250f24071.png">

### 10. Faire le fichier texte `signature.txt`

##### Depuis le terminal, aller dans le dossier contenant votre machine au format .vdi et exécuter la commande suivante:
```
shasum [nom-machine].vdi > signature.txt
```

***
La commande `shasum`calcule une somme de contrôle (appelé checksum) servant de référence pour la vérification de l’intégrité d’un fichier.

L’empreinte générée est constituée de 160 bits convertis en base hexadécimale, soit 40 chiffres en base 16.

Le checksum est calculé à partir du contenu du fichier, et non de son nom : deux fichiers de noms différents mais de même contenu auront la même empreinte, il en va de même pour deux fichiers vides.

Le chevorn `>` permet de écrire le résultat de la commande `shasum` dans le fichier qu'on a nommé `signature.txt`. C'est ce fichier qui doit être déposer dans le dossier git. 

NOTE IMPORTANTE: Il faut envoyer le fichier `signature.txt` contenant uniquement le code!!! 
---

# **TP Introduction Docker / Conteneurs**

Pendant ce TP vous apprendrez les bases de l'utilisation de Docker et des
conteneurs Linux. Vous devriez à la fin de ce TP, savoir récupérer une image
Docker, la lancer avec des paramètres personnalisés afin d'utiliser le programme
qu'elle contient.

## **Prérequis (~30min)**

Ce TP doit être réalisé sur des instances `Docker Ready` de l'Openstack de
l'université.

Uniquement la partie `Démarrer un conteneur` doit être finie pour ce TP, les
parties suivantes sont là pour si vous allez vite ou souhaitez en apprendre plus
sur docker.

Il est néanmoins possible de refaire par la suite sur vos propres machines que
ce soit Linux / MacOs sur lesquelles vous avez les accès administrateurs.
Il est aussi réalisable sur des environnements Windows / WSL mais cela nécessite
des configurations plus complexes pour faire tourner le `Docker daemon`.

Les instructions pour réaliser ce TP hors Openstack ne seront néanmoins pas
toutes fournies et il n'y aura pas d'assistance pour l'installation et la
configuration de docker pendant les heures de TP.

### **Instance OpenStack Docker Ready (~30min)**

Si vous choisissez d'utiliser une instance Openstack `Docker Ready` voici la
configuration que vous devrez faire / avoir.

Vous devez en tout premier générer votre keypair ssh (public et private key)
puis mettre votre clé publique sur l'Openstack afin qu'elle sois mise sur
votre instance lors de sa création.

#### **Génération clé ssh (~10min)**

Ce sont des compétences que vous devriez avoir acquises lors de précédents TPs.
La réponse est donc donnée, il est néanmoins conseillé de tenter de le faire
par vous-même, mais ne perdez pas du temps dessus.

<details>
<summary><em>Réponse</em></summary><br>

Vous devez générer la paire de clés ssh privée / publique avec `ssh-keygen`

```BASH
ssh-keygen -q -b 4096 -t rsa -N "" -f ~/.ssh/id_rsa
```

[La commande expliquée](https://explainshell.com/explain?cmd=ssh-keygen+-q+-b+4096+-t+rsa+-N+%22%22+-f+%7E%2F.ssh%2Fid_rsa)

---

Vous pouvez ensuite aller dans l'Openstack à la section `Paires de clés`,
cliquer `Importer une paires de clés`.
Vous nommerez la clé `tp_docker_keypair` et mettrez le résultat de la commande
suivante dans le cadre `clé publique`.

```BASH
cat ~/.ssh/id_rsa.pub
```

---

</details><br>

#### **Création de la machine et test de la connexion (~20min)**

Une fois la paire de clé ssh générée et la clé publique importée dans
l'Openstack, nous pouvons passer à la création de l'instance `Docker Ready`.

Vous devez aller sur l'Openstack dans la section `Instance` puis
`Démarrer une instance`.

Les paramètres recommandés pour l'instance sont :

```
Nom : [Numéro Étu]_tp_docker
Volume : No
Image : Ubuntu 20.04.3 LTS - Docker Ready
Taille : m1.small
Paire de clé : tp_docker_keypair
```

Une fois l'instance démarrée, vous devez noter l'adresse IP qui lui a été
associée et tester la connexion ssh.

<details>
<summary><em>Réponse</em></summary><br>

```BASH
ssh ubuntu@192.168.X.X
```

---

Si vous avez des problèmes de connexion, vous pouvez tester que vous arrivez à
`ping` l'instance pour vérifier que ce n'est pas un problème réseau.

```BASH
ping 192.168.X.X
```

---

Ensuite vous pouvez vérifier que le port `22` (ssh) de l'instance est
accessible depuis votre machine.

```BASH
nc -vz 192.168.X.X 22
```

[La commande expliquée](https://explainshell.com/explain?cmd=nc+-vz+192.168.X.X+22)

</details><br>

Une fois la connexion ssh établie vous pouvez tester que le `Docker deamon` est
bien présent et en fonctionnement.

<details>
<summary><em>Réponse</em></summary><br>

Vous pouvez récupérer l'état et les informations liées au `Docker deamon`
avec la commande suivante.

```BASH
docker version
```

---

Il est aussi possible de regarder l'état du `deamon` avec son service `systemd`.

```BASH
systemctl status docker
```

---

Vous pouvez ensuite voir l'ensemble des conteneurs sur l'instance.

```BASH
docker ps --all
```

À cette étape du TP il est normal de ne rien avoir en sortie de la commande.

</details><br>

### **Docker sans Openstack (~30min)**

Cette partie est uniquement nécessaire si vous n'utilisez pas une instance
`Docker Ready` de l'Openstack, si c'est votre cas, vous pouvez passer à la suite.

Il est nécessaire d'avoir Docker installé pour la suite de TP.
Les instructions pour son installation sont disponibles à :
https://docs.docker.com/get-docker/

Docker n'est qu'un programme parmi d'autres qui permettent de créer et
administrer des conteneurs, il existe de nombreuses alternatives
(podman, buildah, containderd...) qui se spécialisent ou non dans certaines
parties de la vie de conteneurs (création, runtime...).

Néanmoins Docker étant encore le plus utilisé et celui que vous rencontrerez
le plus nous ne nous pencherons que sur lui dans la suite de ce TP.

## **Démarrer un conteneur (~105min)**

Dans cette première partie, nous allons voir les bases de l'utilisation de
la `Docker cli` pour démarrer et utiliser des conteneurs.

### **1 / Mon premier conteneur (~20min)**

1.1 / Démarrer votre premier conteneur (non détaché) avec une image `nginx`

> [Docker run](https://docs.docker.com/engine/reference/commandline/run/)

> [Nginx image](https://hub.docker.com/_/nginx/)

⚠️ Après avoir lancé votre conteneur vous ne pourrez plus écrire dans votre
terminal car le conteneur y est attaché, pour en sortir, utilisez `CTRL+C`

1.2 / Affichez la liste des conteneurs en fonctionnement sur votre instance

> [Docker ps](https://docs.docker.com/engine/reference/commandline/ps/)

1.3 / Affichez la liste des tous les conteneurs qui existent sur votre instance

1.4 / Pourquoi voyons nous notre conteneur en sortie de la commande à la
question `1.3` mais pas dans celle de la question `1.2` ?

1.5 / Supprimez le conteneur que vous avez précédemment crée

> [Docker rm](https://docs.docker.com/engine/reference/commandline/rm/)

⚠️ Après cette étape la commande utilisée à la question `1.3` doit renvoyer une
liste vide.

1.6 / Lancez le même conteneur que précédemment mais en tâche de fond (détaché)

> [Docker run detach](https://docs.docker.com/engine/reference/run/#detached-vs-foreground)

⚠️ Après avoir lancé votre conteneur votre terminal doit être toujours utilisable
sans avoir à stoper le conteneur.

1.7 / Relancez la commande de la question `1.2`, quelle est la différence ?

1.8 / Stoppez le conteneur que vous avez précédemment crée

> [Docker stop](https://docs.docker.com/engine/reference/commandline/stop/)

1.9 / Supprimez le conteneur que vous avez précédemment stoppé

### **2 / Les tags (~10min)**

2.1 / Démarrez un conteneur détaché avec une image `nginx` sur le
tag `alpine`

2.2 / Affichez la liste des conteneurs qui tournent sur votre instance

2.3 / Quelle est la différence avec le conteneur démarré à la question `1.5` ?

### **3 / Les logs (~10min)**

Lorsque vous démarrez un conteneur il génère des logs.
Ces logs sont importants pour le monitoring de votre application et son debug.

3.1 / Affichez les logs du conteneur démarré à la `2.1`

> [Docker logs](https://docs.docker.com/engine/reference/commandline/logs/)

### **4 / Exécuter dans un conteneur (~20min)**

Pour des raisons de debug il peut être nécessaire d'ouvrir un terminal
dans un conteneur, c'est pour cela que la commande `exec` a été crée.

4.1 / Démarrez un conteneur avec une image `ubuntu` en détaché

4.2 / Affichez la liste des conteneurs en fonctionnement sur votre instance

⚠️ Vous remarquez que le conteneur que vous venez de lancer n'est pas présent.
Par défaut, un conteneur nécessite toujours un programme en cours
et il s'arrête à l'arrêt de ce programme, dans notre cas, l'image ubuntu
ne contient pas de tels programmes, le conteneur s'arrête donc directement après
son lancement.
Vous pouvez contourner cette limitation en lançant un programme de votre choix
en programme initial de votre conteneur.

4.3 / Démarrez un conteneur avec une image `ubuntu` en détaché en utilisant la
commande `sleep infinity` comme programme au démarrage

4.4 / Affichez la liste des conteneurs en fonctionnement sur votre instance

⚠️ Vous devriez désormais voir le conteneur ubuntu démarré

4.5 / Ouvrez un shell bash dans le conteneur ubuntu

> [Docker exec](https://docs.docker.com/engine/reference/commandline/exec/)

4.6 / Listez les processus du conteneur, que remarquez vous ?

> [ps](https://www.man7.org/linux/man-pages/man1/ps.1.html)

4.7 / Faites le ménage et stoppez / supprimez tous les conteneurs présents

### **5 / Exposer des ports (~15min)**

Pour accéder à votre serveur web que vous lancez dans un conteneur il
vous faut exposer les ports du conteneur.

L'image docker `nginx:latest` fait tourner par défaut le serveur web `nginx` sur
le port 80 dans le conteneur.

⚠️ Pour la suite de ce TP il vous faut ajouter une règle pour autoriser l'accès au port
`8080` sur le firewall openstack, seul un groupe dans le projet à besoin d'effectuer cette
étape

5.1 / Démarrez un conteneur `nginx` en détaché en exposant le serveur web
`nginx` sur le port `8080`

> [Docker run publish](https://docs.docker.com/engine/reference/commandline/run/#publish)

5.2 / Testez depuis votre instance avec `curl`, si le serveur web est
disponible sur `localhost:8080`

> [curl](https://man7.org/linux/man-pages/man1/curl.1.html)

5.3 / Testez depuis votre machine locale avec `curl`, si le serveur web est
disponible sur `192.168.X.X:8080`

### **6 / Les volumes (~30min)**

Les conteneurs docker doivent être considérés comme volatiles. La donnée que vous
mettez dedans ne peut pas être considérée comme persistante.
Pour utiliser des applications qui persistent de la donnée nous devons utiliser
des `volumes` et des `mount`.

6.1 / Créez un volume docker nommé `mysql`

> [Docker volumes](https://docs.docker.com/engine/reference/commandline/volume/)

6.2 / Lancez un conteneur `mysql` détaché en montant le volume précedemment
crée dans `var/lib/mysql/`

Les arguments de la commande sont complexes, ils sont donc données ci dessous

<details>
<summary><em>Réponse</em></summary><br>

```BASH
docker run --name mysql -e MYSQL_USER=etu -e MYSQL_PASSWORD=etuPassword -e MYSQL_DATABASE=univ -e MYSQL_ROOT_PASSWORD=toor -v mysql:/var/lib/mysql/ -d mysql:8.0.36
docker exec -it mysql /bin/bash
mysql --password=etuPassword --user=etu
USE univ;
CREATE TABLE students (id int NOT NULL AUTO_INCREMENT, name varchar(255) DEFAULT NULL, PRIMARY KEY (id));
INSERT INTO students (name) VALUES ('testStudent');
SELECT * from students;
```

</details><br>

6.3 / Stoppez et supprimez le conteneur `mysql`

6.4 / Créez un nouveau conteneur `mysql` qui monte le même volume et vérifiez
que la donnée a été conservée

<details>
<summary><em>Réponse</em></summary><br>

```BASH
docker run --name new_mysql -e MYSQL_USER=etu -e MYSQL_PASSWORD=etuPassword -e MYSQL_DATABASE=univ -e MYSQL_ROOT_PASSWORD=toor -v mysql:/var/lib/mysql/ -d mysql
docker exec -it new_mysql /bin/bash
mysql --password=etuPassword --user=etu
USE univ;
SELECT * from students;
```

</details><br>

Nous pouvons voir que la donnée a bien été conservée malgré la suppression
du conteneur.

Les volumes sont importants même si nous n'allons pas supprimer un conteneur,
il arrive que docker supprime les données hors volumes des conteneurs qui
sont stoppés.

## **Créer une image Docker (~30min)**

Nous allons dans cette partie voir la conception d'un `Dockerfile` et le
`build` d'une image docker.

7.1 / Depuis le répertoire dockerfile créez une image docker avec comme
tag `myapp`

> [Docker build](https://docs.docker.com/engine/reference/commandline/build/)

7.2 / Démarrez l'image précédemment crée en n'oubliant pas d'exposer les bons
ports

⚠️ N'oubliez pas de lire la configuration du serveur web nginx !

7.3 / Testez depuis votre machine locale avec votre navigateur que la page web
est bien là.

7.4 / Modifiez la page web `index.html` pour qu'elle affiche `image.jpeg`

⚠️ Vous devrez aussi modifier le `Dockerfile`

> [Dockerfile](https://docs.docker.com/engine/reference/builder/)

7.5 / Créez une seconde image docker `myapp-image` et démarrez la afin que la
page web avec l'image soient accessibles depuis votre machine locale.

⚠️ N'oubliez pas de stopper l'ancien conteneur

7.6 / Expliquez ligne par ligne le contenu du dockerfile

## **Docker compose (~60min)**

`Docker Compose` est une façon de gérer plusieurs conteneurs avec un
réseau virtuel entre eux. C'est très pratique par exemple lorsque vous avez une
application qui dépend d'une base de données, le conteneur de la bdd ne sera
accessible que par le conteneur de votre serveur web, ce qui réduit grandement
la surface d'attaque.

Voici un très bon exercice pour vous entraîner à faire tourner un Wordpress
ainsi que sa base de données dans des conteneurs avec `Docker Compose`

> [Docker compose with wordpress and mysql ](https://github.com/docker/awesome-compose/blob/master/official-documentation-samples/wordpress/README.md)

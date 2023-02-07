# Installation de PySpark - Jupyter

---

L’objectif de ce tutoriel est de connecter un `Jupyter Notebook` sur un machine virtuelle à un service Spark, tournant sur une autre machine. Si les 2 machines sont sur des réseaux internet différent, vous devez établir une connexion VPN entre les 2 réseaux. Un fois que cela est fait, vous pouvez poursuivre le tutoriel.

## Prérequis

---

Dans ce tuto, nous partons du principe que l’application `Spark` et `Python` sont déjà installés et fonctionnent correctement sur votre ordinateur ou machine virtuelle. Si ce n’est pas le cas, veuillez à les installer avant de poursuivre ce tutoriel.

Vérifier le bon fonctionnement de Spark en lançant le `Spark Shell` ou le `PySpark Shell`

```python
bin/spark-shell
```

![Untitled](Installation%20de%20PySpark%20-%20Jupyter%20e668c32ae6d94115913bd7613599f54a/Untitled.png)

Si vous lancez le `PySpark Shell` vous devez avoir le visuel ci-dessous

```python
bin/pyspark
```

![Untitled](Installation%20de%20PySpark%20-%20Jupyter%20e668c32ae6d94115913bd7613599f54a/Untitled%201.png)

## Installation Jupyter Notebook

---

Nous installons `Jupyter Notebook` sur la machine en exécutant successivement

```python
sudo apt install python3-pip
sudo apt install jupyter-notebook

```

On peut vérifier que l’installation a correctement fonctionné en lançant le serveur `Jupyter Notebook`. Le visuel obtenu est celui ci dessous :

```python
jupyter notebook
```

![Untitled](Installation%20de%20PySpark%20-%20Jupyter%20e668c32ae6d94115913bd7613599f54a/Untitled%202.png)

## Port Forwarding (redirection de port)

---

Dans un le tutoriel présent à l’adresse ci-dessous, vous pouvez trouver un autre exemple d’application du port forwarding.

[https://github.com/dhb2k/Network-Tips](https://github.com/dhb2k/Network-Tips)

Pour réussir avec la redirection de port, il faut que le serveur soit lancer avec une adresse ip publique. Or par défaut, comme on peut le constater sur l’image précédente, `jupyter notebook` s’ouvre sur le localhost:8888. Il faut donc changer cette commande pour pouvoir y accéder de manière publique. 

Pour cela, `jupyter notebook` impose que soit défini un mot de passe. Il vous permettra alors de vous identifier. Pour plus d’information sur les protocoles d’accès publiques de `jupyter notebook` veuillez vous referrez à l’aide :

[Running a notebook server - Jupyter Notebook 6.5.2 documentation](https://jupyter-notebook.readthedocs.io/en/stable/public_server.html)

Nous utilisons la méthode ci-dessous, qui permet de définir un mot de passe :

```bash
$ jupyter notebook password
Enter password:  ****
Verify password: ****
[NotebookPasswordApp] Wrote hashed password to /Users/you/.jupyter/jupyter_notebook_config.json

```

Si vous connaissez l’adresse ip de la machine distante (192.168.1.1) sur laquelle tourne le serveur `jupyter`, alors vous pouvez directement faire la commande de port forwarding suivantes sur un terminal de la machine principale :

```bash
ssh -L localhost:9001:192.168.1.1:9001 bridge_username@bridge_ip_adress
```

Si vous ne connaissez pas l’adresse ip publique de la machine distante, exécutez la commande :

```bash
ip a
```

On lie donc le port 9001 du localhost (machine principale) au port port 9001 de la machine distante, en passant par le bridge. Pour rappel, cette redirection de port n’est utile que lorsqu’on ne peut accéder à la machine virtuel qu’en passant par une machine dite bridge.

## Connexion à Jupyter Notebook

---

Sur le terminal de la machine distante, mettez en marche le serveur `Jupyter Notebook` en précisant l’ip publique de la machine en utilisant la commande suivante :

```bash
jupyter notebookjupyter notebook --ip 192.168.1.1 --port 9001
```

Vous pouvez ensuite aller sur le navigateur de la machine principale et saisir :

```bash
http://localhost:9001
```

Il vous sera demander le mot de passe défini plus haut et une fois ce dernier valider, vous pourrez accès à vos fichier depuis le `Jupyter Notebook`

![Untitled](Installation%20de%20PySpark%20-%20Jupyter%20e668c32ae6d94115913bd7613599f54a/Untitled%203.png)

![Untitled](Installation%20de%20PySpark%20-%20Jupyter%20e668c32ae6d94115913bd7613599f54a/Untitled%204.png)

## Connexion Jupyter Spark

---

Maintenant que `Jupyter Notebook` à accès à votre machine distance, sur laquelle est également installé Spark, vous pouvez maintenant connecter `Jupyter Notebook` à `PySpark`. Pour cela, exécutez les commandes ci dessous :

```python
pip install pyspark
```

Redémarrer le noyau pour que la librairie soit mise à jour dans l’environnement jupyter notebook et continuez avec les commandes ci-dessous :

```python
from pyspark.sql import SparkSession
spark = SparkSession \
				.builder \
				.appName('tuto_SparkSession') \
				.config("spark.jars.packages", "org.mongodb.spark:mongo-spark-connector_2.12:3.0.1") \
		    .getOrCreate()
```

Ces commandes de bases vous permettent d’instancier l’objet `spark` qui vous permettra plusieurs type de traitements
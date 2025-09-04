MyBatis JPetStore
=================

[![Java CI](https://github.com/mybatis/jpetstore-6/actions/workflows/ci.yaml/badge.svg)](https://github.com/mybatis/jpetstore-6/actions/workflows/ci.yaml)
[![Container Support](https://github.com/mybatis/jpetstore-6/actions/workflows/support.yaml/badge.svg)](https://github.com/mybatis/jpetstore-6/actions/workflows/support.yaml)
[![Coverage Status](https://coveralls.io/repos/github/mybatis/jpetstore-6/badge.svg?branch=master)](https://coveralls.io/github/mybatis/jpetstore-6?branch=master)
[![License](https://img.shields.io/:license-apache-brightgreen.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)

![mybatis-jpetstore](https://mybatis.org/images/mybatis-logo.png)

JPetStore 6 is a full web application built on top of MyBatis 3, Spring 5 and Stripes.

Essentials
----------

* [See the docs](http://www.mybatis.org/jpetstore-6)

## Other versions that you may want to know about

- JPetstore on top of Spring, Spring MVC, MyBatis 3, and Spring Security https://github.com/making/spring-jpetstore
- JPetstore with Vaadin and Spring Boot with Java Config https://github.com/igor-baiborodine/jpetstore-6-vaadin-spring-boot
- JPetstore on MyBatis Spring Boot Starter https://github.com/kazuki43zoo/mybatis-spring-boot-jpetstore

## Run on Application Server
Running JPetStore sample under Tomcat (using the [cargo-maven2-plugin](https://codehaus-cargo.github.io/cargo/Maven2+plugin.html)).

- Clone this repository

  ```
  $ git clone https://github.com/mybatis/jpetstore-6.git
  ```

- Build war file

  ```
  $ cd jpetstore-6
  $ ./mvnw clean package
  ```

- Startup the Tomcat server and deploy web application

  ```
  $ ./mvnw cargo:run -P tomcat90
  ```

  > Note:
  >
  > We provide maven profiles per application server as follow:
  >
  > | Profile        | Description |
  > | -------------- | ----------- |
  > | tomcat90       | Running under the Tomcat 9.0 |
  > | tomcat85       | Running under the Tomcat 8.5 |
  > | tomee80        | Running under the TomEE 8.0(Java EE 8) |
  > | tomee71        | Running under the TomEE 7.1(Java EE 7) |
  > | wildfly26      | Running under the WildFly 26(Java EE 8) |
  > | wildfly13      | Running under the WildFly 13(Java EE 7) |
  > | liberty-ee8    | Running under the WebSphere Liberty(Java EE 8) |
  > | liberty-ee7    | Running under the WebSphere Liberty(Java EE 7) |
  > | jetty          | Running under the Jetty 9 |
  > | glassfish5     | Running under the GlassFish 5(Java EE 8) |
  > | glassfish4     | Running under the GlassFish 4(Java EE 7) |
  > | resin          | Running under the Resin 4 |

- Run application in browser http://localhost:8080/jpetstore/ 
- Press Ctrl-C to stop the server.

## Run on Docker
```
docker build . -t jpetstore
docker run -p 8080:8080 jpetstore
```
or with Docker Compose:
```
docker compose up -d
```

## Try integration tests

Perform integration tests for screen transition.

```
$ ./mvnw clean verify -P tomcat90
```


## ################## A Suivre avant tout lancement de Test #################### ##

1 - Tout se déroule sur une seule instance
2 - Vérifier l'adresse ip de l'instance dans la configuration :
    - system de jenkins (outil sonarqube)
    - du fichier host ansible
3 - Démarrer le conrenair sonarqube
4 - Vérifier que tous les ports sont ouverts : 8080, 8081, 8090, 9000

## #################################### STEPS #################################### ##

Step 1 -- Create an Ubuntu(22.04) T2 Large Instance (8G RAM min)

Step 2 -- Install Jenkins, Docker, and Trivy

Step 3 -- Install Plugins like JDK, SonarQube Scanner, Maven, OWASP Dependency Check
     3A — Configure Java and Maven in Global Tool Configuration
     3B — Create a Job pipeline

Step 4 -- Configure Sonar Server in Manage Jenkins

Step 5 -- Install OWASP Dependency Check Plugins

Step 6 -- Docker plugin and credential Setup

Step 7 -- Adding Ansible Repository in Ubuntu and install Ansible (en cours)

Step 8 -- Kubernetes Setup - Kubectl on Jenkins to be installed
              Part 1 ———-Master Node————
                      ——-Worker Node———
              Part 2 ————Both Master & Node ————
              Part 3 ————— Master —————
                      ——-Worker Node———
Step 9 -- Master-slave Setup for Ansible and Kubernetes
      ——-Test Ansible Master Slave Connection——-


## ########################### PROCEDURE DETAILLEE ########################### ##

Step 1 -- Create an Ubuntu(22.04) T2 Large Instance (8G RAM min)
  - ouvrir les ports 8080: Application, 8081:jenkins, 8090:weebhook, 9000:sonarqube 

Step 2 -- Install Jenkins, Docker, and Trivy

      2A
          - Téléchargez le référentiel Jenkins : sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
          - Importer la clé Jenkins :  sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
          - sudo apt-get update
          - Installer Java : sudo apt install openjdk-17-jre -y && java -version
          - Installer et démarrer Jenkins : 
                sudo apt install jenkins -y
                sudo systemctl start jenkins
                sudo systemctl status jenkins -l 
                sudo nano /lib/systemd/system/jenkins.service : pour changer le port d'écoute
                sudo systemctl daemon-reload
                sudo systemctl restart jenkins
                sudo systemctl status jenkins
              
          - Accéder à Jenkins http://<your-ec2-ip>:8090
                sudo cat /var/lib/jenkins/secrets/initialAdminPassword

      2B
              sudo apt-get update
              sudo apt-get install docker.io -y
              id $USER
              sudo usermod -aG docker $USER
              id $USER
              newgrp docker
              id $USER

              docker run -d --name sonar-container -p 9000:9000 sonarqube:lts-community

              Now our sonarQube is up and running : http://<your-ec2-ip>:9000
                  username: admin
                  password: admin
              Update New password, This is Sonar Dashboard.

      2C
              # Téléchargez la dernière version
                sudo wget https://github.com/aquasecurity/trivy/releases/download/v0.50.0/trivy_0.50.0_Linux-64bit.deb

              # Installez
                sudo dpkg -i trivy_0.50.0_Linux-64bit.deb


      Pas obligatoire
                Install manuellement
                sudo apt update
                sudo apt install ansible -y
                ansible --version


Next, we will log in to Jenkins and start to configure our Pipeline in Jenkins

Vous avez 2 options : 
    - Installer automatiquement les plugin via jenkins 
    - Telécharger les plugins manuellemnt et configurer les chemins d'accès au bin dans jenkins

Plugins de bases
          - Pipeline: Stage View : Affiche les étapes du pipeline avec leur statut et durée.
          - Blue Ocean : Interface visuelle avancée avec une meilleure représentation des     pipelines
          - Pipeline: Stage Metrics : Fournit des métriques détaillées sur les temps d'exécution des stages.
          - Pipeline Declarative : Essential pour les pipelines déclaratifs 
          - Workspace Cleanup : ettoie les workspaces entre les exécutions (améliore la performance).
          - Email Extension Template
          - Jenkins Monitoring Plugin : Métriques temps réel (CPU, mémoire, disque),Santé de l'instance Jenkins
          - Pipeline: Stage Metrics : Statistiques détaillées sur les temps d'exécution, Identification des goulots d'étranglement
          - Plot Plugin : Créez des graphiques à partir des données de build: Temps d'exécution, couverture de test, etc.
          - Pipeline Utility Steps : pour lire les fichiers json

Plugins pour le projet
          Git, JDK, SonarQube Scanner, Maven, OWASP Dependency Check
Global Tool Configuration is used to configure different tools that we install using Plugins

          Git

          Eclipse Temurin : C'est Une distribution Java (JDK) pour Exécuter et compiler du code Java, Fournit : Compilateur Java (javac), JRE, outils Java

          Maven Integration
          Pipeline Maven Integration

          SonarQube Scanner

          OWASP Dependency Check


Create a Job

PART 1 : Enter this in Pipeline Script,


Step 4 — Configure Sonar Server in Manage Jenkins

Goto your Sonarqube Server http://<your-ec2-ip>:9000 Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token → Create a token with a name and generate

copy Token → Goto Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret Text

The Configure System option is used in Jenkins to configure different server

  Now, go to Dashboard → Manage Jenkins → System → SonarQube servers (fill) → Click on Apply and Save

In the Sonarqube Dashboard add a quality gate also : Administration–> Configuration–>Webhooks

Click on Create

#in url section of quality gate : http://jenkins-public-ip:8090/sonarqube-webhook/

PART 2
Let’s go to our Pipeline and add Sonarqube Stage in our Pipeline Script.
Click on Build now, you will see the stage view like this
To see the report, you can go to SonarQube Server and go to Projects.


PART 3
Install OWASP Dependency Check Plugins : automatically
choose install from github

Create api key : https://nvd.nist.gov/developers/request-an-api-key
add key  to jenkins credential : Goto Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret Text


La NVD est une base de données officielle gérée par le NIST (National Institute of Standards and Technology, aux États-Unis). Elle recense et publie des vulnérabilités de sécurité connues, identifiées par un CVE (Common Vulnerabilities and Exposures).

L’OWASP est une organisation à but non lucratif qui publie des guides et bonnes pratiques de sécurité applicative

Jenkins ne se connecte pas directement à la NVD, mais OWASP Dependency-Check Plugin le fait pour lui : OWASP Dependency-Check Analyse tes dépendances (Maven, npm, pip, etc.), Compare les librairies utilisées avec la base CVE/NVD, Produit un rapport : quelles bibliothèques sont vulnérables, avec score CVSS.

          ┌────────────────────────┐
          │        NVD             │
          │ Base de données CVE    │
          │ (faille identifiée)    │
          └──────────┬─────────────┘
                     │
                     │ utilise les infos
                     ▼
          ┌────────────────────────┐
          │        OWASP           │
          │ Bonnes pratiques,      │
          │ Top 10, outils         │
          └──────────┬─────────────┘
                     │
                     │ aide à prévenir
                     ▼
          ┌────────────────────────┐
          │ Applications + Systèmes │
          │ plus sûrs               │
          └────────────────────────┘

Now go configure → Pipeline and add this stage to your pipeline and build


PART 4

sudo docker pull sonatype/nexus3
sudo docker run -d -p 8081:8081 --name nexus sonatype/nexus3
sudo docker ps

Open a web browser and navigate to: http://<your-server-ip>:8081

Sign in with the default credentials:
   Username: admin
    Password: Found in:
      /opt/sonatype-work/nexus3/admin.password (systemd)
      /nexus-data/admin.password (Docker)
First sign in and change the password

Go to Nexus http://<your-server-ip>:9000 Creating a Nexus Repository

Signed in as admin → click the gear icon on the upper toolbar → click Repositories → Create Repository button.

Choose the type of repository (raw) → Fill the blank spaces → Create Repository button


Connecting Nexus to Jenkins : 
  Creating a Jenkins User in Nexus : You need to identify the user that is trying to push artifacts to the repository

  You need to identify the user that is trying to push artifacts to the repository. Therefore, we need to add a Jenkins user in the Nexus repository. These credentials will be used in Jenkins.
  Settings → Security → Users → Fill the blank spaces and create the user

  Adding Nexus Credentials to Jenkins : username/password

  Installing the Nexus Plugin in Jenkins : Nexus Artifact Uploader



PART 5
    Docker
    Docker Commons
    Docker Pipeline
    Docker API
    docker-build-step
    and click on install without restart

    Manage Jenkins → Tools → Docker → install automatically : docker version: latest

    Use a personnal DockerHub Username/Password or a personal Access token  from the docker hub which is used for ansible-playbook

    copy personal token and save for later

    Add your personnal DockerHub Token under Global Credentials

    Ansible Build-Push-Image


PART 6 Add Ansible stage
    Ansible plugin
    which ansible → Copy that path and add it to the tools section of Jenkins at ansible installations.
    Dashboard → Manage Jenkins → Tool → Ansible


    Now add Credentials ssh to invoke Ansible with Jenkins. (cible)
    In the private key section, Select Enter directly and add your Pem file for the key.


jenkins-ip:8081/jpetstore
          - http://localhost:8081/jpetstore

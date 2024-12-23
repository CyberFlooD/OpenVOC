
-------------------------------------------------------------------------------------------------------------------------

INSTALLATION DE CORTEX SUR ALMALINUX


STEP 1: Installer Elasticsearch

Import the GPG key:

    rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

Add the repository:

    cat > /etc/yum.repos.d/elastic.repo << EOF
    [elasticsearch-7.x]
    name=Elasticsearch repository for 7.x packages
    baseurl=https://artifacts.elastic.co/packages/7.x/yum
    gpgcheck=1
    gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
    enabled=1
    autorefresh=1
    type=rpm-md
    EOF

    yum install elasticsearch-7.11.2

STEP 2: Configure and Start Elasticsearch

Edit /etc/elasticsearch/elasticsearch.yml and modify the following lines:

    http.host: 192.168.150.236 (network.host)

    cluster.name: hive

    thread_pool.search.queue_size: 100000

Start the service:

    service elasticsearch start

SPET 3 : Creez ou Modifiez le fichier thehive-project.repo

Run the following command to import the GPG key :

    sudo rpm --import https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY

The release repository contains packages for Cortex 3.1.0+.

Ajoutez ceci dans /etc/yum.repos.d/thehive-project.repo:

    [thehive-project]
    enabled=1
    priority=1
    name=TheHive-Project RPM repository
    baseurl=https://rpm.thehive-project.org/release/noarch
    gpgcheck=1

Then you will able to install Cortex 3.1.0+ the package using yum:

    yum install cortex


STEP 4: Gererate the secret key

    (cat << _EOF_
    # Secret key
    # ~~~~~
    # The secret key is used to secure cryptographics functions.
    # If you deploy your application to several instances be sure to use the same key!
    play.http.secret.key="$(cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 64 | head -n 1)"
    _EOF_
    ) | sudo tee -a /etc/cortex/application.conf


In /etc/cortex/application.conf modify the Elasicsearch URI : uri = "http://192.168.150.236:9200"

    systemctl start cortex

STEP 5: Acces portail WEB

    http://192.168.150.236:9001

    Clic Update Database

Create new user Admin: admin admin

LOGIN to Cortex App : admin admin

Create new organization : VOC

Add new user: adminvoc



STEP 6 : Analyzers and Responders

Install Python 2 and Python 3

    dnf install python2
    dnf install python3
    pip2 install -U pip setuptools && sudo pip3 install -U pip setuptools

    cd /opt/cortex
    git clone https://github.com/TheHive-Project/Cortex-Analyzers

Installer les analyzers

    for I in $(find Cortex-Analyzers -name 'requirements.txt'); do sudo -H pip2 install -r $I; done && \
    for I in $(find Cortex-Analyzers -name 'requirements.txt'); do sudo -H pip3 install -r $I || true; done

  (etape assez longue)

Ajoutez le chemin absolu des Analiseurs dans /etc/cortex/application.conf  (urls):

    "/opt/cortex/Cortex-Analyzers/analyzers"


Restart the Cortex:
    systemctl restart cortex

Login to http://192.168.150.236:9001 with adminvoc / password 

Nous avons mntn les analiseurs visibles dans la barre de menu, et nous pouvons les activer si l'on le souhaite avec les cles API.
(API à recuperer sur le site officiel des analiseurs : AbuseIPDB, VirusTotal etc...)

Enjoy analyze !


-----------------------------------------------------------------------------------------------------------------------------------
 https://www.youtube.com/watch?v=qz6xtINwK3I&t=187s




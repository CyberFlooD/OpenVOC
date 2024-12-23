# Installer DefectDojo

Installez Docker et docker-compose au préalable.

Clonez le depot et lancez les commandes suivantes:

    git clone https://github.com/DefectDojo/django-DefectDojo

    cd django-DefectDojo

    ./dc-build.sh

    ./dc-up-d.sh postgres-redis

Obtain admin credentials. The initializer can take up to 3 minutes to run.
Use docker compose logs -f initializer to track its progress.
  
    docker compose logs initializer | grep "Admin password:"

----------------------------------------------------------------------------
    https://github.com/DefectDojo/django-DefectDojo

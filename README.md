# DockerProjet
Django+ Postgresql sur docker


Resume 
Créer une dossier pour votre projet 
Créer votre fichier Dockerfile sans extension avec ce contenu : 


          # syntax=docker/dockerfile:1
          FROM python:3
          ENV PYTHONDONTWRITEBYTECODE=1
          ENV PYTHONUNBUFFERED=1
          WORKDIR /code
          COPY requirements.txt /code/
          RUN pip install -r requirements.txt
          COPY . /code/


Creér un fichier requirements.txt avec ce contenu : 

          Django<4.0
          psycopg2>=2.8

  Créer un fichier docker-compose.yml   avec ce contenu : 



          services:
  db:
    image: postgres
    volumes:
      - ./data/db:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - "8000:8000"
    environment:
      - POSTGRES_NAME=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    depends_on:
      - db


Dans le meme dossier créer votre projet Django avec la commande : 
        docker compose run web django-admin startproject nomprojet .

Reglage sur la base de donnée  Ouvrez le dossier de votre projet créer dans settings.py  fait ces modifications dans la partie DATABASES : 


    # settings.py
    
    import os
    
    [...]
    
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': os.environ.get('POSTGRES_NAME'),
            'USER': os.environ.get('POSTGRES_USER'),
            'PASSWORD': os.environ.get('POSTGRES_PASSWORD'),
            'HOST': 'db',
            'PORT': 5432,
        }
    }

    
        


Ensuite la commande : 
        docker compose up 

Ainsi vous aurez la page d'accueil de Django aprés avoir lancer http://localhost:8000 sur le navigateur de votre choix ...

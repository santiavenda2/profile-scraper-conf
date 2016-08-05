
# Profile scraper configuration files

## Dependencias

Para que la aplicación funcione tiene que haber una instancia de mongodb
corriendo en su puerto default (27067) y una instancia de redis corriendo
en el puerto 16379.

Para simular estos dos componentes utilizo dos imágenes preconfiguradas,
que puede ser iniciadas con los siguientes comandos

 * [Docker](https://docs.docker.com/engine/examples/mongodb/):
 $ docker run -p 27017:27017 --name mongo3 -d mongo
 * [Redis](https://hub.docker.com/_/redis/)
 $ docker run -p 16379:6379 --name redis -d redis


## Deploy

### Configuración del Scraper de twitter

Para que el scraper de twiter funcione tenemos que obtener las credenciales
de twitter y crear un archivo scraperconfig.cfg con la siguente info

```
[twitter]
CONSUMER_KEY=<CONSUMER_KEY>
CONSUMER_SECRET=<CONSUMER_SECRET>
ACCESS_TOKEN=<ACCESS_TOKEN>
ACCESS_TOKEN_SECRET=<ACCESS_TOKEN_SECRET>
```


### Pasos para configurar la aplicacion el scraper

1. Bajar este repo

  $ wget https://github.com/santiavenda2/profile-scraper-conf/archive/master.zip

  $ unzip master.zip

  $ cd profile-scraper-conf

2. crear virtualenv

  $ virtualenv venv

  $ source venv/bin/activate

3. instalar dependencias
  
  $ pip install -r requirements.txt

Con esto ya estamos listos para correr los dos componentes de nuestro servicio:
la api rest y los workers de celery

Para iniciar un worker

0. Activar virtualenv si no esta activado
 
  $ source venv/bin/activate
1. Exportar las variables de configuracion
   
 $ export TWITPROF_CELERY_SETTINGS=celeryconfig
 
 $ export TWITPROF_SCRAPER_SETTINGS=scraperconfig.cfg
2. iniciar celery worker
 
 $ celery worker -A twitprof.tasks.celery:app

Para iniciar la api

0. Activar virtualenv si no esta activado
 
  $ source venv/bin/activate
1. Exportar las variables de configuracion
 
  $ export TWITPROF_CELERY_SETTINGS=celeryconfig
 
  $ export TWITPROF_SCRAPER_SETTINGS=scraperconfig.cfg
2. Exportar variable de configuracion de la api (cambiar la direccion al directorio actual)
  
  $ export TWITPROF_API_SETTINGS=<current_dir>/config.cfg
3. iniciar uwsgi

  $ uwsgi --ini uwsgi.ini

Testearlo accediendo a:

http://localhost:8080/twitter/profile/209812358

(cambiar el id para buscar otros perfiles)

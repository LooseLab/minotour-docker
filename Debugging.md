## Basic debugging
In order to debug minotour in docker, there are a couple of possible approaches.

#### Exec in and read celery logs
Due to an unfortunate misunderstanding of docker (by me), celery and django are smushed into one container. This makes reading the logs a little tricky. Fixing this is on my todo list

Whilst the container is running it is possible to see the logged output of celery workers 
```bash
docker exec -it web bash
```
Once the container shell opens the logs are located at

`/var/lib/minotour/logs`

```bash
 tail -f -n 100 /var/lib/minotour/logs/celery.log
 ```

Will stream the logs to your console.

```bash
tail -f -n 100 /var/lib/minotour/logs/uwsgi.log
 ```

Will stream access and the output of any django views - there may be error there.

As these are normal files it is possible to also `grep` for any errors or to seek out specific commands.
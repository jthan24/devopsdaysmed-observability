# devopsdaysmed-observability
Repositorio DEMO para DevOpsDays Medellin

## PreRequisitos
docker

## Desplegar la infra

### Desplegar cadvisor
```bash
docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --volume=/dev/disk/:/dev/disk:ro \
  --publish=8080:8080 \
  --detach=true \
  --name=cadvisor \
  --privileged \
  --device=/dev/kmsg \
  gcr.io/cadvisor/cadvisor:latest

## Acceder a la interfaz de cadvisor
http://localhost:8080

## Validar las metricas de cadvisor
http://localhost:8080/metrics

```
## Actualizar el archivo de prometheus
```bash
#Dependiendo del OS puede validar la salida, este comando retorna la IP suponiendo que estas trabajando sobre una subnet 192.X.X.X si es distinta debes modificar segun la necesidad
ifconfig |grep 192
```

### Desplegar prometheus
Hacer la recoleccion de las metricas y almacenamiento de las mismas
```bash
# Lanzar prometheus con la configuracion del archivo prometheus-conf.yaml
docker run --name prometheus -d -p 9090:9090 -v $(pwd)/prometheus-conf.yaml:/etc/prometheus/prometheus.yml prom/prometheus 

## Acceder a la interfaz de prometheus
http://localhost:9090
## Validar las metricas en recoleccion
http://localhost:9090/targets

```


### Desplegar grafana
Montar los tableros en grafana para monitorear el estado de Docker
```bash
# Lanzar grafana con la conf por defecto
## Lanzar grafana con la configuracion del datasource
docker run -d --name=grafana -p 3000:3000 -v $(pwd)/grafana-provisioning/datasources:/etc/grafana/provisioning/datasources grafana/grafana
## Lanzar grafana con la configuracion del datasource y los tableros
docker run -d --name=grafana -p 3000:3000 -v $(pwd)/grafana-provisioning/datasources:/etc/grafana/provisioning/datasources -v $(pwd)/grafana-provisioning/dashboards:/etc/grafana/provisioning/dashboards grafana/grafana
## Acceder a la interfaz de grafana
http://localhost:3000
```


#### Configure datasource on grafana
Ahora vamos a la parte interesante, como ver las metricas que se estan recolectando en Docker

### (opcional) Agregar cargas de trabajo a grafana
```bash
# Clonar este repositorio
git https://github.com/dockersamples/example-voting-app.git

## Mover la ubicacion actual a la ruta recien descargada
cd example-voiting-app

### Lanzar las nuevas cargas de trabajo
docker compose up

#### Acceder a la aplicacion de voto
http://localhost:5000/
#### Acceder a la aplicacion de resultados
http://localhost:5001/
```


## References
- https://docs.docker.com/config/daemon/prometheus/
- https://prometheus.io/docs/prometheus/latest/installation/
- https://github.com/dockersamples/example-voting-app
- https://grafana.com/docs/grafana/latest/setup-grafana/installation/docker/
- https://hub.docker.com/r/grafana/grafana-oss
- https://medium.com/@nagavenkateshgowru/monitoring-docker-containers-d26bd0a791b4
- https://github.com/google/cadvisor
- https://grafana.com/docs/grafana/latest/administration/provisioning/
- https://github.com/grafana/grafana/blob/main/conf/provisioning/dashboards/sample.yaml
- https://grafana.com/tutorials/provision-dashboards-and-data-sources/
- https://docs.docker.com/storage/volumes/


- https://grafana.com/grafana/dashboards/16310-docker-and-system-monitoring/
- https://grafana.com/grafana/dashboards/3662-prometheus-2-0-overview/
- https://grafana.com/grafana/dashboards/2-prometheus-stats/


- https://grafana.com/grafana/dashboards/15489-prometheus-2-0-stats/
- https://grafana.com/grafana/dashboards/3590-grafana-internals/

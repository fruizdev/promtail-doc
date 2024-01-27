
# Table of Contents

1.  [hosted logs](#org75817ae)
2.  [crear dashboard](#org360cb9d)


<a id="org75817ae"></a>

# hosted logs

nos dirigimos a Home -> Connections -> Add new connection -> Hosted logs

![img](asset/1.png)

y creamos un token para el nuevos dispositivo nos dara una
configuracion agregando el token recien creado ejemplo:

    
    server:
      http_listen_port: 0
      grpc_listen_port: 0
    
    positions:
      filename: /tmp/positions.yaml
    
    client:
      url: https://354058:glc_eyJvIjoiNzY4NDE4IiwibiI6InN0YWNrLTUwNDY5Ni1pbnRlZ3JhdGlvbi1hcnR1cml0byIsImsiOiIzY2ZNWjVoOHY0MjF2M3NaNTZxbjZQdWwiLCJtIjp7InIiOiJ1cyJ9fQ==@logs-prod-017.grafana.net/api/prom/push
    
    scrape_configs:
    - job_name: system
      static_configs:
      - targets:
          - localhost
        labels:
          job: varlogs
          __path__: /var/log/*.log

para agregar una nueva fuente de logs debe quedar asi

    server:
      http_listen_port: 0
      grpc_listen_port: 0
    
    positions:
      filename: /tmp/positions.yaml
    
    client:
      url: https://354058:glc_eyJvIjoiNzY4NDE4IiwibiI6InN0YWNrLTUwNDY5Ni1pbnRlZ3JhdGlvbi1hcnR1cml0byIsImsiOiIzY2ZNWjVoOHY0MjF2M3NaNTZxbjZQdWwiLCJtIjp7InIiOiJ1cyJ9fQ==@logs-prod-017.grafana.net/api/prom/push
    
    scrape_configs:
    - job_name: ar-locker
      static_configs:
      - targets:
          - localhost
        labels:
          job: ar-locker
          __path__: /var/log/*.log
    - job_name: ar-mosquitto
      static_configs:
      - targets:
          - localhost
        labels:
          job: ar-mosquitto
          __path__: /var/log/mosquitto 

esta configuracion debe ir dentro de un directorio llamado promtail

debe quedar algo asi:

    /dir/promtail/config.yaml

siendo dir el directorio donde ejecutaremos el compose o comando de docker

ejemplo de comando docker para ejecutar promtail con esta configuracion

    
    docker run --name promtail \
    --volume "$PWD/promtail:/etc/promtail" \
    --volume "/var/log:/var/log" \
    grafana/promtail:main \
    -config.file=/etc/promtail/config.yaml

donde  &#x2013;volume "/var/log:/var/log"   el primer var log es la ruta desde dondo provienen nuestros logs

tambien es posible ejecutarlo desde docker compose en este caso
seria as:

    version: "3"
    
    networks:
      loki:
    
    services:
      promtail:
        image: grafana/promtail:2.9.0
        volumes:
          - /home/pi/prod/logs:/var/log            #la primera parte es desde donde vienen nuestros logs
          - /var/log/mosquitto/:/var/log/mosquitto #var log mosquitto fue definido en config.yaml
          - ./promtail/config.yaml:/etc/promtail/config.yml
    
        command: -config.file=/etc/promtail/config.yml
        networks:
          - loki


<a id="org360cb9d"></a>

# crear dashboard

para crear un nuevo dasboard nos dirigimos a

[new dashboard](https://innovacion1.grafana.net/dashboard/new)

veremos esto

![img](asset/2.png)

presionamos en add visualization y aparecera los siguiente

![img](asset/3.png)

seleccionam el recolector de logs por defecto en este caso grafanacloud-innovacion1-logs y seleccionamos
dashboard, deberiamos ver algo asi:

![img](asset/4.png)

donde dice time series seleccionamos que tipo de filtro requerimos en este caso
logs

![img](asset/5.png)

una vez seleccionamoy ya tenemos seleccionado la fuente de logs
podemos seleccionar una query desde un archivo como el este ejemplo
que apunta al log de mosquitto

![img](asset/6.png)


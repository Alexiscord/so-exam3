###Universidad ICESI - Sistemas Operativos <br />

# Parcial Tres <br />

**Nombre:** Joan Alexis Córdoba Narváez <br />
**Código:** A00232548 <br />

**URL repositorio:** https://github.com/alexiscord/so-exam3 <br />

Sensu fue intalado siguiendo la guía del repositorio https://github.com/ICESI/so-monitoring/tree/master/server donde se ha usadoel script install.sh y los archivos que se necesitan para luego ser editados según las características de las máquinas. etc/sensu y uchiwa.json <br />

```json
{
    "sensu": [
        {
            "name": "sensu",
            "host": "localhost",
            "ssl": false,
            "port": 4567,
            "path": "",
            "timeout": 5000
        }
    ],
    "uchiwa": {
        "port": 3000,
        "stats": 10,
        "refresh": 10
    }
}
```
 
:
 En la maquina cliente se ejecuta lo siguiente:
``` 
sudo yum install screen vim -y

echo '[sensu]
name=sensu
baseurl=https://sensu.global.ssl.fastly.net/yum/$releasever/$basearch/
gpgcheck=0
enabled=1' | sudo tee /etc/yum.repos.d/sensu.repo

yum install sensu -y
sensu-install -p sensu-plugin
yum install httpd -y
service sensu-client start
``` 
Se procede a editar client.json que esta en client/etc/sensu/conf.d/

```json
{
  "client": {
    "name": "so-centos-cliente",
    "address": "192.168.182.132",
    "subscriptions": ["webservers"]
  }
}
```

Se edita json rabbitmq.json.

```json
{
  "rabbitmq": {
    "host": "192.168.182.131",
    "port": 5672,
    "vhost": "/sensu",
    "user": "sensu",
    "password": "password",
    "heartbeat": 10,
    "prefetch": 50
  }
}
```

Y agregamos el script de /etc/sensu/plugins/check-apache.rb

```
#!/usr/bin/env ruby

procs = `ps aux`
running = false
procs.each_line do |proc|
  running = true if proc.include?('httpd')
end
if running
  puts 'OK - Apache daemon is running'
  exit 0
else
  puts 'WARNING - Apache daemon is NOT running'
  exit 1
end
```

Se hace el llamado hacia el cliente desde el servidor

```
service sensu-client start
```

En el servidor se ejecutan las siguientes líneas para los servicios:

```
service sensu-client start
service sensu-server restart & service sensu-api restart & service uchiwa restart
```

Evidencia de la ejecución:

![][1] 
![][2] 
![][3] 
![][4] 

Se instala el stack ELK(elasticsearch, logstash, kibana, filebeat en el client)) con los comandos de la guia https://gist.github.com/d4n13lbc/be1ad5039dff1c058b335482488d4965

Evidencias de la ejecución:

![][5] 


[1]: imgs/img1.png
[2]: imgs/img2.png
[3]: imgs/img3.png
[4]: imgs/img4.png
[5]: imgs/img5.png

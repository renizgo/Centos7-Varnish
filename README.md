# Centos7-Varnish

Imagem Docker com sistema operacional Centos + Systemd + Varnish


## Introdução

O Varnish Cache tem a função de acelerador de aplicação Web, também conhecido pelo conceito de proxy-reverso, geralmente ele é utilizado na frente de um servidor Web e configurado para armazenar em cache o conteúdo.

Com o uso do Varnish pela documentação você consegue acelerar a entrega em mais de 300 vezes.

O Varnish tem como uma das principais características seu desempenho e flexibilidade usando sua linguagem VCL que é um acrônimo para Varnish Cache Language. Com esta linguagem você consegue escrever políticas sobre como as solicitações de entrada devem ser tratadas e quais os conteúdos que devem possuir cache.

O Varnish é um software livre em licença BSD.


## Instalação

A instalação do Varnish pode ser feita por pacotes ou pelos binários e também é aceito em muitos sistemas operacionais, neste artigo vou simular uma instalação em um Centos em forma de container Docker.

Como vou usar Docker vou simular os passos de uma instalação em um arquivo Dockerfile.


## Container com Centos 7 + Systemd + Varnish Cache


### Arquivo Dockerfile:

```
FROM renizgo/centos7-systemd
RUN yum -y update; yum -y install epel-release; yum -y install varnish ; yum clean all; systemctl enable varnish.service
EXPOSE 80
CMD ["/usr/sbin/init"]
```

### Construindo a imagem:
```
docker build -t renizgo/centos7-varnish .
```

### Rodando o container:
```
docker run --privileged --name centos7-varnish -v /sys/fs/cgroup:/sys/fs/cgroup:ro -p 6081:6081 -d renizgo/centos7-varnish /usr/sbin/init
```

### Verificando a execução do serviço no container:
```
Renato-Mac:renizgo_centos7-varnish renato$ docker exec -it e97c2958cf66 /bin/bash
[root@e97c2958cf66 /]# systemctl status varnish
● varnish.service - Varnish Cache, a high-performance HTTP accelerator
   Loaded: loaded (/usr/lib/systemd/system/varnish.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2017-08-24 16:49:52 UTC; 4min 35s ago
  Process: 22 ExecStart=/usr/sbin/varnishd -P /var/run/varnish.pid -f $VARNISH_VCL_CONF -a ${VARNISH_LISTEN_ADDRESS}:${VARNISH_LISTEN_PORT} -T ${VARNISH_ADMIN_LISTEN_ADDRESS}:${VARNISH_ADMIN_LISTEN_PORT} -S $VARNISH_SECRET_FILE -u $VARNISH_USER -g $VARNISH_GROUP -s $VARNISH_STORAGE $DAEMON_OPTS (code=exited, status=0/SUCCESS)
 Main PID: 137 (varnishd)
   CGroup: /docker/e97c2958cf66f3102fc61dea5554171d903b225b90a3cb2b1179697cefbe5b7f/system.slice/varnish.service
           ├─137 /usr/sbin/varnishd -P /var/run/varnish.pid -f /etc/varnish/default.vcl -a :6081 -T 127.0.0.1:6082 -S /etc/varnish/secret -u varnish -g varnish -s malloc,256M
           └─161 /usr/sbin/varnishd -P /var/run/varnish.pid -f /etc/varnish/default.vcl -a :6081 -T 127.0.0.1:6082 -S /etc/varnish/secret -u varnish -g varnish -s malloc,256M

Aug 24 16:49:52 e97c2958cf66 systemd[1]: Starting Varnish Cache, a high-performance HTTP accelerator...
Aug 24 16:49:52 e97c2958cf66 varnishd[137]: Platform: Linux,4.9.41-moby,x86_64,-smalloc,-smalloc,-hcritbit
Aug 24 16:49:52 e97c2958cf66 systemd[1]: Started Varnish Cache, a high-performance HTTP accelerator.
Aug 24 16:49:52 e97c2958cf66 varnishd[137]: child (161) Started
Aug 24 16:49:52 e97c2958cf66 varnishd[137]: Child (161) said Child starts
```

Acesse a página web do [Localhost:6081](http://127.0.0.1:6081 "Localhost:6081")

Imagem01: 
![Imagem01](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Imagem01")

Esta tela mostra que seu Varnish Cache está funcionando porém sem configurações.

### Configurando o Backend

Edite o arquivo de configuração:
```
# vi /etc/varnish/default.vcl
```

Troque isto:
```
vcl 4.0;

backend default {
    .host = "127.0.0.1";
    .port = "8080";
}
```

Por isso:
```
vcl 4.0;

backend default {
    .host = "www.varnish-cache.org";
    .port = "80";
}
```


Quando acessar o [Localhost:6081](http://127.0.0.1:6081 "Localhost:6081")

Imagem02: 
![Imagem02](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Imagem02")

Imagem03: 
![Imagem03](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Imagem03")


Estas telas mostram que o seu Varnish está funcional.

# Conceitos sobre Docker - Redes

## Mostrar as redes presentes:

``` Docker network ls ```

## o Docker possui três tipos de rede:

### 1.  None <br>
Container isolado via rede!
Sem conexão com rede!

    command:
    > docker container run -d --net none debian
    > ex.:
    > docker container run --rm alpine ash -c "ifconfig"
    > docker container run --rm --net none alpine ash -c "ifconfig"
    
### 2.  Bridge <br>
permite uma camada de isolamento da rede dos container e a rede do host
inspecionar a rede Bridge:

    > docker network inspect bridge
    > "IPAM:": { "Config": [ { "Subnet": ***, "Gateway": *** } ] }
    > Comunicação:
    > docker container run -d --name container1 alpine sleep 1000
    > docker container exec -it container1 ifconfig
    > docker container run -d --name container2 alpine sleep 1000
    > docker container exec -it container2 ifconfig
    > pingar:
    > docker container exec -it container1 ping 172.17.0.3 (container2)
### 3.  Host <br>
    - Não tem mais o Bridge como sendo essa ponte entre as maquinas de rede da maquina hostcom as interfaces de rede do container.
    - Nesse modo o container vai usar diretamente as interfaces de rede da maquina host.
    - Ele possui um nivel de segurança bem mais baixo. 
    - Porque você tira essa camada de isolamento da camada que o modo bridge fornece e tem acesso direto as interfaces de rede.

## comandos:

```
    > docker container run -d --name container4 --net host alpine sleep 1000
    > docker container exec -it container4 ifconfig
```

## Criar uma nova rede:

### comando:

    > docker network create --driver bridge rede-nova

### utilizando essa rede nos containers:

    > docker container run -d --name container3 --net rede-nova alpine sleep 1000

### configurar o container3 para se conectar na rede bridge

    > docker network connect bridge container3
    O container3 tem acesso tanto a rede-nova quanto a rede bridge!
    > docker container exec -it container3 ifconfig
    testando conectividade com o container1:
    > docker container exec -it container3 ping 172.17.0.2
    desconectar o container3 da bridge:
    > docker network disconnect bridge container3
    > docker container exec -it container3 ifconfig (visualizar)

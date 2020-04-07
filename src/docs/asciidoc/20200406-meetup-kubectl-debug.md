# Exercice d’investigation avec Jerome Petazzoni
(7 ans chez Docker inc avant de faire un break https://youtu.be/TiRoge93H0o)

A nice bug report :
```
When I do
I’m expecting
Instead I get
```

Impossible d’accéder au site web

le DNS ?
```
host mon.domain.org
dig mon.domain.org
```
ah non, tout va bien

Bon, et le serveur, il expose quoi ?

`sudo netstat -tuna` (mémotechnique : TUNA PLZ !!!)

tiens, bizarre ça écoute sur un port funky : 88

Impossible de se connecter avec en shell sur le container ...

but why ? il s’agit d’une base from scratch

ça sent mauvais, il faut un accès aux nodes (j’ai mal à ma ceinture de sécurité)

## Okay, et si on y injectait busybox ?

### 1 visualiser l’arborescence et exporter busybox
```
docker container run busybox
docker container export CONTAINER_ID_BUSYBOX | tar -tvf-
docker container cp CONTAINER_ID_BUSYBOX:/bin/busybox crazy-bbox
```

### 2 injecter busybox
`docker container cp $PWD/crazy-bbox CONTAINER_ID_KI_BUG:/bin/busybox`

### 3 connecter
`docker exec -it CONTAINER_ID_KI_BUG /bin/busybox sh`

## Acces au namespace linux pour ce mettre dans le namespace du container
```
docker container inspect CONTAINER_ID-KI_BUG | grep -i pid
nsenter -t PID -u -n -i (mémotechnique : "T'ES PUNI !!!")
```

## Autre option 
```kubectl alpha debug```
on pop un container dans un pod sans rédémarrer le pod
alpha => il faut activer des flag au niveau du control plane et kubelet
Ephemeral containers
ça marche sous avec les runtimes docker et containerd uniquement pour le moment
ça restera en alpha tant qu’il n’y aura pas plus de d’utilisateurs de test

Bon finalement, c'est juste un port mal exposé, mais ce n'est pas vraiment ça qui est intéressant :)

slides : https://docs.google.com/presentation/d/1nCOlPslzAkyCtRvLCmu4qj_XA_QqTE_r5iQF0BzM1bk/edit#slide=id.p

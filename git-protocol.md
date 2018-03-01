# Git Protocols

## Protocols
* Local - git clone /srv/git/project.git (disk access)
* HTTP - git clone https://example.com/gitproject.git (https support, user/pwd auth).
* SSH - git clone [user@]server:project.git (ssh) 
* Git - git clone https://example.com/gitproject.git (fast, no auth, same internal)

## Transfer protocols
Git puede mover datos entre repositorios principalmente de dos formas. Usando el "Dumb protocol" y el "Smart Protocol".

### Dumb protocol
Este protocolo sólo es válido para operaciones de lectura por HTTP.
Es un protocolo enemigo de lo óptimo. Se basa en que el cliente hace multiples peticiones GET al servidor sin que haya ningún flujo de información desde el cliente al servidor.

Procede del siguiente modo:
* Pide las referencias del repositorio.
* Por cada referencia pide el objeto commit al que apuntan.
* Por cada commit saca el commit padre y el tree y pide esos objetos.
* Por cada tree saca sus entradas y pide los objetos (nuevas entradas de tree, o blobs)
* Continua recursivamente hasta que ha procesado todo el grafo de commits / trees.
* Cuando no encuentra un objeto en 'loose' format, pide la lista de los packs y empieza a pedir los .idx hasta que encuentra el objeto que buscaba. En ese momento baja el pack para tener el objeto en el repo local.

En [GitHub](https://blog.github.com/2011-03-09-git-dumb-http-transport-to-be-turned-off-in-90-days/) lleva deshabilitado desde 2011 :-O

```
set GIT_SMART_HTTP=0
git clone http://github.com/rubarax/recursivemerge.git
```

### Smart protocol

#### Subiendo datos (push)
Se basa en los comandos 'send-pack' & 'receive-pack' de git (uno lo ejecuta el cliente y otro el server).

Estos son realmente comandos que pueden desde la CLI y que el propio git lanza internamente a la hora de hacer un fetch/push.

El cliente pide las referencias al servidor push. Si el cliente no la conoce el commit al que apunta la rama que se va a hacer push, el push se rechazará, habrá conflicto en destino ya que hay algún cambio nuevo en el remote repo.

Git no se complica más la vida, incluso en el caso de que el cliente no tenga nuevo cambios para subir si hay un commit nuevo en el server y se intenta un push, este es rechazado en lugar de decir que no hay nada para subir.

**Demo**

Una vez que el cliente sabe en que commit está el server, le es "fácil" (algoritmo de grafos, su famoso revwalker) calcular los commits que tiene que subir. Prepara el pack y lo envía.

#### Bajando datos (fetch)

Requiere "inteligencia" por parte del servidor (capacidad de hacer cálculos, no sólo leer objetos), pero es mucho más optimo que el "dumb". En este caso el flujo de información es bidireccional. El cliente le especifíca al servidor que objetos tiene y que cuales quiere y a partir de ahí el servidor genera un pack con todos los objetos necesarios.

Se basa en los comandos 'fetch-pack' & 'upload-pack' de git.

En este caso hay una pequeña negociación entre el cliente y el servidor. El client le envía entradas de que quiere y que commits tiene ya en el siguiente formato:

```
want ca82a6dff817ec66f44342007202690a93763949 master
want fca82a6dff817ec66f44342007202690a93763949  task
have 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
have 9348934cd89a8789789a004957385bb3bcb608e1
done
```

Esta negociación continúa hasta que el unos de los dos la para y el servidor puede crear el package a enviar.

Hay varios modos de negociación soportados, que introducen pequeños cambios al proceso.

Toda la información detallada [aquí](https://github.com/git/git/blob/master/Documentation/technical/pack-protocol.txt)

**Demo con sniffer**

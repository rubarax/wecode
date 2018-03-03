# Git Objects

Git tiene los siguientes tipos de objetos:
* Commit
* Tree
* Blob
* Tag

Los objetos de Git se pueden encontrar en dos formas "loose" o "packed". Cuando hacemos un clone, todos los objectos del repo los recibimos en un único pack, mientras que si hacemos un commit en nuestro repo local, se creará un fichero por cada objeto

Todos los objetos se pueden explorer directamente a través del siguiente comando (no importa si está en forma loose o packed, el commando es transparente):
```
git cat-file -p sha
```

**Demo**
```
> git clone https://github.com/rubarax/socketperf
....

> git rev-parse HEAD (see the head / loaded commit).
77b568d1f2d157b2e62e94cdf2f9ab144399329c

> type .git\HEAD
ref: refs/heads/master

> type .git\refs\heads\master
77b568d1f2d157b2e62e94cdf2f9ab144399329c

> git cat-file -t 77b568d1f2d157b2e62e94cdf2f9ab144399329c
commit

> git cat-file -s 77b568d1f2d157b2e62e94cdf2f9ab144399329c
216

> git cat-file -p 77b568d1f2d157b2e62e94cdf2f9ab144399329c (commit)
tree b29f753b2f42adf57607f9769f39123a8968cbe3
parent dfccf485f695a2bb05758850aac23a175edcce01
author rubarax <rubarax@gmail.com> 1430236995 +0200
committer Ruben <rubarax@example.com> 1430237386 +0200

simple change

> git cat-file -p dfccf485f695a2bb05758850aac23a175edcce01 (parent commit)
tree 183694dd3c8ce28c66c772040ace09c623231a34
parent 2d656daaf5e19ea5c6e21be1fc6bf65d81414fa8
author Ruben <rubarax@example.com> 1430236271 +0200
committer Ruben <rubarax@example.com> 1430237385 +0200

change in task

> git cat-file -p b29f753b2f42adf57607f9769f39123a8968cbe3 (tree)
100644 blob 3ef11398f77a2de6379752f4fb4899f26027cb05    README.md
040000 tree 6555d1c4d9078156b1cbe546cab667e0a959b9b7    c
040000 tree 5b62bedd5ba3fd67900be88b0710bc0868081c4e    dotnet

> git cat-file -p 6555d1c4d9078156b1cbe546cab667e0a959b9b7
040000 tree e517842439f3a783eb1528d50cce4460a875da0e    linux
040000 tree 1024eeceb6012fa015a5fe3cab34a735a08bb3e5    socketperf

> git cat-file -p 1024eeceb6012fa015a5fe3cab34a735a08bb3e5
100644 blob 3aeb717a44241c31effade3f06ca3697a87decf1    socketperf.c
100644 blob 503cc3922050336fd0a88be819ae762015197502    socketperf.sln
100644 blob 7cafb42d9d750299f4c2dd8af5d9587bfb82bc0e    socketperf.vcxproj
100644 blob c0994a9307bd8727873e835c87b4e03bcd4ded6f    socketperf.vcxproj.filters
```

Desde una referencia a un commit se puede explorar toda la estructura del repo hacía atrás pero nunca hacia adelante, por eso las referencias (HEAD, refs/heads, refs/tags, remotes) son clave, ya que son los que permiten recorrer el repositorio.

En git no se puede saber directamente desde que commits es apuntado un determinado blob.

Tampoco hay una identificación única a nivel de item de un fichero. Es decir, si añado `src/foo.c` y `doc/foo.c` con el mismo contenido, ambos blobs tendrán la misma sha. Si borramos uno y movemos el otro, para Git es imposible saber cual de los dos se movió y cual se borro (se puede vivir con eso :).

La estructura de DAG hace que cuando se hace un cambio en un elemento, también cambian todos los padres del elemento, pero el resto de nodos del árbol se mantienen sin cambios.

**Demo**

```
> git commit -a -m"change in socketperf.c"
[master 696a0d6] change in socketperf.c
 1 file changed, 1 insertion(+), 1 deletion(-)
 
> git rev-parse HEAD
696a0d65933c7f1791a0282dc084fa39b37c5f3e
 
> git cat-file -p 696a0d65933c7f1791a0282dc084fa39b37c5f3e
tree a9890403bb1291b1d2d18f64bd39f62e8451f5b6
parent 77b568d1f2d157b2e62e94cdf2f9ab144399329c
author ruben <rubarax@gmail.com> 1519732233 +0100
committer ruben <rubarax@gmail.com> 1519732233 +0100
 
change in socketperf.c
 
> git cat-file -p a9890403bb1291b1d2d18f64bd39f62e8451f5b6
100644 blob 3ef11398f77a2de6379752f4fb4899f26027cb05    README.md
040000 tree 7b438ea96d4670915ff6ee5222d0e891ee07c7d9    c
040000 tree 5b62bedd5ba3fd67900be88b0710bc0868081c4e    dotnet
```

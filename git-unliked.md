# Git unlinked objects

Git es un repositorio de objetos, pero los objetos en sí no tienen valor por sí mismos. El valor de un objeto viene dado por las referencias.

Si un objeto no es apuntado por ninguna referencia es susceptible de ser borrado del repositorio y por supuesto no saldrá de nuestro repositorio local.

Vamos a ver como un commit puede ser borrado cuando ya nadie apunta a él.

**Demo**
```
> git rev-parse HEAD
fa3d5ed11ec2171ad877754421d42a40446bbe09

> git commit -a -mtrash
[master 844528e] trash
 1 file changed, 1 insertion(+), 1 deletion(-)
 
> notepad .git\refs\heads\master -> REPLACE BY THE PREVIOUS HASH
 
> git reset --hard

> git gc --prune=all

e:\tmp\wecode\recursivemerge>git cat-file -p 844528e
tree 53de7f51eb208b63bf25039ea1aeaf6b52118507
parent fa3d5ed11ec2171ad877754421d42a40446bbe09
author ruben <rubarax@gmail.com> 1519818585 +0100
committer ruben <rubarax@gmail.com> 1519818585 +0100

trash
```

El commit posiblemente no se ha borrado porque aún es referenciado por los logs (información local y volátil), y el algoritmo del gc lo tiene en cuenta como referencia.

Justo estos ficheros de log son la única forma de saber con exactitud en que rama fue creado un commit. Una vez esa información se descarta o fuera del repositorio local, es imposible saber donde se creo con total certeza en mucho escenarios.

```
> git reflog --all
fa3d5ed HEAD@{0}: commit: trash
fa3d5ed HEAD@{1}: commit: trash
fa3d5ed HEAD@{2}: commit: trash
19d1232 HEAD@{3}: commit: trash
dec1c23 HEAD@{4}: clone: from git://github.c

> rmdir /q /s .git\logs

> git reflog --all

> git gc --prune=all

> git cat-file -p 844528e
fatal: Not a valid object name 844528e
```

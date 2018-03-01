# Recursive merge

Git puede utilizar varias estrategias de merge aunque la más utilizada es el merge recursivo (cuando tiene que mezclar dos commits) usando el merge de tres vías (base, src, dst).

La potencia del merge recursivo sólo entra en juego cuando hay varios antecesores posibles.

Para ver su potencia y como funciona vamos a seguir el [blogpost](http://blog.plasticscm.com/2011/09/merge-recursive-strategy.html) que escribimos hace tiempo, cuyo ejemplo está migrado a git en el siguiente repo:

```git://github.com/rubarax/recursivemerge.git```

**Demo**

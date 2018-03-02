# Recursive merge

Git puede utilizar varias estrategias de merge aunque la más utilizada es el merge recursivo (cuando tiene que mezclar dos commits) usando el merge de tres vías (base, src, dst).

La potencia del merge recursivo sólo entra en juego cuando hay varios antecesores posibles.

Para ver su potencia y como funciona vamos a seguir el [blogpost](http://blog.plasticscm.com/2011/09/merge-recursive-strategy.html) que escribimos hace tiempo, cuyo ejemplo está migrado a git en el siguiente repo:

```git://github.com/rubarax/recursivemerge.git```

**Demo**

Para facilitar la comprensión vamos a ver unas tablas de como sería la resolución del merge en cada caso:

**Merge with ancestor cs:4 -> WRONG!**

SRC (cs:6) | BASE (cs:4) | DST (cs:7)| RESULT
--- | --- | --- | ---
a |  |  | a
b | b | b | b
C | c | c | `*C*`
d | d | d | d
E | E | E | E

**Merge with ancestor cs:2 -> RIGHT!**

SRC (cs:6)| BASE (cs:2)| DST (cs:7)| RESULT
--- | --- | --- | ---
a |  |  | a
b | b | b | b
C | C | c | `*c*`
d | d | d | d
E |  | E | E


**Recursive merge - calculte final ancestor -> ALWAYS RIGHT!**

SRC (cs:4)| BASE (cs:1)| DST (cs:2) | RESULT
--- | --- | --- | ---
 |  |  | 
b | b | b | b
c | c | C | `C`
d | d | d | d
E |  |  | E

Using the recurive merge ancestor result:

SRC (cs:6) | BASE (rmr) | DST (cs:7)| RESULT
--- | --- | --- | ---
a |  |  | a
b | b | b | b
C | C | c | `*c*`
d | d | d | d
E | E | E | E


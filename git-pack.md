# Git pack files

 Git es capaz de almacenar varios objetos en un mismo fichero. Estos ficheros se guardan dentro del directorio `.git/objects/pack`.
 
 Por cada pack hay dos ficheros el '.pack' que contiene los objetos en sí mismos y el '.idx' que es un índice a los objetos del pack.
 
  ```
  2,920 pack-e31fd70d21cdf7669be30abeb9d647c8bf290c15.idx
16,063 pack-e31fd70d21cdf7669be30abeb9d647c8bf290c15.pack
 ```
 
 Cuando hacemos un fetch (clone) o un push los objetos siempre viajan en formato pack, aunque en este caso no viaja un .idx asociado. Es el receptor del pack el que se encarga de verificar su integridad (checksum + objects integrity) y generar el .idx a partir de él.
 
 El .idx se puede regenerar sin ningún problema a partir del .pack, es simplemente un índice para mejorar significativamente la velocidad de acceso a los objetos del .pack.

**Demo**
```
> git verify-pack -v .git/objects/pack/pack-e9684ff1c584fda34710078f867eaabbf0ef0f68
dec1c2308e8161aa11a2e3b3ea067c90809223e1 commit 272 285 12
9bd59e03f9e2ce0cd2a57a01cf21b6ff1fccb33b commit 272 285 297
75b054eac4490f457a531953d06e8906ba4d375f commit 212 225 582
faac2e96b180f16f0854fb411e0a2df3c21a4c8c commit 212 225 807
e1a6f9d7dd253159762fd98959e86e7845d782b0 commit 213 226 1032
54811db1c15dfe8c442a79426d1578ad840317ef commit 212 225 1258
3219b6998aac3b08a1e437892cbc47ebb3b1951c commit 213 226 1483
7fa743ce63e8d0230b6623ab4735c8839be81fd2 commit 167 180 1709
ace0c08e6bfd3be1dc502e75ba02c39a0a5e3b4d blob   20 33 1889
1c178c245096194f56626ade28695cfa928256c4 tree   33 46 1922
6987540f5e84c9f1872dbe2edcf438e26b8485e7 tree   33 46 1968
74b31a084118a8aece7791df850fc163a7566b54 blob   21 34 2014
115c91038ea32805f5278db1666eaac87162b83d tree   33 46 2048
ac87b38d3de5889af89a52118e498381f6038a13 blob   19 32 2094
ce2537e115951614152e87d46ef86b0db5d837da tree   33 46 2126
33d33e4d04fe015fcef167c17f9c8338aff179be blob   20 33 2172
a71c8acf5adea347ba2ed611029b2df4bdd6ffd7 tree   33 46 2205
c958ead66563e5a4491bd38e2c969da334ff6ee8 blob   19 32 2251
c3ef47f6e20a82d571175052979d8388b7788b6b tree   33 46 2283
525dff9fbf4b6238c68b687bbce46c6364ee4d35 blob   20 33 2329
non delta: 20 objects
.git/objects/pack/pack-e9684ff1c584fda34710078f867eaabbf0ef0f68.pack: ok

> del /f .git\objects\pack\pack-e9684ff1c584fda34710078f867eaabbf0ef0f68.idx

> git index-pack .git\objects\pack\pack-e31fd70d21cdf7669be30abeb9d647c8bf290c15.pack
```

Cuando hacemos commit en nuestro repositorio local , git crea objetos `loose` y sólo se empaquetan si lo indicamos con un commando explícitamente o bien cuando los packs llegan a unos determinados valores y Git decide dispararlo por sí mismo.

```
e:\tmp\wecode\recursivemerge>dir .git\objects
 Volume in drive E is SSD
 Volume Serial Number is 1EB2-5AD2

 Directory of e:\tmp\wecode\recursivemerge\.git\objects

02/28/2018  12:29    <DIR>          .
02/28/2018  12:29    <DIR>          ..
02/28/2018  12:29    <DIR>          7f
02/28/2018  12:29    <DIR>          9d
02/28/2018  12:29    <DIR>          fa
02/28/2018  12:29    <DIR>          info
02/28/2018  12:29    <DIR>          pack

> git repack
Counting objects: 3, done.
Writing objects: 100% (3/3), done.
Total 3 (delta 0), reused 0 (delta 0)

e:\tmp\wecode\recursivemerge>dir .git\objects\pack
 Volume in drive E is SSD
 Volume Serial Number is 1EB2-5AD2

 Directory of e:\tmp\wecode\recursivemerge\.git\objects\pack

02/28/2018  12:29    <DIR>          .
02/28/2018  12:29    <DIR>          ..
02/28/2018  12:26             1,716 pack-0ea20aced84d1caa2aded59cd2e11364893890c1.idx
02/28/2018  12:26             2,675 pack-0ea20aced84d1caa2aded59cd2e11364893890c1.pack
02/28/2018  12:29             1,156 pack-a13e94ea2ad5a9c6a320042cf7019378f72772ab.idx
02/28/2018  12:29               329 pack-a13e94ea2ad5a9c6a320042cf7019378f72772ab.pack

> git gc
Counting objects: 23, done.
Writing objects: 100% (23/23), done.
Total 23 (delta 0), reused 23 (delta 0)

 Directory of e:\tmp\wecode\recursivemerge\.git\objects

02/28/2018  12:30    <DIR>          .
02/28/2018  12:30    <DIR>          ..
02/28/2018  12:30    <DIR>          info
02/28/2018  12:30    <DIR>          pack
               0 File(s)              0 bytes
               4 Dir(s)  28,046,581,760 bytes free

e:\tmp\wecode\recursivemerge>dir .git\objects\pack
 Volume in drive E is SSD
 Volume Serial Number is 1EB2-5AD2

 Directory of e:\tmp\wecode\recursivemerge\.git\objects\pack

02/28/2018  12:30    <DIR>          .
02/28/2018  12:30    <DIR>          ..
02/28/2018  12:30             1,800 pack-e40a32fd3ee606505f0637e0dd32f74ab345e043.idx
02/28/2018  12:30             2,972 pack-e40a32fd3ee606505f0637e0dd32f74ab345e043.pack
               2 File(s)          4,772 bytes
               2 Dir(s)  28,046,581,760 bytes free               
```

# Introduction: what will be used in this course

## here documents

```
cat << EOF
Je m'appelle Jordy, j'ai 4 ans et je suis petit
EOF

Je m'appelle Jordy, j'ai 4 ans et je suis petit
```

```
source /usr/share/modules/init/sh
g16root=/share/programs/G16_SLATER
GAUSS_SCRDIR=/tmp
export g16root GAUSS_SCRDIR
. $g16root/g16/bsd/g16.profile

NPROC=4
MEM=2000MB

g16 << fin_de_fichier > h2_freq.log
%nproc=$NPROC
%mem=$MEM
# b3lyp/def2tzvp freq

titre

0 1
H 0.0 0.0 0.0
H 0.0 0.0 1.0

fin_de_fichier
```

## watch

```
watch date
```

## srun

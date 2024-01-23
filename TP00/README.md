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

## Interactive sessions : srun

Running an interactive session can be done using the srun command and specifying that the terminal
will be running an interactive shell session.

WARNING: The .bashrc is not loaded, you must do it yourself (. .bashrc).

Interactive session on one node:
```
srun --pty /bin/bash -i
```

Interactive session on one node named *super_name* with 16 tasks and merged standard and error outputs
from in one file named *nameofjob_jobid.jobtask.outerr* :
```
srun -i -J super_name -N 1 -n 16 -o %x_%J.outerr --pty /bin/bash
```
## Redirection

In general, a linux command has 2 kind of outputs:
 * standard output, numbered 1
 * error output, numbered 2
Note that there can be more than 2 outputs but it is very rare (and confusing).

These outputs can be redirected to files or piped (?) using the | symbol to chain the output
to another linux command:

Redirection of standard output:
```
ls > file_list
```
identical to :
```
ls 1> file_list
```

Redirection of standard and error outputs in two different files:
```
ls 1> file_list 2> error_message
```

Redirection of standard and error outputs merged in one file:
```
ls 2>&1> file_list_and_error_message_merged
```

Starndard output of one command given to another. Here, the list of files in the current
directory is sent to the grep command, which outputs the number of files containing "toto" in
their name (hopefully 0).
```
ls | grep -c toto
```

## Return code
A linux command **always** sends back a return code (or exit status).

A value of 0 indicates that everything went fine.
Another value (max 99, I believe) indicates that something went wrong.
This value can be tested right after execution or tested later:

```
ls && echo "Everything went fine"
```

```
ls filenam_that_does_not_exist || echo "Something went wrong"
```

```
ls
if [ $? -eq 0 ]
then
  echo "Everything went fine"
else
  echo "Something went wrong"
fi
```

IMPORTANT: the return code of a script is the return code of the last command
executed in the script.

```
cat << EOF > script.sh
ls
if [ $? -eq 0 ]
then
  echo "Everything went fine"
else
  echo "Something went wrong"
fi
EOF
sh ./script.sh
echo $?
0
#Because the last command executed returned 0: it is the echo command :)
```


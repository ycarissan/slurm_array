# Intro

Up to date state of the queue :
```
watch squeue -u yannick
```

1. Open 2 terminals
2. Create a terminal in which you see the queue state refreshed every 2 seconds

# Dependencies

## Prerequisite
A script will return the rc of the last command executed or of the value
given to the exit command:

```
cat << EOF > script.sh
ls
exit 12
EOF
sh script.sh
if [ $? -eq 0 ]
then
   echo "Return code OK"
elif [ $? -eq 1 ]; then
   echo "Something went wrong"
elif [ $? -eq 12 ]; then
   echo "Something went wrong twelve times"
else
   echo "Bad things happend!"
fi
```

!!!

```
cat << EOF > script.sh
ls
exit 12
EOF
sh script.sh
rc=$?
if [ $rc -eq 0 ]
then
   echo "Return code OK"
elif [ $rc -eq 1 ]; then
   echo "Something went wrong"
elif [ $rc -eq 12 ]; then
   echo "Something went wrong twelve times"
else
   echo "Bad things happend!"
fi
```

## Wait for a job to finish (independent of the rc)

### Wrong solution
```
cat << EOF > submit_01.job
sleep 10
echo 1 > file_01.out
EOF
```

```
cat << EOF > submit_02.job
cp file_01.out file_02.out
EOF
```

```
sbatch submit_01.job
sbatch submit_02.job
```

After both jobs have finished, no file file_02.out.

### Almost not wrong solution
```
rm file_01.out
rm file_02.out
jid01=$(sbatch submit_01.job)
jid02=$(sbatch --dependency=after:${jid01} submit_02.job)
```

After both jobs have finished, no file file_02.out.

### Good solution
```
rm file_01.out
rm file_02.out
jid01=$(sbatch submit_01.job)
jid02=$(sbatch --dependency=afterany:${jid01} submit_02.job)
```

After both jobs have finished, file_02.out exits!

## Wait for a job to finish and check rc
```
cat << EOF > submit_03.job
[ -f file_01.out ]
EOF
```

```
cat << EOF > submit_04.job
echo "file_01.out exists" > resultat.txt
EOF
```

```
cat << EOF > submit_05.job
echo "file_01.out n'existe pas" > resultat.txt
EOF
```

rm file_01.out
jid01=$(sbatch submit_01.job)
jid03=$(sbatch --dependency=afterok:${jid01} submit_03.job)
jid04=$(sbatch --dependency=afternotok:${jid01} submit_04.job)

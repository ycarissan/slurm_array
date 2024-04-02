### 1. Exemple Simple
Supposons que vous ayez un script appelé `my_script.sh` qui prend un argument numérique comme entrée. Vous souhaitez exécuter ce script pour différentes valeurs de cet argument de manière parallèle en utilisant un job array.

Créez un fichier de script appelé `job_array.sh`:

```bash
#!/bin/bash
#SBATCH --job-name=my_array_job
#SBATCH --output=output_%A_%a.txt
#SBATCH --array=1-5

# Chemin vers le script à exécuter
SCRIPT="my_script.sh"

# Exécuter le script avec l'argument correspondant au numéro de tâche
$SCRIPT $SLURM_ARRAY_TASK_ID
```

Dans ce script :
- `--job-name=my_array_job` définit le nom du travail.
- `--output=output_%A_%a.txt` spécifie le modèle de nom de fichier de sortie. `%A` est remplacé par l'ID du travail et `%a` par le numéro de tâche du job array.
- `--array=1-5` définit la plage des tâches de job array.

Assurez-vous que votre script `my_script.sh` accepte un argument, qui sera le numéro de tâche, par exemple :

```bash
#!/bin/bash

echo "Le double de $1 est $(( $1 * 2 ))"
```

### 2. Exemples Complexes

#### a. Utilisation d'intervalles dans la liste
Supposons que vous souhaitiez exécuter des tâches pour des valeurs comprises entre 10 et 100, par pas de 10.

Modifiez le script `job_array.sh` comme suit :

```bash
#!/bin/bash
#SBATCH --job-name=my_array_job
#SBATCH --output=output_%A_%a.txt
#SBATCH --array=10-100:10

# Chemin vers le script à exécuter
SCRIPT="my_script.sh"

# Exécuter le script avec l'argument correspondant au numéro de tâche
$SCRIPT $SLURM_ARRAY_TASK_ID
```

#### b. Utilisation d'une liste mixte
Supposons que vous ayez une liste d'arguments spécifiques que vous voulez utiliser pour chaque tâche.

Créez un fichier `arguments.txt` contenant les arguments pour chaque tâche, par exemple :

```plaintext
etoh
meoh
water
```

Modifiez le script `job_array.sh` pour lire les arguments à partir du fichier :

```bash
#!/bin/bash
#SBATCH --job-name=my_array_job
#SBATCH --output=output_%A_%a.txt
#SBATCH --array=1-3

# Chemin vers le script à exécuter
SCRIPT="my_script.sh"

# Lire l'argument correspondant au numéro de tâche
ARGUMENT=$(sed -n "${SLURM_ARRAY_TASK_ID}p" arguments.txt)

...
initialisation de gaussian
...

# Exécuter le script avec l'argument
g16 $ARGUMENT.com
```

Dans cet exemple, `sed -n "${SLURM_ARRAY_TASK_ID}p" arguments.txt` extrait l'argument correspondant au numéro de tâche du fichier `arguments.txt`.

En utilisant ces exemples, vous pouvez exploiter pleinement la flexibilité des job arrays avec Slurm pour vos tâches parallèles.

### 3. Exemple Réel
Sur le mesocentre, on peut lancer le script submit.job de collection_5:

```bash
sbatch -a 1-22 submit.job
```

Le traitement du fichier cml est assuré par le script submit.job pour les 22 molecules.
Avantage: si on veut modifier un niveau de calcul, il suffit de le faire une seule fois.
Par ailleurs, on peut ne lancer le traitement que pour les molécules 3 à 5, 11 à 14 et 17 ainsi:

```bash
sbatch -a 3-5,11-14,17 submit.job
```

:heart:

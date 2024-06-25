---
lab:
  title: Exécuter un script d’entraînement en tant que travail de commande dans Azure Machine Learning
---

# Exécuter un script d’entraînement en tant que travail de commande dans Azure Machine Learning

Un notebook est idéal pour l’expérimentation et le développement. Une fois que vous avez développé un modèle Machine Learning et qu’il est prêt pour la production, vous allez vouloir l’entraîner avec un script. Vous pouvez exécuter un script en tant que travail de commande.

Dans cet exercice, vous allez tester un script et l’exécuter en tant que travail de commande.

## Avant de commencer

Vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free?azure-portal=true) dans lequel vous avez un accès administratif.

## Provisionner un espace de travail Azure Machine Learning

Un *espace de travail* Azure Machine Learning offre un emplacement central pour gérer toutes les ressources et tous les éléments dont vous avez besoin pour entraîner et gérer vos modèles. Vous pouvez interagir avec l’espace de travail Azure Machine Learning via le studio, le SDK Python et Azure CLI.

Vous allez utiliser Azure CLI pour provisionner l’espace de travail et le calcul nécessaire, puis utiliser le SDK Python pour exécuter un travail de commande.

### Créer l’espace de travail et les ressources de calcul

Pour créer l’espace de travail Azure Machine Learning, une instance de calcul et un cluster de calcul, vous allez utiliser Azure CLI. Toutes les commandes nécessaires sont regroupées dans un script Shell que vous pouvez exécuter.

1. Dans un navigateur, ouvrez le portail Azure sur `https://portal.azure.com/` en vous connectant avec votre compte Microsoft.
1. Sélectionnez le bouton \[>_] (*Cloud Shell*) en haut de la page, à droite de la zone de recherche. Cela a pour effet d’ouvrir un volet de Cloud Shell au bas du portail.
1. Sélectionnez **Bash** si vous y êtes invité. Lorsque vous ouvrez Cloud Shell pour la première fois, vous êtes invité à choisir le type d’interpréteur de commandes que vous souhaitez utiliser (*Bash* ou *PowerShell*).
1. Vérifiez que le bon abonnement est spécifié et sélectionnez **Créer un stockage** si vous êtes invité à créer un stockage pour votre Cloud Shell. Attendez que le compte de stockage soit créé.
1. Dans le terminal, entrez les commandes suivantes pour cloner ce dépôt :

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Utilisez `SHIFT + INSERT` pour coller votre code copié dans Cloud Shell.

1. Une fois que le dépôt a été cloné, entrez les commandes suivantes pour accéder au dossier de ce labo et exécutez le script **setup.sh** qu’il contient :
    
    ```azurecli
    cd azure-ml-labs/Labs/08
    ./setup.sh
    ```

    > Ignorez les messages (d’erreur) indiquant que les extensions n’ont pas été installées.

1. Attendez que le script se termine, ce qui prend généralement entre 5 et 10 minutes.

## Cloner les supports de labo

Une fois que vous avez créé l’espace de travail et les ressources de calcul nécessaires, vous pouvez ouvrir le studio Azure Machine Learning et cloner les supports de labo dans l’espace de travail.

1. Dans le portail Azure, accédez à l’espace de travail Azure Machine Learning appelé **mlw-dp100-...**.
1. Sélectionnez l’espace de travail Azure Machine Learning et, dans sa page **Vue d’ensemble**, sélectionnez **Lancer le studio**. Un autre onglet s’ouvre dans votre navigateur pour ouvrir le studio Azure Machine Learning.
1. Fermez les fenêtres contextuelles qui s’affichent dans le studio.
1. Dans le studio Azure Machine Learning, accédez à la page **Calcul** et vérifiez que l’instance de calcul et le cluster que vous avez créés dans la section précédente existent. L’instance de calcul doit être en cours d’exécution, le cluster doit être inactif et avoir 0 nœud en cours d’exécution.
1. Sous l’onglet **Instances de calcul**, recherchez votre instance de calcul et sélectionnez l’application **Terminal**.
1. Dans le terminal, installez le SDK Python sur l’instance de calcul en exécutant les commandes suivantes :

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorez les éventuels messages (d’erreur) indiquant que les packages n’ont pas pu être trouvés et désinstallés.

1. Exécutez la commande suivante pour cloner un dépôt Git contenant des notebooks, des données et autres fichiers dans votre espace de travail :

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Une fois la commande exécutée, dans le volet **Fichiers**, cliquez sur **&#8635;** pour actualiser l’affichage et vérifier qu’un nouveau dossier **Users/*votre-nom-utilisateur*/azure-ml-labs** a été créé.

## Convertir un notebook en script

L’utilisation d’un notebook attaché à une instance de calcul est idéale pour l’expérimentation et le développement, car elle vous permet d’exécuter immédiatement le code que vous avez écrit et de passer en revue sa sortie. Pour passer du développement à la production, vous devez utiliser des scripts. Dans un premier temps, vous pouvez utiliser le studio Azure Machine Learning pour convertir votre notebook en script.

1. Ouvrez le notebook **Labs/08/src/Train classification model.ipynb**.

    > Sélectionnez **S’authentifier** et suivez les étapes nécessaires si une notification apparaît et vous invite à vous authentifier.

1. Vérifiez que le notebook utilise le noyau **Python 3.8 - AzureML**.
1. Exécutez toutes les cellules pour explorer le code et entraîner un modèle.
1. Sélectionnez l’icône &#9776; en haut du notebook pour afficher le **menu du notebook**.
1. Développez **Exporter en tant que**, puis sélectionnez **Python (.py)** pour convertir le notebook en script Python.
1. Nommez le nouveau fichier `train-classification-model`.
1. Une fois le fichier créé, le script doit s’ouvrir automatiquement. Explorez le fichier et notez qu’il contient le même code que le notebook.
1. Sélectionnez l’icône &#9655;&#9655; en haut du notebook pour **enregistrer et exécuter le script dans le terminal**.
1. Le script est lancé par la commande **python train-classification-model.py** et la sortie devrait apparaître sous la commande.

## Tester un script avec le terminal

Après avoir converti un notebook en script, vous souhaiterez peut-être l’affiner davantage. Une bonne pratique lors de l’utilisation de scripts consiste à utiliser des fonctions. Lorsque votre script se compose de fonctions, il est plus facile de procéder à un test unitaire de votre code. Lorsque vous utilisez des fonctions, votre script se compose de blocs de code, chaque bloc effectuant une tâche spécifique.

1. Ouvrez le script **Labs/08/src/train-model-parameters.py** et explorez son contenu.
    Notez qu’il existe une fonction principale qui comprend quatre autres fonctions :

    - Lire les données
    - Fractionner les données
    - Entraîner le modèle
    - Évaluer le modèle

    Après la fonction principale, chaque fonction est définie. Notez comment chaque fonction définit l’entrée et la sortie attendues.

1. Sélectionnez l’icône &#9655;&#9655; en haut du notebook pour **enregistrer et exécuter le script dans le terminal**. Vous devriez obtenir une erreur après **Lecture des données...** vous indiquant que l’obtention des données est impossible parce que le chemin du fichier n’est pas valide.

    Les scripts vous permettent de paramétrer votre code pour modifier facilement les paramètres ou données d’entrée. Dans le cas présent, le script attend un paramètre d’entrée pour le chemin des données que nous n’avons pas fourni. Vous trouverez les paramètres définis et attendus à la fin du script dans la fonction **parse_args()** .

    Deux paramètres d’entrée sont définis :
    - **--training_data** qui attend une chaîne.
    - **--reg_rate** qui attend un nombre, mais a une valeur par défaut de 0,01.

    Pour exécuter correctement le script, vous devez spécifier la valeur des paramètres de données d’entraînement. Pour ce faire, nous allons référencer le fichier **diabetes.csv** qui est stocké dans le même dossier que le script d’entraînement.

1. Dans le terminal, exécutez les commandes suivantes :

    ```
    cd azure-ml-labs/Labs/08/src/
    python train-model-parameters.py --training_data diabetes.csv
    ```

Le script devrait s’exécuter correctement et la sortie devrait par conséquent afficher la justesse et l’AUC du modèle entraîné.

Le test du script dans le terminal est idéal pour vérifier si le script fonctionne comme prévu. En cas de problème avec le code, vous recevrez une erreur dans le terminal.

**Si vous le souhaitez**, modifiez le code pour forcer une erreur et réexécutez la commande dans le terminal pour exécuter le script. Par exemple, supprimez la ligne **import pandas as pd**, enregistrez et exécutez le script avec le paramètre d’entrée pour vérifier le message d’erreur.

## Exécuter un script en tant que travail de commande

Si vous savez que votre script fonctionne, vous pouvez l’exécuter en tant que travail de commande. En exécutant votre script en tant que travail de commande, vous pouvez suivre toutes les entrées et sorties du script.

1. Ouvrez le notebook **Labs/08/Run script as command job.ipynb**.
1. Exécutez toutes les cellules dans le notebook.
1. Dans le studio Azure Machine Learning, accédez à la page **Travaux**.
1. Accédez au travail **diabetes-train-script** pour explorer la vue d’ensemble du travail de commande que vous avez exécuté.
1. Accédez à l’onglet **Code**. Tout le contenu du dossier **src**, qui était la valeur du paramètre **code** du travail de commande, est copié ici. Vous pouvez passer en revue le script d’entraînement qui a été exécuté par le travail de commande.
1. Accédez à l’onglet **Sorties + journaux**.
1. Ouvrez le fichier **std_log.txt** et explorez son contenu. Le contenu de ce fichier est la sortie de la commande. Rappelez-vous que la même sortie s’affichait dans le terminal lorsque vous y avez testé le script. Si le travail échoue en raison d’un problème avec le script, le message d’erreur s’affiche ici.

**Si vous le souhaitez**, modifiez le code pour forcer une erreur et utilisez le notebook pour relancer le travail de commande. Par exemple, supprimez la ligne **import pandas as pd** du script et enregistrez le script. Vous pouvez également modifier la configuration du travail de commande pour explorer les messages d’erreur en cas de problème avec la configuration du travail elle-même au lieu du script.

## Supprimer les ressources Azure

Une fois que vous avez fini d’explorer Azure Machine Learning, vous devriez supprimer les ressources que vous avez créées afin d’éviter des coûts Azure superflus.

1. Fermez l’onglet du studio Azure Machine Learning et revenez au portail Azure.
1. Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources **rg-dp100-...**.
1. Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.

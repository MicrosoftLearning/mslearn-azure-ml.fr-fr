---
lab:
  title: Rendre les données disponibles dans Azure Machine Learning
---

# Rendre les données disponibles dans Azure Machine Learning

S’il est assez courant d’utiliser des données sur leur système de fichiers local, dans un environnement d’entreprise, il peut être plus efficace de stocker les données dans un emplacement central auquel plusieurs scientifiques des données et ingénieurs de Machine Learning peuvent accéder.

Dans cet exercice, vous allez explorer les *magasins de données* et *ressources de données*, qui sont les principaux objets utilisés pour abstraire l’accès aux données dans Azure Machine Learning.

## Avant de commencer

Vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free?azure-portal=true) dans lequel vous avez un accès administratif.

## Provisionner un espace de travail Azure Machine Learning

Un *espace de travail* Azure Machine Learning offre un emplacement central pour gérer toutes les ressources et tous les éléments dont vous avez besoin pour entraîner et gérer vos modèles. Vous pouvez interagir avec l’espace de travail Azure Machine Learning via le studio, le SDK Python et Azure CLI. 

Vous allez utiliser un script Shell qui utilise Azure CLI pour provisionner l’espace de travail et les ressources nécessaires. Ensuite, vous allez utiliser le Concepteur dans le studio Azure Machine Learning pour entraîner et comparer des modèles.

### Créer l’espace de travail et les ressources de calcul

Pour créer l’espace de travail Azure Machine Learning et les ressources de calcul, vous allez utiliser Azure CLI. Toutes les commandes nécessaires sont regroupées dans un script Shell que vous pouvez exécuter.
1. Dans un navigateur, ouvrez le portail Azure sur `https://portal.azure.com/` en vous connectant avec votre compte Microsoft.
1. Sélectionnez le bouton \[>_] (*Cloud Shell*) en haut de la page, à droite de la zone de recherche. Cela a pour effet d’ouvrir un volet de Cloud Shell au bas du portail.
1. Sélectionnez **Bash** si vous y êtes invité. Quand vous ouvrez Cloud Shell pour la première fois, vous êtes invité à choisir le type d’interpréteur de commandes que vous souhaitez utiliser (*Bash* ou *PowerShell*). 
1. Vérifiez que le bon abonnement est spécifié et sélectionnez **Créer un stockage** si vous êtes invité à créer un stockage pour votre Cloud Shell. Attendez que le stockage soit créé.
1. Entrez les commandes suivantes dans le terminal pour cloner ce dépôt :

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Utilisez `SHIFT + INSERT` pour coller votre code copié dans Cloud Shell. 

1. Entrez les commandes suivantes une fois le dépôt cloné pour accéder au dossier de ce labo et exécutez le script **setup.sh** qu’il contient :

    ```azurecli
    cd azure-ml-labs/Labs/03
    ./setup.sh
    ```

    > Ignorez les messages (d’erreur) indiquant que les extensions n’ont pas été installées. 

1. Attendez que le script se termine, ce qui prend généralement entre 5 et 10 minutes. 

## Explorer les magasins de données par défaut

Quand vous créez un espace de travail Azure Machine Learning, un compte de stockage est automatiquement créé et connecté à votre espace de travail. Vous allez découvrir comment le compte de stockage est connecté.

1. Dans le portail Azure, accédez au nouveau groupe de ressources nommé **rg-dp100-labs**.
1. Sélectionnez le compte de stockage dans le groupe de ressources. Le nom commence souvent par le nom que vous avez fourni pour l’espace de travail (sans traits d’union).
1. Passez en revue la page **Vue d’ensemble** du compte de stockage. Notez que le compte de stockage dispose de plusieurs options pour le **stockage de données**, comme indiqué dans le volet Vue d’ensemble et dans le menu de gauche.
1. Sélectionnez **Conteneurs** pour explorer la partie Stockage d’objets blob du compte de stockage. 
1. Notez le conteneur **azureml-blobstore-...** . Le magasin de données par défaut des ressources de données utilise ce conteneur pour stocker les données. 
1. Avec le bouton &#43; **Conteneur** en haut de l’écran, créez un conteneur et nommez-le `training-data`. 
1. Sélectionnez **Partages de fichiers** dans le menu de gauche pour explorer la partie Partage de fichiers du compte de stockage.
1. Notez le partage de fichiers **code-...** . Tous les notebooks de l’espace de travail y sont stockés. Après avoir cloné les documents du labo, vous trouverez les fichiers dans ce partage de fichiers, dans le dossier **code-.../Users/*votre-nom-utilisateur*/azure-ml-labs**.

## Copier la clé d’accès

Pour créer un magasin de données dans l’espace de travail Azure Machine Learning, vous devez fournir des informations d’identification. Un moyen simple de fournir à l’espace de travail un accès à un stockage d’objets blob consiste à utiliser la clé de compte.

1. Dans le compte de stockage, sélectionnez l’onglet **Clés d’accès** dans le menu de gauche.
1. Notez que deux clés sont fournies : key1 et key2. Chaque clé a la même fonctionnalité. 
1. Sélectionnez **Afficher** pour le champ **Clé** sous **key1**.
1. Copiez la valeur du champ **Clé** dans un bloc-notes. Vous devrez coller cette valeur dans le notebook. 
1. Copiez le nom de votre compte de stockage en haut de la page. Le nom doit commencer par **mlwdp100storage...** Vous devrez également coller cette valeur dans le notebook. 

> **Remarque** : Copiez la clé et le nom du compte dans un bloc-notes pour éviter la mise en majuscule automatique (ce qui se produit dans Word). La clé respecte la casse.

## Cloner les supports de labo

Pour créer un magasin de données et des ressources de données avec le SDK Python, vous devez cloner les documents du labo dans l’espace de travail.

1. Dans le portail Azure, accédez à l’espace de travail Azure Machine Learning nommé **mlw-dp100-labs**.
1. Sélectionnez l’espace de travail Azure Machine Learning et, dans sa page **Vue d’ensemble**, sélectionnez **Lancer le studio**. Un autre onglet s’ouvre dans votre navigateur pour ouvrir le studio Azure Machine Learning.
1. Fermez les fenêtres contextuelles qui s’affichent dans le studio.
1. Dans le studio Azure Machine Learning, accédez à la page **Calcul** et vérifiez que l’instance de calcul et le cluster que vous avez créés dans la section précédente existent. L’instance de calcul doit être en cours d’exécution, le cluster doit être inactif et avoir 0 nœud en cours d’exécution.
1. Sous l’onglet **Instances de calcul**, recherchez votre instance de calcul et sélectionnez l’application **Terminal**.
1. Dans le terminal, installez le SDK Python sur l’instance de calcul en exécutant les commandes suivantes :
    
    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    pip install mltable
    ```

    > Ignorez les messages (d’erreur) indiquant que les packages n’ont pas été installés.

1. Exécutez la commande suivante pour cloner un dépôt Git contenant des notebooks, des données et autres fichiers dans votre espace de travail :
    
    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```
 
1. Une fois la commande exécutée, dans le volet **Fichiers**, cliquez sur **&#8635;** pour actualiser l’affichage et vérifier qu’un dossier **Users/*votre-nom-utilisateur*/azure-ml-labs** a été créé. 

**Éventuellement**, dans un autre onglet du navigateur, revenez au [portail Azure](https://portal.azure.com?azure-portal=true). Dans le compte de stockage, réexplorez le partage de fichiers **code-...**  ; vous trouverez les supports du labo clonés dans le dossier **azure-ml-labs** qui vient d’être créé.

## Créer un magasin de données et des ressources de données

Le code permettant de créer un magasin de données et des ressources de données avec le SDK Python est fourni dans un notebook.

1. Ouvrez le notebook **Labs/03/Work with data.ipynb**.

    > Sélectionnez **S’authentifier** et suivez les étapes nécessaires si une notification apparaît et vous invite à vous authentifier. 

1. Vérifiez que le notebook utilise le noyau **Python 3.8 - AzureML**. 
1. Exécutez toutes les cellules dans le notebook.

## Facultatif : Explorer les ressources de données

**Si vous le souhaitez**, vous pouvez explorer la façon dont les ressources de données sont stockées dans le compte de stockage associé.

1. Accédez à l’onglet **Données** dans le studio Azure Machine Learning pour explorer les ressources de données. 
1. Sélectionnez le nom de ressource de données **diabetes-local** pour explorer les détails de cette ressource. 

    Sous **Sources de données** pour la ressource de données **diabetes-local**, vous trouverez l’emplacement dans lequel le fichier a été chargé. Le chemin commençant par **LocalUpload/...** indique le chemin dans le conteneur de compte de stockage **azureml-blobstore-...** . Vous pouvez vérifier que le fichier existe en accédant à ce chemin dans le portail Azure.

## Supprimer les ressources Azure

Une fois que vous avez fini d’explorer Azure Machine Learning, vous devriez supprimer les ressources que vous avez créées afin d’éviter des coûts Azure superflus.

1. Fermez l’onglet du studio Azure Machine Learning et revenez au portail Azure.
1. Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources **rg-dp100-labs**.
1. Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**. 
1. Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.

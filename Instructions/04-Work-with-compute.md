---
lab:
  title: Utiliser des ressources de calcul dans Azure Machine Learning
---

# Utiliser des ressources de calcul dans Azure Machine Learning

L’un des principaux avantages du cloud est la possibilité d’utiliser des ressources de calcul évolutives à la demande pour le traitement économique de données volumineuses.

Dans cet exercice, vous allez apprendre à utiliser le calcul cloud dans Azure Machine Learning pour exécuter des expériences et du code de production à grande échelle.

## Avant de commencer

Vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free?azure-portal=true) dans lequel vous avez un accès administratif.

## Provisionner un espace de travail Azure Machine Learning

Un *espace de travail* Azure Machine Learning offre un emplacement central pour gérer toutes les ressources et tous les éléments dont vous avez besoin pour entraîner et gérer vos modèles. Vous pouvez interagir avec l’espace de travail Azure Machine Learning via le studio, le SDK Python et Azure CLI.

Pour créer l’espace de travail Azure Machine Learning, vous allez utiliser Azure CLI. Toutes les commandes nécessaires sont regroupées dans un script Shell que vous pouvez exécuter.

1. Dans un navigateur, ouvrez le portail Azure sur `https://portal.azure.com/` en vous connectant avec votre compte Microsoft.
1. Sélectionnez le bouton \[>_] (*Cloud Shell*) en haut de la page, à droite de la zone de recherche. Cela a pour effet d’ouvrir un volet de Cloud Shell au bas du portail.
1. Sélectionnez **Bash** si vous y êtes invité. Quand vous ouvrez Cloud Shell pour la première fois, vous êtes invité à choisir le type d’interpréteur de commandes que vous souhaitez utiliser (*Bash* ou *PowerShell*).
1. Vérifiez que le bon abonnement est spécifié et sélectionnez **Créer un stockage** si vous êtes invité à créer un stockage pour votre Cloud Shell. Attendez que le stockage soit créé.
1. Pour éviter tout conflit avec les versions précédentes, supprimez toutes les extensions CLI ML (les versions 1 et 2) en exécutant cette commande dans le terminal :

    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > Utilisez `SHIFT + INSERT` pour coller votre code copié dans Cloud Shell.

    > Ignorez les messages (d’erreur) indiquant que les extensions n’ont pas été installées.

1. Installez l’extension Azure Machine Learning (v2) à l’aide de la commande suivante :
    
    ```azurecli
    az extension add -n ml -y
    ```

1. Créez un groupe de ressources. Choisissez un emplacement proche de vous.

    ```azurecli
    az group create --name "rg-dp100-labs" --location "eastus"
    ```

1. Créez un espace de travail :

    ```azurecli
    az ml workspace create --name "mlw-dp100-labs" -g "rg-dp100-labs"
    ```

1. Attendez que la commande se termine, ce qui prend généralement entre 5 et 10 minutes.

## Créer le script de configuration d’une instance de calcul

Pour exécuter des notebooks dans l’espace de travail Azure Machine Learning, vous devez avoir une instance de calcul. Vous pouvez utiliser un script d’installation pour configurer l’instance de calcul au moment de la création.

1. Dans le portail Azure, accédez à l’espace de travail Azure Machine Learning nommé **mlw-dp100-labs**.
1. Sélectionnez l’espace de travail Azure Machine Learning et, dans sa page **Vue d’ensemble**, sélectionnez **Lancer le studio**. Un autre onglet s’ouvre dans votre navigateur pour ouvrir le studio Azure Machine Learning.
1. Fermez les fenêtres contextuelles qui s’affichent dans le studio.
1. Dans le studio Azure Machine Learning, accédez à la page **Notebooks**.
1. Dans le volet **Fichiers**, sélectionnez l’icône &#10753; pour **Ajouter des fichiers**.
1. Sélectionnez **Create new file**.
1. Vérifiez que l’emplacement du fichier est **Users/* votre-nom-utilisateur***.
1. Remplacez le type de fichier par **Bash (*.sh)** .
1. Remplacez le nom de fichier par `compute-setup.sh`.
1. Ouvrez le fichier **compute-setup.sh** nouvellement créé et collez-y les éléments suivants :

    ```azurecli
    #!/bin/bash

    # clone repository
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Enregistrez le fichier **compute-setup.sh**.

## Créer l’instance de calcul

Pour créer l’instance de calcul, vous pouvez utiliser le studio, le SDK Python ou Azure CLI. Vous allez utiliser le studio pour créer l’instance de calcul avec le script de configuration que vous venez de créer.

1. Accédez à la page **Calcul** à l’aide du menu de gauche.
1. Sous l’onglet **Instances de calcul**, sélectionnez **Nouvelle**.
1. Configurez (ne créez pas encore) l’instance de calcul avec les paramètres suivants : 
    - **Nom de la capacité de calcul** : *entrez un nom unique*
    - **Type de machine virtuelle** : *Processeur*
    - **Taille de machine virtuelle** : *Standard_DS11_v2*
1. Sélectionnez **Suivant : Paramètres avancés**.
1. Sélectionnez **Ajouter une planification** et configurez la planification pour **arrêter** l’instance de calcul tous les jours à **18:00** ou **6:00 PM**.
1. Passez le bouton bascule sur **Provisionner avec le script de configuration**.
1. Sélectionnez le script **compute-setup.sh** que vous avez créé précédemment.
1. Passez en revue les autres paramètres avancés, mais ne les sélectionnez **pas** :
    - **Activer l’accès SSH** : *Vous pouvez utiliser cette option pour avoir un accès direct à la machine virtuelle en utilisant un client SSH.*
    - **Activer un réseau virtuel** : *Vous utiliserez généralement cette option dans un environnement d’entreprise pour améliorer la sécurité réseau.*
    - **Attribuer à un autre utilisateur** : *Vous pouvez utiliser cette option pour attribuer une instance de calcul à un autre scientifique des données.*
1. **Créez** l’instance de calcul et attendez qu’elle démarre et que son état passe à **En cours d’exécution**.
1. Lorsque l’instance de calcul est en cours d’exécution, accédez à la page **Notebooks**. Dans le volet **Fichiers**, cliquez sur **&#8635;** pour actualiser l’affichage et vérifier qu’un nouveau dossier **Users/*votre-nom-utilisateur*/dp100-azure-ml-labs** a été créé.

## Configurer l’instance de calcul

Une fois que vous avez créé l’instance de calcul, vous pouvez y exécuter des notebooks. Vous devrez peut-être installer certains packages pour exécuter le code que vous voulez. Vous pouvez inclure des packages dans le script de configuration ou les installer en utilisant le terminal.

1. Sous l’onglet **Instances de calcul**, recherchez votre instance de calcul et sélectionnez l’application **Terminal**.
1. Dans le terminal, installez le SDK Python sur l’instance de calcul en exécutant les commandes suivantes :

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorez les messages (d’erreur) indiquant que les packages n’ont pas été installés.

1. Lorsque les packages sont installés, vous pouvez fermer l’onglet pour arrêter le terminal.

## Créer un cluster de calcul

Les notebooks sont parfaits pour le développement ou le travail répétitif pendant l’expérimentation. Lors de l’expérimentation, vous voudrez exécuter des notebooks sur une instance de calcul pour tester et réviser rapidement le code. Une fois en production, vous voudrez exécuter des scripts sur un cluster de calcul. Vous allez créer un cluster de calcul avec le SDK Python, puis l’utiliser pour exécuter un script en tant que travail.

1. Ouvrez le notebook **Labs/04/Work with compute.ipynb**.

    > Sélectionnez **S’authentifier** et suivez les étapes nécessaires si une notification apparaît et vous invite à vous authentifier.

1. Vérifiez que le notebook utilise le noyau **Python 3.8 - AzureML**.
1. Exécutez toutes les cellules dans le notebook.

## Supprimer les ressources Azure

Une fois que vous avez fini d’explorer Azure Machine Learning, vous devriez supprimer les ressources que vous avez créées afin d’éviter des coûts Azure superflus.

1. Fermez l’onglet du studio Azure Machine Learning et revenez au portail Azure.
1. Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources **rg-dp100-labs**.
1. Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.

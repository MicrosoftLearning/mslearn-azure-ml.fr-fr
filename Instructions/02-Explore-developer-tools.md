---
lab:
  title: Explorer les outils de développement pour l’interaction de l’espace de travail
---

# Explorer les outils de développement pour l’interaction de l’espace de travail

Vous pouvez utiliser différents outils pour interagir avec l’espace de travail Azure Machine Learning. En fonction de la tâche à effectuer et de vos préférences en matière d’outil de développement, vous pouvez choisir quel outil utiliser quand. Ce labo est conçu comme une introduction aux outils de développement couramment utilisés pour l’interaction avec l’espace de travail. Si vous souhaitez apprendre à utiliser un outil spécifique plus en profondeur, vous pouvez explorer d’autres labos.

## Avant de commencer

Vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free?azure-portal=true) dans lequel vous avez un accès administratif.

Les outils de développement couramment utilisés pour interagir avec l’espace de travail Azure Machine Learning sont les suivants :

- **Azure CLI** avec l’extension Azure Machine Learning : cette approche en ligne de commande est idéale pour l’automatisation de l’infrastructure.
- **Azure Machine Learning studio** : utilisez l’interface utilisateur conviviale pour explorer l’espace de travail et toutes ses fonctionnalités.
- **SDK) Python** pour Azure Machine Learning : permet d’envoyer des travaux et de gérer des modèles à partir d’un notebook Jupyter, idéal pour les scientifiques des données.

Vous allez explorer chacun de ces outils pour les tâches courantes qu’il permet d’effectuer.

## Provisionner l’infrastructure avec Azure CLI

Pour qu’un scientifique des données entraîne un modèle Machine Learning avec Azure Machine Learning, vous devez configurer l’infrastructure nécessaire. Vous pouvez utiliser Azure CLI avec l’extension Azure Machine Learning pour créer un espace de travail Azure Machine Learning et des ressources telles qu’une instance de calcul.

Pour commencer, ouvrez Azure Cloud Shell, installez l’extension Azure Machine Learning et clonez le dépôt Git.

1. Dans un navigateur, ouvrez le portail Azure sur `https://portal.azure.com/` en vous connectant avec votre compte Microsoft.
1. Sélectionnez le bouton \[>_] (*Cloud Shell*) en haut de la page, à droite de la zone de recherche. Cela a pour effet d’ouvrir un volet de Cloud Shell au bas du portail.
1. Sélectionnez **Bash** si vous y êtes invité. Lorsque vous ouvrez Cloud Shell pour la première fois, vous êtes invité à choisir le type d’interpréteur de commandes que vous souhaitez utiliser (*Bash* ou *PowerShell*).
1. Vérifiez que le bon abonnement est spécifié et que l’option **Aucun compte de stockage requis** est sélectionnée. Sélectionnez **Appliquer**.
1. Supprimez toutes les extensions CLI ML (versions 1 et 2) pour éviter tout conflit avec les versions précédentes en exécutant cette commande :
    
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

1. Créer un espace de travail :
    
    ```azurecli
    az ml workspace create --name "mlw-dp100-labs" -g "rg-dp100-labs"
    ```

1. Attendez que l’espace de travail et les ressources associées soient créés, ce qui prend généralement environ 5 minutes.

## Créer une instance de calcul avec Azure CLI

Le **calcul** est une autre partie importante de l’infrastructure nécessaire à l’entraînement d’un modèle Machine Learning. Même si vous pouvez entraîner des modèles localement, l’utilisation du calcul cloud est plus scalable et économique.

Quand les scientifiques des données développent un modèle Machine Learning dans l’espace de travail Azure Machine Learning, ils souhaitent utiliser une machine virtuelle sur laquelle ils peuvent exécuter des notebooks Jupyter. Pour le développement, une **instance de calcul** est idéale.

Après avoir créé un espace de travail Azure Machine Learning, vous pouvez également créer une instance de calcul avec Azure CLI.

Dans cet exercice, vous allez créer une instance de calcul avec les paramètres suivants :

- **Nom de calcul** : *nom de l’instance de calcul. Doit être unique et comporter moins de 24 caractères.*
- **Taille de machine virtuelle** : STANDARD_DS11_V2
- **Type de calcul** (instance ou cluster) : ComputeInstance
- **Nom d’espace de travail Azure Machine Learning** : mlw-dp100-labs
- **Groupe de ressources** : rg-dp100-labs

1. Utilisez la commande suivante pour créer une instance de calcul dans votre espace de travail. Si le nom de l’instance de calcul contient « XXXX », remplacez-le par des nombres aléatoires pour créer un nom unique.

    ```azurecli
    az ml compute create --name "ciXXXX" --size STANDARD_DS11_V2 --type ComputeInstance -w mlw-dp100-labs -g rg-dp100-labs
    ```

    Si vous obtenez un message d’erreur indiquant qu’une instance de calcul portant le nom existe déjà, changez le nom et réessayez la commande.

## Créer un cluster de calcul avec Azure CLI

Même si une instance de calcul est idéale pour le développement, un cluster de calcul est mieux adapté quand nous voulons entraîner des modèles Machine Learning. Ce n’est que quand un travail est soumis pour utiliser le cluster de calcul que ce dernier est redimensionné à plus de 0 nœud et qu’il exécute le travail. Une fois que le cluster de calcul n’est plus nécessaire, il est automatiquement redimensionné à 0 nœud pour réduire les coûts. 

Pour créer un cluster de calcul, vous pouvez utiliser Azure CLI, à l’image de la création d’une instance de calcul.

Vous allez créer un cluster de calcul avec les paramètres suivants :

- **Nom de calcul** : aml-cluster
- **Taille de machine virtuelle** : STANDARD_DS11_V2
- **Type de calcul** : AmlCompute *(crée un cluster de calcul)*
- **Nombre maximal d’instances** : *nombre maximal de nœuds*
- **Nom d’espace de travail Azure Machine Learning** : mlw-dp100-labs
- **Groupe de ressources** : rg-dp100-labs

1. Utilisez la commande suivante pour créer un cluster de calcul dans votre espace de travail.
    
    ```azurecli
    az ml compute create --name "aml-cluster" --size STANDARD_DS11_V2 --max-instances 2 --type AmlCompute -w mlw-dp100-labs -g rg-dp100-labs
    ```

## Configurez votre station de travail avec Azure Machine Learning studio

Même si Azure CLI est idéal pour l’automatisation, vous pouvez passer en revue la sortie des commandes que vous avez exécutées. Vous pouvez utiliser Azure Machine Learning studio pour vérifier si des ressources et des composants ont été créés, ainsi que pour vérifier si les travaux ont été exécutés correctement ou pour déterminer la raison de l’échec d’un travail. 

1. Dans le portail Azure, accédez à l’espace de travail Azure Machine Learning nommé **mlw-dp100-labs**.
1. Sélectionnez l’espace de travail Azure Machine Learning et, dans sa page **Vue d’ensemble**, sélectionnez **Lancer le studio**. Un autre onglet s’ouvre dans votre navigateur pour ouvrir le studio Azure Machine Learning.
1. Fermez les fenêtres contextuelles qui s’affichent dans le studio.
1. Dans le studio Azure Machine Learning, accédez à la page **Calcul** et vérifiez que l’instance de calcul et le cluster que vous avez créés dans la section précédente existent. L’instance de calcul doit être en cours d’exécution, et le cluster doit être dans l'état Réussi et avoir 0 nœud en cours d’exécution.

## Utiliser le kit SDK Python pour effectuer l’entraînement d’un modèle

Maintenant que vous avez vérifié que le calcul nécessaire a été créé, vous pouvez utiliser le SDK Python pour exécuter un script d’entraînement. Vous allez installer et utiliser le SDK Python sur l’instance de calcul et entraîner le modèle Machine Learning sur le cluster de calcul.

1. Dans votre **instance de calcul**, il y a un certain nombre d’options dans le champ **Applications**. Sélectionnez l’application **Terminal** pour lancer le terminal (il se peut que vous deviez cliquer sur l’ellipse pour élargir la sélection).
1. Dans le terminal, installez le SDK Python sur l’instance de calcul en exécutant les commandes suivantes :

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorez les messages (d’erreur) indiquant que les packages n’ont pas été installés.

1. Exécutez la commande suivante pour cloner un dépôt Git contenant des notebooks, des données et autres fichiers dans votre espace de travail :

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Une fois la commande exécutée, dans le volet **Fichiers**, sélectionnez **&#8635;** pour actualiser l’affichage et vérifier qu’un dossier **Users/*votre-nom-utilisateur*/azure-ml-labs** a été créé.
1. Ouvrez le notebook **Labs/02/Run training script.ipynb**.

    > Sélectionnez **S’authentifier** et suivez les étapes nécessaires si une notification apparaît et vous invite à vous authentifier.

1. Vérifiez que l’ordinateur portable utilise le noyau **Python 3.8 - AzureML** dans le coin supérieur droit de l’environnement de l’ordinateur portable. Chaque noyau a sa propre image avec son propre ensemble de packages préinstallé.
1. Exécutez toutes les cellules dans le notebook.

Un travail est créé dans l’espace de travail Azure Machine Learning. Le travail suit les entrées définies dans la configuration de travail, le code utilisé et les sorties telles que les métriques pour évaluer les modèles.

## Passer en revue l’historique de vos travaux dans le studio Azure Machine Learning

Quand vous envoyez un travail à l’espace de travail Azure Machine Learning, vous pouvez vérifier son état dans le studio Azure Machine Learning.

1. Sélectionnez l’URL de travail fournie comme sortie dans le notebook ou accédez à la page **Travaux** dans le studio Azure Machine Learning.
1. Une nouvelle expérience nommée **diabetes-training** est listée. Sélectionnez le dernier travail **diabetes-pythonv2-train**.
1. Passez en revue les **propriétés** du travail. Notez l’**état** du travail :
    - **Mis en file d’attente** : le travail attend que le calcul soit disponible.
    - **Préparation en cours** : le cluster de calcul est en cours de redimensionnement ou l’environnement est en cours d’installation sur la cible de calcul.
    - **En cours d’exécution** : le script d’entraînement est en cours d’exécution.
    - **Finalisation en cours** : le script d’entraînement a été exécuté et le travail est en cours de mise à jour avec toutes les informations finales.
    - **Effectué** : le travail s’est correctement déroulé et est terminé.
    - **Échec** : le travail a échoué et est terminé.
1. Sous **Sorties + journaux**, vous allez trouver la sortie du script dans **user_logs/std_log.txt**. Les sorties des instructions **print** dans le script s’affichent ici. En cas d’erreur due à un problème avec votre script, vous y trouverez également le message d’erreur.
1. Sous **Code**, vous trouverez le dossier que vous avez spécifié dans la configuration du travail. Ce dossier inclut le script d’entraînement et le jeu de données.

## Supprimer les ressources Azure

Une fois que vous avez fini d’explorer Azure Machine Learning, vous devriez supprimer les ressources que vous avez créées afin d’éviter des coûts Azure superflus.

1. Fermez l’onglet du studio Azure Machine Learning et revenez au portail Azure.
1. Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources **rg-dp100-labs**.
1. Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**. 
1. Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.

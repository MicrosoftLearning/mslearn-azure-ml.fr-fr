---
lab:
  title: Utiliser MLflow pour effectuer le suivi de travaux d’entraînement
---

# Utiliser MLflow pour effectuer le suivi de travaux d’entraînement

MLflow est une plateforme open source qui permet de gérer le cycle de vie du machine learning de bout en bout. MLflow Tracking est un composant qui journalise les métriques, paramètres et artefacts de modèle de vos travaux d’entraînement, et qui en assure le suivi.

Dans cet exercice, vous allez utiliser MLflow pour suivre l’exécution de l’entraînement de modèle en tant que travail de commande.

## Avant de commencer

Vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free?azure-portal=true) dans lequel vous avez un accès administratif.

## Provisionner un espace de travail Azure Machine Learning

Un *espace de travail* Azure Machine Learning offre un emplacement central pour gérer toutes les ressources et tous les éléments dont vous avez besoin pour entraîner et gérer vos modèles. Vous pouvez interagir avec l’espace de travail Azure Machine Learning via le studio, le SDK Python et Azure CLI.

Vous allez utiliser Azure CLI pour provisionner l’espace de travail et le calcul nécessaire, puis utiliser le SDK Python pour exécuter un travail de commande.

### Créer l’espace de travail et les ressources de calcul

Pour créer l’espace de travail Azure Machine Learning, une instance de calcul et un cluster de calcul, vous allez utiliser Azure CLI. Toutes les commandes nécessaires sont regroupées dans un script Shell que vous pouvez exécuter.

1. Dans un navigateur, ouvrez le portail Azure sur `https://portal.azure.com/` en vous connectant avec votre compte Microsoft.
1. Sélectionnez le bouton \[>_] (*Cloud Shell*) en haut de la page, à droite de la zone de recherche. Cela a pour effet d’ouvrir un volet de Cloud Shell au bas du portail.
1. Sélectionnez **Bash** si vous y êtes invité. Quand vous ouvrez Cloud Shell pour la première fois, vous êtes invité à choisir le type d’interpréteur de commandes que vous souhaitez utiliser (*Bash* ou *PowerShell*).
1. Vérifiez que le bon abonnement est spécifié et sélectionnez **Créer un stockage** si vous êtes invité à créer un stockage pour votre Cloud Shell. Attendez que le stockage soit créé.
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

1. Dans le Portail Azure, accédez à l’espace de travail Azure Machine Learning nommé **mlw-dp100-...** .
1. Sélectionnez l’espace de travail Azure Machine Learning et, dans sa page **Vue d’ensemble**, sélectionnez **Lancer le studio**. Un autre onglet s’ouvre dans votre navigateur pour ouvrir le studio Azure Machine Learning.
1. Fermez les fenêtres contextuelles qui s’affichent dans le studio.
1. Dans le studio Azure Machine Learning, accédez à la page **Calcul** et vérifiez que l’instance de calcul et le cluster que vous avez créés dans la section précédente existent. L’instance de calcul doit être en cours d’exécution, et le cluster doit être inactif et avoir 0 nœud en cours d’exécution.
1. Sous l’onglet **Instances de calcul**, recherchez votre instance de calcul et sélectionnez l’application **Terminal**.
1. Dans le terminal, installez le SDK Python sur l’instance de calcul en exécutant les commandes suivantes :

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorez les messages (d’erreur) indiquant que les packages n’ont pas pu être trouvés et désinstallés.

1. Exécutez la commande suivante pour cloner un dépôt Git contenant des notebooks, des données et autres fichiers dans votre espace de travail :

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Une fois la commande exécutée, dans le volet **Fichiers**, cliquez sur **&#8635;** pour actualiser l’affichage et vérifier qu’un dossier **Users/*votre-nom-utilisateur*/azure-ml-labs** a été créé.

## Envoyer des travaux MLflow à partir d’un notebook

Vous disposez maintenant de toutes les ressources nécessaires. Vous pouvez donc exécuter le notebook pour soumettre le travail, qui utilise MLflow afin de suivre les paramètres, les métriques et les artefacts de modèle.

1. Ouvrez le notebook **Labs/08/Use MLflow to track jobs.ipynb**.

    > Sélectionnez **S’authentifier** et suivez les étapes à faire si une notification vous invite à vous authentifier.

1. Vérifiez que le notebook utilise le noyau **Python 3.8 - AzureML**.
1. Exécutez toutes les cellules dans le notebook.

## Supprimer les ressources Azure

Une fois que vous avez fini d’explorer Azure Machine Learning, vous devriez supprimer les ressources que vous avez créées afin d’éviter des coûts Azure superflus.

1. Fermez l’onglet du studio Azure Machine Learning et revenez au portail Azure.
1. Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources **rg-dp100-...** .
1. Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.

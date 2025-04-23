---
lab:
  title: Effectuer le réglage des hyperparamètres avec un travail de balayage
---

# Effectuer le réglage des hyperparamètres avec un travail de balayage

Les hyperparamètres sont des variables qui affectent la façon dont l’apprentissage d’un modèle est effectué, mais qui ne peuvent pas être dérivés des données d’apprentissage. Il peut être difficile de choisir des valeurs d’hyperparamètres optimales pour l’apprentissage du modèle et, en général, cela implique un grand nombre d’essais et d’erreurs.

Dans cet exercice, vous allez utiliser Azure Machine Learning pour régler des hyperparamètres en effectuant plusieurs essais d’entraînement en parallèle.

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
1. Vérifiez que le bon abonnement est spécifié et que l’option **Aucun compte de stockage requis** est sélectionnée. Sélectionnez **Appliquer**.
1. Dans le terminal, entrez les commandes suivantes pour cloner ce dépôt :

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Utilisez `SHIFT + INSERT` pour coller votre code copié dans Cloud Shell.

1. Une fois que le dépôt a été cloné, entrez les commandes suivantes pour accéder au dossier de ce labo et exécutez le script **setup.sh** qu’il contient :

    ```azurecli
    cd azure-ml-labs/Labs/09
    ./setup.sh
    ```

    > Ignorez les messages (d’erreur) indiquant que les extensions n’ont pas été installées.

1. Attendez que le script se termine, ce qui prend généralement entre 5 et 10 minutes.

    <details>
    <summary><b>Conseil sur la résolution de problèmes</b> : erreur de création d’espace de travail</summary><br>
    <p>Si vous rencontrez une erreur lors de l’exécution du script d’installation via l’interface de ligne de commande, vous devez provisionner les ressources manuellement :</p>
    <ol>
        <li>Sur la page d’accueil du portail Azure, sélectionnez <b>+Créer une ressource</b>.</li>
        <li>Recherchez <i>Machine Learning</i>, puis sélectionnez <b>Azure Machine Learning</b>. Sélectionnez <b>Créer</b>.</li>
        <li>Créez une ressource Azure Machine Learning avec les paramètres suivants : <ul>
                <li><b>Abonnement</b> : <i>votre abonnement Azure</i></li>
                <li><b>Groupe de ressources</b> : rg-dp100-labs</li>
                <li><b>Nom de l’espace de travail</b> : mlw-dp100-labs</li>
                <li><b>Région</b> : <i>sélectionnez la région géographique la plus proche de vous</i></li>
                <li><b>Compte de stockage</b> : <i>notez le nouveau compte de stockage par défaut à créer pour votre espace de travail</i></li>
                <li><b>Coffre de clés</b> : <i>notez le nouveau coffre de clés par défaut à créer pour votre espace de travail</i></li>
                <li><b>Application Insights</b> : <i>notez la nouvelle ressource Application Insights par défaut à créer pour votre espace de travail</i></li>
                <li><b>Registre de conteneurs</b> : aucun (<i>un registre est créé automatiquement la première fois que vous déployez un modèle sur un conteneur</i>)</li>
            </ul>
        <li>Sélectionnez <b>Examiner et créer</b> et attendez que l’espace de travail et les ressources associées soient créés. Cela prend généralement environ 5 minutes.</li>
        <li>Sélectionnez <b>Accéder à la ressource</b> et sur la page <b>Vue d’ensemble</b> de la ressource, cliquez sur <b>Lancer Studio</b>. Un autre onglet s’ouvre dans votre navigateur pour ouvrir le studio Azure Machine Learning.</li>
        <li>Fermez les fenêtres contextuelles qui s’affichent dans le studio.</li>
        <li>Dans Azure Machine Learning Studio, accédez à la page <b>Calcul</b> et sélectionnez <b>+ Nouveau</b> dans l’onglet <b>Instances de calcul</b>.</li>
        <li>Nommez l’instance de calcul avec un nom unique, puis sélectionnez <b>Standard_DS11_v2</b> comme taille de machine virtuelle.</li>
        <li>Sélectionnez <b>Vérifier + créer</b>, puis sélectionnez <b>Créer</b>.</li>
        <li>Ensuite, sélectionnez l’onglet <b>Clusters de calcul</b>, puis sélectionnez <b>+ Nouveau</b>.</li>
        <li>Choisissez la même région que celle où vous avez créé votre espace de travail, puis sélectionnez <b>Standard_DS11_v2</b> comme taille de machine virtuelle. Sélectionnez <b>Suivant</b>.</li>
        <li>Nommez le cluster avec un nom unique, puis sélectionnez <b>Créer</b>.</li>
    </ol>
    </details>

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

1. Une fois la commande exécutée, dans le volet **Fichiers**, cliquez sur **&#8635;** pour actualiser la vue et vérifier qu’un dossier **Users/*votre-nom-utilisateur*/azure-ml-labs** a été créé.

## Régler les hyperparamètres avec un travail de balayage

Maintenant que vous disposez de toutes les ressources nécessaires, vous pouvez exécuter le notebook pour soumettre un travail de balayage.

1. Ouvrez le notebook **Labs/09/Hyperparameter tuning.ipynb**.

    > Sélectionnez **S’authentifier** et suivez les étapes à faire si une notification vous invite à vous authentifier.

1. Vérifiez que le notebook utilise le noyau **Python 3.8 - AzureML**.
1. Exécutez toutes les cellules dans le notebook.

## Supprimer les ressources Azure

Une fois que vous avez fini d’explorer Azure Machine Learning, vous devriez supprimer les ressources que vous avez créées afin d’éviter des coûts Azure superflus.

1. Fermez l’onglet du studio Azure Machine Learning et revenez au portail Azure.
1. Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources **rg-dp100-...**.
1. Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.

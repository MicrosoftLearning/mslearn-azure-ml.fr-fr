---
lab:
  title: Entraîner un modèle avec le Concepteur Azure Machine Learning
---

# Entraîner un modèle avec le Concepteur Azure Machine Learning

Le Concepteur Azure Machine Learning offre une interface par glisser-déposer avec laquelle vous pouvez définir un workflow. Vous pouvez créer un workflow pour entraîner un modèle, en testant et en comparant facilement plusieurs algorithmes.

Dans cet exercice, vous allez utiliser le Concepteur pour entraîner et comparer rapidement deux algorithmes de classification.

## Avant de commencer

Vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free?azure-portal=true) dans lequel vous avez un accès administratif.

## Provisionner un espace de travail Azure Machine Learning

Un *espace de travail* Azure Machine Learning offre un emplacement central pour gérer toutes les ressources et tous les éléments dont vous avez besoin pour entraîner et gérer vos modèles. Vous pouvez interagir avec l’espace de travail Azure Machine Learning via le studio, le SDK Python et Azure CLI.

Vous allez utiliser un script Shell qui utilise Azure CLI pour provisionner l’espace de travail et les ressources nécessaires. Ensuite, vous allez utiliser le Concepteur dans le studio Azure Machine Learning pour entraîner et comparer des modèles.

### Créer l’espace de travail et le cluster de calcul

Pour créer l’espace de travail Azure Machine Learning et un cluster de calcul, vous allez utiliser Azure CLI. Toutes les commandes nécessaires sont regroupées dans un script Shell que vous pouvez exécuter.

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

1. Une fois que le dépôt a été cloné, entrez les commandes suivantes pour accéder au dossier de ce labo et exécutez le script setup.sh qu’il contient :

    ```azurecli
    cd azure-ml-labs/Labs/05
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

## Configurer un nouveau pipeline

Une fois que vous avez créé l’espace de travail et le cluster de calcul nécessaire, vous pouvez ouvrir le studio Azure Machine Learning et créer un pipeline d’entraînement avec le Concepteur.

1. Dans le portail Azure, accédez à l’espace de travail Azure Machine Learning appelé **mlw-dp100-...**.
1. Sélectionnez l’espace de travail Azure Machine Learning et, dans sa page **Vue d’ensemble**, sélectionnez **Lancer le studio**. Un autre onglet s’ouvre dans votre navigateur pour ouvrir le studio Azure Machine Learning.
1. Fermez les fenêtres contextuelles qui s’affichent dans le studio.
1. Dans le studio Azure Machine Learning, accédez à la page **Calcul** et vérifiez que le cluster de calcul que vous avez créé dans la section précédente existe. Le cluster doit être inactif et avoir 0 nœud en cours d’exécution.
1. Accédez à la page **Concepteur**.
1. Sélectionnez l’onglet **Personnalisé** en haut de la page.
1. Créez un pipeline vide à l’aide de composants personnalisés.
1. Remplacez le nom du pipeline par défaut (**Pipeline-Created-on-* date***) `Train-Diabetes-Classifier` en sélectionnant l’icône du crayon à sa droite.


## Créer un pipeline

Pour entraîner un modèle, vous avez besoin de données. Vous pouvez utiliser toutes les données stockées dans un magasin de données ou utiliser une URL accessible publiquement.

1. Dans le menu de gauche, sélectionnez l’onglet **Données**.
1. Glissez-déposez le composant **diabetes-folder** dans le canevas.

    Maintenant que vous avez vos données, vous pouvez continuer en créant un pipeline à l’aide de composants personnalisés qui existent déjà dans l’espace de travail (qui ont été créés pour vous pendant la configuration).

1. Dans le menu de gauche, sélectionnez l’onglet **Composants**.
1. Glissez-déposez le composant **Supprimer les lignes vides** dans le canevas, sous **diabetes-folder**.
1. Connectez la sortie des données à l’entrée du nouveau composant.
1. Glissez-déposez le composant **Normaliser les colonnes numériques** dans le canevas, sous **Supprimer les lignes vides**.
1. Connectez la sortie du composant précédent à l’entrée du nouveau composant.
1. Glissez-déposez le composant **Entraîner un modèle de classifieur d’arbre de décision** dans le canevas, sous **Normaliser les colonnes numériques**.
1. Connectez la sortie du composant précédent à l’entrée du nouveau composant.
1. Sélectionnez **Configurer & soumettre** et, dans la page **Configurer la tâche de pipeline**, créez une expérience et appelez-la `diabetes-designer-pipeline`, puis sélectionnez **Suivant**.
1. Dans **Entrées & Sorties**, n’apportez aucune modification et sélectionnez **Suivant**.
1. Dans **Paramètres d’exécution**, sélectionnez **Cluster de calcul**, puis sous **Sélectionner un cluster de calcul Azure ML**, sélectionnez votre *aml-cluster*.
1. Sélectionnez **Vérifier + envoyer**, puis **Envoyer** pour démarrer l’exécution du pipeline.
1. Vous pouvez vérifier l’état de l’exécution en accédant à la page **Pipelines** et en sélectionnant le pipeline **Train-Diabetes-Classifier**.
1. Attendez que tous les composants soient correctement terminés.

    La soumission du travail initialise le cluster de calcul. Comme le cluster de calcul était jusqu’à présent inactif, il peut mettre un certain temps à revenir à une taille qui dépasse 0 nœud. Une fois le cluster redimensionné, il commence automatiquement à exécuter le pipeline.

Vous pourrez suivre l’exécution de chaque composant. Lorsque le pipeline échoue, vous pouvez regarder quel composant a échoué et pourquoi il a échoué. Les messages d’erreur s’affichent sous l’onglet **Sorties + journaux** de la vue d’ensemble du travail.

## Entraîner un deuxième modèle à comparer

Pour comparer les algorithmes et évaluer ceux qui sont les plus performants, vous pouvez entraîner deux modèles au sein d’un même pipeline et les comparer.

1. Revenez au **Concepteur** et sélectionnez le brouillon de pipeline **Train-Diabetes-Classifier**.
1. ajoutez le composant **Entraîner un modèle de classifieur de régression logistique** au canevas, à côté de l’autre composant d’entraînement.
1. Connectez la sortie du composant **Normaliser les colonnes numériques** à l’entrée du nouveau composant d’entraînement.
1. En haut, sélectionnez **Configurer & soumettre**.
1. Dans la page **Informations de base**, créez une expérience appelée `designer-compare-classification`, puis exécutez-la.
1. Sélectionnez **Vérifier + envoyer**, puis **Envoyer** pour démarrer l’exécution du pipeline.
1. Vous pouvez vérifier l’état de l’exécution en accédant à la page **Pipelines** et en sélectionnant le pipeline **Train-Diabetes-Classifier** avec l’expérience **designer-compare-classification**.
1. Attendez que tous les composants soient correctement terminés.  
1. Sélectionnez **Aperçu du travail**, puis sélectionnez l’onglet **Métriques** pour consulter les résultats des deux composants d’entraînement.
1. Essayez de déterminer quel modèle a été le plus performant.

## Supprimer les ressources Azure

Une fois que vous avez fini d’explorer Azure Machine Learning, vous devriez supprimer les ressources que vous avez créées afin d’éviter des coûts Azure superflus.

1. Fermez l’onglet du studio Azure Machine Learning et revenez au portail Azure.
1. Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources **rg-dp100-...**.
1. Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.

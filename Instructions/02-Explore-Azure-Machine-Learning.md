---
lab:
  title: Explorer l’espace de travail Azure Machine Learning
---

# Explorer l’espace de travail Azure Machine Learning

Azure Machine Learning fournit une plateforme de science des données pour entraîner et gérer des modèles Machine Learning. Dans ce labo, vous allez créer un espace de travail Azure Machine Learning et explorer les différentes façons de l’utiliser. Le labo est conçu comme une présentation des différentes fonctionnalités principales d’Azure Machine Learning et des outils de développement. Si vous souhaitez en savoir plus sur les fonctionnalités, il existe d’autres labos à explorer.

## Avant de commencer

Vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free?azure-portal=true) dans lequel vous avez un accès administratif.

## Provisionner un espace de travail Azure Machine Learning

Un **espace de travail** Azure Machine Learning offre un emplacement central pour gérer toutes les ressources et tous les éléments dont vous avez besoin pour entraîner et gérer vos modèles. Vous pouvez provisionner un espace de travail avec l’interface interactive dans le portail Azure, ou vous pouvez utiliser Azure CLI avec l’extension Azure Machine Learning. Dans la plupart des scénarios de production, il est préférable d’automatiser le provisionnement avec l’interface CLI afin d’incorporer le déploiement de ressources dans un processus de développement et d’opérations reproductibles (*DevOps*). 

Dans cet exercice, vous allez utiliser le portail Azure pour provisionner Azure Machine Learning afin d’explorer toutes les options.

1. Connectez-vous à .`https://portal.azure.com/`
2. Créez une ressource **Azure Machine Learning** avec les paramètres suivants :
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : `rg-dp100-labs`
    - **Nom de l’espace de travail** : `mlw-dp100-labs`
    - **Région** : *sélectionnez la région géographique la plus proche de vous*
    - **Compte de stockage** : *notez le nouveau compte de stockage par défaut à créer pour votre espace de travail*
    - **Coffre de clés** : *notez le nouveau coffre de clés par défaut à créer pour votre espace de travail*
    - **Application Insights** : *notez la nouvelle ressource Application Insights par défaut à créer pour votre espace de travail*
    - **Registre de conteneurs** : aucun (*un registre est créé automatiquement la première fois que vous déployez un modèle sur un conteneur*)
3. Attendez que l’espace de travail et les ressources associées soient créés, ce qui prend généralement environ 5 minutes.

> **Remarque** : Lorsque vous créez un espace de travail Azure Machine Learning, vous pouvez utiliser des options avancées pour restreindre l’accès via un *point de terminaison privé*, et spécifier des clés personnalisées pour le chiffrement des données. Nous n’utiliserons pas ces options dans cet exercice mais vous devriez avoir conscience de leur existence.

## Explorer le studio Azure Machine Learning

*Azure Machine Learning studio* est un portail web via lequel vous pouvez accéder à l’espace de travail Azure Machine Learning. Vous pouvez utiliser le studio Azure Machine Learning pour gérer toutes les ressources et tous les éléments au sein de votre espace de travail.

1. Accédez au groupe de ressources nommé **rg-dp100-labs**.
1. Vérifiez que le groupe de ressources contient votre espace de travail Azure Machine Learning, une ressource Application Insights, un coffre de clés et un compte de stockage.
1. Sélectionnez votre espace de travail Azure Machine Learning.
1. Sélectionnez **Lancer Studio** dans la page **Vue d’ensemble**. Un autre onglet s’ouvre dans votre navigateur pour ouvrir le studio Azure Machine Learning.
1. Fermez les fenêtres contextuelles qui s’affichent dans le studio.
1. Notez les différentes pages affichées sur le côté gauche du studio. Si seuls les symboles sont visibles dans le menu, sélectionnez l’icône &#9776; pour développer le menu et explorer les noms des pages.
1. Remarquez la section **Création** qui contient les **Notebooks**, le **ML automatisé** et le **Concepteur**. Il s’agit des trois façons de créer vos propres modèles Machine Learning dans le studio Azure Machine Learning.
1. Notez la section **Ressources**, qui inclut les **Données**, les **Travaux** et les **Modèles**, entre autres. Les ressources sont consommées ou créées lors de l’entraînement ou du scoring d’un modèle. Les ressources sont utilisées pour l’entraînement, le déploiement et la gestion de vos modèles et peuvent être versionnées pour suivre votre historique.
1. Remarquez que la section **Gérer** qui contient le **Calcul** entre autres. Il s’agit de ressources d’infrastructure nécessaires à l’entraînement ou au déploiement d’un modèle Machine Learning.

## Entraîner un modèle à l’aide d’AutoML

Pour explorer l’utilisation des ressources et des éléments dans l’espace de travail Azure Machine Learning, essayons d’entraîner un modèle.

Un moyen rapide d’entraîner et de trouver le meilleur modèle pour une tâche qui utilise vos données consiste à utiliser l’option **AutoML**.

> **Remarque** : Des fenêtres contextuelles peuvent apparaître dans l’ensemble du studio pour vous guider. Vous pouvez fermer et ignorer toutes les fenêtres contextuelles et vous concentrer sur les instructions de ce labo.

1. Téléchargez les données d’entraînement qui seront utilisées à l’adresse `https://github.com/MicrosoftLearning/mslearn-azure-ml/raw/refs/heads/main/Labs/02/diabetes-data.zip`, puis extrayez les fichiers compressés.
1. Dans Azure Machine Learning Studio, sélectionnez la page **AutoML** dans le menu de gauche.
1. Sélectionnez **+ Nouveau travail ML automatisé**.
1. Lors de l’étape **Paramètres de base**, donnez un nom unique à votre travail d’entraînement et testez ou utilisez les valeurs affectées par défaut. Cliquez sur **Suivant**.
1. Lors de l’étape **Type et données de tâche**, sélectionnez **Classification** comme type de tâche, puis sélectionnez **+ Créer** pour ajouter vos données d’entraînement.
2. Sur la page **Créer une ressource de données**, lors de l’étape **Type de données**, donnez un nom à votre ressource de données (par exemple, `training-data`), puis sélectionnez **Suivant**.
1. Lors de l’étape **Source de données**, sélectionnez **Fichiers locaux** pour charger les données d’entraînement que vous avez préalablement téléchargées. Cliquez sur **Suivant**.
1. Lors de l’étape **Type de stockage de destination**, vérifiez que **Stockage Blob Azure** est sélectionné comme type de magasin de données et que **workspaceblobstore** est le magasin de données sélectionné. Cliquez sur **Suivant**.
1. Lors de l’étape **Sélection de la MLTable**, sélectionnez **Charger un dossier**, puis sélectionnez le dossier que vous avez extrait du fichier compressé que vous aviez téléchargé. Cliquez sur **Suivant**.
1. Passez en revue les paramètres de votre ressource de données, puis sélectionnez **Créer**.
1. Une fois revenu à l’étape **Type et données de tâche**, sélectionnez les données que vous venez de charger, puis cliquez sur **Suivant**.

> **Conseil** : vous devrez peut-être sélectionner à nouveau le type de tâche **Classification** avant de passer à l’étape suivante.

1. Lors de l’étape **Paramètres de tâche**, sélectionnez **Diabétique (booléen)** comme colonne cible, puis ouvrez l’option **Afficher les paramètres de configuration supplémentaires**.
1. Dans le volet **Configuration supplémentaire**, remplacez la mesure principale par **Précision**, puis sélectionnez **Enregistrer**.
1. Développez l’option **Limites** et définissez les propriétés suivantes :
    * **Nombre maximal d’essais** : 10
    * **Délai d’expiration de l’expérience (minutes)**  : 60
    * **Délai d’expiration d’une itération (minutes)**  : 15
    * **Autoriser l’arrêt anticipé** : coché

1. Pour les **Données de test**, sélectionnez **Fractionner le test d’entraînement** et vérifiez que le **Pourcentage de test des données** est défini sur 10. Cliquez sur **Suivant**.
1. Lors de l’étape **Calcul**, vérifiez que le type de calcul est **Sans serveur** et que la taille de la machine virtuelle sélectionnée est **Standard_DS3_v2**. Cliquez sur **Suivant**.

> **Remarque** : Les instances de calcul et les clusters sont basés sur des images de machines virtuelles Azure standard. Pour cet exercice, l’image *Standard_DS3_v2* est recommandée afin d’obtenir l’équilibre optimal entre coûts et performances. Si votre abonnement s’accompagne d’un quota qui ne couvre pas cette image, choisissez-en une autre. Gardez cependant à l’esprit qu’une image plus grande peut entraîner des coûts plus élevés, tandis qu’une plus petite risque de ne pas suffire pour effectuer les tâches. Vous pouvez également demander à votre administrateur Azure d’étendre votre quota.

1. Passez en revue tous vos paramètres, puis sélectionnez **Soumettre le travail d’entraînement**.

## Utiliser des travaux pour afficher votre historique

Une fois le travail soumis, une redirection vous amène sur la page du travail. Les travaux vous permettent de suivre les charges de travail que vous avez exécutées et de les comparer entre elles. Les travaux appartiennent à une **expérience**, qui vous permet de regrouper des exécutions de travaux. 

1. Notez que dans les paramètres de **Vue d’ensemble**, vous pouvez consulter l’état du travail, son créateur, sa date de création et le temps total de son exécution (entre autres).
1. L’exécution du travail d’entraînement doit prendre entre 10 et 20 minutes. Une fois le travail terminé, vous pouvez également afficher les détails de chaque exécution pour les composants individuels, y compris leur sortie. N’hésitez pas à explorer la page du travail pour comprendre comment les modèles sont entraînés.

    En outre, Azure Machine Learning effectue automatiquement un suivi des propriétés de votre travail. En utilisant des travaux, vous pouvez facilement afficher votre historique pour comprendre ce que vous ou vos collègues avez déjà fait.
    Pendant l’expérimentation, les travaux permettent de suivre les différents modèles que vous entraînez pour les comparer et identifier le meilleur modèle. Pendant la production, les travaux vous permettent de vérifier si les charges de travail automatisées se sont exécutées comme prévu.

## Supprimer les ressources Azure

Une fois que vous avez fini d’explorer Azure Machine Learning, vous devriez supprimer les ressources que vous avez créées afin d’éviter des coûts Azure superflus.

1. Fermez l’onglet du studio Azure Machine Learning et revenez au portail Azure.
1. Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources **rg-dp100-labs**.
1. Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.

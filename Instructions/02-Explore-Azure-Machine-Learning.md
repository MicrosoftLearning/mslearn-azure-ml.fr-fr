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
1. Notez la section **Auteur**, qui inclut les **Notebooks**, le **ML automatisé** et le **Concepteur**. Il s’agit des trois façons de créer vos propres modèles Machine Learning dans le studio Azure Machine Learning.
1. Notez la section **Ressources**, qui inclut les **Données**, les **Travaux** et les **Modèles**, entre autres. Les ressources sont consommées ou créées lors de l’entraînement ou du scoring d’un modèle. Les ressources sont utilisées pour l’entraînement, le déploiement et la gestion de vos modèles et peuvent être versionnées pour suivre votre historique.
1. Notez la section **Gérer**, qui inclut le **Calcul** et les **Magasins de données**, entre autres. Il s’agit de ressources d’infrastructure nécessaires à l’entraînement ou au déploiement d’un modèle Machine Learning. 

## Créer un pipeline d’entraînement

Pour explorer l’utilisation des ressources et des éléments dans l’espace de travail Azure Machine Learning, essayons d’entraîner un modèle. 

Un moyen rapide de créer un pipeline d’entraînement de modèle consiste à utiliser le **Concepteur**.

> [!Note]
> Des fenêtres contextuelles peuvent apparaître dans l’ensemble du studio pour vous guider. Vous pouvez fermer et ignorer toutes les fenêtres contextuelles et vous concentrer sur les instructions de ce labo.

1. Sélectionnez la page **Concepteur** dans le menu situé à gauche du studio.
1. Sélectionnez l’exemple **Régression - Prédiction du prix des véhicules automobiles (simple)** . 
    
    Un nouveau pipeline s’affiche. En haut du pipeline, un composant s’affiche pour charger les **Données sur le prix des véhicules automobiles (brutes)** . Le pipeline traite les données et entraîne un modèle de régression linéaire pour prédire le prix de chaque automobile.
1. En haut de la page, sélectionnez **Envoyer**. Une erreur s’affiche car vous n’avez pas encore sélectionné de cible de calcul. Le pipeline ne peut pas s’exécuter sans ressources de calcul. 

Créons une cible de calcul.

## Créer une cible de calcul

Pour exécuter une charge de travail dans l’espace de travail Azure Machine Learning, vous devez avoir une ressource de calcul. L’un des avantages d’Azure Machine Learning est la possibilité de créer une ressource de calcul basée sur le cloud, sur laquelle vous pouvez exécuter des expériences et des scripts d’apprentissage à grande échelle.

1. Dans le studio Azure Machine Learning, sélectionnez la page **Calcul** dans le menu de gauche. Vous pouvez utiliser quatre sortes de ressources de calcul :
    - **Instances de calcul** : machine virtuelle gérée par Azure Machine Learning. Idéal pour le développement quand vous explorez des données et expérimentez de manière itérative des modèles Machine Learning. 
    - **Clusters de calcul** : clusters scalables de machines virtuelles pour le traitement à la demande du code d’expérimentation. Idéal pour exécuter du code de production ou des travaux automatisés.
    - **Clusters d’inférence** : cluster Kubernetes utilisé pendant l’inférence. Idéal pour le déploiement de modèles en temps réel à grande échelle.
    - **Capacité de calcul attachée** : attachez vos ressources de calcul Azure existantes à l’espace de travail, telles que les machines virtuelles ou les clusters Azure Databricks.

Pour entraîner un modèle Machine Learning que vous avez créé avec le concepteur, vous pouvez utiliser une instance de calcul ou un cluster de calcul.

2. Sous l’onglet **Instances de calcul**, ajoutez une nouvelle instance de calcul avec les paramètres suivants. 
    - **Nom de la capacité de calcul** : *entrez un nom unique*
    - **Emplacement** : *automatiquement le même emplacement que votre espace de travail*
    - **Type de machine virtuelle** : `CPU`
    - **Taille de machine virtuelle** : `Standard_DS11_v2`
    - **Quota disponible** : affiche les cœurs dédiés disponibles.
    - **Afficher les paramètres avancés** : notez les paramètres suivants, mais ne les sélectionnez pas :
        - **Activer l’accès SSH** : `Unselected` *(vous pouvez utiliser cette option pour avoir un accès direct à la machine virtuelle en utilisant un client SSH)*
        - **Activer un réseau virtuel** : `Unselected` *(vous utiliserez généralement cette option dans un environnement d’entreprise pour améliorer la sécurité réseau)*
        - **Attribuer à un autre utilisateur** : `Unselected` *(vous pouvez utiliser cette option pour attribuer une instance de calcul à un scientifique des données)*
        - **Provisionner avec un script d’installation** : `Unselected` *(permet d’ajouter un script à exécuter sur l’instance distante lors de la création)*

3. Sélectionnez **Créer** et patientez jusqu’à ce que l’instance de calcul démarre et que son état passe à **En cours d’exécution**.

> **Remarque** : Les instances de calcul et les clusters sont basés sur des images de machines virtuelles Azure standard. Pour cet exercice, l’image *Standard_DS11_v2* est recommandée pour obtenir un équilibre optimal entre coûts et performances. Si votre abonnement s’accompagne d’un quota qui ne couvre pas cette image, choisissez-en une autre. Gardez cependant à l’esprit qu’une image plus grande peut entraîner des coûts plus élevés, tandis qu’une plus petite risque de ne pas suffire pour effectuer les tâches. Vous pouvez également demander à votre administrateur Azure d’étendre votre quota.

## Exécuter votre pipeline d’entraînement

Vous avez créé une cible de calcul et vous pouvez maintenant exécuter votre exemple de pipeline d’entraînement dans le Concepteur.

1. Accédez à la page **Concepteur**.
1. Sélectionnez l’onglet **Composants prédéfinis classiques**.
1. Sélectionnez le brouillon de pipeline **Régression - Prédiction du prix des véhicules automobiles (simple)** .
1. Sélectionnez **Paramètres** en haut à droite pour développer le volet **Paramètres**.
1. Sélectionnez **Instance de calcul** sous **Sélectionner le type de calcul**.
1. Sous **Sélectionner un cluster de calcul Azure ML**, sélectionnez votre instance de calcul nouvellement créée. 
1. Sélectionnez **Envoyer** pour réexécuter le pipeline d’entraînement.
1. Une fenêtre contextuelle s’affiche pour vous permettre de configurer le travail de pipeline. Configurez et envoyez un nouveau travail de pipeline avec les paramètres suivants :
    - **Expérience** : `Create new`
    - **Nom de la nouvelle expérience** : `train-regression-designer`
    - Conservez tous les autres paramètres par défaut.

Le pipeline d’entraînement est maintenant soumis à l’instance de calcul. L’exécution du pipeline prendra environ 10 minutes. Explorons d’autres pages en attendant.

## Utiliser des travaux pour afficher votre historique

Chaque fois que vous exécutez un script ou un pipeline dans l’espace de travail Azure Machine Learning, il est enregistré en tant que **travail**. Les travaux vous permettent de suivre les charges de travail que vous avez exécutées et de les comparer entre elles. Les travaux appartiennent à une **expérience**, qui vous permet de regrouper des exécutions de travaux.

1. Accédez à la page **Travaux** avec le menu situé à gauche du studio Azure Machine Learning.
1. Sélectionnez l’expérience **train-regression-designer** pour afficher ses exécutions de travail. Vous y verrez une vue d’ensemble de tous les travaux qui font partie de cette expérience. Si vous avez exécuté plusieurs pipelines d’entraînement, cette vue vous permet de comparer les pipelines et d’identifier le meilleur.
1. Sélectionnez le dernier travail de l’expérience **train-regression-designer**.
1. Une fois le pipeline d’entraînement affiché, vous pouvez voir les composants qui ont été exécutés avec succès ou ayant échoué. Si le travail est toujours en cours d’exécution, vous pouvez également identifier la tâche en cours de réalisation.
1. Pour afficher les détails du travail de pipeline, sélectionnez l’**Aperçu du travail** en haut à droite pour développer l’**Aperçu des tâches du pipeline**. 
1. Notez que dans les paramètres **Vue d’ensemble**, vous trouverez l’état du travail, qui a créé le pipeline, quand il a été créé et le temps qu’il a fallu pour exécuter le pipeline complet (entre autres).

    Quand vous exécutez un script ou un pipeline en tant que travail, vous pouvez définir toutes les entrées et documenter toutes les sorties. En outre, Azure Machine Learning assure automatiquement le suivi des propriétés de votre travail. En utilisant des travaux, vous pouvez facilement afficher votre historique pour comprendre ce que vous ou vos collègues avez déjà fait. 
    
    Pendant l’expérimentation, les travaux permettent de suivre les différents modèles que vous entraînez pour les comparer et identifier le meilleur modèle. Pendant la production, les travaux vous permettent de vérifier si les charges de travail automatisées se sont exécutées comme prévu.

1. Une fois votre travail terminé, vous pouvez également afficher les détails de chaque exécution de composant individuel, y compris la sortie. N’hésitez pas à explorer le pipeline pour comprendre comment le modèle est entraîné.

## Supprimer les ressources Azure

Une fois que vous avez fini d’explorer Azure Machine Learning, supprimez les ressources que vous avez créées afin d’éviter des coûts Azure superflus.

1. Fermez l’onglet du studio Azure Machine Learning et revenez au portail Azure.
1. Dans le portail Azure, dans la page **Accueil**, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources **rg-dp100-labs**.
1. Au sommet de la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**. 
1. Entrez le nom du groupe de ressources pour confirmer que vous souhaitez le supprimer, puis sélectionnez **Supprimer**.
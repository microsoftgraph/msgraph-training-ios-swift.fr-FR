# <a name="contributing-to-microsoft-graph-training-repositories"></a>Contribution aux référentiels de formation Microsoft Graph

Merci d’avoir contribué à ce projet ! Avant d’envoyer votre requête de pull, veillez à prendre en compte les considérations suivantes.

## <a name="overview"></a>Vue d’ensemble

Le code de ce référentiel sert à trois fins :

- Les fichiers Markdown du dossier [didacticiel](/tutorial) sont publiés en tant que didacticiels sur la page de [didacticiels Microsoft Graph.](https://docs.microsoft.com/graph/tutorials)
- L’exemple de projet dans le dossier [de](/demo) démonstration est la source d’un démarrage [rapide Microsoft Graph](https://developer.microsoft.com/graph/quick-start).* *\** _
- L’exemple de projet dans le dossier de démonstration est également téléchargeable directement à partir de GitHub et doit s’exécuter tel qu’il se doit après une configuration simple.

> _*\**_ Tous les référentiels de formation ne sont pas disponibles en tant que démarrages rapides (encore).

Il est important de garder à l’esprit que les modifications apportées à un endroit _may* nécessitent des modifications dans un autre, pour maintenir la synchronisation. Dans la mesure du possible, les fichiers Markdown font référence aux fichiers de code source directement (à l’aide d’une syntaxe personnalisée), afin que la mise à jour du code dans la source met automatiquement à jour le code dans `:::code` Markdown.

## <a name="updating-code"></a>Mise à jour du code

La `:::code` syntaxe utilisée dans Markdown dépend de commentaires spécifiques dans le fichier de code source. Ces commentaires ressemblent à ce qui suit :

```csharp
// <MySnippet>
Console.WriteLine("Hello World!");
// </MySnippet>
```

Si vous mettez à jour le code entre ces commentaires « marqueurs », les fichiers Markdown obtiennent automatiquement ces modifications lorsqu’ils sont publiés sur le site de documentation de Microsoft Graph. Si vous mettez à jour le code en dehors de ces commentaires, il est très probable que vous devrez mettre à jour le Markdown correspondant.

## <a name="adding-features"></a>Ajout de fonctionnalités

Bien que l’enthousiasme soit satisfait, n’envoyez pas de demandes de pull pour ajouter de nouvelles fonctionnalités à l’exemple. Étant donné que ce référentiel est principalement un didacticiel « Créer votre première application », l’ensemble des fonctionnalités est limité, de par sa conception.

## <a name="submitting-pull-requests"></a>Envoi de demandes de pull

Veuillez envoyer toutes les demandes de pull à la `master` branche.

## <a name="when-do-changes-get-published"></a>Quand les modifications sont-elles publiées ?

La publication des mises à jour sur le site [de didacticiels Microsoft Graph](https://docs.microsoft.com/graph/tutorials) n’est pas automatique. Les modifications doivent d’abord être promues dans la branche, puis une build doit `live` être déclenchée par les administrateurs du site. Cette fonction est généralement effectuée « selon les besoins ».

## <a name="code-of-conduct"></a>Code de conduite

Ce projet a adopté le [code de conduite Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Pour plus d’informations, reportez-vous à la [FAQ relative au code de conduite](https://opensource.microsoft.com/codeofconduct/faq/) ou contactez [opencode@microsoft.com](mailto:opencode@microsoft.com) pour toute question ou tout commentaire.

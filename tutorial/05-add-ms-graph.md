<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser le [Kit de développement logiciel (SDK) Microsoft Graph pour effectuer des](https://github.com/microsoftgraph/msgraph-sdk-objc) appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Récupérer les événements de calendrier à partir d’Outlook

Dans cette section, vous allez étendre `GraphManager` la classe pour ajouter une fonction afin d’obtenir les événements de l' `CalendarViewController` utilisateur et la mise à jour pour utiliser ces nouvelles fonctions.

1. Ouvrez **GraphManager. Swift** et ajoutez la méthode suivante à la `GraphManager` classe.

    ```Swift
    public func getEvents(completion: @escaping(Data?, Error?) -> Void) {
        // GET /me/events?$select='subject,organizer,start,end'$orderby=createdDateTime DESC
        // Only return these fields in results
        let select = "$select=subject,organizer,start,end"
        // Sort results by when they were created, newest first
        let orderBy = "$orderby=createdDateTime+DESC"
        let eventsRequest = NSMutableURLRequest(url: URL(string: "\(MSGraphBaseURL)/me/events?\(select)&\(orderBy)")!)
        let eventsDataTask = MSURLSessionDataTask(request: eventsRequest, client: self.client, completion: {
            (data: Data?, response: URLResponse?, graphError: Error?) in
            guard let eventsData = data, graphError == nil else {
                completion(nil, graphError)
                return
            }

            // TEMPORARY
            completion(eventsData, nil)
        })

        // Execute the request
        eventsDataTask?.execute()
    }
    ```

    > [!NOTE]
    > Examinez le contenu du `getEvents` code.
    >
    > - L’URL qui sera appelée est `/v1.0/me/events`.
    > - Le `select` paramètre de requête limite les champs renvoyés pour chaque événement à seulement ceux que l’application utilisera réellement.
    > - Le `orderby` paramètre de requête trie les résultats en fonction de la date et de l’heure de leur création, avec l’élément le plus récent en premier.

1. Ouvrez **CalendarViewController. Swift** et remplacez l’intégralité de son contenu par le code suivant.

    ```Swift
    import UIKit
    import MSGraphClientModels

    class CalendarViewController: UIViewController {

        @IBOutlet var calendarJSON: UITextView!

        private let spinner = SpinnerViewController()

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.
            self.spinner.start(container: self)

            GraphManager.instance.getEvents {
                (data: Data?, error: Error?) in
                DispatchQueue.main.async {
                    self.spinner.stop()

                    guard let eventsData = data, error == nil else {
                        self.calendarJSON.text = error.debugDescription
                        return
                    }

                    let jsonString = String(data: eventsData, encoding: .utf8)
                    self.calendarJSON.text = jsonString
                    self.calendarJSON.sizeToFit()
                }
            }
        }
    }
    ```

Vous pouvez maintenant exécuter l’application, se connecter et appuyer sur l’élément de navigation **calendrier** dans le menu. Vous devriez voir un dump JSON des événements dans l’application.

## <a name="display-the-results"></a>Afficher les résultats

À présent, vous pouvez remplacer le vidage JSON par un texte pour afficher les résultats de manière conviviale. Dans cette section, vous allez modifier la `getEvents` fonction pour renvoyer des objets fortement typés et modifier `CalendarViewController` pour qu’elle affiche les événements à l’aide d’un affichage tableau.

1. Ouvrez **GraphManager. Swift**. Remplacez la fonction `getEvents` existante par ce qui suit.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/GraphManager.swift" id="GetEventsSnippet" highlight="1,17-38":::

1. Créez un fichier de **classe Touch de cacao** dans le projet **GraphTutorial** nommé `CalendarTableViewCell.swift`. Choisissez **UITableViewCell** dans la sous- **classe du** champ.

1. Ouvrez **CalendarTableViewCell. Swift** et ajoutez le code suivant à la `CalendarTableViewCell` classe.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.swift" id="PropertiesSnippet":::

1. Ouvrez **main. Storyboard** et localisez la **scène de calendrier**. Sélectionnez l' **affichage** dans la **scène de calendrier** et supprimez-le.

    ![Capture d’écran de l’affichage dans la scène de calendrier](./images/view-in-calendar-scene.png)

1. Ajoutez un **affichage tableau** de la **bibliothèque** à la **scène calendrier**.
1. Sélectionnez l’affichage tableau, puis sélectionnez l' **inspecteur d’attributs**. Définissez les **cellules prototype** sur **1**.
1. Utilisez la **bibliothèque** pour ajouter trois **étiquettes** à la cellule prototype.
1. Sélectionnez la cellule prototype, puis l' **inspecteur d’identité**. Modifiez **Class** en **CalendarTableViewCell**.
1. Sélectionnez l' **inspecteur d’attributs** et définissez **identificateur** sur `EventCell`.
1. Une fois le **EventCell** sélectionné, sélectionnez l' **inspecteur** de connexions `durationLabel`et `organizerLabel`Connectez- `subjectLabel` vous, puis les étiquettes que vous avez ajoutées à la cellule sur la table de montage séquentiel.
1. Définissez les propriétés et les contraintes sur les trois étiquettes comme suit.

    - **Étiquette de l’objet**
        - Ajouter une contrainte : espace de début à la marge de début de l’affichage du contenu, valeur : 0
        - Ajouter une contrainte : espace de fin à la marge de fin de l’affichage du contenu, valeur : 0
        - Ajouter une contrainte : espace à la marge supérieure de l’affichage du contenu, valeur : 0
    - **Étiquette de l’organisateur**
        - Police : système 12,0
        - Ajouter une contrainte : espace de début à la marge de début de l’affichage du contenu, valeur : 0
        - Ajouter une contrainte : espace de fin à la marge de fin de l’affichage du contenu, valeur : 0
        - Ajouter une contrainte : espace vers le bas de l’étiquette d’objet, valeur : standard
    - **Étiquette de durée**
        - Police : système 12,0
        - Couleur : gris foncé
        - Ajouter une contrainte : espace de début à la marge de début de l’affichage du contenu, valeur : 0
        - Ajouter une contrainte : espace de fin à la marge de fin de l’affichage du contenu, valeur : 0
        - Ajouter une contrainte : espace vers le bas de l’étiquette de l’organisateur, valeur : standard
        - Ajouter une contrainte : espace inférieur au contenu afficher la marge inférieure, valeur : 8

    ![Capture d’écran de la mise en page de cellule prototype](./images/prototype-cell-layout.png)

1. Ouvrez **CalendarViewController. Swift** et remplacez son contenu par le code suivant.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.swift" id="CalendarViewSnippet":::

1. Exécutez l’application, connectez-vous, puis appuyez sur l’onglet **calendrier** . La liste des événements doit s’afficher.

    ![Capture d’écran du tableau des événements](./images/calendar-list.png)

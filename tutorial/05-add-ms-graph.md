<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser le [SDK Microsoft Graph pour l’objectif C](https://github.com/microsoftgraph/msgraph-sdk-objc) pour effectuer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Récupérer les événements de calendrier à partir d’Outlook

Dans cette section, vous allez étendre la classe pour ajouter une fonction afin d’obtenir les événements de l’utilisateur pour la semaine en cours et mettre à jour pour utiliser `GraphManager` `CalendarViewController` ces nouvelles fonctions.

1. Ouvrez **GraphManager.swift** et ajoutez la méthode suivante à la `GraphManager` classe.

    ```Swift
    public func getCalendarView(viewStart: String,
                                viewEnd: String,
                                completion: @escaping(Data?, Error?) -> Void) {
        // GET /me/calendarview
        // Set start and end of the view
        let start = "startDateTime=\(viewStart)"
        let end = "endDateTime=\(viewEnd)"
        // Only return these fields in results
        let select = "$select=subject,organizer,start,end"
        // Sort results by when they were created, newest first
        let orderBy = "$orderby=start/dateTime"
        // Request at most 25 results
        let top = "$top=25"

        let eventsRequest = NSMutableURLRequest(url: URL(string: "\(MSGraphBaseURL)/me/calendarview?\(start)&\(end)&\(select)&\(orderBy)&\(top)")!)

        // Add the Prefer: outlook.timezone header to get start and end times
        // in user's time zone
        eventsRequest.addValue("outlook.timezone=\"\(self.userTimeZone)\"", forHTTPHeaderField: "Prefer")

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
    > Réfléchissez à ce que fait `getCalendarView` le code.
    >
    > - L’URL qui sera appelée est `/v1.0/me/calendarview`.
    >   - Les `startDateTime` `endDateTime` paramètres de requête définissent le début et la fin de l’affichage Calendrier.
    >   - Le paramètre de requête limite les champs renvoyés pour chaque événement à ceux que `select` l’affichage utilisera réellement.
    >   - Le `orderby` paramètre de requête trie les résultats par heure de début.
    >   - Le `top` paramètre de requête demande 25 résultats par page.
    >   - L’en-tête indique à Microsoft Graph de renvoyer les heures de début et de fin de chaque événement dans le `Prefer: outlook.timezone` fuseau horaire de l’utilisateur.

1. Créez un **fichier Swift** dans le **projet GraphTutorial** nommé **GraphToIana.swift**. Ajoutez le code suivant au fichier.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/GraphToIana.swift" id="GraphToIanaSnippet":::

    Cette opération permet de rechercher simplement un identificateur de fuseau horaire IANA basé sur le nom de fuseau horaire renvoyé par Microsoft Graph.

1. Ouvrez **CalendarViewController.swift** et remplacez tout son contenu par le code suivant.

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

            // Calculate the start and end of the current week
            let timeZone = GraphToIana.getIanaIdentifier(graphIdentifer: GraphManager.instance.userTimeZone)
            let now = Date()
            var calendar = Calendar(identifier: .gregorian)
            calendar.timeZone = TimeZone(identifier: timeZone)!

            let startOfWeek = calendar.dateComponents([.calendar, .yearForWeekOfYear, .weekOfYear], from: now).date!
            let endOfWeek = calendar.date(byAdding: .day, value: 7, to: startOfWeek)!

            // Convert start and end to ISO 8601 strings
            let isoFormatter = ISO8601DateFormatter()
            let viewStart = isoFormatter.string(from: startOfWeek)
            let viewEnd = isoFormatter.string(from: endOfWeek)

            GraphManager.instance.getCalendarView(viewStart: viewStart, viewEnd: viewEnd) {
                (data: Data?, error: Error?) in
                DispatchQueue.main.async {
                    self.spinner.stop()

                    // TEMPORARY
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

Vous pouvez maintenant exécuter l’application, vous connectez et appuyez sur l’élément **de** navigation Calendrier dans le menu. Vous devriez voir un vidage JSON des événements dans l’application.

## <a name="display-the-results"></a>Afficher les résultats

Vous pouvez désormais remplacer le vidage JSON par un autre qui permet d’afficher les résultats de manière conviviale. Dans cette section, vous allez modifier la fonction pour renvoyer des objets fortement typés et modifier pour utiliser une vue de `getCalendarView` tableau pour afficher les `CalendarViewController` événements.

1. Ouvrez **GraphManager.swift.** Remplacez la fonction `getCalendarView` existante par ce qui suit.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/GraphManager.swift" id="GetEventsSnippet" highlight="3,28-49":::

1. Créez un **fichier de classe Cocoa Touch** dans le projet **GraphTutorial** nommé `CalendarTableViewController.swift` . Choisissez **UITableViewController dans** la **sous-classe du** champ.

1. Ouvrez **CalendarTableViewController.swift** et remplacez son contenu par ce qui suit.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewController.swift" id="CalendarTableViewControllerSnippet":::

1. Créez un **fichier de classe Cocoa Touch** dans le projet **GraphTutorial** nommé `CalendarTableViewCell.swift` . Choisissez **UITableViewCell dans** la **sous-classe du** champ.

1. Ouvrez **CalendarTableViewCell.swift** et ajoutez les propriétés suivantes à la `CalendarTableViewCell` classe.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.swift" id="PropertiesSnippet":::

1. Ouvrez **Main.storyboard et** recherchez la **scène de calendrier.** Supprimez le défilement de l’affichage racine.
1. À **l’aide de la** bibliothèque, ajoutez une **barre de navigation** en haut de l’affichage.
1. Double-cliquez sur **le titre dans** la barre de navigation et mettez-le à `Calendar` jour.
1. À **l’aide de la bibliothèque,** ajoutez un élément de bouton **de** barre à droite de la barre de navigation.
1. Sélectionnez le nouveau bouton de barre, puis l’inspecteur **d’attributs.** Modifier **l’image** **sur plus**.
1. Ajoutez **un affichage conteneur** de la **bibliothèque** à l’affichage sous la barre de navigation. Resize the container view to take all of the remaining space in the view.
1. Définissez les contraintes sur la barre de navigation et l’affichage conteneur comme suit.

    - **Barre de navigation**
        - Ajouter une contrainte : Hauteur, valeur : 44
        - Add constraint: Leading space to Safe Area, value: 0
        - Add constraint: Trailing space to Safe Area, value: 0
        - Ajouter une contrainte : espace supérieur à la zone sécurisée, valeur : 0
    - **Affichage conteneur**
        - Add constraint: Leading space to Safe Area, value: 0
        - Add constraint: Trailing space to Safe Area, value: 0
        - Ajouter une contrainte : espace supérieur dans la barre de navigation en bas, valeur : 0
        - Ajouter une contrainte : espace inférieur à la zone sécurisée, valeur : 0

1. Recherchez le deuxième contrôleur d’affichage ajouté au storyboard lorsque vous avez ajouté l’affichage conteneur. Il est connecté à la **scène de calendrier** par une ségue d’incorporation. Sélectionnez ce contrôleur et utilisez **l’inspecteur d’identité** pour modifier **la** classe **en CalendarTableViewController**.
1. Supprimez **l’affichage** du **contrôleur d’affichage de table de calendrier.**
1. Ajoutez **un affichage Table à** partir de la **bibliothèque** au **contrôleur d’affichage de table de calendrier.**
1. Sélectionnez l’affichage Tableau, puis **l’inspecteur d’attributs.** Définissez **les cellules prototype** sur **1**.
1. Faites glisser le bord inférieur de la cellule prototype pour vous donner une zone plus grande à travailler.
1. Utilisez la **bibliothèque pour** ajouter trois **étiquettes** à la cellule prototype.
1. Sélectionnez la cellule prototype, puis l’inspecteur **d’identité.** Change **Class** to **CalendarTableViewCell**.
1. Sélectionnez **l’inspecteur d’attributs** et définissez **l’identificateur** sur `EventCell` .
1. Une fois **EventCell** sélectionné, sélectionnez l’Inspecteur de connexions et connectez-vous, ainsi qu’aux étiquettes que vous avez **ajoutées** à la cellule `durationLabel` dans le `organizerLabel` `subjectLabel` storyboard.
1. Définissez les propriétés et les contraintes sur les trois étiquettes comme suit.

    - **Étiquette d’objet**
        - Add constraint: Leading space to Content View Leading Margin, value: 0
        - Add constraint: Trailing space to Content View Trailing Margin, value: 0
        - Ajouter une contrainte : espace supérieur à la marge supérieure de l’affichage de contenu, valeur : 0
    - **Étiquette d’organisateur**
        - Police : System 12.0
        - Ajouter une contrainte : Hauteur, valeur : 15
        - Add constraint: Leading space to Content View Leading Margin, value: 0
        - Add constraint: Trailing space to Content View Trailing Margin, value: 0
        - Add constraint: Top space to Subject Label Bottom, value: Standard
    - **Étiquette de durée**
        - Police : System 12.0
        - Couleur : gris foncé
        - Ajouter une contrainte : Hauteur, valeur : 15
        - Add constraint: Leading space to Content View Leading Margin, value: 0
        - Add constraint: Trailing space to Content View Trailing Margin, value: 0
        - Add constraint: Top space to Organizer Label Bottom, value: Standard
        - Ajouter une contrainte : espace inférieur à la marge inférieure de l’affichage du contenu, valeur : 0

1. Sélectionnez **EventCell,** puis **l’inspecteur de taille.** Activer **automatique pour** la hauteur de **ligne**.

    ![Capture d’écran des contrôleurs d’affichage de calendrier et de table de calendrier](images/calendar-table-storyboard.png)

1. Ouvrez **CalendarViewController.swift** et remplacez son contenu par le code suivant.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.swift" id="CalendarViewSnippet":::

1. Exécutez l’application, connectez-vous et appuyez sur **l’onglet** Calendrier. Vous devriez voir la liste des événements.

    ![Capture d’écran du tableau des événements](images/calendar-list.png)

<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser le [Kit de développement logiciel (SDK) Microsoft Graph pour effectuer des](https://github.com/microsoftgraph/msgraph-sdk-objc) appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Obtenir des événements de calendrier à partir d’Outlook

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

### <a name="update-getevents"></a>Mettre à jour getEvents

1. Ouvrez **GraphManager. Swift**. Modifiez la `getEvents` déclaration de la fonction comme suit.

    ```Swift
    public func getEvents(completion: @escaping([MSGraphEvent]?, Error?) -> Void)
    ```

1. Remplacez la ligne `completion(eventsData, nil)` par le code suivant.

    ```Swift
    do {
        // Deserialize response as events collection
        let eventsCollection = try MSCollection(data: eventsData)
        var eventArray: [MSGraphEvent] = []

        eventsCollection.value.forEach({
            (rawEvent: Any) in
            // Convert JSON to a dictionary
            guard let eventDict = rawEvent as? [String: Any] else {
                return
            }

            // Deserialize event from the dictionary
            let event = MSGraphEvent(dictionary: eventDict)!
            eventArray.append(event)
        })

        // Return the array
        completion(eventArray, nil)
    } catch {
        completion(nil, error)
    }
    ```

### <a name="update-calendarviewcontroller"></a>Mettre à jour CalendarViewController

1. Créez un fichier de **classe Touch de cacao** dans le projet **GraphTutorial** nommé `CalendarTableViewCell.swift`. Choisissez **UITableViewCell** dans la sous- **classe du** champ.
1. Ouvrez **CalendarTableViewCell. Swift** et ajoutez le code suivant à la `CalendarTableViewCell` classe.

    ```Swift
    @IBOutlet var subjectLabel: UILabel!
    @IBOutlet var organizerLabel: UILabel!
    @IBOutlet var durationLabel: UILabel!

    var subject: String? {
        didSet {
            subjectLabel.text = subject
        }
    }

    var organizer: String? {
        didSet {
            organizerLabel.text = organizer
        }
    }

    var duration: String? {
        didSet {
            durationLabel.text = duration
        }
    }
    ```

1. Ouvrez **main. Storyboard** et localisez la **scène de calendrier**. Sélectionnez l' **affichage** dans la **scène de calendrier** et supprimez-le.

    ![Capture d’écran de l’affichage dans la scène de calendrier](./images/view-in-calendar-scene.png)

1. Ajoutez un **affichage tableau** de la **bibliothèque** à la **scène calendrier**.
1. Sélectionnez l’affichage tableau, puis sélectionnez l' **inspecteur d’attributs**. Définissez les **cellules prototype** sur **1**.
1. Utilisez la **bibliothèque** pour ajouter trois **étiquettes** à la cellule prototype.
1. Sélectionnez la cellule prototype, puis l' **inspecteur d’identité**. Modifiez **Class** en **CalendarTableViewCell**.
1. Sélectionnez l' **inspecteur d’attributs** et définissez **identificateur** sur `EventCell`.
1. Une fois le **EventCell** sélectionné, sélectionnez l' **inspecteur** de connexions `durationLabel`et `organizerLabel`Connectez- `subjectLabel` vous, puis les étiquettes que vous avez ajoutées à la cellule sur la table de montage séquentiel.

    ![Capture d’écran de la mise en page de cellule prototype](./images/prototype-cell-layout.png)

1. Ouvrez **CalendarViewController. Swift** et remplacez son contenu par le code suivant.

    ```Swift
    import UIKit
    import MSGraphClientModels

    class CalendarViewController: UITableViewController {

        private let tableCellIdentifier = "EventCell"
        private let spinner = SpinnerViewController()
        private var events: [MSGraphEvent]?

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.
            self.spinner.start(container: self)

            GraphManager.instance.getEvents {
                (eventArray: [MSGraphEvent]?, error: Error?) in
                DispatchQueue.main.async {
                    self.spinner.stop()

                    guard let events = eventArray, error == nil else {
                        // Show the error
                        let alert = UIAlertController(title: "Error getting events",
                                                      message: error.debugDescription,
                                                      preferredStyle: .alert)

                        alert.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
                        self.present(alert, animated: true)
                        return
                    }

                    self.events = events
                    self.tableView.reloadData()
                }
            }
        }

        // Number of sections, always 1
        override func numberOfSections(in tableView: UITableView) -> Int {
            return 1
        }

        // Return the number of events in the table
        override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
            return events?.count ?? 0
        }

        override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
            let cell = tableView.dequeueReusableCell(withIdentifier: tableCellIdentifier, for: indexPath) as! CalendarTableViewCell

            // Get the event that corresponds to the row
            let event = events?[indexPath.row]

            // Configure the cell
            cell.subject = event?.subject
            cell.organizer = event?.organizer?.emailAddress?.name

            // Build a duration string
            let duration = "\(self.formatGraphDateTime(dateTime: event?.start)) to \(self.formatGraphDateTime(dateTime: event?.end))"
            cell.duration = duration

            return cell
        }

        private func formatGraphDateTime(dateTime: MSGraphDateTimeTimeZone?) -> String {
            guard let graphDateTime = dateTime else {
                return ""
            }

            // Create a formatter to parse Graph's date format
            let isoFormatter = DateFormatter()
            isoFormatter.dateFormat = "yyyy-MM-dd'T'HH:mm:ss.SSSSSSS"

            // Specify the timezone
            isoFormatter.timeZone = TimeZone(identifier: graphDateTime.timeZone!)

            let date = isoFormatter.date(from: graphDateTime.dateTime)

            // Output like 5/5/2019, 2:00 PM
            let dateFormatter = DateFormatter()
            dateFormatter.dateStyle = .short
            dateFormatter.timeStyle = .short

            return dateFormatter.string(from: date!)
        }
    }
    ```

1. Exécutez l’application, connectez-vous, puis appuyez sur l’onglet **calendrier** . La liste des événements doit s’afficher.

    ![Capture d’écran du tableau des événements](./images/calendar-list.png)

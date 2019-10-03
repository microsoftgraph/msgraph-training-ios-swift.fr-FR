<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="1aa7a-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="1aa7a-102">Pour cette application, vous allez utiliser le [Kit de développement logiciel (SDK) Microsoft Graph pour effectuer des](https://github.com/microsoftgraph/msgraph-sdk-objc) appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-102">For this application, you will use the [Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="1aa7a-103">Obtenir des événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="1aa7a-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="1aa7a-104">Dans cette section, vous allez étendre `GraphManager` la classe pour ajouter une fonction afin d’obtenir les événements de l' `CalendarViewController` utilisateur et la mise à jour pour utiliser ces nouvelles fonctions.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-104">In this section you will extend the `GraphManager` class to add a function to get the user's events and update `CalendarViewController` to use these new functions.</span></span>

1. <span data-ttu-id="1aa7a-105">Ouvrez **GraphManager. Swift** et ajoutez la méthode suivante à la `GraphManager` classe.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-105">Open **GraphManager.swift** and add the following method to the `GraphManager` class.</span></span>

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
    > <span data-ttu-id="1aa7a-106">Examinez le contenu du `getEvents` code.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-106">Consider what the code in `getEvents` is doing.</span></span>
    >
    > - <span data-ttu-id="1aa7a-107">L’URL qui sera appelée est `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-107">The URL that will be called is `/v1.0/me/events`.</span></span>
    > - <span data-ttu-id="1aa7a-108">Le `select` paramètre de requête limite les champs renvoyés pour chaque événement à seulement ceux que l’application utilisera réellement.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-108">The `select` query parameter limits the fields returned for each events to just those the app will actually use.</span></span>
    > - <span data-ttu-id="1aa7a-109">Le `orderby` paramètre de requête trie les résultats en fonction de la date et de l’heure de leur création, avec l’élément le plus récent en premier.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-109">The `orderby` query parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="1aa7a-110">Ouvrez **CalendarViewController. Swift** et remplacez l’intégralité de son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-110">Open **CalendarViewController.swift** and replace its entire contents with the following code.</span></span>

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

<span data-ttu-id="1aa7a-111">Vous pouvez maintenant exécuter l’application, se connecter et appuyer sur l’élément de navigation **calendrier** dans le menu.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-111">You can now run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="1aa7a-112">Vous devriez voir un dump JSON des événements dans l’application.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-112">You should see a JSON dump of the events in the app.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="1aa7a-113">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="1aa7a-113">Display the results</span></span>

<span data-ttu-id="1aa7a-114">À présent, vous pouvez remplacer le vidage JSON par un texte pour afficher les résultats de manière conviviale.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-114">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="1aa7a-115">Dans cette section, vous allez modifier la `getEvents` fonction pour renvoyer des objets fortement typés et modifier `CalendarViewController` pour qu’elle affiche les événements à l’aide d’un affichage tableau.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-115">In this section, you will modify the `getEvents` function to return strongly-typed objects, and modify `CalendarViewController` to use a table view to render the events.</span></span>

### <a name="update-getevents"></a><span data-ttu-id="1aa7a-116">Mettre à jour getEvents</span><span class="sxs-lookup"><span data-stu-id="1aa7a-116">Update getEvents</span></span>

1. <span data-ttu-id="1aa7a-117">Ouvrez **GraphManager. Swift**.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-117">Open **GraphManager.swift**.</span></span> <span data-ttu-id="1aa7a-118">Modifiez la `getEvents` déclaration de la fonction comme suit.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-118">Change the `getEvents` function declaration to the following.</span></span>

    ```Swift
    public func getEvents(completion: @escaping([MSGraphEvent]?, Error?) -> Void)
    ```

1. <span data-ttu-id="1aa7a-119">Remplacez la ligne `completion(eventsData, nil)` par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-119">Replace the `completion(eventsData, nil)` line with the following code.</span></span>

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

### <a name="update-calendarviewcontroller"></a><span data-ttu-id="1aa7a-120">Mettre à jour CalendarViewController</span><span class="sxs-lookup"><span data-stu-id="1aa7a-120">Update CalendarViewController</span></span>

1. <span data-ttu-id="1aa7a-121">Créez un fichier de **classe Touch de cacao** dans le projet **GraphTutorial** nommé `CalendarTableViewCell.swift`.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-121">Create a new **Cocoa Touch Class** file in the **GraphTutorial** project named `CalendarTableViewCell.swift`.</span></span> <span data-ttu-id="1aa7a-122">Choisissez **UITableViewCell** dans la sous- **classe du** champ.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-122">Choose **UITableViewCell** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="1aa7a-123">Ouvrez **CalendarTableViewCell. Swift** et ajoutez le code suivant à la `CalendarTableViewCell` classe.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-123">Open **CalendarTableViewCell.swift** and add the following code to the `CalendarTableViewCell` class.</span></span>

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

1. <span data-ttu-id="1aa7a-124">Ouvrez **main. Storyboard** et localisez la **scène de calendrier**.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-124">Open **Main.storyboard** and locate the **Calendar Scene**.</span></span> <span data-ttu-id="1aa7a-125">Sélectionnez l' **affichage** dans la **scène de calendrier** et supprimez-le.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-125">Select the **View** in the **Calendar Scene** and delete it.</span></span>

    ![Capture d’écran de l’affichage dans la scène de calendrier](./images/view-in-calendar-scene.png)

1. <span data-ttu-id="1aa7a-127">Ajoutez un **affichage tableau** de la **bibliothèque** à la **scène calendrier**.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-127">Add a **Table View** from the **Library** to the **Calendar Scene**.</span></span>
1. <span data-ttu-id="1aa7a-128">Sélectionnez l’affichage tableau, puis sélectionnez l' **inspecteur d’attributs**.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-128">Select the table view, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="1aa7a-129">Définissez les **cellules prototype** sur **1**.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-129">Set **Prototype Cells** to **1**.</span></span>
1. <span data-ttu-id="1aa7a-130">Utilisez la **bibliothèque** pour ajouter trois **étiquettes** à la cellule prototype.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-130">Use the **Library** to add three **Labels** to the prototype cell.</span></span>
1. <span data-ttu-id="1aa7a-131">Sélectionnez la cellule prototype, puis l' **inspecteur d’identité**.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-131">Select the prototype cell, then select the **Identity Inspector**.</span></span> <span data-ttu-id="1aa7a-132">Modifiez **Class** en **CalendarTableViewCell**.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-132">Change **Class** to **CalendarTableViewCell**.</span></span>
1. <span data-ttu-id="1aa7a-133">Sélectionnez l' **inspecteur d’attributs** et définissez **identificateur** sur `EventCell`.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-133">Select the **Attributes Inspector** and set **Identifier** to `EventCell`.</span></span>
1. <span data-ttu-id="1aa7a-134">Une fois le **EventCell** sélectionné, sélectionnez l' **inspecteur** de connexions `durationLabel`et `organizerLabel`Connectez- `subjectLabel` vous, puis les étiquettes que vous avez ajoutées à la cellule sur la table de montage séquentiel.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-134">With the **EventCell** selected, select the **Connections Inspector** and connect `durationLabel`, `organizerLabel`, and `subjectLabel` to the labels you added to the cell on the storyboard.</span></span>

    ![Capture d’écran de la mise en page de cellule prototype](./images/prototype-cell-layout.png)

1. <span data-ttu-id="1aa7a-136">Ouvrez **CalendarViewController. Swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-136">Open **CalendarViewController.swift** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="1aa7a-137">Exécutez l’application, connectez-vous, puis appuyez sur l’onglet **calendrier** . La liste des événements doit s’afficher.</span><span class="sxs-lookup"><span data-stu-id="1aa7a-137">Run the app, sign in, and tap the **Calendar** tab. You should see the list of events.</span></span>

    ![Capture d’écran du tableau des événements](./images/calendar-list.png)

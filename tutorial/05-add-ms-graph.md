<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="55841-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="55841-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="55841-102">Pour cette application, vous allez utiliser le [SDK Microsoft Graph pour l’objectif C](https://github.com/microsoftgraph/msgraph-sdk-objc) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="55841-102">For this application, you will use the [Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="55841-103">Récupérer les événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="55841-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="55841-104">Dans cette section, vous allez étendre la classe pour ajouter une fonction afin d’obtenir les événements de l’utilisateur pour la semaine en cours et mettre à jour pour utiliser `GraphManager` `CalendarViewController` ces nouvelles fonctions.</span><span class="sxs-lookup"><span data-stu-id="55841-104">In this section you will extend the `GraphManager` class to add a function to get the user's events for the current week and update `CalendarViewController` to use these new functions.</span></span>

1. <span data-ttu-id="55841-105">Ouvrez **GraphManager.swift** et ajoutez la méthode suivante à la `GraphManager` classe.</span><span class="sxs-lookup"><span data-stu-id="55841-105">Open **GraphManager.swift** and add the following method to the `GraphManager` class.</span></span>

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
    > <span data-ttu-id="55841-106">Réfléchissez à ce que fait `getCalendarView` le code.</span><span class="sxs-lookup"><span data-stu-id="55841-106">Consider what the code in `getCalendarView` is doing.</span></span>
    >
    > - <span data-ttu-id="55841-107">L’URL qui sera appelée est `/v1.0/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="55841-107">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
    >   - <span data-ttu-id="55841-108">Les `startDateTime` `endDateTime` paramètres de requête définissent le début et la fin de l’affichage Calendrier.</span><span class="sxs-lookup"><span data-stu-id="55841-108">The `startDateTime` and `endDateTime` query parameters define the start and end of the calendar view.</span></span>
    >   - <span data-ttu-id="55841-109">Le paramètre de requête limite les champs renvoyés pour chaque événement à ceux que `select` l’affichage utilisera réellement.</span><span class="sxs-lookup"><span data-stu-id="55841-109">The `select` query parameter limits the fields returned for each events to just those the view will actually use.</span></span>
    >   - <span data-ttu-id="55841-110">Le `orderby` paramètre de requête trie les résultats par heure de début.</span><span class="sxs-lookup"><span data-stu-id="55841-110">The `orderby` query parameter sorts the results by start time.</span></span>
    >   - <span data-ttu-id="55841-111">Le `top` paramètre de requête demande 25 résultats par page.</span><span class="sxs-lookup"><span data-stu-id="55841-111">The `top` query parameter requests 25 results per page.</span></span>
    >   - <span data-ttu-id="55841-112">L’en-tête indique à Microsoft Graph de renvoyer les heures de début et de fin de chaque événement dans le `Prefer: outlook.timezone` fuseau horaire de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="55841-112">the `Prefer: outlook.timezone` header causes the Microsoft Graph to return the start and end times of each event in the user's time zone.</span></span>

1. <span data-ttu-id="55841-113">Créez un **fichier Swift** dans le **projet GraphTutorial** nommé **GraphToIana.swift**.</span><span class="sxs-lookup"><span data-stu-id="55841-113">Create a new **Swift File** in the **GraphTutorial** project named **GraphToIana.swift**.</span></span> <span data-ttu-id="55841-114">Ajoutez le code suivant au fichier.</span><span class="sxs-lookup"><span data-stu-id="55841-114">Add the following code to the file.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/GraphToIana.swift" id="GraphToIanaSnippet":::

    <span data-ttu-id="55841-115">Cette opération permet de rechercher simplement un identificateur de fuseau horaire IANA basé sur le nom de fuseau horaire renvoyé par Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="55841-115">This does a simple lookup to find an IANA time zone identifier based on the time zone name returned by Microsoft Graph.</span></span>

1. <span data-ttu-id="55841-116">Ouvrez **CalendarViewController.swift** et remplacez tout son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="55841-116">Open **CalendarViewController.swift** and replace its entire contents with the following code.</span></span>

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

<span data-ttu-id="55841-117">Vous pouvez maintenant exécuter l’application, vous connectez et appuyez sur l’élément **de** navigation Calendrier dans le menu.</span><span class="sxs-lookup"><span data-stu-id="55841-117">You can now run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="55841-118">Vous devriez voir un vidage JSON des événements dans l’application.</span><span class="sxs-lookup"><span data-stu-id="55841-118">You should see a JSON dump of the events in the app.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="55841-119">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="55841-119">Display the results</span></span>

<span data-ttu-id="55841-120">Vous pouvez désormais remplacer le vidage JSON par un autre qui permet d’afficher les résultats de manière conviviale.</span><span class="sxs-lookup"><span data-stu-id="55841-120">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="55841-121">Dans cette section, vous allez modifier la fonction pour renvoyer des objets fortement typés et modifier pour utiliser une vue de `getCalendarView` tableau pour afficher les `CalendarViewController` événements.</span><span class="sxs-lookup"><span data-stu-id="55841-121">In this section, you will modify the `getCalendarView` function to return strongly-typed objects, and modify `CalendarViewController` to use a table view to render the events.</span></span>

1. <span data-ttu-id="55841-122">Ouvrez **GraphManager.swift.**</span><span class="sxs-lookup"><span data-stu-id="55841-122">Open **GraphManager.swift**.</span></span> <span data-ttu-id="55841-123">Remplacez la fonction `getCalendarView` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="55841-123">Replace the existing `getCalendarView` function with the following.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/GraphManager.swift" id="GetEventsSnippet" highlight="3,28-49":::

1. <span data-ttu-id="55841-124">Créez un **fichier de classe Cocoa Touch** dans le projet **GraphTutorial** nommé `CalendarTableViewController.swift` .</span><span class="sxs-lookup"><span data-stu-id="55841-124">Create a new **Cocoa Touch Class** file in the **GraphTutorial** project named `CalendarTableViewController.swift`.</span></span> <span data-ttu-id="55841-125">Choisissez **UITableViewController dans** la **sous-classe du** champ.</span><span class="sxs-lookup"><span data-stu-id="55841-125">Choose **UITableViewController** in the **Subclass of** field.</span></span>

1. <span data-ttu-id="55841-126">Ouvrez **CalendarTableViewController.swift** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="55841-126">Open **CalendarTableViewController.swift** and replace its contents with the following.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewController.swift" id="CalendarTableViewControllerSnippet":::

1. <span data-ttu-id="55841-127">Créez un **fichier de classe Cocoa Touch** dans le projet **GraphTutorial** nommé `CalendarTableViewCell.swift` .</span><span class="sxs-lookup"><span data-stu-id="55841-127">Create a new **Cocoa Touch Class** file in the **GraphTutorial** project named `CalendarTableViewCell.swift`.</span></span> <span data-ttu-id="55841-128">Choisissez **UITableViewCell dans** la **sous-classe du** champ.</span><span class="sxs-lookup"><span data-stu-id="55841-128">Choose **UITableViewCell** in the **Subclass of** field.</span></span>

1. <span data-ttu-id="55841-129">Ouvrez **CalendarTableViewCell.swift** et ajoutez les propriétés suivantes à la `CalendarTableViewCell` classe.</span><span class="sxs-lookup"><span data-stu-id="55841-129">Open **CalendarTableViewCell.swift** and add the following properties to the `CalendarTableViewCell` class.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.swift" id="PropertiesSnippet":::

1. <span data-ttu-id="55841-130">Ouvrez **Main.storyboard et** recherchez la **scène de calendrier.**</span><span class="sxs-lookup"><span data-stu-id="55841-130">Open **Main.storyboard** and locate the **Calendar Scene**.</span></span> <span data-ttu-id="55841-131">Supprimez le défilement de l’affichage racine.</span><span class="sxs-lookup"><span data-stu-id="55841-131">Delete the scroll view from the root view.</span></span>
1. <span data-ttu-id="55841-132">À **l’aide de la** bibliothèque, ajoutez une **barre de navigation** en haut de l’affichage.</span><span class="sxs-lookup"><span data-stu-id="55841-132">Using the **Library**, add a **Navigation Bar** to the top of the view.</span></span>
1. <span data-ttu-id="55841-133">Double-cliquez sur **le titre dans** la barre de navigation et mettez-le à `Calendar` jour.</span><span class="sxs-lookup"><span data-stu-id="55841-133">Double-click the **Title** in the navigation bar and update it to `Calendar`.</span></span>
1. <span data-ttu-id="55841-134">À **l’aide de la bibliothèque,** ajoutez un élément de bouton **de** barre à droite de la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="55841-134">Using the **Library**, add a **Bar Button Item** to the right-hand side of the navigation bar.</span></span>
1. <span data-ttu-id="55841-135">Sélectionnez le nouveau bouton de barre, puis l’inspecteur **d’attributs.**</span><span class="sxs-lookup"><span data-stu-id="55841-135">Select the new bar button, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="55841-136">Modifier **l’image** **sur plus**.</span><span class="sxs-lookup"><span data-stu-id="55841-136">Change **Image** to **plus**.</span></span>
1. <span data-ttu-id="55841-137">Ajoutez **un affichage conteneur** de la **bibliothèque** à l’affichage sous la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="55841-137">Add a **Container View** from the **Library** to the view under the navigation bar.</span></span> <span data-ttu-id="55841-138">Resize the container view to take all of the remaining space in the view.</span><span class="sxs-lookup"><span data-stu-id="55841-138">Resize the container view to take all of the remaining space in the view.</span></span>
1. <span data-ttu-id="55841-139">Définissez les contraintes sur la barre de navigation et l’affichage conteneur comme suit.</span><span class="sxs-lookup"><span data-stu-id="55841-139">Set constraints on the navigation bar and container view as follows.</span></span>

    - <span data-ttu-id="55841-140">**Barre de navigation**</span><span class="sxs-lookup"><span data-stu-id="55841-140">**Navigation Bar**</span></span>
        - <span data-ttu-id="55841-141">Ajouter une contrainte : Hauteur, valeur : 44</span><span class="sxs-lookup"><span data-stu-id="55841-141">Add constraint: Height, value: 44</span></span>
        - <span data-ttu-id="55841-142">Add constraint: Leading space to Safe Area, value: 0</span><span class="sxs-lookup"><span data-stu-id="55841-142">Add constraint: Leading space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="55841-143">Add constraint: Trailing space to Safe Area, value: 0</span><span class="sxs-lookup"><span data-stu-id="55841-143">Add constraint: Trailing space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="55841-144">Ajouter une contrainte : espace supérieur à la zone sécurisée, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="55841-144">Add constraint: Top space to Safe Area, value: 0</span></span>
    - <span data-ttu-id="55841-145">**Affichage conteneur**</span><span class="sxs-lookup"><span data-stu-id="55841-145">**Container View**</span></span>
        - <span data-ttu-id="55841-146">Add constraint: Leading space to Safe Area, value: 0</span><span class="sxs-lookup"><span data-stu-id="55841-146">Add constraint: Leading space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="55841-147">Add constraint: Trailing space to Safe Area, value: 0</span><span class="sxs-lookup"><span data-stu-id="55841-147">Add constraint: Trailing space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="55841-148">Ajouter une contrainte : espace supérieur dans la barre de navigation en bas, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="55841-148">Add constraint: Top space to Navigation Bar Bottom, value: 0</span></span>
        - <span data-ttu-id="55841-149">Ajouter une contrainte : espace inférieur à la zone sécurisée, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="55841-149">Add constraint: Bottom space to Safe Area, value: 0</span></span>

1. <span data-ttu-id="55841-150">Recherchez le deuxième contrôleur d’affichage ajouté au storyboard lorsque vous avez ajouté l’affichage conteneur.</span><span class="sxs-lookup"><span data-stu-id="55841-150">Locate the second view controller added to the storyboard when you added the container view.</span></span> <span data-ttu-id="55841-151">Il est connecté à la **scène de calendrier** par une ségue d’incorporation.</span><span class="sxs-lookup"><span data-stu-id="55841-151">It is connected to the **Calendar Scene** by an embed segue.</span></span> <span data-ttu-id="55841-152">Sélectionnez ce contrôleur et utilisez **l’inspecteur d’identité** pour modifier **la** classe **en CalendarTableViewController**.</span><span class="sxs-lookup"><span data-stu-id="55841-152">Select this controller and use the **Identity Inspector** to change **Class** to **CalendarTableViewController**.</span></span>
1. <span data-ttu-id="55841-153">Supprimez **l’affichage** du **contrôleur d’affichage de table de calendrier.**</span><span class="sxs-lookup"><span data-stu-id="55841-153">Delete the **View** from the **Calendar Table View Controller**.</span></span>
1. <span data-ttu-id="55841-154">Ajoutez **un affichage Table à** partir de la **bibliothèque** au **contrôleur d’affichage de table de calendrier.**</span><span class="sxs-lookup"><span data-stu-id="55841-154">Add a **Table View** from the **Library** to the **Calendar Table View Controller**.</span></span>
1. <span data-ttu-id="55841-155">Sélectionnez l’affichage Tableau, puis **l’inspecteur d’attributs.**</span><span class="sxs-lookup"><span data-stu-id="55841-155">Select the table view, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="55841-156">Définissez **les cellules prototype** sur **1**.</span><span class="sxs-lookup"><span data-stu-id="55841-156">Set **Prototype Cells** to **1**.</span></span>
1. <span data-ttu-id="55841-157">Faites glisser le bord inférieur de la cellule prototype pour vous donner une zone plus grande à travailler.</span><span class="sxs-lookup"><span data-stu-id="55841-157">Drag the bottom edge of the prototype cell to give you a larger area to work with.</span></span>
1. <span data-ttu-id="55841-158">Utilisez la **bibliothèque pour** ajouter trois **étiquettes** à la cellule prototype.</span><span class="sxs-lookup"><span data-stu-id="55841-158">Use the **Library** to add three **Labels** to the prototype cell.</span></span>
1. <span data-ttu-id="55841-159">Sélectionnez la cellule prototype, puis l’inspecteur **d’identité.**</span><span class="sxs-lookup"><span data-stu-id="55841-159">Select the prototype cell, then select the **Identity Inspector**.</span></span> <span data-ttu-id="55841-160">Change **Class** to **CalendarTableViewCell**.</span><span class="sxs-lookup"><span data-stu-id="55841-160">Change **Class** to **CalendarTableViewCell**.</span></span>
1. <span data-ttu-id="55841-161">Sélectionnez **l’inspecteur d’attributs** et définissez **l’identificateur** sur `EventCell` .</span><span class="sxs-lookup"><span data-stu-id="55841-161">Select the **Attributes Inspector** and set **Identifier** to `EventCell`.</span></span>
1. <span data-ttu-id="55841-162">Une fois **EventCell** sélectionné, sélectionnez l’Inspecteur de connexions et connectez-vous, ainsi qu’aux étiquettes que vous avez **ajoutées** à la cellule `durationLabel` dans le `organizerLabel` `subjectLabel` storyboard.</span><span class="sxs-lookup"><span data-stu-id="55841-162">With the **EventCell** selected, select the **Connections Inspector** and connect `durationLabel`, `organizerLabel`, and `subjectLabel` to the labels you added to the cell on the storyboard.</span></span>
1. <span data-ttu-id="55841-163">Définissez les propriétés et les contraintes sur les trois étiquettes comme suit.</span><span class="sxs-lookup"><span data-stu-id="55841-163">Set the properties and constraints on the three labels as follows.</span></span>

    - <span data-ttu-id="55841-164">**Étiquette d’objet**</span><span class="sxs-lookup"><span data-stu-id="55841-164">**Subject Label**</span></span>
        - <span data-ttu-id="55841-165">Add constraint: Leading space to Content View Leading Margin, value: 0</span><span class="sxs-lookup"><span data-stu-id="55841-165">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="55841-166">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span><span class="sxs-lookup"><span data-stu-id="55841-166">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="55841-167">Ajouter une contrainte : espace supérieur à la marge supérieure de l’affichage de contenu, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="55841-167">Add constraint: Top space to Content View Top Margin, value: 0</span></span>
    - <span data-ttu-id="55841-168">**Étiquette d’organisateur**</span><span class="sxs-lookup"><span data-stu-id="55841-168">**Organizer Label**</span></span>
        - <span data-ttu-id="55841-169">Police : System 12.0</span><span class="sxs-lookup"><span data-stu-id="55841-169">Font: System 12.0</span></span>
        - <span data-ttu-id="55841-170">Ajouter une contrainte : Hauteur, valeur : 15</span><span class="sxs-lookup"><span data-stu-id="55841-170">Add constraint: Height, value: 15</span></span>
        - <span data-ttu-id="55841-171">Add constraint: Leading space to Content View Leading Margin, value: 0</span><span class="sxs-lookup"><span data-stu-id="55841-171">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="55841-172">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span><span class="sxs-lookup"><span data-stu-id="55841-172">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="55841-173">Add constraint: Top space to Subject Label Bottom, value: Standard</span><span class="sxs-lookup"><span data-stu-id="55841-173">Add constraint: Top space to Subject Label Bottom, value: Standard</span></span>
    - <span data-ttu-id="55841-174">**Étiquette de durée**</span><span class="sxs-lookup"><span data-stu-id="55841-174">**Duration Label**</span></span>
        - <span data-ttu-id="55841-175">Police : System 12.0</span><span class="sxs-lookup"><span data-stu-id="55841-175">Font: System 12.0</span></span>
        - <span data-ttu-id="55841-176">Couleur : gris foncé</span><span class="sxs-lookup"><span data-stu-id="55841-176">Color: Dark Gray Color</span></span>
        - <span data-ttu-id="55841-177">Ajouter une contrainte : Hauteur, valeur : 15</span><span class="sxs-lookup"><span data-stu-id="55841-177">Add constraint: Height, value: 15</span></span>
        - <span data-ttu-id="55841-178">Add constraint: Leading space to Content View Leading Margin, value: 0</span><span class="sxs-lookup"><span data-stu-id="55841-178">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="55841-179">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span><span class="sxs-lookup"><span data-stu-id="55841-179">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="55841-180">Add constraint: Top space to Organizer Label Bottom, value: Standard</span><span class="sxs-lookup"><span data-stu-id="55841-180">Add constraint: Top space to Organizer Label Bottom, value: Standard</span></span>
        - <span data-ttu-id="55841-181">Ajouter une contrainte : espace inférieur à la marge inférieure de l’affichage du contenu, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="55841-181">Add constraint: Bottom space to Content View Bottom Margin, value: 0</span></span>

1. <span data-ttu-id="55841-182">Sélectionnez **EventCell,** puis **l’inspecteur de taille.**</span><span class="sxs-lookup"><span data-stu-id="55841-182">Select the **EventCell**, then select the **Size Inspector**.</span></span> <span data-ttu-id="55841-183">Activer **automatique pour** la hauteur de **ligne**.</span><span class="sxs-lookup"><span data-stu-id="55841-183">Enable **Automatic** for **Row Height**.</span></span>

    ![Capture d’écran des contrôleurs d’affichage de calendrier et de table de calendrier](images/calendar-table-storyboard.png)

1. <span data-ttu-id="55841-185">Ouvrez **CalendarViewController.swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="55841-185">Open **CalendarViewController.swift** and replace its contents with the following code.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.swift" id="CalendarViewSnippet":::

1. <span data-ttu-id="55841-186">Exécutez l’application, connectez-vous et appuyez sur **l’onglet** Calendrier. Vous devriez voir la liste des événements.</span><span class="sxs-lookup"><span data-stu-id="55841-186">Run the app, sign in, and tap the **Calendar** tab. You should see the list of events.</span></span>

    ![Capture d’écran du tableau des événements](images/calendar-list.png)

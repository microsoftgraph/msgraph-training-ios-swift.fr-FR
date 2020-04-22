<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="73aad-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="73aad-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="73aad-102">Pour cette application, vous allez utiliser le [Kit de développement logiciel (SDK) Microsoft Graph pour effectuer des](https://github.com/microsoftgraph/msgraph-sdk-objc) appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="73aad-102">For this application, you will use the [Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="73aad-103">Récupérer les événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="73aad-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="73aad-104">Dans cette section, vous allez étendre `GraphManager` la classe pour ajouter une fonction afin d’obtenir les événements de l' `CalendarViewController` utilisateur et la mise à jour pour utiliser ces nouvelles fonctions.</span><span class="sxs-lookup"><span data-stu-id="73aad-104">In this section you will extend the `GraphManager` class to add a function to get the user's events and update `CalendarViewController` to use these new functions.</span></span>

1. <span data-ttu-id="73aad-105">Ouvrez **GraphManager. Swift** et ajoutez la méthode suivante à la `GraphManager` classe.</span><span class="sxs-lookup"><span data-stu-id="73aad-105">Open **GraphManager.swift** and add the following method to the `GraphManager` class.</span></span>

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
    > <span data-ttu-id="73aad-106">Examinez le contenu du `getEvents` code.</span><span class="sxs-lookup"><span data-stu-id="73aad-106">Consider what the code in `getEvents` is doing.</span></span>
    >
    > - <span data-ttu-id="73aad-107">L’URL qui sera appelée est `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="73aad-107">The URL that will be called is `/v1.0/me/events`.</span></span>
    > - <span data-ttu-id="73aad-108">Le `select` paramètre de requête limite les champs renvoyés pour chaque événement à seulement ceux que l’application utilisera réellement.</span><span class="sxs-lookup"><span data-stu-id="73aad-108">The `select` query parameter limits the fields returned for each events to just those the app will actually use.</span></span>
    > - <span data-ttu-id="73aad-109">Le `orderby` paramètre de requête trie les résultats en fonction de la date et de l’heure de leur création, avec l’élément le plus récent en premier.</span><span class="sxs-lookup"><span data-stu-id="73aad-109">The `orderby` query parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="73aad-110">Ouvrez **CalendarViewController. Swift** et remplacez l’intégralité de son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="73aad-110">Open **CalendarViewController.swift** and replace its entire contents with the following code.</span></span>

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

<span data-ttu-id="73aad-111">Vous pouvez maintenant exécuter l’application, se connecter et appuyer sur l’élément de navigation **calendrier** dans le menu.</span><span class="sxs-lookup"><span data-stu-id="73aad-111">You can now run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="73aad-112">Vous devriez voir un dump JSON des événements dans l’application.</span><span class="sxs-lookup"><span data-stu-id="73aad-112">You should see a JSON dump of the events in the app.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="73aad-113">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="73aad-113">Display the results</span></span>

<span data-ttu-id="73aad-114">À présent, vous pouvez remplacer le vidage JSON par un texte pour afficher les résultats de manière conviviale.</span><span class="sxs-lookup"><span data-stu-id="73aad-114">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="73aad-115">Dans cette section, vous allez modifier la `getEvents` fonction pour renvoyer des objets fortement typés et modifier `CalendarViewController` pour qu’elle affiche les événements à l’aide d’un affichage tableau.</span><span class="sxs-lookup"><span data-stu-id="73aad-115">In this section, you will modify the `getEvents` function to return strongly-typed objects, and modify `CalendarViewController` to use a table view to render the events.</span></span>

1. <span data-ttu-id="73aad-116">Ouvrez **GraphManager. Swift**.</span><span class="sxs-lookup"><span data-stu-id="73aad-116">Open **GraphManager.swift**.</span></span> <span data-ttu-id="73aad-117">Remplacez la fonction `getEvents` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="73aad-117">Replace the existing `getEvents` function with the following.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/GraphManager.swift" id="GetEventsSnippet" highlight="1,17-38":::

1. <span data-ttu-id="73aad-118">Créez un fichier de **classe Touch de cacao** dans le projet **GraphTutorial** nommé `CalendarTableViewCell.swift`.</span><span class="sxs-lookup"><span data-stu-id="73aad-118">Create a new **Cocoa Touch Class** file in the **GraphTutorial** project named `CalendarTableViewCell.swift`.</span></span> <span data-ttu-id="73aad-119">Choisissez **UITableViewCell** dans la sous- **classe du** champ.</span><span class="sxs-lookup"><span data-stu-id="73aad-119">Choose **UITableViewCell** in the **Subclass of** field.</span></span>

1. <span data-ttu-id="73aad-120">Ouvrez **CalendarTableViewCell. Swift** et ajoutez le code suivant à la `CalendarTableViewCell` classe.</span><span class="sxs-lookup"><span data-stu-id="73aad-120">Open **CalendarTableViewCell.swift** and add the following code to the `CalendarTableViewCell` class.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.swift" id="PropertiesSnippet":::

1. <span data-ttu-id="73aad-121">Ouvrez **main. Storyboard** et localisez la **scène de calendrier**.</span><span class="sxs-lookup"><span data-stu-id="73aad-121">Open **Main.storyboard** and locate the **Calendar Scene**.</span></span> <span data-ttu-id="73aad-122">Sélectionnez l' **affichage** dans la **scène de calendrier** et supprimez-le.</span><span class="sxs-lookup"><span data-stu-id="73aad-122">Select the **View** in the **Calendar Scene** and delete it.</span></span>

    ![Capture d’écran de l’affichage dans la scène de calendrier](./images/view-in-calendar-scene.png)

1. <span data-ttu-id="73aad-124">Ajoutez un **affichage tableau** de la **bibliothèque** à la **scène calendrier**.</span><span class="sxs-lookup"><span data-stu-id="73aad-124">Add a **Table View** from the **Library** to the **Calendar Scene**.</span></span>
1. <span data-ttu-id="73aad-125">Sélectionnez l’affichage tableau, puis sélectionnez l' **inspecteur d’attributs**.</span><span class="sxs-lookup"><span data-stu-id="73aad-125">Select the table view, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="73aad-126">Définissez les **cellules prototype** sur **1**.</span><span class="sxs-lookup"><span data-stu-id="73aad-126">Set **Prototype Cells** to **1**.</span></span>
1. <span data-ttu-id="73aad-127">Utilisez la **bibliothèque** pour ajouter trois **étiquettes** à la cellule prototype.</span><span class="sxs-lookup"><span data-stu-id="73aad-127">Use the **Library** to add three **Labels** to the prototype cell.</span></span>
1. <span data-ttu-id="73aad-128">Sélectionnez la cellule prototype, puis l' **inspecteur d’identité**.</span><span class="sxs-lookup"><span data-stu-id="73aad-128">Select the prototype cell, then select the **Identity Inspector**.</span></span> <span data-ttu-id="73aad-129">Modifiez **Class** en **CalendarTableViewCell**.</span><span class="sxs-lookup"><span data-stu-id="73aad-129">Change **Class** to **CalendarTableViewCell**.</span></span>
1. <span data-ttu-id="73aad-130">Sélectionnez l' **inspecteur d’attributs** et définissez **identificateur** sur `EventCell`.</span><span class="sxs-lookup"><span data-stu-id="73aad-130">Select the **Attributes Inspector** and set **Identifier** to `EventCell`.</span></span>
1. <span data-ttu-id="73aad-131">Une fois le **EventCell** sélectionné, sélectionnez l' **inspecteur** de connexions `durationLabel`et `organizerLabel`Connectez- `subjectLabel` vous, puis les étiquettes que vous avez ajoutées à la cellule sur la table de montage séquentiel.</span><span class="sxs-lookup"><span data-stu-id="73aad-131">With the **EventCell** selected, select the **Connections Inspector** and connect `durationLabel`, `organizerLabel`, and `subjectLabel` to the labels you added to the cell on the storyboard.</span></span>
1. <span data-ttu-id="73aad-132">Définissez les propriétés et les contraintes sur les trois étiquettes comme suit.</span><span class="sxs-lookup"><span data-stu-id="73aad-132">Set the properties and constraints on the three labels as follows.</span></span>

    - <span data-ttu-id="73aad-133">**Étiquette de l’objet**</span><span class="sxs-lookup"><span data-stu-id="73aad-133">**Subject Label**</span></span>
        - <span data-ttu-id="73aad-134">Ajouter une contrainte : espace de début à la marge de début de l’affichage du contenu, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="73aad-134">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="73aad-135">Ajouter une contrainte : espace de fin à la marge de fin de l’affichage du contenu, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="73aad-135">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="73aad-136">Ajouter une contrainte : espace à la marge supérieure de l’affichage du contenu, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="73aad-136">Add constraint: Top space to Content View Top Margin, value: 0</span></span>
    - <span data-ttu-id="73aad-137">**Étiquette de l’organisateur**</span><span class="sxs-lookup"><span data-stu-id="73aad-137">**Organizer Label**</span></span>
        - <span data-ttu-id="73aad-138">Police : système 12,0</span><span class="sxs-lookup"><span data-stu-id="73aad-138">Font: System 12.0</span></span>
        - <span data-ttu-id="73aad-139">Ajouter une contrainte : espace de début à la marge de début de l’affichage du contenu, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="73aad-139">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="73aad-140">Ajouter une contrainte : espace de fin à la marge de fin de l’affichage du contenu, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="73aad-140">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="73aad-141">Ajouter une contrainte : espace vers le bas de l’étiquette d’objet, valeur : standard</span><span class="sxs-lookup"><span data-stu-id="73aad-141">Add constraint: Top space to Subject Label Bottom, value: Standard</span></span>
    - <span data-ttu-id="73aad-142">**Étiquette de durée**</span><span class="sxs-lookup"><span data-stu-id="73aad-142">**Duration Label**</span></span>
        - <span data-ttu-id="73aad-143">Police : système 12,0</span><span class="sxs-lookup"><span data-stu-id="73aad-143">Font: System 12.0</span></span>
        - <span data-ttu-id="73aad-144">Couleur : gris foncé</span><span class="sxs-lookup"><span data-stu-id="73aad-144">Color: Dark Gray Color</span></span>
        - <span data-ttu-id="73aad-145">Ajouter une contrainte : espace de début à la marge de début de l’affichage du contenu, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="73aad-145">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="73aad-146">Ajouter une contrainte : espace de fin à la marge de fin de l’affichage du contenu, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="73aad-146">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="73aad-147">Ajouter une contrainte : espace vers le bas de l’étiquette de l’organisateur, valeur : standard</span><span class="sxs-lookup"><span data-stu-id="73aad-147">Add constraint: Top space to Organizer Label Bottom, value: Standard</span></span>
        - <span data-ttu-id="73aad-148">Ajouter une contrainte : espace inférieur au contenu afficher la marge inférieure, valeur : 8</span><span class="sxs-lookup"><span data-stu-id="73aad-148">Add constraint: Bottom space to Content View Bottom Margin, value: 8</span></span>

    ![Capture d’écran de la mise en page de cellule prototype](./images/prototype-cell-layout.png)

1. <span data-ttu-id="73aad-150">Ouvrez **CalendarViewController. Swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="73aad-150">Open **CalendarViewController.swift** and replace its contents with the following code.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.swift" id="CalendarViewSnippet":::

1. <span data-ttu-id="73aad-151">Exécutez l’application, connectez-vous, puis appuyez sur l’onglet **calendrier** . La liste des événements doit s’afficher.</span><span class="sxs-lookup"><span data-stu-id="73aad-151">Run the app, sign in, and tap the **Calendar** tab. You should see the list of events.</span></span>

    ![Capture d’écran du tableau des événements](./images/calendar-list.png)

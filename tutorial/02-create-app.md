<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="08097-101">Commencez par créer un projet SWIFT.</span><span class="sxs-lookup"><span data-stu-id="08097-101">Begin by creating a new Swift project.</span></span>

1. <span data-ttu-id="08097-102">Ouvrez Xcode.</span><span class="sxs-lookup"><span data-stu-id="08097-102">Open Xcode.</span></span> <span data-ttu-id="08097-103">Dans le menu **fichier** , sélectionnez **nouveau**, puis **projet**.</span><span class="sxs-lookup"><span data-stu-id="08097-103">On the **File** menu, select **New**, then **Project**.</span></span>
1. <span data-ttu-id="08097-104">Choisissez le modèle d' **application vue unique** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="08097-104">Choose the **Single View App** template and select **Next**.</span></span>

    ![Capture d’écran de la boîte de dialogue de sélection du modèle Xcode](./images/xcode-select-template.png)

1. <span data-ttu-id="08097-106">Définissez le **nom** du produit `GraphTutorial` sur et la **langue** sur **SWIFT**.</span><span class="sxs-lookup"><span data-stu-id="08097-106">Set the **Product Name** to `GraphTutorial` and the **Language** to **Swift**.</span></span>
1. <span data-ttu-id="08097-107">Renseignez les champs restants, puis cliquez sur **suivant**.</span><span class="sxs-lookup"><span data-stu-id="08097-107">Fill in the remaining fields and select **Next**.</span></span>
1. <span data-ttu-id="08097-108">Choisissez un emplacement pour le projet et sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="08097-108">Choose a location for the project and select **Create**.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="08097-109">Installer les dépendances</span><span class="sxs-lookup"><span data-stu-id="08097-109">Install dependencies</span></span>

<span data-ttu-id="08097-110">Avant de poursuivre, installez des dépendances supplémentaires que vous utiliserez plus tard.</span><span class="sxs-lookup"><span data-stu-id="08097-110">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="08097-111">[Bibliothèque d’authentification Microsoft (MSAL) pour iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) pour l’authentification auprès d’Azure ad.</span><span class="sxs-lookup"><span data-stu-id="08097-111">[Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) for authenticating to with Azure AD.</span></span>
- <span data-ttu-id="08097-112">[Kit de développement logiciel (SDK) Microsoft Graph pour objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) pour les appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="08097-112">[Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="08097-113">[Kit de développement logiciel (SDK) des modèles Microsoft Graph pour objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) pour les objets fortement typés représentant des ressources Microsoft Graph, comme des utilisateurs ou des événements.</span><span class="sxs-lookup"><span data-stu-id="08097-113">[Microsoft Graph Models SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) for strongly-typed objects representing Microsoft Graph resources like users or events.</span></span>

1. <span data-ttu-id="08097-114">Quittez Xcode.</span><span class="sxs-lookup"><span data-stu-id="08097-114">Quit Xcode.</span></span>
1. <span data-ttu-id="08097-115">Ouvrez terminal et modifiez le répertoire à l’emplacement de votre projet **GraphTutorial** .</span><span class="sxs-lookup"><span data-stu-id="08097-115">Open Terminal and change the directory to the location of your **GraphTutorial** project.</span></span>
1. <span data-ttu-id="08097-116">Exécutez la commande suivante pour créer un Podfile.</span><span class="sxs-lookup"><span data-stu-id="08097-116">Run the following command to create a Podfile.</span></span>

    ```Shell
    pod init
    ```

1. <span data-ttu-id="08097-117">Ouvrez le Podfile et ajoutez les lignes suivantes juste après la `use_frameworks!` ligne.</span><span class="sxs-lookup"><span data-stu-id="08097-117">Open the Podfile and add the following lines just after the `use_frameworks!` line.</span></span>

    ```Ruby
    pod 'MSAL', '~> 1.1.1'
    pod 'MSGraphClientSDK', ' ~> 1.0.0'
    pod 'MSGraphClientModels', '~> 1.3.0'
    ```

1. <span data-ttu-id="08097-118">Enregistrez le Podfile, puis exécutez la commande suivante pour installer les dépendances.</span><span class="sxs-lookup"><span data-stu-id="08097-118">Save the Podfile, then run the following command to install the dependencies.</span></span>

    ```Shell
    pod install
    ```

1. <span data-ttu-id="08097-119">Une fois la commande exécutée, ouvrez le **GraphTutorial. xcworkspace** nouvellement créé dans Xcode.</span><span class="sxs-lookup"><span data-stu-id="08097-119">Once the command completes, open the newly created **GraphTutorial.xcworkspace** in Xcode.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="08097-120">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="08097-120">Design the app</span></span>

<span data-ttu-id="08097-121">Dans cette section, vous allez créer les affichages de l’application : une page de connexion, un navigateur de barre d’onglets, une page d’accueil et une page de calendrier.</span><span class="sxs-lookup"><span data-stu-id="08097-121">In this section you will create the views for the app: a sign in page, a tab bar navigator, a welcome page, and a calendar page.</span></span> <span data-ttu-id="08097-122">Vous allez également créer une superposition des indicateurs d’activité.</span><span class="sxs-lookup"><span data-stu-id="08097-122">You'll also create an activity indicator overlay.</span></span>

### <a name="create-sign-in-page"></a><span data-ttu-id="08097-123">Créer une page de connexion</span><span class="sxs-lookup"><span data-stu-id="08097-123">Create sign in page</span></span>

1. <span data-ttu-id="08097-124">Développez le dossier **GraphTutorial** dans Xcode, puis sélectionnez **ViewController. Swift**.</span><span class="sxs-lookup"><span data-stu-id="08097-124">Expand the **GraphTutorial** folder in Xcode, then select **ViewController.swift**.</span></span>
1. <span data-ttu-id="08097-125">Dans l' **inspecteur de fichiers**, remplacez le **nom** du fichier par `SignInViewController.swift`.</span><span class="sxs-lookup"><span data-stu-id="08097-125">In the **File Inspector**, change the **Name** of the file to `SignInViewController.swift`.</span></span>

    ![Capture d’écran de l’inspecteur de fichiers](./images/rename-file.png)

1. <span data-ttu-id="08097-127">Ouvrez **SignInViewController. Swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="08097-127">Open **SignInViewController.swift** and replace its contents with the following code.</span></span>

    ```Swift
    import UIKit

    class SignInViewController: UIViewController {

        override func viewDidLoad() {
            super.viewDidLoad()
            // Do any additional setup after loading the view.
        }

        @IBAction func signIn() {
            self.performSegue(withIdentifier: "userSignedIn", sender: nil)
        }
    }
    ```

1. <span data-ttu-id="08097-128">Ouvrez le fichier **principal. Storyboard** .</span><span class="sxs-lookup"><span data-stu-id="08097-128">Open the **Main.storyboard** file.</span></span>
1. <span data-ttu-id="08097-129">Développez **View Controller Scene**, puis sélectionnez **View Controller**.</span><span class="sxs-lookup"><span data-stu-id="08097-129">Expand **View Controller Scene**, then select **View Controller**.</span></span>

    ![Capture d’écran de Xcode avec le contrôleur de vue sélectionné](./images/storyboard-select-view-controller.png)

1. <span data-ttu-id="08097-131">Sélectionnez l' **inspecteur d’identité**, puis définissez la liste déroulante de **classe** sur **SignInViewController**.</span><span class="sxs-lookup"><span data-stu-id="08097-131">Select the **Identity Inspector**, then change the **Class** dropdown to **SignInViewController**.</span></span>

    ![Capture d’écran de l’inspecteur d’identité](./images/change-class.png)

1. <span data-ttu-id="08097-133">Sélectionnez la **bibliothèque**, puis faites glisser un **bouton** sur le **contrôleur de vue de connexion**.</span><span class="sxs-lookup"><span data-stu-id="08097-133">Select the **Library**, then drag a **Button** onto the **Sign In View Controller**.</span></span>

    ![Capture d’écran de la bibliothèque dans Xcode](./images/add-button-to-view.png)

1. <span data-ttu-id="08097-135">Lorsque le bouton est sélectionné, sélectionnez l' **inspecteur d’attributs** et remplacez le **titre** du bouton `Sign In`par.</span><span class="sxs-lookup"><span data-stu-id="08097-135">With the button selected, select the **Attributes Inspector** and change the **Title** of the button to `Sign In`.</span></span>

    ![Une capture d’écran du champ title dans l’inspecteur des attributs de Xcode](./images/set-button-title.png)

1. <span data-ttu-id="08097-137">Lorsque le bouton est sélectionné, sélectionnez le bouton **Aligner** en bas de la table de montage séquentiel.</span><span class="sxs-lookup"><span data-stu-id="08097-137">With the button selected, select the **Align** button at the bottom of the storyboard.</span></span> <span data-ttu-id="08097-138">Sélectionnez le **conteneur horizontalement dans le conteneur** et **verticalement dans** les contraintes du conteneur, conservez la valeur 0, puis sélectionnez **Ajouter 2 contraintes**.</span><span class="sxs-lookup"><span data-stu-id="08097-138">Select both the **Horizontally in container** and **Vertically in container** constraints, leave their values as 0, then select **Add 2 constraints**.</span></span>

    ![Capture d’écran des paramètres de contraintes d’alignement dans Xcode](./images/add-alignment-constraints.png)

1. <span data-ttu-id="08097-140">Sélectionnez le **contrôleur d’affichage de connexion**, puis sélectionnez l' **inspecteur de connexions**.</span><span class="sxs-lookup"><span data-stu-id="08097-140">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="08097-141">Sous **actions reçues**, faites glisser le cercle non rempli en regard de **connexion** sur le bouton.</span><span class="sxs-lookup"><span data-stu-id="08097-141">Under **Received Actions**, drag the unfilled circle next to **signIn** onto the button.</span></span> <span data-ttu-id="08097-142">Sélectionnez **retoucher** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="08097-142">Select **Touch Up Inside** on the pop-up menu.</span></span>

    ![Capture d’écran du déplacement de l’action de connexion vers le bouton dans Xcode](./images/connect-sign-in-button.png)

### <a name="create-tab-bar"></a><span data-ttu-id="08097-144">Créer une barre d’onglets</span><span class="sxs-lookup"><span data-stu-id="08097-144">Create tab bar</span></span>

1. <span data-ttu-id="08097-145">Sélectionnez la **bibliothèque**, puis faites glisser un **contrôleur de barre d’onglets** sur la table de montage séquentiel.</span><span class="sxs-lookup"><span data-stu-id="08097-145">Select the **Library**, then drag a **Tab Bar Controller** onto the storyboard.</span></span>
1. <span data-ttu-id="08097-146">Sélectionnez le **contrôleur d’affichage de connexion**, puis sélectionnez l' **inspecteur de connexions**.</span><span class="sxs-lookup"><span data-stu-id="08097-146">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="08097-147">Sous **déclencheur SEGUES**, faites glisser le cercle qui n’est pas rempli en regard de **Manuel** sur le contrôleur de la **barre d’onglets** sur la table de montage séquentiel.</span><span class="sxs-lookup"><span data-stu-id="08097-147">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Tab Bar Controller** on the storyboard.</span></span> <span data-ttu-id="08097-148">Sélectionnez une **Présentation modale** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="08097-148">Select **Present Modally** in the pop-up menu.</span></span>

    ![Capture d’écran du déplacement d’une segue manuelle vers le nouveau contrôleur de barre d’onglets dans Xcode](./images/add-segue.png)

1. <span data-ttu-id="08097-150">Sélectionnez le segue que vous venez d’ajouter, puis sélectionnez l' **inspecteur d’attributs**.</span><span class="sxs-lookup"><span data-stu-id="08097-150">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="08097-151">Définissez le champ **identificateur** sur `userSignedIn`et définissez la **Présentation** en **plein écran**.</span><span class="sxs-lookup"><span data-stu-id="08097-151">Set the **Identifier** field to `userSignedIn`, and set **Presentation** to **Full Screen**.</span></span>

    ![Capture d’écran du champ identifier dans l’inspecteur des attributs de Xcode](./images/set-segue-identifier.png)

1. <span data-ttu-id="08097-153">Sélectionnez la **scène de l’élément 1**, puis sélectionnez l' **inspecteur de connexions**.</span><span class="sxs-lookup"><span data-stu-id="08097-153">Select the **Item 1 Scene**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="08097-154">Sous **déclencheur SEGUES**, faites glisser le cercle non rempli en regard de **Manuel** sur le **contrôleur de vue de connexion** sur la table de montage séquentiel.</span><span class="sxs-lookup"><span data-stu-id="08097-154">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Sign In View Controller** on the storyboard.</span></span> <span data-ttu-id="08097-155">Sélectionnez une **Présentation modale** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="08097-155">Select **Present Modally** in the pop-up menu.</span></span>
1. <span data-ttu-id="08097-156">Sélectionnez le segue que vous venez d’ajouter, puis sélectionnez l' **inspecteur d’attributs**.</span><span class="sxs-lookup"><span data-stu-id="08097-156">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="08097-157">Définissez le champ **identificateur** sur `userSignedOut`et définissez la **Présentation** en **plein écran**.</span><span class="sxs-lookup"><span data-stu-id="08097-157">Set the **Identifier** field to `userSignedOut`, and set **Presentation** to **Full Screen**.</span></span>

### <a name="create-welcome-page"></a><span data-ttu-id="08097-158">Créer une page d’accueil</span><span class="sxs-lookup"><span data-stu-id="08097-158">Create welcome page</span></span>

1. <span data-ttu-id="08097-159">Sélectionnez le fichier **Assets. xcassets** .</span><span class="sxs-lookup"><span data-stu-id="08097-159">Select the **Assets.xcassets** file.</span></span>
1. <span data-ttu-id="08097-160">Dans le menu **éditeur** , sélectionnez **Ajouter des ressources**, puis **nouveau jeu d’images**.</span><span class="sxs-lookup"><span data-stu-id="08097-160">On the **Editor** menu, select **Add Assets**, then **New Image Set**.</span></span>
1. <span data-ttu-id="08097-161">Sélectionnez la nouvelle **image** et utilisez l' **inspecteur d’attributs** pour définir son **nom** sur `DefaultUserPhoto`.</span><span class="sxs-lookup"><span data-stu-id="08097-161">Select the new **Image** asset and use the **Attribute Inspector** to set its **Name** to `DefaultUserPhoto`.</span></span>
1. <span data-ttu-id="08097-162">Ajoutez l’image que vous souhaitez utiliser comme photo de profil utilisateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="08097-162">Add any image you like to serve as a default user profile photo.</span></span>

    ![Capture d’écran de l’affichage de l’ensemble d’images dans Xcode](./images/add-default-user-photo.png)

1. <span data-ttu-id="08097-164">Créez un fichier de **classe Touch de cacao** dans le dossier **GraphTutorial** nommé `WelcomeViewController`.</span><span class="sxs-lookup"><span data-stu-id="08097-164">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `WelcomeViewController`.</span></span> <span data-ttu-id="08097-165">Choisissez **UIViewController** dans la sous- **classe du** champ.</span><span class="sxs-lookup"><span data-stu-id="08097-165">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="08097-166">Ouvrez **WelcomeViewController. Swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="08097-166">Open **WelcomeViewController.swift** and replace its contents with the following code.</span></span>

    ```Swift
    import UIKit

    class WelcomeViewController: UIViewController {

        @IBOutlet var userProfilePhoto: UIImageView!
        @IBOutlet var userDisplayName: UILabel!
        @IBOutlet var userEmail: UILabel!

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.

            // TEMPORARY
            self.userProfilePhoto.image = UIImage(imageLiteralResourceName: "DefaultUserPhoto")
            self.userDisplayName.text = "Default User"
            self.userEmail.text = "default@contoso.com"
        }

        @IBAction func signOut() {
            self.performSegue(withIdentifier: "userSignedOut", sender: nil)
        }
    }
    ```

1. <span data-ttu-id="08097-167">Ouvrez **main. Storyboard**.</span><span class="sxs-lookup"><span data-stu-id="08097-167">Open **Main.storyboard**.</span></span> <span data-ttu-id="08097-168">Sélectionnez la **scène de l’élément 1**, puis sélectionnez l' **inspecteur d’identité**.</span><span class="sxs-lookup"><span data-stu-id="08097-168">Select the **Item 1 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="08097-169">Remplacez la valeur de la **classe** par **WelcomeViewController**.</span><span class="sxs-lookup"><span data-stu-id="08097-169">Change the **Class** value to **WelcomeViewController**.</span></span>
1. <span data-ttu-id="08097-170">À l’aide de la **bibliothèque**, ajoutez les éléments suivants à la scène de l' **élément 1**.</span><span class="sxs-lookup"><span data-stu-id="08097-170">Using the **Library**, add the following items to the **Item 1 Scene**.</span></span>

    - <span data-ttu-id="08097-171">Affichage d’une **image**</span><span class="sxs-lookup"><span data-stu-id="08097-171">One **Image View**</span></span>
    - <span data-ttu-id="08097-172">Deux **étiquettes**</span><span class="sxs-lookup"><span data-stu-id="08097-172">Two **Labels**</span></span>
    - <span data-ttu-id="08097-173">Un **bouton**</span><span class="sxs-lookup"><span data-stu-id="08097-173">One **Button**</span></span>
1. <span data-ttu-id="08097-174">À l’aide de l' **inspecteur de connexions**, effectuez les connexions suivantes.</span><span class="sxs-lookup"><span data-stu-id="08097-174">Using the **Connections Inspector**, make the following connections.</span></span>

    - <span data-ttu-id="08097-175">Liez la sortie **userDisplayName** à la première étiquette.</span><span class="sxs-lookup"><span data-stu-id="08097-175">Link the **userDisplayName** outlet to the first label.</span></span>
    - <span data-ttu-id="08097-176">Reliez la sortie de **userEmail** à la deuxième étiquette.</span><span class="sxs-lookup"><span data-stu-id="08097-176">Link the **userEmail** outlet to the second label.</span></span>
    - <span data-ttu-id="08097-177">Liez la sortie **userProfilePhoto** à la vue d’image.</span><span class="sxs-lookup"><span data-stu-id="08097-177">Link the **userProfilePhoto** outlet to the image view.</span></span>
    - <span data-ttu-id="08097-178">Liez l’action **signOut** reçue au bouton **retoucher dans**.</span><span class="sxs-lookup"><span data-stu-id="08097-178">Link the **signOut** received action to the button's **Touch Up Inside**.</span></span>

1. <span data-ttu-id="08097-179">Sélectionnez la vue image, puis l' **inspecteur de taille**.</span><span class="sxs-lookup"><span data-stu-id="08097-179">Select the image view, then select the **Size Inspector**.</span></span>
1. <span data-ttu-id="08097-180">Définissez la **largeur** et la **hauteur** sur 196.</span><span class="sxs-lookup"><span data-stu-id="08097-180">Set the **Width** and **Height** to 196.</span></span>
1. <span data-ttu-id="08097-181">Utilisez le bouton **Aligner** pour ajouter la contrainte **de conteneur horizontalement dans le conteneur** avec une valeur de 0.</span><span class="sxs-lookup"><span data-stu-id="08097-181">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="08097-182">Utilisez le bouton **ajouter de nouvelles contraintes** (en regard du bouton **Aligner** ) pour ajouter les contraintes suivantes :</span><span class="sxs-lookup"><span data-stu-id="08097-182">Use the **Add New Constraints** button (next to the **Align** button) to add the following constraints:</span></span>

    - <span data-ttu-id="08097-183">Aligner en haut sur : zone admissible, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="08097-183">Align Top to: Safe Area, value: 0</span></span>
    - <span data-ttu-id="08097-184">Espace inférieur à : nom d’affichage de l’utilisateur, valeur : standard</span><span class="sxs-lookup"><span data-stu-id="08097-184">Bottom Space to: User Display Name, value: Standard</span></span>
    - <span data-ttu-id="08097-185">Hauteur, valeur : 196</span><span class="sxs-lookup"><span data-stu-id="08097-185">Height, value: 196</span></span>
    - <span data-ttu-id="08097-186">Width, value : 196</span><span class="sxs-lookup"><span data-stu-id="08097-186">Width, value: 196</span></span>

    ![Capture d’écran des nouveaux paramètres de contraintes dans Xcode](./images/add-new-constraints.png)

1. <span data-ttu-id="08097-188">Sélectionnez la première étiquette, puis utilisez le bouton **Aligner** pour ajouter la contrainte **horizontalement dans le conteneur** avec une valeur de 0.</span><span class="sxs-lookup"><span data-stu-id="08097-188">Select the first label, then use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="08097-189">Utilisez le bouton **ajouter de nouvelles contraintes** pour ajouter les contraintes suivantes :</span><span class="sxs-lookup"><span data-stu-id="08097-189">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="08097-190">Espace du haut vers : photo de profil utilisateur, valeur : standard</span><span class="sxs-lookup"><span data-stu-id="08097-190">Top Space to: User Profile Photo, value: Standard</span></span>
    - <span data-ttu-id="08097-191">Espace inférieur à : adresse de messagerie de l’utilisateur, valeur : standard</span><span class="sxs-lookup"><span data-stu-id="08097-191">Bottom Space to: User Email, value: Standard</span></span>

1. <span data-ttu-id="08097-192">Sélectionnez la deuxième étiquette, puis l' **inspecteur d’attributs**.</span><span class="sxs-lookup"><span data-stu-id="08097-192">Select the second label, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="08097-193">Modifiez la **couleur** en **gris foncé**et remplacez la **police** par le **système 12,0**.</span><span class="sxs-lookup"><span data-stu-id="08097-193">Change the **Color** to **Dark Gray Color**, and change the **Font** to **System 12.0**.</span></span>
1. <span data-ttu-id="08097-194">Utilisez le bouton **Aligner** pour ajouter la contrainte **de conteneur horizontalement dans le conteneur** avec une valeur de 0.</span><span class="sxs-lookup"><span data-stu-id="08097-194">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="08097-195">Utilisez le bouton **ajouter de nouvelles contraintes** pour ajouter les contraintes suivantes :</span><span class="sxs-lookup"><span data-stu-id="08097-195">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="08097-196">Espace du haut à : nom complet de l’utilisateur, valeur : standard</span><span class="sxs-lookup"><span data-stu-id="08097-196">Top Space to: User Display Name, value: Standard</span></span>
    - <span data-ttu-id="08097-197">Espace inférieur vers : déconnexion, valeur : 14</span><span class="sxs-lookup"><span data-stu-id="08097-197">Bottom Space to: Sign Out, value: 14</span></span>

1. <span data-ttu-id="08097-198">Sélectionnez le bouton, puis l' **inspecteur d’attributs**.</span><span class="sxs-lookup"><span data-stu-id="08097-198">Select the button, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="08097-199">Remplacez le **titre** par `Sign Out`.</span><span class="sxs-lookup"><span data-stu-id="08097-199">Change the **Title** to `Sign Out`.</span></span>
1. <span data-ttu-id="08097-200">Utilisez le bouton **Aligner** pour ajouter la contrainte **de conteneur horizontalement dans le conteneur** avec une valeur de 0.</span><span class="sxs-lookup"><span data-stu-id="08097-200">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="08097-201">Utilisez le bouton **ajouter de nouvelles contraintes** pour ajouter les contraintes suivantes :</span><span class="sxs-lookup"><span data-stu-id="08097-201">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="08097-202">Espace disponible à : adresse de messagerie de l’utilisateur, valeur : 14</span><span class="sxs-lookup"><span data-stu-id="08097-202">Top Space to: User Email, value: 14</span></span>

1. <span data-ttu-id="08097-203">Sélectionnez l’élément de la barre d’onglets au bas de la scène, puis sélectionnez l' **inspecteur des attributs**.</span><span class="sxs-lookup"><span data-stu-id="08097-203">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="08097-204">Remplacez le **titre** par `Me`.</span><span class="sxs-lookup"><span data-stu-id="08097-204">Change the **Title** to `Me`.</span></span>

<span data-ttu-id="08097-205">La scène de bienvenue doit ressembler à ceci une fois que vous avez fini.</span><span class="sxs-lookup"><span data-stu-id="08097-205">The welcome scene should look similar to this once you're done.</span></span>

![Capture d’écran de la disposition de la scène de bienvenue](./images/welcome-scene-layout.png)

### <a name="create-calendar-page"></a><span data-ttu-id="08097-207">Créer une page de calendrier</span><span class="sxs-lookup"><span data-stu-id="08097-207">Create calendar page</span></span>

1. <span data-ttu-id="08097-208">Créez un fichier de **classe Touch de cacao** dans le dossier **GraphTutorial** nommé `CalendarViewController`.</span><span class="sxs-lookup"><span data-stu-id="08097-208">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `CalendarViewController`.</span></span> <span data-ttu-id="08097-209">Choisissez **UIViewController** dans la sous- **classe du** champ.</span><span class="sxs-lookup"><span data-stu-id="08097-209">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="08097-210">Ouvrez **CalendarViewController. Swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="08097-210">Open **CalendarViewController.swift** and replace its contents with the following code.</span></span>

    ```Swift
    import UIKit

    class CalendarViewController: UIViewController {

        @IBOutlet var calendarJSON: UITextView!

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.

            // TEMPORARY
            calendarJSON.text = "Calendar"
            calendarJSON.sizeToFit()
        }
    }
    ```

1. <span data-ttu-id="08097-211">Ouvrez **main. Storyboard**.</span><span class="sxs-lookup"><span data-stu-id="08097-211">Open **Main.storyboard**.</span></span> <span data-ttu-id="08097-212">Sélectionnez la **scène de l’élément 2**, puis sélectionnez l' **inspecteur d’identité**.</span><span class="sxs-lookup"><span data-stu-id="08097-212">Select the **Item 2 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="08097-213">Remplacez la valeur de la **classe** par **CalendarViewController**.</span><span class="sxs-lookup"><span data-stu-id="08097-213">Change the **Class** value to **CalendarViewController**.</span></span>
1. <span data-ttu-id="08097-214">À l’aide de la **bibliothèque**, ajoutez un **affichage de texte** à la scène de l' **élément 2**.</span><span class="sxs-lookup"><span data-stu-id="08097-214">Using the **Library**, add a **Text View** to the **Item 2 Scene**.</span></span>
1. <span data-ttu-id="08097-215">Sélectionnez l’affichage de texte que vous venez d’ajouter.</span><span class="sxs-lookup"><span data-stu-id="08097-215">Select the text view you just added.</span></span> <span data-ttu-id="08097-216">Dans le menu **éditeur** , choisissez **incorporer dans**, puis appuyez sur la touche de **défilement**.</span><span class="sxs-lookup"><span data-stu-id="08097-216">On the **Editor** menu, choose **Embed In**, then **Scroll View**.</span></span>
1. <span data-ttu-id="08097-217">À l’aide de l' **inspecteur de connexions**, connectez la sortie **calendarJSON** à l’affichage de texte.</span><span class="sxs-lookup"><span data-stu-id="08097-217">Using the **Connections Inspector**, connect the **calendarJSON** outlet to the text view.</span></span>
1. <span data-ttu-id="08097-218">Sélectionnez l’élément de la barre d’onglets au bas de la scène, puis sélectionnez l' **inspecteur des attributs**.</span><span class="sxs-lookup"><span data-stu-id="08097-218">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="08097-219">Remplacez le **titre** par `Calendar`.</span><span class="sxs-lookup"><span data-stu-id="08097-219">Change the **Title** to `Calendar`.</span></span>
1. <span data-ttu-id="08097-220">Dans le menu **éditeur** , sélectionnez **résoudre les problèmes de disposition automatique**, puis sélectionnez **Ajouter les contraintes manquantes** sous **toutes les vues dans le contrôleur d’affichage de bienvenue**.</span><span class="sxs-lookup"><span data-stu-id="08097-220">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>

<span data-ttu-id="08097-221">La scène de calendrier doit ressembler à ceci une fois que vous avez fini.</span><span class="sxs-lookup"><span data-stu-id="08097-221">The calendar scene should look similar to this once you're done.</span></span>

![Capture d’écran de la mise en page de la scène de calendrier](./images/calendar-scene-layout.png)

### <a name="create-activity-indicator"></a><span data-ttu-id="08097-223">Indicateur de création d’activité</span><span class="sxs-lookup"><span data-stu-id="08097-223">Create activity indicator</span></span>

1. <span data-ttu-id="08097-224">Créez un fichier de **classe Touch de cacao** dans le dossier **GraphTutorial** nommé `SpinnerViewController`.</span><span class="sxs-lookup"><span data-stu-id="08097-224">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `SpinnerViewController`.</span></span> <span data-ttu-id="08097-225">Choisissez **UIViewController** dans la sous- **classe du** champ.</span><span class="sxs-lookup"><span data-stu-id="08097-225">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="08097-226">Ouvrez **SpinnerViewController. Swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="08097-226">Open **SpinnerViewController.swift** and replace its contents with the following code.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/SpinnerViewController.swift" id="SpinnerSnippet":::

## <a name="test-the-app"></a><span data-ttu-id="08097-227">Tester l'application</span><span class="sxs-lookup"><span data-stu-id="08097-227">Test the app</span></span>

<span data-ttu-id="08097-228">Enregistrez vos modifications et lancez l’application.</span><span class="sxs-lookup"><span data-stu-id="08097-228">Save your changes and launch the app.</span></span> <span data-ttu-id="08097-229">Vous devriez pouvoir vous déplacer entre les écrans à l’aide des boutons **de connexion** et de **déconnexion** , ainsi que de la barre d’onglets.</span><span class="sxs-lookup"><span data-stu-id="08097-229">You should be able to move between the screens using the **Sign In** and **Sign Out** buttons and the tab bar.</span></span>

![Captures d’écran de l’application](./images/app-screens.png)

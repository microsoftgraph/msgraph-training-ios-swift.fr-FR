<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="4217e-101">Commencez par créer un nouveau projet Swift.</span><span class="sxs-lookup"><span data-stu-id="4217e-101">Begin by creating a new Swift project.</span></span>

1. <span data-ttu-id="4217e-102">Ouvrez Xcode.</span><span class="sxs-lookup"><span data-stu-id="4217e-102">Open Xcode.</span></span> <span data-ttu-id="4217e-103">Dans **le** menu Fichier, **sélectionnez Nouveau,** puis **Projet**.</span><span class="sxs-lookup"><span data-stu-id="4217e-103">On the **File** menu, select **New**, then **Project**.</span></span>
1. <span data-ttu-id="4217e-104">Choisissez le modèle **d’application** et sélectionnez **Suivant.**</span><span class="sxs-lookup"><span data-stu-id="4217e-104">Choose the **App** template and select **Next**.</span></span>

    ![Capture d’écran de la boîte de dialogue de sélection du modèle Xcode](images/xcode-select-template.png)

1. <span data-ttu-id="4217e-106">Définissez **le nom du produit** et la `GraphTutorial` **langue** sur **Swift**.</span><span class="sxs-lookup"><span data-stu-id="4217e-106">Set the **Product Name** to `GraphTutorial` and the **Language** to **Swift**.</span></span>
1. <span data-ttu-id="4217e-107">Remplissez les champs restants et sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="4217e-107">Fill in the remaining fields and select **Next**.</span></span>
1. <span data-ttu-id="4217e-108">Choisissez un emplacement pour le projet, puis sélectionnez **Créer.**</span><span class="sxs-lookup"><span data-stu-id="4217e-108">Choose a location for the project and select **Create**.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="4217e-109">Installer les dépendances</span><span class="sxs-lookup"><span data-stu-id="4217e-109">Install dependencies</span></span>

<span data-ttu-id="4217e-110">Avant de passer à autre chose, installez des dépendances supplémentaires que vous utiliserez ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="4217e-110">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="4217e-111">[Bibliothèque d’authentification Microsoft (MSAL) pour iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) pour l’authentification auprès d’Azure AD.</span><span class="sxs-lookup"><span data-stu-id="4217e-111">[Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) for authenticating to with Azure AD.</span></span>
- <span data-ttu-id="4217e-112">[SDK Microsoft Graph pour l’objectif C](https://github.com/microsoftgraph/msgraph-sdk-objc) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="4217e-112">[Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="4217e-113">[Microsoft Graph Models SDK pour l’objectif C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) pour les objets fortement typés représentant des ressources Microsoft Graph telles que des utilisateurs ou des événements.</span><span class="sxs-lookup"><span data-stu-id="4217e-113">[Microsoft Graph Models SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) for strongly-typed objects representing Microsoft Graph resources like users or events.</span></span>

1. <span data-ttu-id="4217e-114">Quittez Xcode.</span><span class="sxs-lookup"><span data-stu-id="4217e-114">Quit Xcode.</span></span>
1. <span data-ttu-id="4217e-115">Ouvrez Terminal et modifiez le répertoire à l’emplacement de **votre projet GraphTutorial.**</span><span class="sxs-lookup"><span data-stu-id="4217e-115">Open Terminal and change the directory to the location of your **GraphTutorial** project.</span></span>
1. <span data-ttu-id="4217e-116">Exécutez la commande suivante pour créer un podfile.</span><span class="sxs-lookup"><span data-stu-id="4217e-116">Run the following command to create a Podfile.</span></span>

    ```Shell
    pod init
    ```

1. <span data-ttu-id="4217e-117">Ouvrez le podfile et ajoutez les lignes suivantes juste après la `use_frameworks!` ligne.</span><span class="sxs-lookup"><span data-stu-id="4217e-117">Open the Podfile and add the following lines just after the `use_frameworks!` line.</span></span>

    ```Ruby
    pod 'MSAL', '~> 1.1.13'
    pod 'MSGraphClientSDK', ' ~> 1.0.0'
    pod 'MSGraphClientModels', '~> 1.3.0'
    ```

1. <span data-ttu-id="4217e-118">Enregistrez le podfile, puis exécutez la commande suivante pour installer les dépendances.</span><span class="sxs-lookup"><span data-stu-id="4217e-118">Save the Podfile, then run the following command to install the dependencies.</span></span>

    ```Shell
    pod install
    ```

1. <span data-ttu-id="4217e-119">Une fois la commande terminée, ouvrez l’espace **GraphTutorial.xcworkspace** nouvellement créé dans Xcode.</span><span class="sxs-lookup"><span data-stu-id="4217e-119">Once the command completes, open the newly created **GraphTutorial.xcworkspace** in Xcode.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="4217e-120">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="4217e-120">Design the app</span></span>

<span data-ttu-id="4217e-121">Dans cette section, vous allez créer les vues de l’application : une page de signature, un navigateur de barre d’onglets, une page d’accueil et une page de calendrier.</span><span class="sxs-lookup"><span data-stu-id="4217e-121">In this section you will create the views for the app: a sign in page, a tab bar navigator, a welcome page, and a calendar page.</span></span> <span data-ttu-id="4217e-122">Vous allez également créer une superposition d’indicateur d’activité.</span><span class="sxs-lookup"><span data-stu-id="4217e-122">You'll also create an activity indicator overlay.</span></span>

### <a name="create-sign-in-page"></a><span data-ttu-id="4217e-123">Page Créer une signature</span><span class="sxs-lookup"><span data-stu-id="4217e-123">Create sign in page</span></span>

1. <span data-ttu-id="4217e-124">Développez **le dossier GraphTutorial** dans Xcode, puis **sélectionnez ViewController.swift**.</span><span class="sxs-lookup"><span data-stu-id="4217e-124">Expand the **GraphTutorial** folder in Xcode, then select **ViewController.swift**.</span></span>
1. <span data-ttu-id="4217e-125">Dans **l’Inspecteur de** fichier, modifiez **le nom** du fichier en `SignInViewController.swift` .</span><span class="sxs-lookup"><span data-stu-id="4217e-125">In the **File Inspector**, change the **Name** of the file to `SignInViewController.swift`.</span></span>

    ![Capture d’écran de l’Inspecteur de fichier](images/rename-file.png)

1. <span data-ttu-id="4217e-127">Ouvrez **SignInViewController.swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="4217e-127">Open **SignInViewController.swift** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="4217e-128">Ouvrez **Main.storyboard**.</span><span class="sxs-lookup"><span data-stu-id="4217e-128">Open **Main.storyboard**.</span></span> <span data-ttu-id="4217e-129">Développez **La scène du contrôleur de** vue, puis sélectionnez Contrôleur de **vue.**</span><span class="sxs-lookup"><span data-stu-id="4217e-129">Expand **View Controller Scene**, then select **View Controller**.</span></span>

    ![Capture d’écran de Xcode avec le contrôleur d’affichage sélectionné](images/storyboard-select-view-controller.png)

1. <span data-ttu-id="4217e-131">Sélectionnez **l’Inspecteur d’identité,** puis modifiez **la** classe de la catégorie à **SignInViewController**.</span><span class="sxs-lookup"><span data-stu-id="4217e-131">Select the **Identity Inspector**, then change the **Class** dropdown to **SignInViewController**.</span></span>

    ![Capture d’écran de l’Inspecteur d’identité](images/change-class.png)

1. <span data-ttu-id="4217e-133">Sélectionnez **la bibliothèque,** puis faites glisser un **bouton sur** le contrôleur **d’affichage de la signature.**</span><span class="sxs-lookup"><span data-stu-id="4217e-133">Select the **Library**, then drag a **Button** onto the **Sign In View Controller**.</span></span>

    ![Capture d’écran de la bibliothèque dans Xcode](images/add-button-to-view.png)

1. <span data-ttu-id="4217e-135">Une fois le bouton sélectionné, sélectionnez  l’Inspecteur d’attributs et modifiez le titre du bouton sur  `Sign In` .</span><span class="sxs-lookup"><span data-stu-id="4217e-135">With the button selected, select the **Attributes Inspector** and change the **Title** of the button to `Sign In`.</span></span>

    ![Capture d’écran du champ Titre dans l’inspecteur d’attributs dans Xcode](images/set-button-title.png)

1. <span data-ttu-id="4217e-137">Une fois le bouton sélectionné, sélectionnez le bouton **Aligner** en bas du storyboard.</span><span class="sxs-lookup"><span data-stu-id="4217e-137">With the button selected, select the **Align** button at the bottom of the storyboard.</span></span> <span data-ttu-id="4217e-138">Sélectionnez les contraintes **Horizontalement** dans le conteneur et **Verticalement** dans les contraintes de conteneur, laissez leurs valeurs sous la forme 0, puis sélectionnez Ajouter **2 contraintes.**</span><span class="sxs-lookup"><span data-stu-id="4217e-138">Select both the **Horizontally in container** and **Vertically in container** constraints, leave their values as 0, then select **Add 2 constraints**.</span></span>

    ![Capture d’écran des paramètres de contraintes d’alignement dans Xcode](images/add-alignment-constraints.png)

1. <span data-ttu-id="4217e-140">Sélectionnez **le contrôleur d’affichage de** connexion, puis **l’inspecteur de connexions.**</span><span class="sxs-lookup"><span data-stu-id="4217e-140">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="4217e-141">Sous **Actions reçues,** faites glisser le cercle non rempli en dessous de **la** signature sur le bouton.</span><span class="sxs-lookup"><span data-stu-id="4217e-141">Under **Received Actions**, drag the unfilled circle next to **signIn** onto the button.</span></span> <span data-ttu-id="4217e-142">Sélectionnez **Toucher à l’intérieur** dans le menu déroulant.</span><span class="sxs-lookup"><span data-stu-id="4217e-142">Select **Touch Up Inside** on the pop-up menu.</span></span>

    ![Capture d’écran du glissement de l’action signIn vers le bouton dans Xcode](images/connect-sign-in-button.png)

### <a name="create-tab-bar"></a><span data-ttu-id="4217e-144">Créer une barre d’onglets</span><span class="sxs-lookup"><span data-stu-id="4217e-144">Create tab bar</span></span>

1. <span data-ttu-id="4217e-145">Sélectionnez la **bibliothèque,** puis faites glisser un contrôleur **de barre** d’onglets vers le storyboard.</span><span class="sxs-lookup"><span data-stu-id="4217e-145">Select the **Library**, then drag a **Tab Bar Controller** onto the storyboard.</span></span>
1. <span data-ttu-id="4217e-146">Sélectionnez **le contrôleur d’affichage de** connexion, puis **l’inspecteur de connexions.**</span><span class="sxs-lookup"><span data-stu-id="4217e-146">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="4217e-147">Sous **Segues déclenchés,** faites glisser le cercle non rempli à côté du **manuel** sur le contrôleur de barre de **tabulation** dans le storyboard.</span><span class="sxs-lookup"><span data-stu-id="4217e-147">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Tab Bar Controller** on the storyboard.</span></span> <span data-ttu-id="4217e-148">Sélectionnez **Présenter en mode mod** dans le menu déroulant.</span><span class="sxs-lookup"><span data-stu-id="4217e-148">Select **Present Modally** in the pop-up menu.</span></span>

    ![Capture d’écran du glissement d’un segue manuel vers le nouveau contrôleur de barre d’onglets dans Xcode](images/add-segue.png)

1. <span data-ttu-id="4217e-150">Sélectionnez le segue que vous avez ajouté, puis sélectionnez **l’inspecteur d’attributs.**</span><span class="sxs-lookup"><span data-stu-id="4217e-150">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="4217e-151">Définissez le **champ identificateur** sur , et `userSignedIn` **définissez Présentation** en plein **écran**.</span><span class="sxs-lookup"><span data-stu-id="4217e-151">Set the **Identifier** field to `userSignedIn`, and set **Presentation** to **Full Screen**.</span></span>

    ![Capture d’écran du champ identificateur dans l’inspecteur d’attributs dans Xcode](images/set-segue-identifier.png)

1. <span data-ttu-id="4217e-153">Sélectionnez **la scène d’élément 1,** puis l’inspecteur **de connexions.**</span><span class="sxs-lookup"><span data-stu-id="4217e-153">Select the **Item 1 Scene**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="4217e-154">Sous **Segues déclenchés,** faites glisser le  cercle non rempli en regard du manuel sur le contrôleur d’affichage de la signature **dans** le storyboard.</span><span class="sxs-lookup"><span data-stu-id="4217e-154">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Sign In View Controller** on the storyboard.</span></span> <span data-ttu-id="4217e-155">Sélectionnez **Présenter en mode mod** dans le menu déroulant.</span><span class="sxs-lookup"><span data-stu-id="4217e-155">Select **Present Modally** in the pop-up menu.</span></span>
1. <span data-ttu-id="4217e-156">Sélectionnez le segue que vous avez ajouté, puis sélectionnez **l’inspecteur d’attributs.**</span><span class="sxs-lookup"><span data-stu-id="4217e-156">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="4217e-157">Définissez le **champ identificateur** sur , et `userSignedOut` **définissez Présentation** en plein **écran**.</span><span class="sxs-lookup"><span data-stu-id="4217e-157">Set the **Identifier** field to `userSignedOut`, and set **Presentation** to **Full Screen**.</span></span>

### <a name="create-welcome-page"></a><span data-ttu-id="4217e-158">Créer une page d’accueil</span><span class="sxs-lookup"><span data-stu-id="4217e-158">Create welcome page</span></span>

1. <span data-ttu-id="4217e-159">Sélectionnez **le fichier Assets.x vhs.**</span><span class="sxs-lookup"><span data-stu-id="4217e-159">Select the **Assets.xcassets** file.</span></span>
1. <span data-ttu-id="4217e-160">Dans le menu **Éditeur,** **sélectionnez Ajouter un nouvel actif,** puis Jeu **d’images.**</span><span class="sxs-lookup"><span data-stu-id="4217e-160">On the **Editor** menu, select **Add New Asset**, then **Image Set**.</span></span>
1. <span data-ttu-id="4217e-161">Sélectionnez la nouvelle **image et** utilisez l’inspecteur **d’attributs** pour définir son **nom** `DefaultUserPhoto` sur .</span><span class="sxs-lookup"><span data-stu-id="4217e-161">Select the new **Image** asset and use the **Attribute Inspector** to set its **Name** to `DefaultUserPhoto`.</span></span>
1. <span data-ttu-id="4217e-162">Ajoutez n’importe quelle image que vous souhaitez servir de photo de profil utilisateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="4217e-162">Add any image you like to serve as a default user profile photo.</span></span>

    ![Capture d’écran de l’affichage actif Jeu d’images dans Xcode](images/add-default-user-photo.png)

1. <span data-ttu-id="4217e-164">Créez un **fichier de classe Cocoa Touch** dans le dossier **GraphTutorial** nommé `WelcomeViewController` .</span><span class="sxs-lookup"><span data-stu-id="4217e-164">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `WelcomeViewController`.</span></span> <span data-ttu-id="4217e-165">Choisissez **UIViewController dans** la **sous-classe du** champ.</span><span class="sxs-lookup"><span data-stu-id="4217e-165">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="4217e-166">Ouvrez **WelcomeViewController.swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="4217e-166">Open **WelcomeViewController.swift** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="4217e-167">Ouvrez **Main.storyboard**.</span><span class="sxs-lookup"><span data-stu-id="4217e-167">Open **Main.storyboard**.</span></span> <span data-ttu-id="4217e-168">Sélectionnez la **scène d’élément 1,** puis l’inspecteur **d’identité.**</span><span class="sxs-lookup"><span data-stu-id="4217e-168">Select the **Item 1 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="4217e-169">Modifiez **la valeur** de classe en **WelcomeViewController**.</span><span class="sxs-lookup"><span data-stu-id="4217e-169">Change the **Class** value to **WelcomeViewController**.</span></span>
1. <span data-ttu-id="4217e-170">À **l’aide de la bibliothèque**, ajoutez les éléments suivants à la **scène d’élément 1.**</span><span class="sxs-lookup"><span data-stu-id="4217e-170">Using the **Library**, add the following items to the **Item 1 Scene**.</span></span>

    - <span data-ttu-id="4217e-171">Un **affichage d’image**</span><span class="sxs-lookup"><span data-stu-id="4217e-171">One **Image View**</span></span>
    - <span data-ttu-id="4217e-172">Deux **étiquettes**</span><span class="sxs-lookup"><span data-stu-id="4217e-172">Two **Labels**</span></span>
    - <span data-ttu-id="4217e-173">Un **bouton**</span><span class="sxs-lookup"><span data-stu-id="4217e-173">One **Button**</span></span>
1. <span data-ttu-id="4217e-174">À **l’aide de l’Inspecteur de connexions,** établir les connexions suivantes.</span><span class="sxs-lookup"><span data-stu-id="4217e-174">Using the **Connections Inspector**, make the following connections.</span></span>

    - <span data-ttu-id="4217e-175">Lier la **sortie userDisplayName** à la première étiquette.</span><span class="sxs-lookup"><span data-stu-id="4217e-175">Link the **userDisplayName** outlet to the first label.</span></span>
    - <span data-ttu-id="4217e-176">Lier la **sortie userEmail** à la deuxième étiquette.</span><span class="sxs-lookup"><span data-stu-id="4217e-176">Link the **userEmail** outlet to the second label.</span></span>
    - <span data-ttu-id="4217e-177">Lier la **sortie userProfilePhoto** à la vue d’image.</span><span class="sxs-lookup"><span data-stu-id="4217e-177">Link the **userProfilePhoto** outlet to the image view.</span></span>
    - <span data-ttu-id="4217e-178">Lier **l’action reçue de** connexion à l’action Touch Up Inside du **bouton.**</span><span class="sxs-lookup"><span data-stu-id="4217e-178">Link the **signOut** received action to the button's **Touch Up Inside**.</span></span>

1. <span data-ttu-id="4217e-179">Sélectionnez l’affichage d’image, puis **l’inspecteur de taille.**</span><span class="sxs-lookup"><span data-stu-id="4217e-179">Select the image view, then select the **Size Inspector**.</span></span>
1. <span data-ttu-id="4217e-180">Définissez **la largeur** et la **hauteur** sur 196.</span><span class="sxs-lookup"><span data-stu-id="4217e-180">Set the **Width** and **Height** to 196.</span></span>
1. <span data-ttu-id="4217e-181">Utilisez le **bouton Aligner** pour ajouter la contrainte **Horizontalement dans le** conteneur avec la valeur 0.</span><span class="sxs-lookup"><span data-stu-id="4217e-181">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="4217e-182">Utilisez le **bouton Ajouter de nouvelles contraintes** (à côté du bouton **Aligner)** pour ajouter les contraintes suivantes :</span><span class="sxs-lookup"><span data-stu-id="4217e-182">Use the **Add New Constraints** button (next to the **Align** button) to add the following constraints:</span></span>

    - <span data-ttu-id="4217e-183">Aligner le haut sur : zone sécurisée, valeur : 0</span><span class="sxs-lookup"><span data-stu-id="4217e-183">Align Top to: Safe Area, value: 0</span></span>
    - <span data-ttu-id="4217e-184">Espace inférieur à : Nom d’affichage de l’utilisateur, valeur : Standard</span><span class="sxs-lookup"><span data-stu-id="4217e-184">Bottom Space to: User Display Name, value: Standard</span></span>
    - <span data-ttu-id="4217e-185">Hauteur, valeur : 196</span><span class="sxs-lookup"><span data-stu-id="4217e-185">Height, value: 196</span></span>
    - <span data-ttu-id="4217e-186">Largeur, valeur : 196</span><span class="sxs-lookup"><span data-stu-id="4217e-186">Width, value: 196</span></span>

    ![Capture d’écran des nouveaux paramètres de contraintes dans Xcode](./images/add-new-constraints.png)

1. <span data-ttu-id="4217e-188">Sélectionnez la première étiquette, puis utilisez le bouton **Aligner** pour ajouter la contrainte **Horizontalement** dans le conteneur avec la valeur 0.</span><span class="sxs-lookup"><span data-stu-id="4217e-188">Select the first label, then use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="4217e-189">Utilisez le **bouton Ajouter de nouvelles contraintes** pour ajouter les contraintes suivantes :</span><span class="sxs-lookup"><span data-stu-id="4217e-189">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="4217e-190">Espace supérieur vers : Photo de profil utilisateur, valeur : Standard</span><span class="sxs-lookup"><span data-stu-id="4217e-190">Top Space to: User Profile Photo, value: Standard</span></span>
    - <span data-ttu-id="4217e-191">Bottom Space to: User Email, value: Standard</span><span class="sxs-lookup"><span data-stu-id="4217e-191">Bottom Space to: User Email, value: Standard</span></span>

1. <span data-ttu-id="4217e-192">Sélectionnez la deuxième étiquette, puis **l’inspecteur d’attributs.**</span><span class="sxs-lookup"><span data-stu-id="4217e-192">Select the second label, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="4217e-193">Modifiez **la couleur** en gris **foncé** et modifiez la **police** sur **System 12.0**.</span><span class="sxs-lookup"><span data-stu-id="4217e-193">Change the **Color** to **Dark Gray Color**, and change the **Font** to **System 12.0**.</span></span>
1. <span data-ttu-id="4217e-194">Utilisez le **bouton Aligner** pour ajouter la contrainte **Horizontalement dans le** conteneur avec la valeur 0.</span><span class="sxs-lookup"><span data-stu-id="4217e-194">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="4217e-195">Utilisez le **bouton Ajouter de nouvelles contraintes** pour ajouter les contraintes suivantes :</span><span class="sxs-lookup"><span data-stu-id="4217e-195">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="4217e-196">Espace supérieur : Nom d’affichage de l’utilisateur, valeur : Standard</span><span class="sxs-lookup"><span data-stu-id="4217e-196">Top Space to: User Display Name, value: Standard</span></span>
    - <span data-ttu-id="4217e-197">Espace inférieur pour : Se sortir, valeur : 14</span><span class="sxs-lookup"><span data-stu-id="4217e-197">Bottom Space to: Sign Out, value: 14</span></span>

1. <span data-ttu-id="4217e-198">Sélectionnez le bouton, puis l’inspecteur **d’attributs.**</span><span class="sxs-lookup"><span data-stu-id="4217e-198">Select the button, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="4217e-199">Changez **le titre** en `Sign Out` .</span><span class="sxs-lookup"><span data-stu-id="4217e-199">Change the **Title** to `Sign Out`.</span></span>
1. <span data-ttu-id="4217e-200">Utilisez le **bouton Aligner** pour ajouter la contrainte **Horizontalement dans le** conteneur avec la valeur 0.</span><span class="sxs-lookup"><span data-stu-id="4217e-200">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="4217e-201">Utilisez le **bouton Ajouter de nouvelles contraintes** pour ajouter les contraintes suivantes :</span><span class="sxs-lookup"><span data-stu-id="4217e-201">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="4217e-202">Espace supérieur à : Courrier électronique de l’utilisateur, valeur : 14</span><span class="sxs-lookup"><span data-stu-id="4217e-202">Top Space to: User Email, value: 14</span></span>

1. <span data-ttu-id="4217e-203">Sélectionnez l’élément de barre d’onglets en bas de la scène, puis sélectionnez **l’inspecteur d’attributs.**</span><span class="sxs-lookup"><span data-stu-id="4217e-203">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="4217e-204">Changez **le titre** en `Me` .</span><span class="sxs-lookup"><span data-stu-id="4217e-204">Change the **Title** to `Me`.</span></span>

<span data-ttu-id="4217e-205">La scène d’accueil doit ressembler à ceci une fois que vous avez terminé.</span><span class="sxs-lookup"><span data-stu-id="4217e-205">The welcome scene should look similar to this once you're done.</span></span>

![Capture d’écran de la disposition de la scène d’accueil](images/welcome-scene-layout.png)

### <a name="create-calendar-page"></a><span data-ttu-id="4217e-207">Créer une page de calendrier</span><span class="sxs-lookup"><span data-stu-id="4217e-207">Create calendar page</span></span>

1. <span data-ttu-id="4217e-208">Créez un **fichier de classe Cocoa Touch** dans le dossier **GraphTutorial** nommé `CalendarViewController` .</span><span class="sxs-lookup"><span data-stu-id="4217e-208">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `CalendarViewController`.</span></span> <span data-ttu-id="4217e-209">Choisissez **UIViewController dans** la **sous-classe du** champ.</span><span class="sxs-lookup"><span data-stu-id="4217e-209">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="4217e-210">Ouvrez **CalendarViewController.swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="4217e-210">Open **CalendarViewController.swift** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="4217e-211">Ouvrez **Main.storyboard**.</span><span class="sxs-lookup"><span data-stu-id="4217e-211">Open **Main.storyboard**.</span></span> <span data-ttu-id="4217e-212">Sélectionnez **la scène d’élément 2,** puis l’inspecteur **d’identité.**</span><span class="sxs-lookup"><span data-stu-id="4217e-212">Select the **Item 2 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="4217e-213">Modifiez **la valeur de** classe en **CalendarViewController**.</span><span class="sxs-lookup"><span data-stu-id="4217e-213">Change the **Class** value to **CalendarViewController**.</span></span>
1. <span data-ttu-id="4217e-214">À **l’aide de la bibliothèque**, ajoutez **un affichage texte** à la **scène d’élément 2.**</span><span class="sxs-lookup"><span data-stu-id="4217e-214">Using the **Library**, add a **Text View** to the **Item 2 Scene**.</span></span>
1. <span data-ttu-id="4217e-215">Sélectionnez l’affichage texte que vous avez ajouté.</span><span class="sxs-lookup"><span data-stu-id="4217e-215">Select the text view you just added.</span></span> <span data-ttu-id="4217e-216">Dans le menu **Éditeur,** **sélectionnez Incorporer** dans, puis **Défilement en mode .**</span><span class="sxs-lookup"><span data-stu-id="4217e-216">On the **Editor** menu, choose **Embed In**, then **Scroll View**.</span></span>
1. <span data-ttu-id="4217e-217">Resize the scroll view and text view to take up the entire screen.</span><span class="sxs-lookup"><span data-stu-id="4217e-217">Resize the scroll view and text view to take up the entire screen.</span></span>
1. <span data-ttu-id="4217e-218">À **l’aide de l’Inspecteur de connexions,** connectez la sortie **calendarJSON** à l’affichage texte.</span><span class="sxs-lookup"><span data-stu-id="4217e-218">Using the **Connections Inspector**, connect the **calendarJSON** outlet to the text view.</span></span>
1. <span data-ttu-id="4217e-219">Sélectionnez l’élément de barre d’onglets en bas de la scène, puis sélectionnez **l’inspecteur d’attributs.**</span><span class="sxs-lookup"><span data-stu-id="4217e-219">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="4217e-220">Changez **le titre** en `Calendar` .</span><span class="sxs-lookup"><span data-stu-id="4217e-220">Change the **Title** to `Calendar`.</span></span>
1. <span data-ttu-id="4217e-221">Dans le menu **Éditeur,** sélectionnez Résoudre les problèmes de disposition **automatique,** puis sélectionnez Ajouter des **contraintes manquantes** sous Tous les affichages dans le **contrôleur d’affichage calendrier.**</span><span class="sxs-lookup"><span data-stu-id="4217e-221">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Calendar View Controller**.</span></span>

<span data-ttu-id="4217e-222">La scène de calendrier doit ressembler à ceci une fois que vous avez terminé.</span><span class="sxs-lookup"><span data-stu-id="4217e-222">The calendar scene should look similar to this once you're done.</span></span>

![Capture d’écran de la disposition de la scène calendrier](images/calendar-scene-layout.png)

### <a name="create-activity-indicator"></a><span data-ttu-id="4217e-224">Créer un indicateur d’activité</span><span class="sxs-lookup"><span data-stu-id="4217e-224">Create activity indicator</span></span>

1. <span data-ttu-id="4217e-225">Créez un **fichier de classe Cocoa Touch** dans le dossier **GraphTutorial** nommé `SpinnerViewController` .</span><span class="sxs-lookup"><span data-stu-id="4217e-225">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `SpinnerViewController`.</span></span> <span data-ttu-id="4217e-226">Choisissez **UIViewController dans** la **sous-classe du** champ.</span><span class="sxs-lookup"><span data-stu-id="4217e-226">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="4217e-227">Ouvrez **SpinnerViewController.swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="4217e-227">Open **SpinnerViewController.swift** and replace its contents with the following code.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/SpinnerViewController.swift" id="SpinnerSnippet":::

## <a name="test-the-app"></a><span data-ttu-id="4217e-228">Tester l'application</span><span class="sxs-lookup"><span data-stu-id="4217e-228">Test the app</span></span>

<span data-ttu-id="4217e-229">Enregistrez vos modifications et lancez l’application.</span><span class="sxs-lookup"><span data-stu-id="4217e-229">Save your changes and launch the app.</span></span> <span data-ttu-id="4217e-230">Vous devriez être en mesure de vous déplacer entre les écrans à l’aide des boutons **Se** connectez et se connectez **et** de la barre d’onglets.</span><span class="sxs-lookup"><span data-stu-id="4217e-230">You should be able to move between the screens using the **Sign In** and **Sign Out** buttons and the tab bar.</span></span>

![Captures d’écran de l’application](images/app-screens.png)

<!-- markdownlint-disable MD002 MD041 -->

Commencez par créer un nouveau projet Swift.

1. Ouvrez Xcode. Dans **le** menu Fichier, **sélectionnez Nouveau,** puis **Projet**.
1. Choisissez le modèle **d’application** et sélectionnez **Suivant.**

    ![Capture d’écran de la boîte de dialogue de sélection du modèle Xcode](images/xcode-select-template.png)

1. Définissez **le nom du produit** et la `GraphTutorial` **langue** sur **Swift**.
1. Remplissez les champs restants et sélectionnez **Suivant**.
1. Choisissez un emplacement pour le projet, puis sélectionnez **Créer.**

## <a name="install-dependencies"></a>Installer les dépendances

Avant de passer à autre chose, installez des dépendances supplémentaires que vous utiliserez ultérieurement.

- [Bibliothèque d’authentification Microsoft (MSAL) pour iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) pour l’authentification auprès d’Azure AD.
- [SDK Microsoft Graph pour l’objectif C](https://github.com/microsoftgraph/msgraph-sdk-objc) pour effectuer des appels à Microsoft Graph.
- [Microsoft Graph Models SDK pour l’objectif C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) pour les objets fortement typés représentant des ressources Microsoft Graph telles que des utilisateurs ou des événements.

1. Quittez Xcode.
1. Ouvrez Terminal et modifiez le répertoire à l’emplacement de **votre projet GraphTutorial.**
1. Exécutez la commande suivante pour créer un podfile.

    ```Shell
    pod init
    ```

1. Ouvrez le podfile et ajoutez les lignes suivantes juste après la `use_frameworks!` ligne.

    ```Ruby
    pod 'MSAL', '~> 1.1.13'
    pod 'MSGraphClientSDK', ' ~> 1.0.0'
    pod 'MSGraphClientModels', '~> 1.3.0'
    ```

1. Enregistrez le podfile, puis exécutez la commande suivante pour installer les dépendances.

    ```Shell
    pod install
    ```

1. Une fois la commande terminée, ouvrez l’espace **GraphTutorial.xcworkspace** nouvellement créé dans Xcode.

## <a name="design-the-app"></a>Concevoir l’application

Dans cette section, vous allez créer les vues de l’application : une page de signature, un navigateur de barre d’onglets, une page d’accueil et une page de calendrier. Vous allez également créer une superposition d’indicateur d’activité.

### <a name="create-sign-in-page"></a>Page Créer une signature

1. Développez **le dossier GraphTutorial** dans Xcode, puis **sélectionnez ViewController.swift**.
1. Dans **l’Inspecteur de** fichier, modifiez **le nom** du fichier en `SignInViewController.swift` .

    ![Capture d’écran de l’Inspecteur de fichier](images/rename-file.png)

1. Ouvrez **SignInViewController.swift** et remplacez son contenu par le code suivant.

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

1. Ouvrez **Main.storyboard**. Développez **La scène du contrôleur de** vue, puis sélectionnez Contrôleur de **vue.**

    ![Capture d’écran de Xcode avec le contrôleur d’affichage sélectionné](images/storyboard-select-view-controller.png)

1. Sélectionnez **l’Inspecteur d’identité,** puis modifiez **la** classe de la catégorie à **SignInViewController**.

    ![Capture d’écran de l’Inspecteur d’identité](images/change-class.png)

1. Sélectionnez **la bibliothèque,** puis faites glisser un **bouton sur** le contrôleur **d’affichage de la signature.**

    ![Capture d’écran de la bibliothèque dans Xcode](images/add-button-to-view.png)

1. Une fois le bouton sélectionné, sélectionnez  l’Inspecteur d’attributs et modifiez le titre du bouton sur  `Sign In` .

    ![Capture d’écran du champ Titre dans l’inspecteur d’attributs dans Xcode](images/set-button-title.png)

1. Une fois le bouton sélectionné, sélectionnez le bouton **Aligner** en bas du storyboard. Sélectionnez les contraintes **Horizontalement** dans le conteneur et **Verticalement** dans les contraintes de conteneur, laissez leurs valeurs sous la forme 0, puis sélectionnez Ajouter **2 contraintes.**

    ![Capture d’écran des paramètres de contraintes d’alignement dans Xcode](images/add-alignment-constraints.png)

1. Sélectionnez **le contrôleur d’affichage de** connexion, puis **l’inspecteur de connexions.**
1. Sous **Actions reçues,** faites glisser le cercle non rempli en dessous de **la** signature sur le bouton. Sélectionnez **Toucher à l’intérieur** dans le menu déroulant.

    ![Capture d’écran du glissement de l’action signIn vers le bouton dans Xcode](images/connect-sign-in-button.png)

### <a name="create-tab-bar"></a>Créer une barre d’onglets

1. Sélectionnez la **bibliothèque,** puis faites glisser un contrôleur **de barre** d’onglets vers le storyboard.
1. Sélectionnez **le contrôleur d’affichage de** connexion, puis **l’inspecteur de connexions.**
1. Sous **Segues déclenchés,** faites glisser le cercle non rempli à côté du **manuel** sur le contrôleur de barre de **tabulation** dans le storyboard. Sélectionnez **Présenter en mode mod** dans le menu déroulant.

    ![Capture d’écran du glissement d’un segue manuel vers le nouveau contrôleur de barre d’onglets dans Xcode](images/add-segue.png)

1. Sélectionnez le segue que vous avez ajouté, puis sélectionnez **l’inspecteur d’attributs.** Définissez le **champ identificateur** sur , et `userSignedIn` **définissez Présentation** en plein **écran**.

    ![Capture d’écran du champ identificateur dans l’inspecteur d’attributs dans Xcode](images/set-segue-identifier.png)

1. Sélectionnez **la scène d’élément 1,** puis l’inspecteur **de connexions.**
1. Sous **Segues déclenchés,** faites glisser le  cercle non rempli en regard du manuel sur le contrôleur d’affichage de la signature **dans** le storyboard. Sélectionnez **Présenter en mode mod** dans le menu déroulant.
1. Sélectionnez le segue que vous avez ajouté, puis sélectionnez **l’inspecteur d’attributs.** Définissez le **champ identificateur** sur , et `userSignedOut` **définissez Présentation** en plein **écran**.

### <a name="create-welcome-page"></a>Créer une page d’accueil

1. Sélectionnez **le fichier Assets.x vhs.**
1. Dans le menu **Éditeur,** **sélectionnez Ajouter un nouvel actif,** puis Jeu **d’images.**
1. Sélectionnez la nouvelle **image et** utilisez l’inspecteur **d’attributs** pour définir son **nom** `DefaultUserPhoto` sur .
1. Ajoutez n’importe quelle image que vous souhaitez servir de photo de profil utilisateur par défaut.

    ![Capture d’écran de l’affichage actif Jeu d’images dans Xcode](images/add-default-user-photo.png)

1. Créez un **fichier de classe Cocoa Touch** dans le dossier **GraphTutorial** nommé `WelcomeViewController` . Choisissez **UIViewController dans** la **sous-classe du** champ.
1. Ouvrez **WelcomeViewController.swift** et remplacez son contenu par le code suivant.

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

1. Ouvrez **Main.storyboard**. Sélectionnez la **scène d’élément 1,** puis l’inspecteur **d’identité.** Modifiez **la valeur** de classe en **WelcomeViewController**.
1. À **l’aide de la bibliothèque**, ajoutez les éléments suivants à la **scène d’élément 1.**

    - Un **affichage d’image**
    - Deux **étiquettes**
    - Un **bouton**
1. À **l’aide de l’Inspecteur de connexions,** établir les connexions suivantes.

    - Lier la **sortie userDisplayName** à la première étiquette.
    - Lier la **sortie userEmail** à la deuxième étiquette.
    - Lier la **sortie userProfilePhoto** à la vue d’image.
    - Lier **l’action reçue de** connexion à l’action Touch Up Inside du **bouton.**

1. Sélectionnez l’affichage d’image, puis **l’inspecteur de taille.**
1. Définissez **la largeur** et la **hauteur** sur 196.
1. Utilisez le **bouton Aligner** pour ajouter la contrainte **Horizontalement dans le** conteneur avec la valeur 0.
1. Utilisez le **bouton Ajouter de nouvelles contraintes** (à côté du bouton **Aligner)** pour ajouter les contraintes suivantes :

    - Aligner le haut sur : zone sécurisée, valeur : 0
    - Espace inférieur à : Nom d’affichage de l’utilisateur, valeur : Standard
    - Hauteur, valeur : 196
    - Largeur, valeur : 196

    ![Capture d’écran des nouveaux paramètres de contraintes dans Xcode](./images/add-new-constraints.png)

1. Sélectionnez la première étiquette, puis utilisez le bouton **Aligner** pour ajouter la contrainte **Horizontalement** dans le conteneur avec la valeur 0.
1. Utilisez le **bouton Ajouter de nouvelles contraintes** pour ajouter les contraintes suivantes :

    - Espace supérieur vers : Photo de profil utilisateur, valeur : Standard
    - Bottom Space to: User Email, value: Standard

1. Sélectionnez la deuxième étiquette, puis **l’inspecteur d’attributs.**
1. Modifiez **la couleur** en gris **foncé** et modifiez la **police** sur **System 12.0**.
1. Utilisez le **bouton Aligner** pour ajouter la contrainte **Horizontalement dans le** conteneur avec la valeur 0.
1. Utilisez le **bouton Ajouter de nouvelles contraintes** pour ajouter les contraintes suivantes :

    - Espace supérieur : Nom d’affichage de l’utilisateur, valeur : Standard
    - Espace inférieur pour : Se sortir, valeur : 14

1. Sélectionnez le bouton, puis l’inspecteur **d’attributs.**
1. Changez **le titre** en `Sign Out` .
1. Utilisez le **bouton Aligner** pour ajouter la contrainte **Horizontalement dans le** conteneur avec la valeur 0.
1. Utilisez le **bouton Ajouter de nouvelles contraintes** pour ajouter les contraintes suivantes :

    - Espace supérieur à : Courrier électronique de l’utilisateur, valeur : 14

1. Sélectionnez l’élément de barre d’onglets en bas de la scène, puis sélectionnez **l’inspecteur d’attributs.** Changez **le titre** en `Me` .

La scène d’accueil doit ressembler à ceci une fois que vous avez terminé.

![Capture d’écran de la disposition de la scène d’accueil](images/welcome-scene-layout.png)

### <a name="create-calendar-page"></a>Créer une page de calendrier

1. Créez un **fichier de classe Cocoa Touch** dans le dossier **GraphTutorial** nommé `CalendarViewController` . Choisissez **UIViewController dans** la **sous-classe du** champ.
1. Ouvrez **CalendarViewController.swift** et remplacez son contenu par le code suivant.

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

1. Ouvrez **Main.storyboard**. Sélectionnez **la scène d’élément 2,** puis l’inspecteur **d’identité.** Modifiez **la valeur de** classe en **CalendarViewController**.
1. À **l’aide de la bibliothèque**, ajoutez **un affichage texte** à la **scène d’élément 2.**
1. Sélectionnez l’affichage texte que vous avez ajouté. Dans le menu **Éditeur,** **sélectionnez Incorporer** dans, puis **Défilement en mode .**
1. Resize the scroll view and text view to take up the entire screen.
1. À **l’aide de l’Inspecteur de connexions,** connectez la sortie **calendarJSON** à l’affichage texte.
1. Sélectionnez l’élément de barre d’onglets en bas de la scène, puis sélectionnez **l’inspecteur d’attributs.** Changez **le titre** en `Calendar` .
1. Dans le menu **Éditeur,** sélectionnez Résoudre les problèmes de disposition **automatique,** puis sélectionnez Ajouter des **contraintes manquantes** sous Tous les affichages dans le **contrôleur d’affichage calendrier.**

La scène de calendrier doit ressembler à ceci une fois que vous avez terminé.

![Capture d’écran de la disposition de la scène calendrier](images/calendar-scene-layout.png)

### <a name="create-activity-indicator"></a>Créer un indicateur d’activité

1. Créez un **fichier de classe Cocoa Touch** dans le dossier **GraphTutorial** nommé `SpinnerViewController` . Choisissez **UIViewController dans** la **sous-classe du** champ.
1. Ouvrez **SpinnerViewController.swift** et remplacez son contenu par le code suivant.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/SpinnerViewController.swift" id="SpinnerSnippet":::

## <a name="test-the-app"></a>Tester l'application

Enregistrez vos modifications et lancez l’application. Vous devriez être en mesure de vous déplacer entre les écrans à l’aide des boutons **Se** connectez et se connectez **et** de la barre d’onglets.

![Captures d’écran de l’application](images/app-screens.png)

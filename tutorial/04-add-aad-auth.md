<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="fca98-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="fca98-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="fca98-102">Cette étape est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="fca98-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="fca98-103">Pour ce faire, vous allez intégrer la bibliothèque d’authentification [Microsoft (MSAL) pour iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fca98-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) into the application.</span></span>

1. <span data-ttu-id="fca98-104">Créez un **fichier de liste de** propriétés dans le projet **GraphTutorial** nommé **AuthSettings.plist**.</span><span class="sxs-lookup"><span data-stu-id="fca98-104">Create a new **Property List** file in the **GraphTutorial** project named **AuthSettings.plist**.</span></span>
1. <span data-ttu-id="fca98-105">Ajoutez les éléments suivants au fichier dans **le dictionnaire** racine.</span><span class="sxs-lookup"><span data-stu-id="fca98-105">Add the following items to the file in the **Root** dictionary.</span></span>

    | <span data-ttu-id="fca98-106">Clé</span><span class="sxs-lookup"><span data-stu-id="fca98-106">Key</span></span> | <span data-ttu-id="fca98-107">Type</span><span class="sxs-lookup"><span data-stu-id="fca98-107">Type</span></span> | <span data-ttu-id="fca98-108">Valeur</span><span class="sxs-lookup"><span data-stu-id="fca98-108">Value</span></span> |
    |-----|------|-------|
    | `AppId` | <span data-ttu-id="fca98-109">Chaîne</span><span class="sxs-lookup"><span data-stu-id="fca98-109">String</span></span> | <span data-ttu-id="fca98-110">ID d’application à partir du portail Azure</span><span class="sxs-lookup"><span data-stu-id="fca98-110">The application ID from the Azure portal</span></span> |
    | `GraphScopes` | <span data-ttu-id="fca98-111">Tableau</span><span class="sxs-lookup"><span data-stu-id="fca98-111">Array</span></span> | <span data-ttu-id="fca98-112">Trois valeurs de `User.Read` chaîne : `MailboxSettings.Read` , et `Calendars.ReadWrite`</span><span class="sxs-lookup"><span data-stu-id="fca98-112">Three String values: `User.Read`, `MailboxSettings.Read`, and `Calendars.ReadWrite`</span></span> |

    ![Capture d’écran du fichier AuthSettings.plist dans Xcode](images/auth-settings.png)

> [!IMPORTANT]
> <span data-ttu-id="fca98-114">Si vous utilisez un contrôle source tel que Git, il est temps d’exclure le fichier **AuthSettings.plist** du contrôle source afin d’éviter toute fuite accidentelle de votre ID d’application.</span><span class="sxs-lookup"><span data-stu-id="fca98-114">If you're using source control such as git, now would be a good time to exclude the **AuthSettings.plist** file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="fca98-115">Implémentation de la connexion</span><span class="sxs-lookup"><span data-stu-id="fca98-115">Implement sign-in</span></span>

<span data-ttu-id="fca98-116">Dans cette section, vous allez configurer le projet pour MSAL, créer une classe de gestionnaire d’authentification et mettre à jour l’application pour qu’elle se connecte et se connecte.</span><span class="sxs-lookup"><span data-stu-id="fca98-116">In this section you will configure the project for MSAL, create an authentication manager class, and update the app to sign in and sign out.</span></span>

### <a name="configure-project-for-msal"></a><span data-ttu-id="fca98-117">Configurer le projet pour MSAL</span><span class="sxs-lookup"><span data-stu-id="fca98-117">Configure project for MSAL</span></span>

1. <span data-ttu-id="fca98-118">Ajoutez un nouveau groupe dechains aux fonctionnalités de votre projet.</span><span class="sxs-lookup"><span data-stu-id="fca98-118">Add a new keychain group to your project's capabilities.</span></span>
    1. <span data-ttu-id="fca98-119">Sélectionnez **le projet GraphTutorial,** puis & **fonctionnalités de signature.**</span><span class="sxs-lookup"><span data-stu-id="fca98-119">Select the **GraphTutorial** project, then **Signing & Capabilities**.</span></span>
    1. <span data-ttu-id="fca98-120">Sélectionnez **+ Fonctionnalité,** puis double-cliquez sur **Partage duchain.**</span><span class="sxs-lookup"><span data-stu-id="fca98-120">Select **+ Capability**, then double-click **Keychain Sharing**.</span></span>
    1. <span data-ttu-id="fca98-121">Ajoutez un groupe de chaînes de clés avec la valeur `com.microsoft.adalcache` .</span><span class="sxs-lookup"><span data-stu-id="fca98-121">Add a keychain group with the value `com.microsoft.adalcache`.</span></span>

1. <span data-ttu-id="fca98-122">Contrôlez **cliquez sur Info.plist** et **sélectionnez Ouvrir sous,** puis **Code source.**</span><span class="sxs-lookup"><span data-stu-id="fca98-122">Control click **Info.plist** and select **Open As**, then **Source Code**.</span></span>
1. <span data-ttu-id="fca98-123">Ajoutez ce qui suit à l’intérieur de `<dict>` l’élément.</span><span class="sxs-lookup"><span data-stu-id="fca98-123">Add the following inside the `<dict>` element.</span></span>

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        </array>
      </dict>
    </array>
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauthv2</string>
        <string>msauthv3</string>
    </array>
    ```

1. <span data-ttu-id="fca98-124">Ouvrez **AppDelegate.swift** et ajoutez l’instruction import suivante en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="fca98-124">Open **AppDelegate.swift** and add the following import statement at the top of the file.</span></span>

    ```Swift
    import MSAL
    ```

1. <span data-ttu-id="fca98-125">Ajoutez la fonction suivante à la classe `AppDelegate`.</span><span class="sxs-lookup"><span data-stu-id="fca98-125">Add the following function to the `AppDelegate` class.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/AppDelegate.swift" id="HandleMsalResponseSnippet":::

### <a name="create-authentication-manager"></a><span data-ttu-id="fca98-126">Créer un gestionnaire d’authentification</span><span class="sxs-lookup"><span data-stu-id="fca98-126">Create authentication manager</span></span>

1. <span data-ttu-id="fca98-127">Créez un **fichier Swift dans** le projet **GraphTutorial** nommé **AuthenticationManager.swift**.</span><span class="sxs-lookup"><span data-stu-id="fca98-127">Create a new **Swift File** in the **GraphTutorial** project named **AuthenticationManager.swift**.</span></span> <span data-ttu-id="fca98-128">Ajoutez le code suivant au fichier.</span><span class="sxs-lookup"><span data-stu-id="fca98-128">Add the following code to the file.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.swift" id="AuthManagerSnippet":::

### <a name="add-sign-in-and-sign-out"></a><span data-ttu-id="fca98-129">Ajout de la signature et de la sortie</span><span class="sxs-lookup"><span data-stu-id="fca98-129">Add sign-in and sign-out</span></span>

1. <span data-ttu-id="fca98-130">Ouvrez **SignInViewController.swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="fca98-130">Open **SignInViewController.swift** and replace its contents with the following code.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/SignInViewController.swift" id="SignInViewSnippet":::

1. <span data-ttu-id="fca98-131">Ouvrez **WelcomeViewController.swift** et remplacez la fonction `signOut` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="fca98-131">Open **WelcomeViewController.swift** and replace the existing `signOut` function with the following.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.swift" id="SignOutSnippet":::

1. <span data-ttu-id="fca98-132">Enregistrez vos modifications et redémarrez l’application dans simulateur.</span><span class="sxs-lookup"><span data-stu-id="fca98-132">Save your changes and restart the application in Simulator.</span></span>

<span data-ttu-id="fca98-133">Si vous vous connectez à l’application, un jeton d’accès doit s’afficher dans la fenêtre de sortie dans Xcode.</span><span class="sxs-lookup"><span data-stu-id="fca98-133">If you sign in to the app, you should see an access token displayed in the output window in Xcode.</span></span>

![Capture d’écran de la fenêtre de sortie dans Xcode affichant un jeton d’accès](images/access-token-output.png)

## <a name="get-user-details"></a><span data-ttu-id="fca98-135">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="fca98-135">Get user details</span></span>

<span data-ttu-id="fca98-136">Dans cette section, vous allez créer une classe d’aide pour contenir tous les appels à Microsoft Graph et mettre à jour la classe pour utiliser cette nouvelle classe afin d’obtenir l’utilisateur `WelcomeViewController` connecté.</span><span class="sxs-lookup"><span data-stu-id="fca98-136">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `WelcomeViewController` to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="fca98-137">Créez un **fichier Swift** dans le **projet GraphTutorial** nommé **GraphManager.swift**.</span><span class="sxs-lookup"><span data-stu-id="fca98-137">Create a new **Swift File** in the **GraphTutorial** project named **GraphManager.swift**.</span></span> <span data-ttu-id="fca98-138">Ajoutez le code suivant au fichier.</span><span class="sxs-lookup"><span data-stu-id="fca98-138">Add the following code to the file.</span></span>

    ```Swift
    import Foundation
    import MSGraphClientSDK
    import MSGraphClientModels

    class GraphManager {

        // Implement singleton pattern
        static let instance = GraphManager()

        private let client: MSHTTPClient?

        public var userTimeZone: String

        private init() {
            client = MSClientFactory.createHTTPClient(with: AuthenticationManager.instance)
            userTimeZone = "UTC"
        }

        public func getMe(completion: @escaping(MSGraphUser?, Error?) -> Void) {
            // GET /me
            let select = "$select=displayName,mail,mailboxSettings,userPrincipalName"
            let meRequest = NSMutableURLRequest(url: URL(string: "\(MSGraphBaseURL)/me?\(select)")!)
            let meDataTask = MSURLSessionDataTask(request: meRequest, client: self.client, completion: {
                (data: Data?, response: URLResponse?, graphError: Error?) in
                guard let meData = data, graphError == nil else {
                    completion(nil, graphError)
                    return
                }

                do {
                    // Deserialize response as a user
                    let user = try MSGraphUser(data: meData)
                    completion(user, nil)
                } catch {
                    completion(nil, error)
                }
            })

            // Execute the request
            meDataTask?.execute()
        }
    }
    ```

1. <span data-ttu-id="fca98-139">Ouvrez **WelcomeViewController.swift** et ajoutez l’instruction `import` suivante en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="fca98-139">Open **WelcomeViewController.swift** and add the following `import` statement at the top of the file.</span></span>

    ```Swift
    import MSGraphClientModels
    ```

1. <span data-ttu-id="fca98-140">Ajoutez la propriété ci-dessous à la classe `WelcomeViewController`.</span><span class="sxs-lookup"><span data-stu-id="fca98-140">Add the following property to the `WelcomeViewController` class.</span></span>

    ```Swift
    private let spinner = SpinnerViewController()
    ```

1. <span data-ttu-id="fca98-141">Remplacez `viewDidLoad` l’existant par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="fca98-141">Replace the existing `viewDidLoad` with the following code.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.swift" id="ViewDidLoadSnippet":::

<span data-ttu-id="fca98-142">Si vous enregistrez vos modifications et redémarrez l’application maintenant, après la mise à jour de l’interface utilisateur avec le nom d’affichage et l’adresse e-mail de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fca98-142">If you save your changes and restart the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>

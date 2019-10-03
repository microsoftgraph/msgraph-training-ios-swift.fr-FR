<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="77981-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="77981-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="77981-102">Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="77981-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="77981-103">Pour ce faire, vous allez intégrer la [bibliothèque d’authentification Microsoft (MSAL) pour iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="77981-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) into the application.</span></span>

1. <span data-ttu-id="77981-104">Créez un nouveau fichier de **liste de propriétés** dans le projet **GraphTutorial** nommé **AuthSettings. plist**.</span><span class="sxs-lookup"><span data-stu-id="77981-104">Create a new **Property List** file in the **GraphTutorial** project named **AuthSettings.plist**.</span></span>
1. <span data-ttu-id="77981-105">Ajoutez les éléments suivants au fichier dans le dictionnaire **racine** .</span><span class="sxs-lookup"><span data-stu-id="77981-105">Add the following items to the file in the **Root** dictionary.</span></span>

    | <span data-ttu-id="77981-106">Key</span><span class="sxs-lookup"><span data-stu-id="77981-106">Key</span></span> | <span data-ttu-id="77981-107">Type</span><span class="sxs-lookup"><span data-stu-id="77981-107">Type</span></span> | <span data-ttu-id="77981-108">Valeur</span><span class="sxs-lookup"><span data-stu-id="77981-108">Value</span></span> |
    |-----|------|-------|
    | `AppId` | <span data-ttu-id="77981-109">String</span><span class="sxs-lookup"><span data-stu-id="77981-109">String</span></span> | <span data-ttu-id="77981-110">ID de l’application à partir du portail Azure</span><span class="sxs-lookup"><span data-stu-id="77981-110">The application ID from the Azure portal</span></span> |
    | `GraphScopes` | <span data-ttu-id="77981-111">Tableau</span><span class="sxs-lookup"><span data-stu-id="77981-111">Array</span></span> | <span data-ttu-id="77981-112">Deux valeurs de chaîne `User.Read` : et`Calendars.Read`</span><span class="sxs-lookup"><span data-stu-id="77981-112">Two String values: `User.Read` and `Calendars.Read`</span></span> |

    ![Capture d’écran du fichier AuthSettings. plist dans Xcode](./images/auth-settings.png)

> [!IMPORTANT]
> <span data-ttu-id="77981-114">Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le fichier **AuthSettings. plist** du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application.</span><span class="sxs-lookup"><span data-stu-id="77981-114">If you're using source control such as git, now would be a good time to exclude the **AuthSettings.plist** file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="77981-115">Mettre en œuvre la connexion</span><span class="sxs-lookup"><span data-stu-id="77981-115">Implement sign-in</span></span>

<span data-ttu-id="77981-116">Dans cette section, vous allez configurer le projet pour MSAL, créer une classe de gestionnaire d’authentification, puis mettre à jour l’application pour se connecter et se déconnecter.</span><span class="sxs-lookup"><span data-stu-id="77981-116">In this section you will configure the project for MSAL, create an authentication manager class, and update the app to sign in and sign out.</span></span>

### <a name="configure-project-for-msal"></a><span data-ttu-id="77981-117">Configurer Project pour MSAL</span><span class="sxs-lookup"><span data-stu-id="77981-117">Configure project for MSAL</span></span>

1. <span data-ttu-id="77981-118">Contrôle cliquez sur **info. plist** , sélectionnez **ouvrir en tant que**, puis **code source**.</span><span class="sxs-lookup"><span data-stu-id="77981-118">Control click **Info.plist** and select **Open As**, then **Source Code**.</span></span>
1. <span data-ttu-id="77981-119">Ajoutez les éléments suivants à `<dict>` l’intérieur de l’élément.</span><span class="sxs-lookup"><span data-stu-id="77981-119">Add the following inside the `<dict>` element.</span></span>

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
    <array>
        <string>msauthv2</string>
        <string>msauthv3</string>
    </array>
    ```

1. <span data-ttu-id="77981-120">Ouvrez **AppDelegate. Swift** et ajoutez l’instruction import suivante en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="77981-120">Open **AppDelegate.swift** and add the following import statement at the top of the file.</span></span>

    ```Swift
    import MSAL
    ```

1. <span data-ttu-id="77981-121">Ajoutez la fonction suivante à la classe `AppDelegate`.</span><span class="sxs-lookup"><span data-stu-id="77981-121">Add the following function to the `AppDelegate` class.</span></span>

    ```Swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
    ```

### <a name="create-authentication-manager"></a><span data-ttu-id="77981-122">Créer le gestionnaire d’authentification</span><span class="sxs-lookup"><span data-stu-id="77981-122">Create authentication manager</span></span>

1. <span data-ttu-id="77981-123">Créez un **fichier SWIFT** dans le projet **GraphTutorial** nommé **AuthenticationManager. Swift**.</span><span class="sxs-lookup"><span data-stu-id="77981-123">Create a new **Swift File** in the **GraphTutorial** project named **AuthenticationManager.swift**.</span></span> <span data-ttu-id="77981-124">Ajoutez le code suivant au fichier.</span><span class="sxs-lookup"><span data-stu-id="77981-124">Add the following code to the file.</span></span>

    ```Swift
    import Foundation
    import MSAL

    class AuthenticationManager {

        // Implement singleton pattern
        static let instance = AuthenticationManager()

        private let publicClient: MSALPublicClientApplication?
        private let appId: String
        private let graphScopes: Array<String>

        private init() {
            // Get app ID and scopes from AuthSettings.plist
            let bundle = Bundle.main
            let authConfigPath = bundle.path(forResource: "AuthSettings", ofType: "plist")!
            let authConfig = NSDictionary(contentsOfFile: authConfigPath)!

            self.appId = authConfig["AppId"] as! String
            self.graphScopes = authConfig["GraphScopes"] as! Array<String>

            do {
                // Create the MSAL client
                try self.publicClient = MSALPublicClientApplication(clientId: self.appId,
                                                                    keychainGroup: bundle.bundleIdentifier)
            } catch {
                print("Error creating MSAL public client: \(error)")
                self.publicClient = nil
            }
        }

        public func getPublicClient() -> MSALPublicClientApplication? {
            return self.publicClient
        }

        public func getTokenInteractively(completion: @escaping(_ accessToken: String?, Error?) -> Void) {
            // Call acquireToken to open a browser so the user can sign in
            publicClient?.acquireToken(forScopes: self.graphScopes, completionBlock: {
                (result: MSALResult?, error: Error?) in
                guard let tokenResult = result, error == nil else {
                    print("Error getting token interactively: \(String(describing: error))")
                    completion(nil, error)
                    return
                }

                print("Got token interactively: \(tokenResult.accessToken)")
                completion(tokenResult.accessToken, nil)
            })
        }

        public func getTokenSilently(completion: @escaping(_ accessToken: String?, Error?) -> Void) {
            // Check if there is an account in the cache
            var userAccount: MSALAccount?

            do {
                userAccount = try publicClient?.allAccounts().first
            } catch {
                print("Error getting account: \(error)")
            }

            if (userAccount != nil) {
                // Attempt to get token silently
                publicClient?.acquireTokenSilent(forScopes: self.graphScopes, account: userAccount!, completionBlock: {
                    (result: MSALResult?, error: Error?) in
                    guard let tokenResult = result, error == nil else {
                        print("Error getting token silently: \(String(describing: error))")
                        completion(nil, error)
                        return
                    }

                    print("Got token silently: \(tokenResult.accessToken)")
                    completion(tokenResult.accessToken, nil)
                })
            } else {
                print("No account in cache")
                completion(nil, NSError(domain: "AuthenticationManager",
                                        code: MSALErrorCode.interactionRequired.rawValue, userInfo: nil))
            }
        }

        public func signOut() -> Void {
            do {
                // Remove all accounts from the cache
                let accounts = try publicClient?.allAccounts()

                try accounts!.forEach({
                    (account: MSALAccount) in
                    try publicClient?.remove(account)
                })
            } catch {
                print("Sign out error: \(String(describing: error))")
            }
        }
    }
    ```

### <a name="add-sign-in-and-sign-out"></a><span data-ttu-id="77981-125">Ajouter la connexion et la déconnexion</span><span class="sxs-lookup"><span data-stu-id="77981-125">Add sign-in and sign-out</span></span>

1. <span data-ttu-id="77981-126">Ouvrez **SignInViewController. Swift** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="77981-126">Open **SignInViewController.swift** and replace its contents with the following code.</span></span>

    ```Swift
    import UIKit

    class SignInViewController: UIViewController {

        private let spinner = SpinnerViewController()

        override func viewDidLoad() {
            super.viewDidLoad()
            // Do any additional setup after loading the view.

            // See if a user is already signed in
            spinner.start(container: self)

            AuthenticationManager.instance.getTokenSilently {
                (token: String?, error: Error?) in

                DispatchQueue.main.async {
                    self.spinner.stop()

                    guard let _ = token, error == nil else {
                        // If there is no token or if there's an error,
                        // no user is signed in, so stay here
                        return
                    }

                    // Since we got a token, a user is signed in
                    // Go to welcome page
                    self.performSegue(withIdentifier: "userSignedIn", sender: nil)
                }
            }
        }

        @IBAction func signIn() {
            spinner.start(container: self)

            // Do an interactive sign in
            AuthenticationManager.instance.getTokenInteractively {
                (token: String?, error: Error?) in

                DispatchQueue.main.async {
                    self.spinner.stop()

                    guard let _ = token, error == nil else {
                        // Show the error and stay on the sign-in page
                        let alert = UIAlertController(title: "Error signing in",
                                                      message: error.debugDescription,
                                                      preferredStyle: .alert)

                        alert.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
                        self.present(alert, animated: true)
                        return
                    }

                    // Signed in successfully
                    // Go to welcome page
                    self.performSegue(withIdentifier: "userSignedIn", sender: nil)
                }
            }
        }
    }
    ```

1. <span data-ttu-id="77981-127">Ouvrez **WelcomeViewController. Swift** et remplacez la fonction `signOut` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="77981-127">Open **WelcomeViewController.swift** and replace the existing `signOut` function with the following.</span></span>

    ```Swift
    @IBAction func signOut() {
        AuthenticationManager.instance.signOut()
        self.performSegue(withIdentifier: "userSignedOut", sender: nil)
    }
    ```

1. <span data-ttu-id="77981-128">Enregistrez vos modifications et redémarrez l’application dans Simulator.</span><span class="sxs-lookup"><span data-stu-id="77981-128">Save your changes and restart the application in Simulator.</span></span>

<span data-ttu-id="77981-129">Si vous vous connectez à l’application, vous devriez voir un jeton d’accès affiché dans la fenêtre sortie dans Xcode.</span><span class="sxs-lookup"><span data-stu-id="77981-129">If you sign in to the app, you should see an access token displayed in the output window in Xcode.</span></span>

![Capture d’écran de la fenêtre sortie dans Xcode affichant un jeton d’accès](./images/access-token-output.png)

## <a name="get-user-details"></a><span data-ttu-id="77981-131">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="77981-131">Get user details</span></span>

<span data-ttu-id="77981-132">Dans cette section, vous allez créer une classe d’assistance pour contenir tous les appels à Microsoft Graph et mettre à `WelcomeViewController` jour le pour utiliser cette nouvelle classe afin d’obtenir l’utilisateur connecté.</span><span class="sxs-lookup"><span data-stu-id="77981-132">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `WelcomeViewController` to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="77981-133">Ouvrez **AuthenticationManager. Swift** et ajoutez la fonction suivante à la `AuthenticationManager` classe.</span><span class="sxs-lookup"><span data-stu-id="77981-133">Open **AuthenticationManager.swift** and add the following function to the `AuthenticationManager` class.</span></span>

    ```Swift
    public func getGraphAuthProvider() -> MSALAuthenticationProvider? {
        // Create an MSAL auth provider for use with the Graph client
        return MSALAuthenticationProvider(publicClientApplication: self.publicClient,
                                          andScopes: self.graphScopes)
    }
    ```

1. <span data-ttu-id="77981-134">Créez un **fichier SWIFT** dans le projet **GraphTutorial** nommé **GraphManager. Swift**.</span><span class="sxs-lookup"><span data-stu-id="77981-134">Create a new **Swift File** in the **GraphTutorial** project named **GraphManager.swift**.</span></span> <span data-ttu-id="77981-135">Ajoutez le code suivant au fichier.</span><span class="sxs-lookup"><span data-stu-id="77981-135">Add the following code to the file.</span></span>

    ```Swift
    import Foundation
    import MSGraphClientSDK
    import MSGraphClientModels

    class GraphManager {

        // Implement singleton pattern
        static let instance = GraphManager()

        private let client: MSHTTPClient?

        private init() {
            client = MSClientFactory.createHTTPClient(with: AuthenticationManager.instance.getGraphAuthProvider())
        }

        public func getMe(completion: @escaping(MSGraphUser?, Error?) -> Void) {
            // GET /me
            let meRequest = NSMutableURLRequest(url: URL(string: "\(MSGraphBaseURL)/me")!)
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

1. <span data-ttu-id="77981-136">Ouvrez **WelcomeViewController. Swift** et ajoutez l’instruction `import` suivante en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="77981-136">Open **WelcomeViewController.swift** and add the following `import` statement at the top of the file.</span></span>

    ```Swift
    import MSGraphClientModels
    ```

1. <span data-ttu-id="77981-137">Ajoutez la propriété suivante à la `WelcomeViewController` classe.</span><span class="sxs-lookup"><span data-stu-id="77981-137">Add the following property to the `WelcomeViewController` class.</span></span>

    ```Swift
    private let spinner = SpinnerViewController()
    ```

1. <span data-ttu-id="77981-138">Remplacez le code `viewDidLoad` existant par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="77981-138">Replace the existing `viewDidLoad` with the following code.</span></span>

    ```Swift
    override func viewDidLoad() {
        super.viewDidLoad()

        // Do any additional setup after loading the view.

        self.spinner.start(container: self)

        // Get the signed-in user
        self.userProfilePhoto.image = UIImage(imageLiteralResourceName: "DefaultUserPhoto")

        GraphManager.instance.getMe {
            (user: MSGraphUser?, error: Error?) in

            DispatchQueue.main.async {
                self.spinner.stop()

                guard let currentUser = user, error == nil else {
                    print("Error getting user: \(String(describing: error))")
                    return
                }

                // Set display name
                self.userDisplayName.text = currentUser.displayName ?? "Mysterious Stranger"
                self.userDisplayName.sizeToFit()

                // AAD users have email in the mail attribute
                // Personal accounts have email in the userPrincipalName attribute
                self.userEmail.text = currentUser.mail ?? currentUser.userPrincipalName ?? ""
                self.userEmail.sizeToFit()
            }
        }
    }
    ```

<span data-ttu-id="77981-139">Si vous enregistrez vos modifications et redémarrez l’application maintenant, une fois connecté, l’interface utilisateur est mise à jour avec le nom d’affichage et l’adresse de messagerie de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="77981-139">If you save your changes and restart the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>

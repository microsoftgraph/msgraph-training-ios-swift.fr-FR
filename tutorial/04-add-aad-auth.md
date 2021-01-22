<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD. Cette étape est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph. Pour ce faire, vous allez intégrer la bibliothèque d’authentification [Microsoft (MSAL) pour iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) dans l’application.

1. Créez un **fichier de liste de** propriétés dans le projet **GraphTutorial** nommé **AuthSettings.plist**.
1. Ajoutez les éléments suivants au fichier dans **le dictionnaire** racine.

    | Clé | Type | Valeur |
    |-----|------|-------|
    | `AppId` | Chaîne | ID d’application à partir du portail Azure |
    | `GraphScopes` | Tableau | Trois valeurs de `User.Read` chaîne : `MailboxSettings.Read` , et `Calendars.ReadWrite` |

    ![Capture d’écran du fichier AuthSettings.plist dans Xcode](images/auth-settings.png)

> [!IMPORTANT]
> Si vous utilisez un contrôle source tel que Git, il est temps d’exclure le fichier **AuthSettings.plist** du contrôle source afin d’éviter toute fuite accidentelle de votre ID d’application.

## <a name="implement-sign-in"></a>Implémentation de la connexion

Dans cette section, vous allez configurer le projet pour MSAL, créer une classe de gestionnaire d’authentification et mettre à jour l’application pour qu’elle se connecte et se connecte.

### <a name="configure-project-for-msal"></a>Configurer le projet pour MSAL

1. Ajoutez un nouveau groupe dechains aux fonctionnalités de votre projet.
    1. Sélectionnez **le projet GraphTutorial,** puis & **fonctionnalités de signature.**
    1. Sélectionnez **+ Fonctionnalité,** puis double-cliquez sur **Partage duchain.**
    1. Ajoutez un groupe de chaînes de clés avec la valeur `com.microsoft.adalcache` .

1. Contrôlez **cliquez sur Info.plist** et **sélectionnez Ouvrir sous,** puis **Code source.**
1. Ajoutez ce qui suit à l’intérieur de `<dict>` l’élément.

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

1. Ouvrez **AppDelegate.swift** et ajoutez l’instruction import suivante en haut du fichier.

    ```Swift
    import MSAL
    ```

1. Ajoutez la fonction suivante à la classe `AppDelegate`.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/AppDelegate.swift" id="HandleMsalResponseSnippet":::

### <a name="create-authentication-manager"></a>Créer un gestionnaire d’authentification

1. Créez un **fichier Swift dans** le projet **GraphTutorial** nommé **AuthenticationManager.swift**. Ajoutez le code suivant au fichier.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.swift" id="AuthManagerSnippet":::

### <a name="add-sign-in-and-sign-out"></a>Ajout de la signature et de la sortie

1. Ouvrez **SignInViewController.swift** et remplacez son contenu par le code suivant.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/SignInViewController.swift" id="SignInViewSnippet":::

1. Ouvrez **WelcomeViewController.swift** et remplacez la fonction `signOut` existante par ce qui suit.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.swift" id="SignOutSnippet":::

1. Enregistrez vos modifications et redémarrez l’application dans simulateur.

Si vous vous connectez à l’application, un jeton d’accès doit s’afficher dans la fenêtre de sortie dans Xcode.

![Capture d’écran de la fenêtre de sortie dans Xcode affichant un jeton d’accès](images/access-token-output.png)

## <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

Dans cette section, vous allez créer une classe d’aide pour contenir tous les appels à Microsoft Graph et mettre à jour la classe pour utiliser cette nouvelle classe afin d’obtenir l’utilisateur `WelcomeViewController` connecté.

1. Créez un **fichier Swift** dans le **projet GraphTutorial** nommé **GraphManager.swift**. Ajoutez le code suivant au fichier.

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

1. Ouvrez **WelcomeViewController.swift** et ajoutez l’instruction `import` suivante en haut du fichier.

    ```Swift
    import MSGraphClientModels
    ```

1. Ajoutez la propriété ci-dessous à la classe `WelcomeViewController`.

    ```Swift
    private let spinner = SpinnerViewController()
    ```

1. Remplacez `viewDidLoad` l’existant par le code suivant.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.swift" id="ViewDidLoadSnippet":::

Si vous enregistrez vos modifications et redémarrez l’application maintenant, après la mise à jour de l’interface utilisateur avec le nom d’affichage et l’adresse e-mail de l’utilisateur.

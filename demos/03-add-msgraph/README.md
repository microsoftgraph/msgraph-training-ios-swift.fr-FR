# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="e2d6f-101">Exécution du projet terminé</span><span class="sxs-lookup"><span data-stu-id="e2d6f-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e2d6f-102">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="e2d6f-102">Prerequisites</span></span>

<span data-ttu-id="e2d6f-103">Pour exécuter le projet terminé dans ce dossier, vous avez besoin des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="e2d6f-103">To run the completed project in this folder, you need the following:</span></span>

- [<span data-ttu-id="e2d6f-104">Xcode</span><span class="sxs-lookup"><span data-stu-id="e2d6f-104">Xcode</span></span>](https://developer.apple.com/xcode/)
- [<span data-ttu-id="e2d6f-105">CocoaPods</span><span class="sxs-lookup"><span data-stu-id="e2d6f-105">CocoaPods</span></span>](https://cocoapods.org)
- <span data-ttu-id="e2d6f-106">Soit un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com, soit un compte professionnel ou scolaire Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-106">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

> <span data-ttu-id="e2d6f-107">**Remarque :** Ce didacticiel a été écrit à l’aide de Xcode version 10,3 et CocoaPods version 1.7.5.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-107">**Note:** This tutorial was written using Xcode version 10.3 and CocoaPods version 1.7.5.</span></span> <span data-ttu-id="e2d6f-108">Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-108">The steps in this guide may work with other versions, but that has not been tested.</span></span>

<span data-ttu-id="e2d6f-109">Si vous n’avez pas de compte Microsoft, vous disposez de deux options pour obtenir un compte gratuit :</span><span class="sxs-lookup"><span data-stu-id="e2d6f-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="e2d6f-110">Vous pouvez vous [inscrire pour obtenir un nouveau compte Microsoft personnel](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="e2d6f-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="e2d6f-111">Vous pouvez vous [inscrire au programme pour les développeurs office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement gratuit à Office 365.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="e2d6f-112">Enregistrer une application Web avec le centre d’administration Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="e2d6f-112">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="e2d6f-113">Ouvrez un navigateur et accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="e2d6f-113">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="e2d6f-114">Connectez-vous à l’aide d’un **compte personnel** (compte Microsoft) ou d’un **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="e2d6f-115">Sélectionnez **Azure Active Directory** dans le volet de navigation de gauche, puis sélectionnez **inscriptions des applications** sous **gérer**.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-115">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="e2d6f-116">Capture d’écran des inscriptions d’application</span><span class="sxs-lookup"><span data-stu-id="e2d6f-116">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="e2d6f-117">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-117">Select **New registration**.</span></span> <span data-ttu-id="e2d6f-118">Sur la page **Inscrire une application**, définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-118">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="e2d6f-119">Définissez le **Nom** sur `iOS Swift Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-119">Set **Name** to `iOS Swift Graph Tutorial`.</span></span>
    - <span data-ttu-id="e2d6f-120">Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-120">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="e2d6f-121">Sous **URI de redirection**, remplacez la liste déroulante par **client Public (mobile & Desktop)** et définissez `msauth.YOUR_BUNDLE_ID://auth`la valeur `YOUR_BUNDLE_ID` sur, en remplaçant par l’ID de la tresse de votre application.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-121">Under **Redirect URI**, change the dropdown to **Public client (mobile & desktop)**, and set the value to `msauth.YOUR_BUNDLE_ID://auth`, replacing `YOUR_BUNDLE_ID` with the bundle ID for your app.</span></span>

    ![Capture d’écran de la page inscrire une application](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="e2d6f-123">Choisissez **Inscrire**.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-123">Choose **Register**.</span></span> <span data-ttu-id="e2d6f-124">Sur la page **didacticiel de Graph SWIFT iOS** , copiez la valeur de l' **ID d’application (client)** et enregistrez-la, vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-124">On the **iOS Swift Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Capture d’écran de l’ID d’application de la nouvelle inscription de l’application](/tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a><span data-ttu-id="e2d6f-126">Configurer l’exemple</span><span class="sxs-lookup"><span data-stu-id="e2d6f-126">Configure the sample</span></span>

1. <span data-ttu-id="e2d6f-127">Renommez `AuthSettings.plist.example` le fichier `AuthSettings.plist`.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-127">Rename the `AuthSettings.plist.example` file to `AuthSettings.plist`.</span></span>
1. <span data-ttu-id="e2d6f-128">Modifiez le `AuthSettings.plist` fichier et effectuez les modifications suivantes.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-128">Edit the `AuthSettings.plist` file and make the following changes.</span></span>
    1. <span data-ttu-id="e2d6f-129">Remplacez `YOUR_APP_ID_HERE` par l' **ID d’application** que vous avez obtenu à partir du portail d’inscription des applications.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-129">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="e2d6f-130">Ouvrez terminal dans le répertoire **GraphTutorial** et exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-130">Open Terminal in the **GraphTutorial** directory and run the following command.</span></span>

    ```Shell
    pod install
    ```

## <a name="run-the-sample"></a><span data-ttu-id="e2d6f-131">Exécution de l’exemple</span><span class="sxs-lookup"><span data-stu-id="e2d6f-131">Run the sample</span></span>

<span data-ttu-id="e2d6f-132">Dans Xcode, sélectionnez le menu du **produit** , puis sélectionnez **exécuter**.</span><span class="sxs-lookup"><span data-stu-id="e2d6f-132">In Xcode, select the **Product** menu, then select **Run**.</span></span>

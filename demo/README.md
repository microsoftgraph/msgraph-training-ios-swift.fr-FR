# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="bc19b-101">Exécution du projet terminé</span><span class="sxs-lookup"><span data-stu-id="bc19b-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bc19b-102">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="bc19b-102">Prerequisites</span></span>

<span data-ttu-id="bc19b-103">Pour exécuter le projet terminé dans ce dossier, vous avez besoin des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="bc19b-103">To run the completed project in this folder, you need the following:</span></span>

- [<span data-ttu-id="bc19b-104">Xcode</span><span class="sxs-lookup"><span data-stu-id="bc19b-104">Xcode</span></span>](https://developer.apple.com/xcode/)
- [<span data-ttu-id="bc19b-105">CocoaPods</span><span class="sxs-lookup"><span data-stu-id="bc19b-105">CocoaPods</span></span>](https://cocoapods.org)
- <span data-ttu-id="bc19b-106">Soit un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com, soit un compte professionnel ou scolaire Microsoft.</span><span class="sxs-lookup"><span data-stu-id="bc19b-106">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

> <span data-ttu-id="bc19b-107">**Remarque :** Ce didacticiel a été écrit à l’aide de Xcode version 11,4 et CocoaPods version 1.9.1.</span><span class="sxs-lookup"><span data-stu-id="bc19b-107">**Note:** This tutorial was written using Xcode version 11.4 and CocoaPods version 1.9.1.</span></span> <span data-ttu-id="bc19b-108">Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.</span><span class="sxs-lookup"><span data-stu-id="bc19b-108">The steps in this guide may work with other versions, but that has not been tested.</span></span>

<span data-ttu-id="bc19b-109">Si vous n’avez pas de compte Microsoft, vous disposez de deux options pour obtenir un compte gratuit :</span><span class="sxs-lookup"><span data-stu-id="bc19b-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="bc19b-110">Vous pouvez vous [inscrire pour obtenir un nouveau compte Microsoft personnel](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="bc19b-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="bc19b-111">Vous pouvez vous [inscrire au programme pour les développeurs office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement gratuit à Office 365.</span><span class="sxs-lookup"><span data-stu-id="bc19b-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="bc19b-112">Enregistrer une application Web avec le centre d’administration Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="bc19b-112">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="bc19b-113">Ouvrez un navigateur et accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="bc19b-113">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="bc19b-114">Connectez-vous à l’aide d’un **compte personnel** (compte Microsoft) ou d’un **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="bc19b-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="bc19b-115">Sélectionnez **Azure Active Directory** dans le volet de navigation gauche, puis sélectionnez **Inscriptions d’applications** sous **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="bc19b-115">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="bc19b-116">Une capture d’écran des inscriptions d’applications</span><span class="sxs-lookup"><span data-stu-id="bc19b-116">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="bc19b-117">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="bc19b-117">Select **New registration**.</span></span> <span data-ttu-id="bc19b-118">Sur la page **Inscrire une application**, définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="bc19b-118">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="bc19b-119">Définissez le **Nom** sur `iOS Swift Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="bc19b-119">Set **Name** to `iOS Swift Graph Tutorial`.</span></span>
    - <span data-ttu-id="bc19b-120">Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="bc19b-120">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="bc19b-121">Laissez **Redirect URI** vide.</span><span class="sxs-lookup"><span data-stu-id="bc19b-121">Leave **Redirect URI** empty.</span></span>

    ![Capture d’écran de la page Inscrire une application](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="bc19b-123">Choisissez **Inscrire**.</span><span class="sxs-lookup"><span data-stu-id="bc19b-123">Choose **Register**.</span></span> <span data-ttu-id="bc19b-124">Sur la page **didacticiel de Graph SWIFT iOS** , copiez la valeur de l' **ID d’application (client)** et enregistrez-la, vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="bc19b-124">On the **iOS Swift Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Une capture d’écran de l’ID d’application de la nouvelle inscription d'application](/tutorial/images/aad-application-id.png)

1. <span data-ttu-id="bc19b-126">Sous **Gérer**, sélectionnez **Authentification**.</span><span class="sxs-lookup"><span data-stu-id="bc19b-126">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="bc19b-127">Sélectionnez **Ajouter une plateforme**, puis **iOS/MacOS**.</span><span class="sxs-lookup"><span data-stu-id="bc19b-127">Select **Add a platform**, then **iOS / macOS**.</span></span>

1. <span data-ttu-id="bc19b-128">Entrez l’ID de package de votre application, sélectionnez **configurer**, puis **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="bc19b-128">Enter your app's Bundle ID and select **Configure**, then select **Done**.</span></span>

## <a name="configure-the-sample"></a><span data-ttu-id="bc19b-129">Configurer l’exemple</span><span class="sxs-lookup"><span data-stu-id="bc19b-129">Configure the sample</span></span>

1. <span data-ttu-id="bc19b-130">Renommez `AuthSettings.plist.example` le fichier `AuthSettings.plist`.</span><span class="sxs-lookup"><span data-stu-id="bc19b-130">Rename the `AuthSettings.plist.example` file to `AuthSettings.plist`.</span></span>
1. <span data-ttu-id="bc19b-131">Modifiez le `AuthSettings.plist` fichier et effectuez les modifications suivantes.</span><span class="sxs-lookup"><span data-stu-id="bc19b-131">Edit the `AuthSettings.plist` file and make the following changes.</span></span>
    1. <span data-ttu-id="bc19b-132">Remplacez `YOUR_APP_ID_HERE` par l' **ID d’application** que vous avez obtenu à partir du portail d’inscription des applications.</span><span class="sxs-lookup"><span data-stu-id="bc19b-132">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="bc19b-133">Ouvrez terminal dans le répertoire **GraphTutorial** et exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="bc19b-133">Open Terminal in the **GraphTutorial** directory and run the following command.</span></span>

    ```Shell
    pod install
    ```

## <a name="run-the-sample"></a><span data-ttu-id="bc19b-134">Exécution de l’exemple</span><span class="sxs-lookup"><span data-stu-id="bc19b-134">Run the sample</span></span>

<span data-ttu-id="bc19b-135">Dans Xcode, sélectionnez le menu du **produit** , puis sélectionnez **exécuter**.</span><span class="sxs-lookup"><span data-stu-id="bc19b-135">In Xcode, select the **Product** menu, then select **Run**.</span></span>

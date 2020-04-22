<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="2e6d2-101">Dans cet exercice, vous allez créer une application native Azure AD à l’aide du centre d’administration Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-101">In this exercise you will create a new Azure AD native application using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="2e6d2-102">Ouvrez un navigateur, accédez au [Centre d’administration Azure Active Directory ](https://aad.portal.azure.com) et connectez-vous à l’aide d’un **compte personnel** (ou compte Microsoft) ou d’un **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="2e6d2-103">Sélectionnez **Azure Active Directory** dans le volet de navigation gauche, puis sélectionnez **Inscriptions d’applications** sous **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-103">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="2e6d2-104">Une capture d’écran des inscriptions d’applications</span><span class="sxs-lookup"><span data-stu-id="2e6d2-104">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="2e6d2-105">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-105">Select **New registration**.</span></span> <span data-ttu-id="2e6d2-106">Sur la page **Inscrire une application**, définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-106">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="2e6d2-107">Définissez le **Nom** sur `iOS Swift Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-107">Set **Name** to `iOS Swift Graph Tutorial`.</span></span>
    - <span data-ttu-id="2e6d2-108">Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-108">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="2e6d2-109">Laissez **Redirect URI** vide.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-109">Leave **Redirect URI** empty.</span></span>

    ![Capture d’écran de la page Inscrire une application](./images/aad-register-an-app.png)

1. <span data-ttu-id="2e6d2-111">Sélectionner **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-111">Select **Register**.</span></span> <span data-ttu-id="2e6d2-112">Sur la page **didacticiel de Graph SWIFT iOS** , copiez la valeur de l' **ID d’application (client)** et enregistrez-la, vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-112">On the **iOS Swift Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Une capture d’écran de l’ID d’application de la nouvelle inscription d'application](./images/aad-application-id.png)

1. <span data-ttu-id="2e6d2-114">Sous **Gérer**, sélectionnez **Authentification**.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-114">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="2e6d2-115">Sélectionnez **Ajouter une plateforme**, puis **iOS/MacOS**.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-115">Select **Add a platform**, then **iOS / macOS**.</span></span>

1. <span data-ttu-id="2e6d2-116">Entrez l’ID de package de votre application, sélectionnez **configurer**, puis **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="2e6d2-116">Enter your app's Bundle ID and select **Configure**, then select **Done**.</span></span>

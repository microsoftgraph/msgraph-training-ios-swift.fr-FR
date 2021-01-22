<!-- markdownlint-disable MD002 MD041 -->

Dans cette section, vous allez ajouter la possibilité de créer des événements sur le calendrier de l’utilisateur.

1. Ouvrez **GraphManager.swift** et ajoutez la fonction suivante pour créer un événement sur le calendrier de l’utilisateur.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/GraphManager.swift" id="CreateEventSnippet":::

1. Créez un **fichier de classe Cocoa Touch** dans le dossier **GraphTutorial** nommé `NewEventViewController` . Choisissez **UIViewController dans** la **sous-classe du** champ.
1. Ouvrez **NewEventViewController.swift** et remplacez son contenu par ce qui suit.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/NewEventViewController.swift" id="NewEventViewControllerSnippet":::

1. Ouvrez **Main.storyboard**. Utilisez la **bibliothèque pour** faire glisser un contrôleur **de vue** vers le storyboard.
1. À **l’aide de la bibliothèque,** ajoutez une **barre de navigation** au contrôleur d’affichage.
1. Double-cliquez sur **le titre dans** la barre de navigation et mettez-le à `New Event` jour.
1. À **l’aide de la bibliothèque,** ajoutez un élément de bouton **de** barre sur le côté gauche de la barre de navigation.
1. Sélectionnez le nouveau bouton de barre, puis l’inspecteur **d’attributs.** Change **title** to `Cancel` .
1. À **l’aide de la bibliothèque,** ajoutez un élément de bouton **de** barre à droite de la barre de navigation.
1. Sélectionnez le nouveau bouton de barre, puis l’inspecteur **d’attributs.** Change **title** to `Create` .
1. Sélectionnez le contrôleur d’affichage, puis **l’inspecteur d’identité.** Change **class** to **NewEventViewController**.
1. Ajoutez les contrôles suivants de **la bibliothèque** à l’affichage.

    - Ajoutez une **étiquette sous** la barre de navigation. Définissez son texte sur `Subject` .
    - Ajoutez **un champ de texte** sous l’étiquette. Définissez son **attribut d’espace** réservé sur `Subject` .
    - Ajoutez une **étiquette** sous le champ de texte. Définissez son texte sur `Attendees` .
    - Ajoutez **un champ de texte** sous l’étiquette. Définissez son **attribut d’espace** réservé sur `Separate multiple entries with ;` .
    - Ajoutez une **étiquette** sous le champ de texte. Définissez son texte sur `Start` .
    - Ajoutez un **s sélectionneur de dates** sous l’étiquette. Définissez **son style préféré sur** **Compact,** son **intervalle** **à 15 minutes** et sa hauteur sur **35**.
    - Ajoutez une **étiquette sous** le s sélectionneur de dates. Définissez son texte sur `End` .
    - Ajoutez un **s sélectionneur de dates** sous l’étiquette. Définissez **son style préféré sur** **Compact,** son **intervalle** **à 15 minutes** et sa hauteur sur **35**.
    - Ajoutez un **affichage texte sous** le s sélectionneur de dates.

1. Sélectionnez **le nouveau contrôleur d’affichage d’événements** et utilisez **l’Inspecteur de** connexion pour établir les connexions suivantes.

    - Connectez **l’action d’annulation** reçue au **bouton annuler** la barre.
    - Connectez **l’action reçue createEvent** au **bouton Créer une** barre.
    - Connectez la **sortie de** l’objet au premier champ de texte.
    - Connectez **la sortie des participants** au deuxième champ de texte.
    - Connectez **la sortie de** début au premier s sélectionneur de dates.
    - Connectez **la sortie** de fin au deuxième s picker de date.
    - Connectez la **sortie du** corps à l’affichage texte.

1. Ajoutez les contraintes suivantes.

    - **Barre de navigation**
        - Espace de pointe vers la zone sécurisée, valeur : 0
        - Espace à la fin de la zone sécurisée, valeur : 0
        - Espace supérieur vers la zone sécurisée, valeur : 0
        - Hauteur, valeur : 44
    - **Étiquette d’objet**
        - Espace de tête vers la marge d’affichage, valeur : 0
        - Espace à la fin de la marge d’affichage, valeur : 0
        - Espace supérieur de la barre de navigation, valeur : 20
    - **Champ de texte de l’objet**
        - Espace de tête vers la marge d’affichage, valeur : 0
        - Espace à la fin de la marge d’affichage, valeur : 0
        - Espace supérieur à l’étiquette de l’objet, valeur : Standard
    - **Étiquette Attendees**
        - Espace de tête vers la marge d’affichage, valeur : 0
        - Espace à la fin de la marge d’affichage, valeur : 0
        - Espace supérieur au champ texte de l’objet, valeur : Standard
    - **Champ de texte Attendees**
        - Espace de tête vers la marge d’affichage, valeur : 0
        - Espace à la fin de la marge d’affichage, valeur : 0
        - Espace supérieur à l’étiquette Attendees, valeur : Standard
    - **Start Label**
        - Espace de tête vers la marge d’affichage, valeur : 0
        - Espace à la fin de la marge d’affichage, valeur : 0
        - Espace supérieur au champ texte de l’objet, valeur : Standard
    - **Socheur de date de début**
        - Espace de tête vers la marge d’affichage, valeur : 0
        - Espace à la fin de la marge d’affichage, valeur : 0
        - Espace supérieur à l’étiquette Attendees, valeur : Standard
        - Hauteur, valeur : 35
    - **End Label**
        - Espace de tête vers la marge d’affichage, valeur : 0
        - Espace à la fin de la marge d’affichage, valeur : 0
        - Espace supérieur au s picker de date de début, valeur : Standard
    - **Socheur de date de fin**
        - Espace de tête vers la marge d’affichage, valeur : 0
        - Espace à la fin de la marge d’affichage, valeur : 0
        - Espace supérieur à l’étiquette de fin, valeur : Standard
        - Hauteur : 35
    - **Affichage corps de texte**
        - Espace de tête vers la marge d’affichage, valeur : 0
        - Espace à la fin de la marge d’affichage, valeur : 0
        - Espace supérieur au s picker de date de fin, valeur : Standard
        - Espace inférieur pour afficher la marge, valeur : 0

    ![Capture d’écran du nouveau formulaire d’événement dans la storyboard](images/new-event-form.png)

1. Sélectionnez **la scène de** calendrier, puis **l’inspecteur de connexions.**
1. Sous **Segues déclenchés,** faites glisser le cercle non rempli en regard du **manuel** sur le nouveau contrôleur d’affichage d’événements dans le storyboard.  Sélectionnez **Présenter en mode mod** dans le menu déroulant.
1. Sélectionnez le segue que vous avez ajouté, puis sélectionnez **l’inspecteur d’attributs.** Définissez le **champ identificateur** sur `showEventForm` .
1. Connectez **l’action reçue showNewEventForm** au bouton **+** de barre de navigation.
1. Enregistrez vos modifications, puis redémarrez l’application. Go to the calendar page and tap the **+** button. Remplissez le formulaire et **appuyez sur Créer** pour créer un événement.

    ![Capture d’écran du nouveau formulaire d’événement](images/create-event.png)

---
title: 'Zelf studie: gematigde Facebook-inhoud-Content Moderator'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie leert u hoe u met behulp van machine learning en Content Moderator berichten en opmerkingen voor Facebook kunt controleren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 9f25c845302d62e3bc9e230b4a6f8f2669f4ac35
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76774274"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Zelf studie: gematige Facebook-berichten en-opdrachten met Azure Content Moderator

In deze zelf studie leert u hoe u Azure Content Moderator kunt gebruiken om de berichten en opmerkingen op een Facebook-pagina te lezen. Facebook verzendt de inhoud die wordt Gepost door bezoekers naar de Content Moderator-service. Vervolgens worden de inhoud door uw Content Moderator-werk stromen gepubliceerd of worden er in het beoordelings programma beoordelingen gemaakt, afhankelijk van de scores en drempel waarden van de inhoud. Raadpleeg de [video Build 2017-demo](https://channel9.msdn.com/Events/Build/2017/T6033) voor een werkend voor beeld van dit scenario.

In deze handleiding ontdekt u hoe u:

> [!div class="checklist"]
> * Een Content Moderator-team samenstellen.
> * Azure-functies maken die luisteren naar HTTP-gebeurtenissen van Content Moderator en Facebook.
> * Een Facebook-pagina koppelen aan Content Moderator met behulp van een Facebook-toepassing.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Dit diagram illustreert elk onderdeel van dit scenario:

![Diagram van Content Moderator het ontvangen van informatie van Facebook via ' FBListener ' en het verzenden van informatie via ' CMListener '](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> In 2018 heeft Facebook een strengere hebben-beleid geïmplementeerd voor Facebook-apps. U kunt de stappen van deze zelf studie niet volt ooien als uw app niet is gecontroleerd en goedgekeurd door het Facebook Review-team.

## <a name="prerequisites"></a>Vereisten

- Een abonnementssleutel voor Content Moderator. Volg de instructies in [Een Cognitive Services-account maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op Content Moderator en een sleutel op te halen.
- Een [Facebook-account](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Een beoordelingsteam maken

Raadpleeg de Snelstartgids [Content moderator op het web](quick-start.md) voor instructies over hoe u zich kunt registreren voor het [hulp programma content moderator beoordeling](https://contentmoderator.cognitive.microsoft.com/) en maak een beoordelings team. Noteer de waarde voor **Team Id** op de pagina **Create review team**.

## <a name="configure-image-moderation-workflow"></a>Werk stroom voor afbeeldings toezicht configureren

Raadpleeg de hand leiding voor het [definiëren, testen en gebruiken van werk stromen](review-tool-user-guide/workflows.md) om een werk stroom voor aangepaste afbeeldingen te maken. Content Moderator gebruikt deze werk stroom om automatisch afbeeldingen op Facebook te controleren en enkele berichten naar het beoordelings programma te verzenden. Noteer de **naam**van de werk stroom.

## <a name="configure-text-moderation-workflow"></a>Werk stroom voor tekst toezicht configureren

Raadpleeg de hand leiding voor het [definiëren, testen en gebruiken van werk stromen](review-tool-user-guide/workflows.md) . op deze tijd maakt u een werk stroom voor aangepaste tekst. Content Moderator maakt gebruik van deze werk stroom om automatisch tekst inhoud te controleren. Noteer de **naam**van de werk stroom.

![Tekstwerkstroom configureren](images/text-workflow-configure.PNG)

Test uw werk stroom met behulp van de knop **werk stroom uitvoeren** .

![Tekstwerkstroom testen](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Azure-functies maken

Meld u aan bij de [Azure Portal](https://portal.azure.com/) en voer de volgende stappen uit:

1. Maak een Azure-functie-app zoals wordt weergegeven op de pagina [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
1. Ga naar de zojuist gemaakte functie-app.
1. Ga in de app naar het tabblad **platform functies** en selecteer **configuratie**. Selecteer in de sectie **Toepassings instellingen** van de volgende pagina de optie **nieuwe toepassing** om de volgende sleutel/waarde-paren toe te voegen:
    
    | Naam van app-instelling | waarde   | 
    | -------------------- |-------------|
    | cm:TeamId   | De id van het Content Moderator-team  | 
    | cm:SubscriptionKey | Uw abonnementssleutel voor Content Moderator: zie [Referenties](review-tool-user-guide/credentials.md) |
    | cm:Region | De naam van uw Content Moderator-regio, zonder de spaties. U kunt dit vinden in het veld **locatie** van het tabblad **overzicht** van uw Azure-resource.|
    | cm:ImageWorkflow | De naam van de werkstroom om uit te voeren voor afbeeldingen |
    | cm:TextWorkflow | De naam van de werkstroom om uit te voeren voor tekst |
    | cm:CallbackEndpoint | De URL voor de CMListener-functie-app die u later in deze hand leiding gaat maken |
    | fb:VerificationToken | Een geheim token dat u maakt, dat wordt gebruikt voor het abonneren op Facebook-feeds gebeurtenissen |
    | fb:PageAccessToken | Het toegangstoken voor de Facebook Graph-API verloopt niet en maakt het mogelijk de functie voor het verbergen/verwijderen van berichten namens u uit te voeren. U ontvangt dit token in een latere stap. |

    Klik boven aan de pagina op de knop **Opslaan** .

1. Ga terug naar het tabblad **platform functies** . Gebruik de **+** knop in het linkerdeel venster om het **nieuwe functie** deel venster weer te geven. De functie die u gaat maken, ontvangt gebeurtenissen van Facebook.

    ![Azure Functions deel venster met de knop functie toevoegen gemarkeerd.](images/new-function.png)

    1. Klik op de tegel met de melding **http-trigger**.
    1. Voer de naam **FBListener** in. Stel **Autorisatieniveau** in op **Functie**.
    1. Klik op **maken**.
    1. Vervang de inhoud van het **Run. CSX** door de inhoud van **FbListener/run. CSX**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Maak een nieuwe **http-trigger** functie met de naam **CMListener**. Deze functie ontvangt gebeurtenissen van Content Moderator. Vervang de inhoud van het **Run. CSX** door de inhoud van **CMListener/run. CSX**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>De Facebook-pagina en -app configureren

1. Maak een Facebook-app.

    ![Facebook-ontwikkelaars pagina](images/facebook-developer-app.png)

    1. Navigeer naar de [site voor Facebook-ontwikkelaars](https://developers.facebook.com/)
    1. Klik op **My Apps**.
    1. Voeg een nieuw app toe.
    1. Geef deze een naam
    1. **Webhooks selecteren-> instellen**
    1. Selecteer **pagina** in het vervolg keuzemenu en selecteer **Abonneren op dit object**
    1. Geef **FBListener Url** op als de Callback URL en het **Verify Token** dat u hebt geconfigureerd onder **Function App Settings**
    1. Als u zich hebt geabonneerd, bladert u omlaag naar de feed en selecteert u **subscribe**.
    1. Klik op de knop **testen** van de rij **invoer** om een test bericht naar de Azure-functie FBListener te verzenden en vervolgens op de knop **verzenden naar mijn server** te drukken. U ziet dat de aanvraag wordt ontvangen op uw FBListener.

1. Maak een Facebook-pagina.

    > [!IMPORTANT]
    > In 2018 heeft Facebook een strengere hebben Facebook-apps geïmplementeerd. Het is niet mogelijk om secties 2, 3 en 4 uit te voeren als uw app niet is gecontroleerd en goedgekeurd door het Facebook Review-team.

    1. Navigeer naar [Facebook](https://www.facebook.com/bookmarks/pages) en maak een **nieuwe Facebook-pagina**.
    1. Volg deze stappen om toe te staan dat de Facebook-app toegang heeft tot deze pagina:
        1. Navigeer naar de [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        1. Selecteer de **toepassing**.
        1. Selecteer **Page Access Token**, verstuur een **Get**-aanvraag.
        1. Klik op de **Page ID** in het antwoord.
        1. Voeg de **/subscribed_apps** toe aan de URL en verstuur een **Get**-aanvraag (leeg antwoord).
        1. Verstuur een **Post**-aanvraag. U krijgt het antwoord als **success: true**.

3. Maak een Graph API-toegangstoken dat niet verloopt.

    1. Navigeer naar de [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
    2. Selecteer de optie **Application**.
    3. Selecteer de optie **Get User Access Token**.
    4. Selecteer onder **Select Permissions** de opties **manage_pages** en **publish_pages**.
    5. We gebruiken het **toegangstoken** (token met korte levensduur) in de volgende stap.

4. We gebruiken Postman voor de volgende stappen.

    1. Open **Postman** (of downloadt het programma [hier](https://www.getpostman.com/)).
    2. Importeer deze twee bestanden:
        1. [Postman Collection](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman Environment](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Werk deze omgevingsvariabelen bij:
    
        | Sleutel | Waarde   | 
        | -------------------- |-------------|
        | appId   | Voeg hier de id van uw Facebook-app in  | 
        | appSecret | Voeg hier het geheim van uw Facebook-app in | 
        | token_met_korte_levensduur | Voeg hier het toegangstoken met korte levensduur van de gebruiker in dat u in de vorige stap hebt gegenereerd |
    4. Voer nu de drie API's uit die in de verzameling worden vermeld: 
        1. Selecteer **Generate Long-Lived Access Token** en klik op **Send**.
        2. Selecteer **Get User ID** en klik op **Send**.
        3. Selecteer **Get Permanent Page Access Token** en klik op **Send**.
    5. Kopieer de waarde van **access_token** in het antwoord en wijs deze toe aan de app-instelling, **fb:PageAccessToken**.

De oplossing verzendt alle afbeeldingen en tekst die op uw Facebook-pagina worden geplaatst naar Content Moderator. Vervolgens worden de werk stromen aangeroepen die u eerder hebt geconfigureerd. De inhoud die niet voldoet aan de criteria die zijn gedefinieerd in de werk stromen, wordt door gegeven aan beoordelingen binnen het beoordelings programma. De rest van de inhoud wordt automatisch gepubliceerd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een programma gemaakt voor het analyseren van productafbeeldingen om deze te taggen op producttype, waarna een beoordelingsteam kan beslissen of de afbeeldingen al dan niet geschikt zijn. In de volgende zelfstudie wordt meer aandacht besteed aan het beoordelen van gecontroleerde afbeeldingen.

> [!div class="nextstepaction"]
> [Beheer van afbeeldingen](./image-moderation-api.md)

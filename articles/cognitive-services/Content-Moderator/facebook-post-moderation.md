---
title: 'Zelfstudie: Facebook-inhoud beheren - Inhoudsmoderator'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774274"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Zelfstudie: Facebook-berichten en -opdrachten beheren met Azure-inhoudsmoderator

In deze zelfstudie leert u hoe u Azure Content Moderator gebruiken om de berichten en reacties op een Facebook-pagina te beheren. Facebook stuurt de inhoud die door bezoekers wordt geplaatst naar de Content Moderator-service. Vervolgens publiceren de werkstromen van inhoudsmoderator de inhoud of maken ze beoordelingen in het hulpprogramma Controleren, afhankelijk van de inhoudsscores en -drempels. Bekijk de [demovideo Build 2017](https://channel9.msdn.com/Events/Build/2017/T6033) voor een werkend voorbeeld van dit scenario.

In deze handleiding ontdekt u hoe u:

> [!div class="checklist"]
> * Een Content Moderator-team samenstellen.
> * Azure-functies maken die luisteren naar HTTP-gebeurtenissen van Content Moderator en Facebook.
> * Een Facebook-pagina koppelen aan Inhoudsmoderator met behulp van een Facebook-toepassing.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

Dit diagram illustreert elk onderdeel van dit scenario:

![Diagram van Content Moderator ontvangen van informatie van Facebook via "FBListener" en het verzenden van informatie via "CMListener"](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> In 2018 voerde Facebook een strenger doorlichtingsbeleid uit voor Facebook Apps. Je de stappen van deze zelfstudie niet voltooien als je app niet is beoordeeld en goedgekeurd door het Facebook-beoordelingsteam.

## <a name="prerequisites"></a>Vereisten

- Een abonnementssleutel voor Content Moderator. Volg de instructies in [Een Cognitive Services-account maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op Content Moderator en een sleutel op te halen.
- Een [Facebook-account](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Een beoordelingsteam maken

Raadpleeg de [Moderator van inhoud uitproberen op het web](quick-start.md) snelstart voor instructies over hoe u zich aanmeldt voor de tool [Inhoudsmoderator en](https://contentmoderator.cognitive.microsoft.com/) maak een beoordelingsteam. Noteer de waarde voor **Team Id** op de pagina **Create review team**.

## <a name="configure-image-moderation-workflow"></a>Werkstroom voor beeldbeheer configureren

Raadpleeg de handleiding [Voor het definiëren, testen en gebruiken van werkstromen](review-tool-user-guide/workflows.md) om een aangepaste afbeeldingswerkstroom te maken. Content Moderator gebruikt deze workflow om automatisch afbeeldingen op Facebook te controleren en een aantal naar de beoordelingstool te sturen. Let op de **naam**van de werkstroom .

## <a name="configure-text-moderation-workflow"></a>Werkstroom voor tekstbeheer configureren

Raadpleeg nogmaals de handleiding [Werkstromen definiëren, testen en gebruiken;](review-tool-user-guide/workflows.md) maak deze keer een aangepaste tekstwerkstroom. Content Moderator gebruikt deze workflow om automatisch tekstinhoud te controleren. Let op de **naam**van de werkstroom .

![Tekstwerkstroom configureren](images/text-workflow-configure.PNG)

Test uw werkstroom met de knop **Werkstroom uitvoeren.**

![Tekstwerkstroom testen](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Azure-functies maken

Meld u aan bij de [Azure-portal](https://portal.azure.com/) en volg de volgende stappen:

1. Maak een Azure-functie-app zoals wordt weergegeven op de pagina [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
1. Ga naar de nieuw gemaakte functie-app.
1. Ga in de App naar het tabblad **Platformfuncties** en selecteer **Configuratie**. Selecteer in het gedeelte **Toepassingsinstellingen** van de volgende pagina de optie **Nieuwe toepassingsinstelling** selecteren om de volgende sleutel-/waardeparen toe te voegen:
    
    | Naam app-instelling | waarde   | 
    | -------------------- |-------------|
    | cm:TeamId   | De id van het Content Moderator-team  | 
    | cm:SubscriptionKey | Uw abonnementssleutel voor Content Moderator: zie [Referenties](review-tool-user-guide/credentials.md) |
    | cm:Region | De naam van uw Content Moderator-regio, zonder de spaties. U dit vinden in het veld **Locatie** van het tabblad **Overzicht** van uw Azure-bron.|
    | cm:ImageWorkflow | De naam van de werkstroom om uit te voeren voor afbeeldingen |
    | cm:TextWorkflow | De naam van de werkstroom om uit te voeren voor tekst |
    | cm:CallbackEndpoint | Url voor de CMListener-functie-app die u later in deze handleiding maakt |
    | fb:VerificationToken | Een geheim token dat je maakt, gebruikt om je te abonneren op de Facebook-feedgebeurtenissen |
    | fb:PageAccessToken | Het toegangstoken voor de Facebook Graph-API verloopt niet en maakt het mogelijk de functie voor het verbergen/verwijderen van berichten namens u uit te voeren. U krijgt dit token in een latere stap. |

    Klik boven aan de pagina op de knop **Opslaan.**

1. Ga terug naar het tabblad **+** Functies van het **platform.** Gebruik de knop in het linkerdeelvenster om het deelvenster Nieuwe **functie** weer te geven. De functie die je gaat maken, ontvangt evenementen van Facebook.

    ![Het deelvenster Azure-functies met de knop Functie toevoegen gemarkeerd.](images/new-function.png)

    1. Klik op de tegel met de tekst **Http trigger**.
    1. Voer de naam **FBListener** in. Stel **Autorisatieniveau** in op **Functie**.
    1. Klik **op Maken**.
    1. Vervang de inhoud van de **run.csx** door de inhoud van **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Een nieuwe **Http-triggerfunctie** met de naam **CMListener maken.** Deze functie ontvangt gebeurtenissen van Content Moderator. Vervang de inhoud van de **run.csx** door de inhoud van **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>De Facebook-pagina en -app configureren

1. Maak een Facebook-app.

    ![facebook ontwikkelaarspagina](images/facebook-developer-app.png)

    1. Navigeer naar de [site voor Facebook-ontwikkelaars](https://developers.facebook.com/)
    1. Klik op **My Apps**.
    1. Voeg een nieuw app toe.
    1. noem het iets
    1. **Webhooks selecteren -> instellen**
    1. Selecteer **Pagina** in het vervolgkeuzemenu en selecteer **Abonneren op dit object**
    1. Geef **FBListener Url** op als de Callback URL en het **Verify Token** dat u hebt geconfigureerd onder **Function App Settings**
    1. Als u zich hebt geabonneerd, bladert u omlaag naar de feed en selecteert u **subscribe**.
    1. Klik op de **knop Testen** van de **feedrij** om een testbericht naar uw FBListener Azure-functie te verzenden en druk vervolgens op de knop **Verzenden naar mijn server.** U ziet dat het verzoek wordt ontvangen op uw FBListener.

1. Maak een Facebook-pagina.

    > [!IMPORTANT]
    > In 2018 heeft Facebook een strengere controle van Facebook-apps geïmplementeerd. Je de secties 2, 3 en 4 niet uitvoeren als je app niet is beoordeeld en goedgekeurd door het Facebook-beoordelingsteam.

    1. Navigeer naar [Facebook](https://www.facebook.com/bookmarks/pages) en maak een **nieuwe Facebook-pagina**.
    1. Volg deze stappen om toe te staan dat de Facebook-app toegang heeft tot deze pagina:
        1. Navigeer naar de [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        1. Selecteer **Toepassing**.
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

De oplossing verzendt alle afbeeldingen en tekst die op uw Facebook-pagina worden geplaatst naar Content Moderator. Vervolgens worden de werkstromen aangeroepen die u eerder hebt geconfigureerd. De inhoud die niet voldoet aan de criteria die in de werkstromen zijn gedefinieerd, wordt doorgegeven aan beoordelingen in de beoordelingstool. De rest van de inhoud wordt automatisch gepubliceerd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een programma gemaakt voor het analyseren van productafbeeldingen om deze te taggen op producttype, waarna een beoordelingsteam kan beslissen of de afbeeldingen al dan niet geschikt zijn. In de volgende zelfstudie wordt meer aandacht besteed aan het beoordelen van gecontroleerde afbeeldingen.

> [!div class="nextstepaction"]
> [Beeldmatiging](./image-moderation-api.md)

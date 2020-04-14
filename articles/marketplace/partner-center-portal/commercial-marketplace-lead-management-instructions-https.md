---
title: HTTPS-eindpunt | Azure Marketplace
description: Leadbeheer configureren voor een HTTPS-eindpunt.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 33359883df86091120295b93618a13476e428d2f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262547"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Leadbeheer configureren met behulp van een HTTPS-eindpunt

>[!Note]
>De Power Automate-connector die in deze instructies wordt gebruikt, vereist een betaald abonnement op Power Automate. Houd hier rekening mee voordat u de instructies in dit document volgt.

Als uw CRM-systeem (Customer Relationship Management) niet expliciet wordt ondersteund in het Partnercentrum voor het ontvangen van Azure Marketplace- en AppSource-leads, u een HTTPS-eindpunt in Power Automate gebruiken om deze leads te verwerken. Met een HTTPS-eindpunt kunnen deze leads worden verzonden als een e-mailmelding of kunnen ze worden geschreven naar een CRM-systeem (Customer Relationship Management) dat wordt ondersteund door Power Automate. De instructies in dit artikel leiden u door het basisproces om een nieuwe stroom te maken met Behulp van Power Automate, die de HTTP POST-URL genereert die u invoert in de publicatieportal voor het **>-url-veld voor eindpunthttps.** Ook inbegrepen zijn instructies over hoe u uw stroom testen met behulp van een tool genaamd [Postman](https://www.getpostman.com/downloads/) die online kan worden gevonden.

## <a name="create-a-flow-using-power-automate"></a>Een stroom maken met Power Automate

1. Open de webpagina [Flow.](https://flow.microsoft.com/) Selecteer **Aanmelden**of als u nog geen account hebt, selecteert **u Aanmelden gratis** om een gratis Flow-account aan te maken.

2. Log in en selecteer **Mijn flows** op de menubalk.

3. Selecteer **+Geautomatiseerd - van leeg**.

    ![Mijn flows + Automated - van blanco](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. Selecteer **Overslaan**in *het venster Een automatische stroom* maken . 

    ![Geautomatiseerde stroom bouwen - Overslaan](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. Typ 'verzoek' in het veld **Zoeken en activeert** om de connector Verzoek te zoeken.
6. Selecteer *onder Triggers*de optie Wanneer een **HTTP-aanvraag wordt ontvangen**. 

    ![aanvraagconnector - Triggers](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. Klik op het tekstvak *Wanneer een HTTP-aanvraag wordt ontvangen,* en plak het onderstaande JSON-schema in het tekstvak **Json-schema van aanvraag.** Dit schema wordt door Microsoft gebruikt om uw leadgegevens te bevatten.

    ![aanvraagconnector - Triggers](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **JSON-schema**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

>[!Note]
>Op dit punt in de configuratie u ervoor kiezen om verbinding te maken met een CRM-systeem of een e-mailmelding te configureren. Volg de overige instructies op basis van uw keuze.

### <a name="to-connect-to-a-crm-system"></a>Verbinding maken met een CRM-systeem

1. Selecteer **+ Nieuwe stap**.
2. Kies het CRM-systeem van uw keuze door ernaar te zoeken waar het *zoekconnectors en -acties*opstaat en selecteer het onder de sectie *Acties* met de actie om een nieuwe record te maken. De volgende schermopname toont **Dynamics 365 - Maak** een nieuwe record als voorbeeld.

    ![Een nieuwe record maken](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Geef de **organisatienaam** op die is gekoppeld aan het CRM-systeem. Selecteer **Leads** in de vervolgkeuzelijst **Entiteitsnaam.**

    ![Leads selecteren](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow toont een formulier voor het verstrekken van leadinformatie. U items uit het invoerverzoek toewijzen door te kiezen voor het toevoegen van dynamische inhoud. In de volgende schermopname ziet **U's OfferTitle** als voorbeeld.

    ![Dynamische inhoud toevoegen](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Breng de gewenste velden in kaart en selecteer **Opslaan** om uw stroom op te slaan. Er wordt een HTTP-POST-URL gemaakt en is toegankelijk in het venster *Wanneer een HTTP-aanvraag wordt ontvangen.* Kopieer deze URL met behulp van het kopieerbesturingselement dat zich aan de rechterkant van de HTTP POST URL bevindt - dit is belangrijk zodat u niet per ongeluk een deel van de gehele URL mist. Sla deze URL op zoals u deze nodig hebt wanneer u leadbeheer configureert in de publicatieportal.

    ![Wanneer een HTTP-aanvraag is ontvangen.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>E-mailmelding instellen

1. Nu u het JSON-schema hebt voltooid, selecteert u **+ Nieuwe stap**.
2. Selecteer **Acties** **onder Een actie kiezen**.
3. Selecteer **onder Acties**de optie Een **e-mail verzenden (Office 365 Outlook)** selecteren.

    >[!Note]
    >Als u een andere e-mailprovider wilt gebruiken en *een e-mailmelding (E-mail)* wilt verzenden als actie.

    ![Een e-mailactie toevoegen](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. Configureer in **Een e-mailvenster verzenden** de volgende vereiste velden:

   - **Om** - Voer ten minste één geldig e-mailadres in, waar de leads worden verzonden.
   - **Onderwerp** - Flow geeft je de mogelijkheid om Dynamische inhoud toe te voegen, zoals **LeadSource** in de volgende schermopname. Begin met het typen van een veldnaam, gevolgd door te klikken op de lijst met dynamische inhoud in het pop-upvenster. 

        >[!Note] 
        > Wanneer u veldnamen toevoegt, u elk met een : volgen en vervolgens invoeren om een nieuwe rij te maken. Zodra u uw veldnamen hebt toegevoegd, u vervolgens elke bijbehorende parameter toevoegen aan de dynamische keuzelijst.

        ![Een e-mailactie toevoegen met dynamische inhoud](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Hoofdtekst** - Voeg in de lijst Met de lijst Dynamische inhoud de gewenste informatie toe in de hoofdtekst van de e-mail. Bijvoorbeeld Achternaam, Voornaam, E-mail en Bedrijf. <br> <br> Wanneer u klaar bent met het instellen van de e-mailmelding, ziet deze eruit als het voorbeeld in de volgende schermopname.


       ![Een e-mailactie toevoegen](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Selecteer **Opslaan** om de stroom te voltooien. Er wordt een HTTP-post-URL gemaakt die toegankelijk is in het venster *Wanneer een HTTP-aanvraag wordt ontvangen.* Kopieer deze URL met behulp van het kopieerbesturingselement dat zich aan de rechterkant van de HTTP POST URL bevindt - dit is belangrijk zodat u niet per ongeluk een deel van de gehele URL mist. Sla deze URL op zoals u deze nodig hebt wanneer u leadbeheer configureert in de publicatieportal.

   ![HTTP-BERICHT-URL ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testen

U testen of alles werkt zoals verwacht met behulp van de volgende stappen met behulp van een tool genaamd [Postman](https://app.getpostman.com/app/download/win64), die online kan worden gedownload. Dit is beschikbaar voor Windows. 

1. Start Postman en selecteer **Nieuw** > **verzoek** om uw testtool in te stellen. 

   ![Verzoek om het instellen van uw testtool](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Vul het formulier *Aanvraagformulier opslaan* in en **sla vervolgens op in** de map die u hebt gemaakt.

   ![Aanvraag opslaan](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Selecteer **POST** in de vervolgkeuzelijst. 

   ![Mijn flow testen](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Plak de HTTP-POST-URL uit de stroom die u hebt gemaakt in Power Automatiseer waar de *url van aanvraag invoeren*staat .

   ![De HTTP-POST-URL plakken](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Ga terug naar [Flow](https://flow.microsoft.com/) en zoek de stroom die u hebt gemaakt om leads te verzenden, door naar **Mijn stromen** te gaan via de menubalk Flow.  Selecteer de 3 puntjes naast de stroomnaam en selecteer **Bewerken**.

   ![Mijn stromen - Bewerken](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Selecteer **Testen** in de rechterbovenhoek en selecteer 'Ik voer de triggeractie uit' en selecteer **Testen.** U ziet een indicatie in de bovenkant van het scherm die aangeeft dat de test is begonnen

   ![Teststroom - trigger](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Ga terug naar je Postman-app en selecteer **Verzenden** naast de plaats waar je de HTTPS-URL hebt geplakt.

   ![Test mijn flow - Verzenden](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Ga terug naar je flow en controleer het resultaat. Als alles werkt zoals verwacht ziet u een bericht dat aangeeft dat het gelukt is.

   ![Flow - Resultaten controleren](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Je had ook een e-mail moeten ontvangen. Controleer uw e-mailinbox. 

    >[!Note] 
    >Als u geen e-mail van de test ziet, controleer dan uw spam- en ongewenste mappen. Hieronder ziet u alleen de veldlabels die u hebt toegevoegd bij het configureren van de e-mailmelding. Als dit een werkelijke lead was die uit uw aanbieding wordt gegenereerd, ziet u ook de werkelijke informatie van het contactpersooncontact in het lichaam en in de onderwerpregel.

   ![Ontvangen e-mail](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Uw aanbieding configureren om leads naar het HTTPS-eindpunt te verzenden

Wanneer u klaar bent om de leadmanagementgegevens voor uw aanbieding in de publicatieportal te configureren, volgt u de volgende stappen:

1. Navigeer naar de pagina **Installatie van aanbieding** voor uw aanbieding.
2. Selecteer **Verbinding maken** onder de sectie Leadbeheer.
3. Selecteer in het pop-upvenster **Verbindingsdetails https-eindpunt** voor de **leadbestemming** en plak in de HTTP-POST-URL van de stroom die u hebt gemaakt door eerdere stappen in het **URL-veld HTTPS-eindpunt te** volgen.
4. **E-mail contact** opnemen : e-mails verstrekken aan mensen in uw bedrijf die e-mailmeldingen moeten ontvangen wanneer een nieuwe lead wordt ontvangen. U meerdere e-mails verstrekken door ze te scheiden met een puntkomma.
5. Selecteer **OK**.

Klik op de knop Valideren om ervoor te zorgen dat u met succes verbinding hebt gemaakt met een leadbestemming. Als dit lukt, heb je een testlead in de hoofdbestemming.

>[!Note] 
>U moet de rest van de aanbieding voltooien en publiceren voordat u leads voor de aanbieding ontvangen.

Wanneer leads worden gegenereerd, stuurt Microsoft leads naar de stroom, die worden doorgestuurd naar het CRM-systeem of e-mailadres dat u hebt geconfigureerd.

![Leadmanagement - verbinding maken](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Verbindingsdetails](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Verbindingsdetails](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)


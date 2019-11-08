---
title: HTTPS-eind punt | Azure Marketplace
description: Beheer van leads configureren voor een HTTPS-eind punt.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 83efb9cfd1ee7464a334ebc4064dbfaa20ab30de
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812295"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Beheer van potentiële klanten configureren met behulp van een HTTPS-eind punt

Als uw Customer Relationship Management-systeem (CRM) niet expliciet wordt ondersteund in het partner centrum voor het ontvangen van Azure Marketplace-en AppSource-leads, kunt u een HTTPS-eind punt in MS flow gebruiken om deze leads af te handelen. Met een HTTPS-eind punt kunnen deze leads worden verzonden als een e-mail melding of kunnen ze worden geschreven naar een Customer Relationship Management (CRM)-systeem dat wordt ondersteund door MS flow. De instructies in dit artikel begeleiden u bij het basis proces voor het maken van een nieuwe stroom met behulp van Microsoft Flow, waarmee de HTTP POST-URL wordt gegenereerd die u gaat invoeren in de portal voor publiceren van het veld **https-eind punt** voor het beheer van leads >. Ook vindt u hier instructies over hoe u uw stroom kunt testen met behulp van een hulp programma dat is opgenomen in ' [postman](https://www.getpostman.com/downloads/) ', dat online kan worden gevonden.

## <a name="create-a-flow-using-microsoft-flow"></a>Een stroom maken met behulp van Microsoft Flow

1. Open de pagina [stroom](https://flow.microsoft.com/) . Selecteer **Aanmelden**of als u nog geen account hebt, selecteert u **gratis registreren** om een gratis stroom account te maken.

2. Meld u aan en selecteer **mijn stromen** in de menu balk.

3. Selecteer **+ automatisch: leeg**.

    ![Mijn stromen + automatisch-van leeg](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. Selecteer in het venster *een automatische stroom maken* de optie **overs Laan**. 

    ![Geautomatiseerde stroom bouwen-overs Laan](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. Typ ' aanvraag ' in het veld **connectors en triggers zoeken** om de aanvraag connector te vinden.
6. Onder *Triggers*selecteert u **Wanneer een HTTP-aanvraag wordt ontvangen**. 

    ![connector voor aanvragen-triggers](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. Op het venster *Wanneer een HTTP-aanvraag is ontvangen* kopieert en plakt u het JSON-schema hieronder in het tekstvak **aanvraag tekst JSON schema** . Dit schema wordt door micro soft gebruikt voor het opnemen van uw lead gegevens.

    ![connector voor aanvragen-triggers](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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
>Op dit punt in de configuratie kunt u selecteren om verbinding te maken met een CRM-systeem of een e-mail melding configureren. Volg de overige instructies op basis van uw keuze.

### <a name="to-connect-to-a-crm-system"></a>Verbinding maken met een CRM-systeem

1. Selecteer **+ nieuwe stap**.
2. Kies het CRM-systeem van uw keuze door ernaar te zoeken waar *Zoek connectors en acties*worden vermeld, en selecteer dit onder de sectie *acties* met de actie om een nieuwe record te maken. In de volgende scherm opname wordt **Dynamics 365-** een nieuwe record gemaakt als voor beeld.

    ![Een nieuwe record maken](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Geef de **naam** van de organisatie op die aan het CRM-systeem is gekoppeld. Selecteer **leads** in de vervolg keuzelijst **entiteits naam** .

    ![Leads selecteren](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow toont een formulier voor het leveren van lead gegevens. U kunt items van de invoer aanvraag toewijzen door dynamische inhoud toe te voegen. In de volgende scherm opname wordt **OfferTitle** weer gegeven als een voor beeld.

    ![Dynamische inhoud toevoegen](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Wijs de gewenste velden toe en selecteer **Opslaan** om uw stroom op te slaan. Er wordt een HTTP POST-URL gemaakt en deze is toegankelijk in het venster *Wanneer een HTTP-aanvraag wordt ontvangen* . Kopieer deze URL met behulp van het Kopieer besturings element dat zich rechts van de HTTP POST-URL bevindt. Dit is belang rijk, zodat u niet per ongeluk een deel van de volledige URL mist. Sla deze URL op zoals u deze nodig hebt wanneer u Lead beheer configureert in de portal voor publiceren.

    ![Wanneer een HTTP-aanvraag wordt ontvangen.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>E-mail meldingen instellen

1. Nu u het JSON-schema hebt voltooid, selecteert u **+ nieuwe stap**.
2. Onder **Kies een actie**selecteert u **acties**.
3. Onder **acties**, selecteert u **een E-mail verzenden (Office 365 Outlook)** .

    >[!Note]
    >Als u een andere e-mail provider wilt gebruiken, zoekt en selecteert u in plaats daarvan *een e-mail melding (e-mail) verzenden* als actie.

    ![Een e-mail actie toevoegen](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. Configureer in het venster **een E-mail verzenden** de volgende vereiste velden:

   - Voer ten minste één geldig e-mail adres **in** , waarbij de leads worden verzonden.
   - **Met de** optie stroom kunt u dynamische inhoud toevoegen, zoals **LeadSource** in de volgende scherm opname. Begin met het typen van een veld naam gevolgd door te klikken op de lijst met dynamische inhoud in het pop-upvenster. 

        >[!Note] 
        > Bij het toevoegen van veld namen kunt u elk een ': ' volgen en vervolgens op ENTER om een nieuwe rij te maken. Zodra u de veld namen hebt toegevoegd, kunt u elke gekoppelde para meter toevoegen uit de dynamische keuze lijst.

        ![Een e-mail actie toevoegen met behulp van dynamische inhoud](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Hoofd tekst** : Voeg de gewenste informatie toe aan de hoofd tekst van het e-mail bericht in de lijst met dynamische inhoud. Voor beeld: LastName, FirstName, email en Company. <br> <br> Wanneer u klaar bent met het instellen van de e-mail melding, ziet u in het voor beeld in de volgende scherm opname.


       ![Een e-mail actie toevoegen](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Selecteer **Opslaan** om de stroom te volt ooien. Er wordt een HTTP POST-URL gemaakt en deze is toegankelijk in het venster *Wanneer een HTTP-aanvraag wordt ontvangen* . Kopieer deze URL met behulp van het Kopieer besturings element dat zich rechts van de HTTP POST-URL bevindt. Dit is belang rijk, zodat u niet per ongeluk een deel van de volledige URL mist. Sla deze URL op zoals u deze nodig hebt wanneer u Lead beheer configureert in de portal voor publiceren.

   ![HTTP POST-URL ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testen

U kunt testen of alles werkt zoals verwacht, met behulp van een hulp programma dat is aangeduid als ' [postman](https://app.getpostman.com/app/download/win64)', dat online kan worden gedownload. Dit is beschikbaar voor Windows. 

1. Start postman en selecteer **nieuwe** > **aanvraag** om uw test programma in te stellen. 

   ![Aanvraag voor het instellen van uw test programma](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Vul het formulier *aanvraag opslaan* in en **Sla** het bestand op in de map die u hebt gemaakt.

   ![Aanvraag opslaan](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Selecteer **bericht** in de vervolg keuzelijst. 

   ![Mijn stroom testen](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Plak de URL van de HTTP-POST uit de stroom die u hebt gemaakt in MS flow, waar de *aanvraag-URL wordt ingevoerd*.

   ![De HTTP POST-URL plakken](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Ga terug naar [flow](https://flow.microsoft.com/) en zoek de stroom die u hebt gemaakt voor het verzenden van leads door te gaan naar **mijn stromen** in de menu balk van de stroom.  Selecteer de drie puntjes naast de naam van de stroom en selecteer **bewerken**.

   ![Mijn stromen-bewerken](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Selecteer in de rechter bovenhoek de optie **testen** , selecteer ik voer de trigger actie uit en selecteer vervolgens **testen**. Boven aan het scherm wordt een indicatie weer gegeven dat aangeeft dat de test is gestart

   ![Test stroom-trigger](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Ga terug naar uw postman-app en selecteer **verzenden** naast de locatie waar u de HTTPS-URL hebt geplakt.

   ![Mijn stroom testen: verzenden](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Ga terug naar uw stroom en controleer het resultaat. Als alles naar behoren werkt, wordt er een bericht weer gegeven waarin staat dat het is geslaagd.

   ![Stroom-resultaten controleren](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. U hebt ook een e-mail bericht ontvangen. Controleer uw postvak in. 

    >[!Note] 
    >Als u geen e-mail bericht van de test ziet, controleert u vervolgens uw spam en ongewenste e-mail. Hieronder ziet u alleen de veldlabels die u hebt toegevoegd bij het configureren van de e-mail melding. Als dit een daad werkelijke lead is die is gegenereerd op basis van uw aanbieding, ziet u ook de werkelijke gegevens van de contact persoon van de lead in de hoofd tekst en op de regel onderwerp.

   ![E-mail ontvangen](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Uw aanbieding configureren om leads naar het HTTPS-eind punt te verzenden

Wanneer u klaar bent om de informatie over het beheer van leads voor uw aanbieding te configureren in de portal voor publiceren, volgt u de onderstaande stappen:

1. Navigeer naar de pagina voor het instellen van de **aanbieding** voor uw aanbieding.
2. Selecteer **verbinding maken** in het gedeelte Lead beheer.
3. Selecteer in het pop-upvenster verbindings Details het **https-eind punt** voor de **doel bestemming** en plak de URL van de http post vanuit de stroom die u hebt gemaakt door eerdere stappen in het veld **https-eind punt URL** te volgen.
4. Selecteer **Opslaan**. 

>[!Note] 
>U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen.

Wanneer leads worden gegenereerd, stuurt micro soft leads naar de stroom die worden doorgestuurd naar het CRM-systeem of e-mail adres dat u hebt geconfigureerd.

![Lead beheer-verbinding maken](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Verbindingsdetails](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Verbindingsdetails](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)


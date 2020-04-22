---
title: Leadbeheer configureren met behulp van een HTTPS-eindpunt | Azure Marketplace
description: Meer informatie over het gebruik van een HTTP-eindpunt voor het verwerken van Microsoft AppSource- en Azure Marketplace-leads.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770195"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Leadbeheer configureren met behulp van een HTTPS-eindpunt

U een HTTPS-eindpunt gebruiken om Microsoft AppSource- en Azure Marketplace-leads te verwerken. Deze leads kunnen worden geschreven naar een CRM-systeem (Customer Relationship Management) of als e-mailmelding worden verzonden. In dit artikel wordt beschreven hoe u de [automatiseringsservice microsoft Power Automate](https://powerapps.microsoft.com/automate-processes/) gebruiken om leadbeheer te configureren.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Een stroom maken met Microsoft Power Automate

1. Open de webpagina [Power Automate.](https://flow.microsoft.com/) Selecteer **Aanmelden** of selecteer **Aanmelden om een** gratis Flow-account aan te maken.

1. Log in en selecteer **Mijn flows** op de menubalk.
    > [!div class="mx-imgBorder"]
    > ![Mijn stromen](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. Selecteer **onder + Nieuw**+ **Direct- van leeg**.
    > [!div class="mx-imgBorder"]
    > ![Leeg item maken](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Geef uw stroom een naam en selecteer onder **Kiezen voor deze stroom**deze stroom , selecteer Wanneer een **HTTP-aanvraag is ontvangen**.

    > [!div class="mx-imgBorder"]
    > ![Selecteer de HTTP-aanvraagontvangen trigger](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Klik op de stroomstap om deze uit te vouwen.

    > [!div class="mx-imgBorder"]
    > ![De stroomstap uitvouwen](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. Gebruik een van de volgende methoden om het **JSON-schema voor aanvraagbody**te configureren:

   - Kopieer het [JSON-schema](#json-schema) aan het einde van dit artikel naar het tekstvak **Json-schema van aanvraag.**
   - Selecteer **Voorbeeldnettolading om een schema te genereren**. Plak in het tekstvak **Json-payload van een voorbeeld een** voorbeeld van JSON in het voorbeeld [JSON](#json-example). Selecteer **Gereed** om het schema te maken.

   >[!Note]
   >Op dit punt in de flow u verbinding maken met een CRM-systeem of een e-mailmelding configureren.

### <a name="to-connect-to-a-crm-system"></a>Verbinding maken met een CRM-systeem

1. Selecteer **+ Nieuwe stap**.
2. Kies het CRM-systeem van uw keuze met de actie om een nieuwe record te maken. De volgende schermopname toont **Dynamics 365 - Maak een nieuwe record** als voorbeeld.

    ![Een nieuwe record maken](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Geef de **organisatienaam** op die de verbindingsingangen voor uw connector zijn. Selecteer **Leads** in de vervolgkeuzelijst **Entiteitsnaam.**

    ![Leads selecteren](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flow toont een formulier voor het verstrekken van leadinformatie. U items uit het invoerverzoek toewijzen door te kiezen voor het toevoegen van dynamische inhoud. In de volgende schermopname ziet **U's OfferTitle** als voorbeeld.

    ![Dynamische inhoud toevoegen](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Breng de gewenste velden in kaart en selecteer **Opslaan** om uw stroom op te slaan.

6. Er wordt een HTTP-POST-URL gemaakt in de aanvraag. Kopieer deze URL en gebruik deze als https-eindpunt.

    ![HTTP-bericht-URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>E-mailmelding instellen

1. Selecteer **+ Nieuwe stap**.
2. Selecteer **Acties** **onder Een actie kiezen**.
3. Selecteer **Een e-mail verzenden** onder **Acties**.

    ![Een e-mailactie toevoegen](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. Configureer in **Een e-mail verzenden**de volgende vereiste velden:

   - **Om** - Voer ten minste één geldig e-mailadres in.
   - **Onderwerp** - Flow geeft je de mogelijkheid om Dynamische inhoud toe te voegen, zoals **LeadSource** in de volgende schermopname.

     ![Een e-mailactie toevoegen met dynamische inhoud](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Hoofdtekst** - Voeg in de lijst Dynamische inhoud de gewenste informatie toe in de hoofdtekst van de e-mail. Bijvoorbeeld Achternaam, Voornaam, E-mail en Bedrijf.

   Wanneer u klaar bent met het instellen van de e-mailmelding, ziet deze eruit als het voorbeeld in de volgende schermopname.

   ![Een e-mailactie toevoegen](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Selecteer **Opslaan** om de stroom te voltooien.

6. Er wordt een HTTP-POST-URL gemaakt in de aanvraag. Kopieer deze URL en gebruik deze als https-eindpunt.

    ![HTTP-bericht-URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Uw aanbieding configureren om leads naar het HTTPS-eindpunt te verzenden

Wanneer u de leadbeheergegevens voor uw aanbieding configureert, selecteert u **HTTPS-eindpunt** voor de **leadbestemming** en plakt u in de HTTP-POST-URL die u in de vorige stap hebt gekopieerd.  

![Dynamische inhoud toevoegen](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Wanneer leads worden gegenereerd, stuurt Microsoft leads naar uw Power Automate-stroom, die wordt doorgestuurd naar het CRM-systeem of het e-mailadres dat u hebt geconfigureerd.

## <a name="json-schema-and-example"></a>JSON-schema en voorbeeld

In het voorbeeld van de JSON-test wordt het volgende schema gebruikt:

### <a name="json-schema"></a>JSON-schema

``` json
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
    "Description": {
      "id": "/properties/Description",
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

U het volgende JSON-voorbeeld kopiëren en bewerken om als test in uw flow te gebruiken.

### <a name="json-example"></a>Json voorbeeld

```json
{
  "UserDetails": {
    "FirstName": "Some",
    "LastName": "One",
    "Email": "someone@contoso.com",
    "Phone": "16175555555",
    "Country": "USA",
    "Company": "Contoso",
    "Title": "Esquire"
 },
  "LeadSource": "AzureMarketplace",
  "ActionCode": "INS",
  "OfferTitle": "Test Microsoft",
  "Description": "Test run through Power Automate"
}
```

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, configureert u [klantleads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) in de Cloud Partner Portal.

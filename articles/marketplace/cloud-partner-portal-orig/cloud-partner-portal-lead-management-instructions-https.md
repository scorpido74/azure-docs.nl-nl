---
title: Beheer van potentiële klanten configureren met behulp van een HTTPS-eind punt | Azure Marketplace
description: Meer informatie over het gebruik van een HTTP-eind punt voor het afhandelen van Microsoft AppSource en Azure Marketplace-leads.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770195"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Beheer van potentiële klanten configureren met behulp van een HTTPS-eind punt

U kunt een HTTPS-eind punt gebruiken voor het afhandelen van Microsoft AppSource en Azure Marketplace-leads. Deze leads kunnen naar een CRM-systeem (Customer Relationship Management) worden geschreven of als e-mail melding worden verzonden. In dit artikel wordt beschreven hoe u de [micro soft power](https://powerapps.microsoft.com/automate-processes/) Automation-Service kunt gebruiken om beheer van leads te configureren.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Een stroom maken met micro soft power Automatiseer

1. Open de webpagina voor [automatische energie automatisering](https://flow.microsoft.com/) . Selecteer **Aanmelden** of selecteer **gratis registreren** om een gratis stroom account te maken.

1. Meld u aan en selecteer **mijn stromen** in de menu balk.
    > [!div class="mx-imgBorder"]
    > ![Mijn stromen](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. Onder **+ Nieuw**selecteert u **+ direct, leeg**.
    > [!div class="mx-imgBorder"]
    > ![Leeg item maken](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Geef uw stroom een naam en selecteer vervolgens onder **kiezen hoe deze stroom moet worden geactiveerd**, **Wanneer een HTTP-aanvraag wordt ontvangen**.

    > [!div class="mx-imgBorder"]
    > ![Selecteer de trigger voor ontvangen HTTP-aanvragen](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Klik op de stap van de stroom om deze uit te vouwen.

    > [!div class="mx-imgBorder"]
    > ![De stroom stap uitvouwen](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. Gebruik een van de volgende methoden om het JSON-schema van de **aanvraag tekst**te configureren:

   - Kopieer het [JSON-schema](#json-schema) aan het einde van dit artikel in het tekstvak aanvraag tekst voor **JSON-schema** .
   - Selecteer **Voorbeeldnettolading om een schema te genereren**. Plak in het tekstvak **een voor beeld van een JSON-nettolading invoeren of plakken** in het [JSON-voor beeld](#json-example). Selecteer **gereed** om het schema te maken.

   >[!Note]
   >Op dit punt in de stroom kunt u verbinding maken met een CRM-systeem of een e-mail melding configureren.

### <a name="to-connect-to-a-crm-system"></a>Verbinding maken met een CRM-systeem

1. Selecteer **+ Nieuwe stap**.
2. Kies het CRM-systeem van uw keuze met de actie om een nieuwe record te maken. In de volgende scherm opname wordt **Dynamics 365-een nieuwe record gemaakt** als voor beeld.

    ![Een nieuwe record maken](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Geef de **naam** van de organisatie op die de verbindings invoer voor uw connector is. Selecteer **leads** in de vervolg keuzelijst **entiteits naam** .

    ![Leads selecteren](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flow toont een formulier voor het leveren van lead gegevens. U kunt items van de invoer aanvraag toewijzen door dynamische inhoud toe te voegen. In de volgende scherm opname wordt **OfferTitle** weer gegeven als een voor beeld.

    ![Dynamische inhoud toevoegen](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Wijs de gewenste velden toe en selecteer **Opslaan** om uw stroom op te slaan.

6. Er wordt een HTTP POST-URL in de aanvraag gemaakt. Kopieer deze URL en gebruik deze als het HTTPS-eind punt.

    ![HTTP post-URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>E-mail meldingen instellen

1. Selecteer **+ Nieuwe stap**.
2. Onder **Kies een actie**selecteert u **acties**.
3. Selecteer **Een e-mail verzenden** onder **Acties**.

    ![Een e-mail actie toevoegen](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. Configureer in **E-mail verzenden**de volgende vereiste velden:

   - Om ten minste één geldig e-mail adres **in te** voeren.
   - **Met de** optie stroom kunt u dynamische inhoud toevoegen, zoals **LeadSource** in de volgende scherm opname.

     ![Een e-mail actie toevoegen met behulp van dynamische inhoud](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Hoofd tekst** : Voeg in de lijst met dynamische inhoud de gewenste informatie toe aan de hoofd tekst van het e-mail bericht. Voor beeld: LastName, FirstName, email en Company.

   Wanneer u klaar bent met het instellen van de e-mail melding, ziet u in het voor beeld in de volgende scherm opname.

   ![Een e-mail actie toevoegen](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Selecteer **Opslaan** om de stroom te volt ooien.

6. Er wordt een HTTP POST-URL in de aanvraag gemaakt. Kopieer deze URL en gebruik deze als het HTTPS-eind punt.

    ![HTTP post-URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Uw aanbieding configureren om leads naar het HTTPS-eind punt te verzenden

Wanneer u de Lead beheer gegevens voor uw aanbieding configureert, selecteert u **https-eind punt** voor de **doel bestemming** en PLAKT u de http post-URL die u tijdens de vorige stap hebt gekopieerd.  

![Dynamische inhoud toevoegen](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Wanneer leads worden gegenereerd, stuurt micro soft leads naar uw stroom voor stroom gebruik, die wordt doorgestuurd naar het CRM-systeem of e-mail adres dat u hebt geconfigureerd.

## <a name="json-schema-and-example"></a>JSON-schema en-voor beeld

In het voor beeld van de JSON-test wordt het volgende schema gebruikt:

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

U kunt het volgende JSON-voor beeld kopiëren en bewerken om te gebruiken als test in uw stroom.

### <a name="json-example"></a>JSON-voor beeld

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

Als u dit nog niet hebt gedaan, configureert u [leads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) van klanten in de Cloud Partner-Portal.

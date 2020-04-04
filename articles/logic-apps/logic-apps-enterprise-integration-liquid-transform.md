---
title: Json-gegevens converteren met Liquid-transformaties
description: Transformaties of kaarten maken voor geavanceerde JSON-transformaties met logic-apps en vloeistofsjabloon
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: 0ab9297e772a3b75a077da1c2ae74e5058b2731f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657196"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Geavanceerde JSON-transformaties uitvoeren met Liquid-sjablonen in Azure Logic Apps

U basis-JSON-transformaties uitvoeren in uw logische apps met native data-bewerkingsacties zoals **Compose** of **Parse JSON**. Als u geavanceerde JSON-transformaties wilt uitvoeren, u sjablonen of kaarten maken met [Liquid,](https://shopify.github.io/liquid/)een open-sourcesjabloontaal voor flexibele web-apps. Een liquid-sjabloon definieert hoe JSON-uitvoer kan worden getransformeerd en ondersteunt complexere JSON-transformaties, zoals iteraties, besturingsstromen, variabelen, enzovoort.

Voordat u een Liquid-transformatie in uw logica-app uitvoeren, moet u eerst de JSON-map naar JSON definiëren met een Liquid-sjabloon en die kaart opslaan in uw integratieaccount. In dit artikel ziet u hoe u deze liquid-sjabloon of kaart maken en gebruiken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Een [basisintegratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Basiskennis over [vloeibare sjabloontaal](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Vloeibare sjabloon of kaart maken voor uw integratieaccount

1. Maak in dit voorbeeld de in deze stap beschreven sjabloon monstervloeistof. In uw liquide sjabloon u [vloeibare filters](https://shopify.github.io/liquid/basics/introduction/#filters)gebruiken, die [gebruikmaken van DotLiquid-](https://dotliquidmarkup.org/) en C#-naamgevingsconventies.

   > [!NOTE]
   > Zorg ervoor dat de filternamen *de zinsbehuizing* in uw sjabloon gebruiken. Anders werken de filters niet. Kaarten hebben ook [limieten voor bestandsgrootte.](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}
   
   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
   }
   ```

1. Voer in de [Azure-portal](https://portal.azure.com)in `integration accounts`het azure-zoekvak integratieaccounts in en selecteer **deze.**

   ![Zoek 'Integratieaccounts'](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Zoek en selecteer uw integratieaccount.

   ![Integratieaccount selecteren](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. Selecteer **Kaarten**in het deelvenster **Overzicht** onder **Componenten**.

    ![De tegel 'Kaarten' selecteren](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. Selecteer in het deelvenster **Kaarten** de optie **Toevoegen** en geef deze gegevens op voor uw kaart:

   | Eigenschap | Waarde | Beschrijving | 
   |----------|-------|-------------|
   | **Naam** | `JsonToJsonTemplate` | De naam voor uw kaart, die "JsonToJsonTemplate" in dit voorbeeld is | 
   | **Kaarttype** | **Vloeistof** | Het type voor uw kaart. Voor JSON- tot JSON-transformatie moet u **vloeistof**selecteren. | 
   | **Kaart** | `SimpleJsonToJsonTemplate.liquid` | Een bestaande Liquid template of kaart bestand te gebruiken voor transformatie, dat is "SimpleJsonToJsonTemplate.liquid" in dit voorbeeld. Als u dit bestand wilt vinden, u de bestandenkiezer gebruiken. Zie Limieten en [configuratie](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)voor limieten voor kaartgrootte . |
   ||| 

   ![Sjabloon Vloeibare toevoegen](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Voeg de vloeibare actie voor JSON-transformatie toe

1. Voer in de Azure-portal de volgende stappen uit om een lege logische app te [maken.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. Voeg in de Logic App Designer de [trigger van Aanvragen](../connectors/connectors-native-reqres.md#add-request) toe aan uw logische app.

1. Kies Onder de trigger De optie **Nieuwe stap**. Voer in het `liquid` zoekvak in als filter en selecteer deze actie: **Transform JSON to JSON - Liquid**

   ![Vloeibare actie zoeken en selecteren](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Open de **lijst Kaart** en selecteer de sjabloon Liquid, namelijk 'JsonToJsonTemplate' in dit voorbeeld.

   ![Kaart selecteren](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Als de lijst met kaarten leeg is, is uw logische app waarschijnlijk niet gekoppeld aan uw integratieaccount. 
   Voer de volgende stappen uit om uw logica-app te koppelen aan het integratieaccount met de sjabloon Liquid of map:

   1. Selecteer **werkstroominstellingen**in het menu van de logische app .

   1. Selecteer in de lijst **Een integratieaccount selecteren** uw integratieaccount en selecteer **Opslaan**.

      ![Logische app koppelen aan integratieaccount](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Voeg nu de eigenschap **Inhoud** toe aan deze actie. Open de lijst **Nieuwe parameter toevoegen** en selecteer **Inhoud**.

   ![Eigenschap Inhoud toevoegen aan actie](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Als u de **eigenschapinhoudswaarde** wilt instellen, klikt u in het vak **Inhoud** zodat de lijst met dynamische inhoud wordt weergegeven. Selecteer het **tekenteken** dat de uitvoer van de hoofdinhoud van de trigger vertegenwoordigt.

   ![Selecteer 'Body'-token voor eigenschapswaarde 'Inhoud'](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Wanneer u klaar bent, ziet de actie eruit als in dit voorbeeld:

   ![Voltooide actie "Transform JSON to JSON"](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Uw logische app testen

Plaats JSON-invoer naar uw logische app vanuit [Postman](https://www.getpostman.com/postman) of een soortgelijk hulpmiddel. De getransformeerde JSON-uitvoer van uw logische app ziet er als volgt uit:
  
![Voorbeelduitvoer](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Meer Vloeibare actievoorbeelden
Vloeistof is niet beperkt tot alleen JSON transformaties. Hier zijn andere beschikbare transformatieacties die Liquid gebruiken.

* JSON omzetten in tekst
  
  Hier is de sjabloon Liquid die voor dit voorbeeld wordt gebruikt:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Hier volgen voorbeeldingangen en uitgangen:
  
   ![Voorbeeld uitvoer JSON naar tekst](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML omzetten in JSON
  
  Hier is de sjabloon Liquid die voor dit voorbeeld wordt gebruikt:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Hier volgen voorbeeldingangen en uitgangen:

   ![Voorbeeld uitvoer XML naar JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* XML omzetten in tekst
  
  Hier is de sjabloon Liquid die voor dit voorbeeld wordt gebruikt:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Hier volgen voorbeeldingangen en uitgangen:

   ![Voorbeeld van uitvoer-XML naar tekst](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Meer informatie over enterprise integration pack")  
* [Meer informatie over kaarten](../logic-apps/logic-apps-enterprise-integration-maps.md "Meer informatie over kaarten voor bedrijfsintegratie")  


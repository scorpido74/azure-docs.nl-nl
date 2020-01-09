---
title: JSON-gegevens converteren met liquide trans formaties
description: Trans formaties of kaarten voor geavanceerde JSON-trans formaties maken met behulp van Logic Apps en vloei bare sjabloon
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: fb9f9cfdba07ebe0bc5800def6d93950869e9727
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456643"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Geavanceerde JSON-trans formaties uitvoeren met liquide sjablonen in Azure Logic Apps

U kunt eenvoudige JSON-trans formaties uitvoeren in uw Logic apps met systeem eigen gegevens bewerkingen, zoals het **opstellen** of **parseren van JSON**. Als u geavanceerde JSON-trans formaties wilt uitvoeren, kunt u sjablonen of kaarten met [liquide middelen](https://shopify.github.io/liquid/)maken. Dit is een open source sjabloon taal voor flexibele web-apps. Een vloeistof sjabloon definieert hoe de JSON-uitvoer moet worden getransformeerd en ondersteunt complexere JSON-trans formaties, zoals iteraties, controle stromen, variabelen, enzovoort. 

Voordat u een liquide trans formatie in uw logische app kunt uitvoeren, moet u eerst de JSON definiëren in JSON-toewijzing met een vloeistof sjabloon en die kaart opslaan in uw integratie account. In dit artikel wordt beschreven hoe u deze vloeistof sjabloon of kaart maakt en gebruikt. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement hebt, kunt u [beginnen met een gratis Azure-account](https://azure.microsoft.com/free/). U kunt [zich ook registreren voor een abonnement met betalen per gebruik](https://azure.microsoft.com/pricing/purchase-options/).

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Een basis [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Basis kennis over [taal van liquide sjablonen](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Vloeistof sjabloon of kaart maken voor uw integratie account

1. Voor dit voor beeld maakt u de voor beeld vloeistof sjabloon die wordt beschreven in deze stap. In uw vloeistof sjabloon kunt u [liquide filters](https://shopify.github.io/liquid/basics/introduction/#filters)gebruiken die gebruikmaken van [DotLiquid](https://dotliquidmarkup.org/) en C# naam conventies. 

   > [!NOTE]
   > Zorg ervoor dat de namen van de filters *zinnen* in de sjabloon gebruiken. Anders werken de filters niet. Daarnaast hebben Maps de [Bestands grootte limieten](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits).

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

2. Meld u aan bij de [Azure Portal](https://portal.azure.com). Selecteer **alle resources**in het hoofd menu van Azure. Zoek en selecteer uw integratie account in het zoekvak.

   ![Integratie account selecteren](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  Selecteer **kaarten**onder **onderdelen**.

    ![Kaarten selecteren](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Kies **toevoegen** en geef deze details op voor uw kaart:

   | Eigenschap | Waarde | Beschrijving | 
   |----------|-------|-------------|
   | **Naam** | JsonToJsonTemplate | De naam voor uw kaart, in dit voor beeld ' JsonToJsonTemplate ' | 
   | **Toewijzings type** | **Liquid** | Het type voor uw kaart. Voor JSON naar JSON-trans formatie moet u **liquide middelen**selecteren. | 
   | **Kaart** | "SimpleJsonToJsonTemplate. liquide" | In dit voor beeld wordt een bestaande vloeistof sjabloon of kaart bestand gebruikt voor trans formatie, dat "SimpleJsonToJsonTemplate. liquide" is. U kunt dit bestand vinden met behulp van de bestands kiezer. Zie [limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)voor limieten voor kaart grootte. |
   ||| 

   ![Vloeistof sjabloon toevoegen](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>De vloei bare actie voor JSON-trans formatie toevoegen

1. Voer in de Azure Portal de volgende stappen uit om [een lege logische app te maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Voeg in de ontwerp functie voor logische apps de [aanvraag trigger](../connectors/connectors-native-reqres.md#add-request) toe aan uw logische app.

3. Kies **nieuwe stap**onder de trigger. 
   In het zoekvak voert u "liquide" in als uw filter en selecteert u deze actie: **JSON transformeren naar JSON-liquide**

   ![Een liquide actie zoeken en selecteren](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Klik in het vak **inhoud** zodat de lijst met dynamische inhoud wordt weer gegeven en selecteer het token **Body** .
  
   ![Hoofd tekst selecteren](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Selecteer in de lijst **kaart** uw vloeistof sjabloon in dit voor beeld: ' JsonToJsonTemplate '.

   ![Kaart selecteren](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Als de lijst met kaarten leeg is, is de logische app waarschijnlijk niet gekoppeld aan uw integratie account. 
   Voer de volgende stappen uit om uw logische app te koppelen aan het integratie account met de vloeistof sjabloon of kaart:

   1. Selecteer **werk stroom instellingen**in het menu van de logische app.

   2. Selecteer in de lijst **een integratie account selecteren** het integratie account en kies **Opslaan**.

      ![Logische app koppelen aan integratie account](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Uw logische app testen

Post JSON input naar uw logische app van [postman](https://www.getpostman.com/postman) of een soortgelijk hulp programma. De getransformeerde JSON-uitvoer van uw logische app ziet eruit als in dit voor beeld:
  
![Voorbeeld uitvoer](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Meer voor beelden van liquide acties
Vloei stof is niet beperkt tot JSON-trans formaties. Dit zijn andere beschik bare transformatie acties die gebruikmaken van liquide activiteiten.

* JSON transformeren naar tekst
  
  Dit is de vloeistof sjabloon die wordt gebruikt voor dit voor beeld:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Hier volgen enkele voor beelden van invoer en uitvoer:
  
   ![Voorbeeld uitvoer van JSON naar tekst](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML transformeren naar JSON
  
  Dit is de vloeistof sjabloon die wordt gebruikt voor dit voor beeld:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Hier volgen enkele voor beelden van invoer en uitvoer:

   ![Voor beeld van uitvoer-XML naar JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* XML naar tekst transformeren
  
  Dit is de vloeistof sjabloon die wordt gebruikt voor dit voor beeld:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Hier volgen enkele voor beelden van invoer en uitvoer:

   ![Voor beeld van uitvoer-XML naar tekst](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Meer informatie over Enterprise Integration Pack")  
* [Meer informatie over Maps](../logic-apps/logic-apps-enterprise-integration-maps.md "Meer informatie over Enter prise Integration Maps")  


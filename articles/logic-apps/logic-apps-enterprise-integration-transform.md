---
title: XML tussen indelingen transformeren
description: Transformaties of kaarten maken die XML converteren tussen indelingen in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 500769a39ba7658b35c1abb80101f6234170c941
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792391"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Kaarten maken die XML transformeren tussen indelingen in Azure Logic Apps met Enterprise Integration Pack

De Enterprise Integration Transform-connector converteert gegevens van de ene indeling naar de andere indeling. U bijvoorbeeld een binnenkomend bericht hebben dat de huidige datum bevat in de jaarmaanddag-indeling. U een transformatie gebruiken om de datum in de MonthDayYear-indeling opnieuw te formatteren.

## <a name="what-does-a-transform-do"></a>Wat doet een transformatie?
Een transformatie, die ook wel een kaart wordt genoemd, bestaat uit een Bron XML-schema (de invoer) en een Target XML-schema (de uitvoer). U verschillende ingebouwde functies gebruiken om de gegevens te manipuleren of te beheren, waaronder tekenreeksmanipulaties, voorwaardelijke toewijzingen, rekenkundige expressies, datumtijd voor zaken en zelfs loopingconstructies.

## <a name="how-to-create-a-transform"></a>Hoe maak je een transformatie?
U een transformatie/kaart maken met behulp van de Visual Studio [Enterprise Integration SDK.](https://aka.ms/vsmapsandschemas) Wanneer u klaar bent met het maken en testen van de transformatie, uploadt u de transformatie naar uw integratieaccount. 

## <a name="how-to-use-a-transform"></a>Hoe een transformatie te gebruiken
Nadat u de transformatie/kaart hebt geüpload naar uw integratieaccount, u deze gebruiken om een Logic-app te maken. De Logic-app voert uw transformaties uit wanneer de Logic-app wordt geactiveerd (en er is invoerinhoud die moet worden getransformeerd).

**Hier zijn de stappen om een transformatie te gebruiken:**

### <a name="prerequisites"></a>Vereisten

* Een integratieaccount maken en er een kaart aan toevoegen  

Nu u de vereisten hebt geregeld, is het tijd om uw Logic-app te maken:  

1. Maak een Logic-app en [koppel deze aan uw integratieaccount](../logic-apps/logic-apps-enterprise-integration-accounts.md "Meer informatie over het koppelen van een integratieaccount aan een Logic-app") dat de kaart bevat.
2. Een **trigger van een aanvraag** toevoegen aan uw Logic-app  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. De actie **XML transformeren** toevoegen door eerst een **actie toevoegen** te selecteren   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Voer de *woordtransformatie* in het zoekvak in om alle acties te filteren op de acties die u wilt gebruiken  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. De actie **XML transformeren** selecteren   
6. Voeg de **XML-inhoud** toe die u transformeert. U alle XML-gegevens die u in het HTTP-verzoek ontvangt, als **inhoud**gebruiken. Selecteer in dit voorbeeld de hoofdtekst van het HTTP-verzoek dat de Logic-app heeft geactiveerd.

   > [!NOTE]
   > Controleer of de inhoud voor de **TRANSFORM XML** XML is. Als de inhoud zich niet in XML bevindt of is gecodeerd met een basis64, moet u een expressie opgeven die de inhoud verwerkt. U bijvoorbeeld [functies](logic-apps-workflow-definition-language.md#functions)gebruiken, zoals ```@base64ToBinary``` voor ```@xml``` het decoderen van inhoud of voor het verwerken van de inhoud als XML.
 

7. Selecteer de naam van **de MAP** die u wilt gebruiken om de transformatie uit te voeren. De kaart moet al in uw integratieaccount staan. In een eerdere stap gaf u uw Logic-app al toegang tot uw integratieaccount dat uw kaart bevat.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Uw werk opslaan  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Op dit moment bent u klaar met het instellen van uw kaart. In een echte wereldtoepassing u de getransformeerde gegevens opslaan in een LOB-toepassing zoals SalesForce. U eenvoudig als actie de uitvoer van de transformatie naar Salesforce verzenden. 

U uw transformatie nu testen door een aanvraag in te dienen bij het HTTP-eindpunt.  


## <a name="features-and-use-cases"></a>Functies en use cases
* De transformatie die in een kaart is gemaakt, kan eenvoudig zijn, zoals het kopiëren van een naam en adres van het ene document naar het andere. U ook complexere transformaties maken met behulp van de kant-en-klare kaartbewerkingen.  
* Meerdere kaartbewerkingen of -functies zijn direct beschikbaar, waaronder tekenreeksen, datumtijdfuncties, enzovoort.  
* U een directe gegevenskopie tussen de schema's maken. In de Mapper die in de SDK is opgenomen, is dit net zo eenvoudig als het tekenen van een lijn die de elementen in het bronschema verbindt met hun tegenhangers in het doelschema.  
* Bij het maken van een kaart bekijkt u een grafische weergave van de kaart, die alle relaties en koppelingen toont die u maakt.
* Gebruik de functie Testkaart om een voorbeeld-XML-bericht toe te voegen. Met een simpele klik u de kaart die u hebt gemaakt testen en de gegenereerde uitvoer bekijken.  
* Bestaande kaarten uploaden  
* Bevat ondersteuning voor de XML-indeling.

## <a name="advanced-features"></a>Geavanceerde functies

### <a name="reference-assembly-or-custom-code-from-maps"></a>Referentieverzameling of aangepaste code van kaarten 
De transformatieactie ondersteunt ook kaarten of transformaties met betrekking tot externe assemblage. Met deze mogelijkheid kunnen oproepen rechtstreeks vanuit XSLT-kaarten naar aangepaste .NET-code. Hier zijn de voorwaarden om assemblage in kaarten te gebruiken.

* De kaart en de assemblage waarnaar van de kaart wordt verwezen, moeten worden [geüpload naar het integratieaccount.](./logic-apps-enterprise-integration-maps.md) 

  > [!NOTE]
  > Kaart en montage moeten worden geüpload in een specifieke volgorde. U moet de assemblage uploaden voordat u de kaart uploadt die naar de assemblage verwijst.

* De kaart moet ook deze kenmerken en een CDATA-sectie bevatten die de aanroep naar de assemblagecode bevat:

    * **naam** is de aangepaste assemblagenaam.
    * **naamruimte** is de naamruimte in uw verzameling die de aangepaste code bevat.

  In dit voorbeeld wordt een kaart weergegeven die verwijst naar een `circumreference` assemblage met de naam "XslUtilitiesLib" en de methode van de assemblage aanroept.

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
     <circles>
        <xsl:for-each select="circle">
            <circle>
                <xsl:copy-of select="node()"/>
                    <circumference>
                        <xsl:value-of select="user:circumference(radius)"/>
                    </circumference>
            </circle>
        </xsl:for-each>
     </circles>
    </xsl:template>
    </xsl:stylesheet>
  ```


### <a name="byte-order-mark"></a>Byte Order Mark
Standaard begint het antwoord van de transformatie met het stempelvan de lijst met volgorde 'Byte Order'. U hebt alleen toegang tot deze functionaliteit tijdens het werken in de Code View-editor. Als u deze functionaliteit `disableByteOrderMark` wilt `transformOptions` uitschakelen, geeft u op voor de eigenschap:

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```





## <a name="learn-more"></a>Meer informatie
* [Meer informatie over het Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Meer informatie over enterprise integration pack")  
* [Meer informatie over kaarten](../logic-apps/logic-apps-enterprise-integration-maps.md "Meer informatie over kaarten voor bedrijfsintegratie")  


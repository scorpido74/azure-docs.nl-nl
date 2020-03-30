---
title: De topologie van verbonden fabriek configureren - Azure | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u de connected factory-oplossingsversneller configureren, inclusief de topologie.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 5fa3d4d4fdfa0dd81cd8ab8772ffb3903dda289f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820119"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>De oplossingsversneller van Connected Factory configureren

De Connected Factory oplossingsversneller toont een gesimuleerd dashboard voor een fictief bedrijf Contoso. Dit bedrijf heeft fabrieken op tal van wereldwijde locaties wereldwijd.

In dit artikel wordt Contoso als voorbeeld gebruikt om te beschrijven hoe u de topologie van een Connected Factory-oplossing configureert.

## <a name="simulated-factories-configuration"></a>Gesimuleerde configuratie van fabrieken

Elke Contoso fabriek heeft productielijnen die bestaan uit drie stations elk. Elk station is een echte OPC UA-server met een specifieke rol:

* Montagestation
* Teststation
* Verpakkingsstation

Deze OPC UA-servers hebben OPC UA-knooppunten en [OPC Publisher](overview-opc-publisher.md) stuurt de waarden van deze knooppunten naar Connected Factory. Dit omvat:

* Huidige operationele status, zoals het huidige stroomverbruik.
* Productie-informatie, zoals het aantal geproduceerde producten.

U het dashboard gebruiken om in te zoomen op de topologie van de Contoso-fabriek, van een globale weergave tot een weergave op stationsniveau. Het Connected Factory-dashboard maakt:

* De visualisatie van OEE- en KPI-cijfers voor elke laag in de topologie.
* De visualisatie van de huidige waarden van OPC UA-knooppunten in de stations.
* De aggregatie van de OEE- en KPI-cijfers van het stationsniveau naar het wereldwijde niveau.
* De visualisatie van waarschuwingen en acties die moeten worden uitgevoerd als waarden specifieke drempelwaarden bereiken.

## <a name="connected-factory-topology"></a>Verbonden fabriekstopologie

De topologie van fabrieken, productielijnen en stations is hiërarchisch:

* Het wereldwijde niveau heeft fabrieksknooppunten als kinderen.
* De fabrieken hebben productielijnknooppunten als kinderen.
* De productielijnen hebben stationknooppunten als kinderen.
* De stations (OPC UA-servers) hebben OPC UA-knooppunten als kinderen.

Elk knooppunt in de topologie heeft een gemeenschappelijke set eigenschappen die definiëren:

* Een unieke id voor het topologieknooppunt.
* Een naam.
* Een beschrijving.
* Een beeld.
* De kinderen van het topologieknooppunt.
* Minimum-, doel- en maximumwaarden voor OEE- en KPI-cijfers en de uit te voeren waarschuwingsacties.

## <a name="topology-configuration-file"></a>Configuratiebestand topologie

Als u de eigenschappen in de vorige sectie wilt configureren, gebruikt de oplossing Verbonden fabriek een configuratiebestand met de naam [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

U dit bestand vinden in `WebApp/Contoso/Topology` de broncode van de oplossing in de map.

In het volgende fragment `ContosoTopologyDescription.json` ziet u een overzicht van het configuratiebestand:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

De gemeenschappelijke `<global_configuration>` `<factory_configuration>`eigenschappen `<production_line_configuration>`van `<station_configuration>` , , en zijn:

* **Naam** (teksttekenreeks)

  Hiermee definieert u een beschrijvende naam, die slechts één woord moet zijn voor het topologieknooppunt dat in het dashboard wordt weergegeven.

* **Beschrijving** (teksttekenreeks)

  Beschrijft het topologieknooppunt in meer detail.

* **Afbeelding** (teksttekenreeks)

  Het pad naar een afbeelding in de WebApp-oplossing om weer te geven wanneer informatie over het topologieknooppunt wordt weergegeven in het dashboard.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, `<performance_definition>` **Kpi1**, **Kpi2** (type )

  Deze eigenschappen definiëren minimale, doel- en maximale waarden van de operationele figuur die wordt gebruikt om waarschuwingen te genereren. Deze eigenschappen definiëren ook de acties die moeten worden uitgevoerd als een waarschuwing wordt gedetecteerd.

De `<factory_configuration>` `<production_line_configuration>` items en items hebben een eigenschap:

* **Guid** (teksttekenreeks)

  Identificeert op unieke wijze het topologieknooppunt.

`<factory_configuration>`heeft een eigenschap:

* **Locatie** (type) `<location_definition>`

  Hiermee geeft u aan waar de fabriek zich bevindt.

`<station_configuration>`eigenschappen heeft:

* **OpcUri** (teksttekenreeks)

  Deze eigenschap moet zijn ingesteld op de OPC UA Application URI van de OPC UA-server.
  Omdat het wereldwijd uniek moet zijn volgens de OPC UA-specificatie, wordt deze eigenschap gebruikt om het stationtopologieknooppunt te identificeren.

* **OpcNodes**, een array van OPC UA-knooppunten (type) `<opc_node_description>`

`<location_definition>`eigenschappen heeft:

* **Plaats** (teksttekenreeks)

  Naam van de stad die het dichtst bij de locatie ligt

* **Land** (teksttekenreeks)

  Land van de locatie

* **Latitude** (type dubbel)

  Breedtegraad van de locatie

* **Longitude** (type dubbel)

  Verlangfunctie van de locatie

`<performance_definition>`eigenschappen heeft:

* **Minimum** (type dubbel)

  Lagere drempel waarde kan bereiken. Als de huidige waarde onder deze drempelwaarde ligt, wordt een waarschuwing gegenereerd.

* **Doel** (type dubbel)

  Ideale doelwaarde.

* **Maximum** (type dubbel)

  Bovendrempel de waarde kan bereiken. Als de huidige waarde boven deze drempelwaarde ligt, wordt een waarschuwing gegenereerd.

* **MinimumWaarschuwingsacties** (type) `<alert_action>`

  Hiermee definieert u de set acties die kunnen worden uitgevoerd als reactie op een minimale waarschuwing.

* **Maximumwaarschuwingsacties** (type `<alert_action>`)

  Hiermee definieert u de set acties die kunnen worden uitgevoerd als reactie op een maximale waarschuwing.

`<alert_action`> heeft eigenschappen:

* **Tekst** (teksttekenreeks)

  Type van de waarschuwingsactie. De volgende typen zijn bekend:

  * **AcknowledgeAlert:** de status van de waarschuwing moet worden gewijzigd in erkend.
  * **CloseAlert**: alle oudere waarschuwingen van hetzelfde type mogen niet meer in het dashboard worden weergegeven.
  * **CallOpcMethod**: een OPC UA-methode moet worden aangeroepen.
  * **OpenWebPage**: een browservenster moet worden geopend met aanvullende contextuele informatie.

* **Beschrijving** (teksttekenreeks)

  Beschrijving van de actie in het dashboard.

* **Parameter** (teksttekenreeks)

  Parameters die nodig zijn om de actie uit te voeren. De waarde is afhankelijk van het actietype.

  * **AcknowledgeAlert:** geen parameter vereist.
  * **CloseAlert:** geen parameter vereist.
  * **CallOpcMethod:** de knooppuntinformatie en parameters van de OPC UA-methode om aan te roepen in het formaat "NodeId van bovenliggend knooppunt, Knooppunt van de methode om aan te roepen, URI van de OPC UA-server."
  * **OpenWebPage:** de URL die moet worden weergegeven in het browservenster.

`<opc_node_description>`bevat informatie over OPC UA-knooppunten in een station (OPC UA-server). Knooppunten die geen bestaande OPC UA-knooppunten vertegenwoordigen, maar worden gebruikt als opslag in de berekeningslogica van Connected Factory, zijn ook geldig. Het heeft de volgende eigenschappen:

* **NodeId** (typetekenreeks)

  Adres van het OPC UA-knooppunt in de adresruimte van de OPC UA-server van het station(OPC UA-server). Syntaxis moet worden opgegeven in de OPC UA-specificatie voor een nodeid.

* **SymbolicName** (teksttekenreeks)

  Naam die in het dashboard moet worden weergegeven wanneer de waarde van dit OPC UA-knooppunt wordt weergegeven.

* **Relevantie** (matrix van teksttekenreeks)

  Geeft aan voor welke berekening van OEE of KPI de OPC UA-knooppuntwaarde relevant is. Elk array-element kan een van de volgende waarden zijn:

  * **OeeAvailability_Running**: de waarde is relevant voor de berekening van de beschikbaarheid van OEE.
  * **OeeAvailability_Fault**: de waarde is relevant voor de berekening van de beschikbaarheid van OEE.
  * **OeePerformance_Ideal:** de waarde is relevant voor de berekening van OEE Performance en is doorgaans een constante waarde.
  * **OeePerformance_Actual**: de waarde is relevant voor de berekening van oee-prestaties.
  * **OeeQuality_Good**: de waarde is relevant voor de berekening van oee-kwaliteit.
  * **OeeQuality_Bad**: de waarde is relevant voor de berekening van oee-kwaliteit.
  * **Kpi1**: de waarde is relevant voor de berekening van KPI1.
  * **Kpi2**: de waarde is relevant voor de berekening van KPI2.

* **OpCode** (teksttekenreeks)

  Geeft aan hoe de waarde van het OPC UA-knooppunt wordt verwerkt in Time Series Insight-query's en OEE/KPI-berekeningen. Elke Time Series Insight-query is gericht op een specifieke tijdspanne, die een parameter van de query is en een resultaat oplevert. De OpCode bepaalt hoe het resultaat wordt berekend en kan een van de volgende waarden zijn:

  * **Diff**: verschil tussen de laatste en de eerste waarde in de tijdspanne.
  * **Avg**: het gemiddelde van alle waarden in de tijdspanne.
  * **Som**: de som van alle waarden in de tijdspanne.
  * **Laatste**: momenteel niet gebruikt.
  * **Aantal**: het aantal waarden in de tijdspanne.
  * **Max:** de maximale waarde in de tijdspanne.
  * **Min**: de minimale waarde in de tijdspanne.
  * **Const**: het resultaat is de waarde die is opgegeven door property ConstValue.
  * **SubMaxMin**: het verschil tussen de maximale en de minimale waarde.
  * **Tijdspanne**: de tijdspanne.

* **Eenheden** (teksttekenreeks)

  Hiermee definieert u een eenheid van de waarde voor weergave in het dashboard.

* **Zichtbaar** (typ booleaans)

  Hiermee bepaalt u of de waarde in het dashboard moet worden weergegeven.

* **ConstValue** (type dubbel)

  Als de **OpCode** **Const**is, dan is deze eigenschap de waarde van het knooppunt.

* **Minimum** (type dubbel)

  Als de huidige waarde onder deze waarde zakt, wordt een minimale waarschuwing gegenereerd.

* **Maximum** (type dubbel)

  Als de huidige waarde boven deze waarde stijgt, wordt een maximale waarschuwing gegenereerd.

* **MinimumWaarschuwingsacties** (type) `<alert_action>`

  Hiermee definieert u de set acties die kunnen worden uitgevoerd als reactie op een minimale waarschuwing.

* **Maximumwaarschuwingsacties** (type `<alert_action>`)

  Hiermee definieert u de set acties die kunnen worden uitgevoerd als reactie op een maximale waarschuwing.

Op stationsniveau zie je ook **simulatieobjecten.** Deze objecten worden alleen gebruikt om de Connected Factory-simulatie te configureren en mogen niet worden gebruikt om een echte topologie te configureren.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Hoe de configuratiegegevens worden gebruikt tijdens uitvoering

Alle eigenschappen die in het configuratiebestand worden gebruikt, kunnen worden gegroepeerd in verschillende categorieën, afhankelijk van hoe ze worden gebruikt. Deze categorieën zijn:

### <a name="visual-appearance"></a>Uiterlijk

Eigenschappen in deze categorie definiëren de visuele weergave van het connected factory-dashboard. Voorbeelden zijn:

* Name
* Beschrijving
* Installatiekopie
* Locatie
* Eenheden
* Zichtbaar

### <a name="internal-topology-tree-addressing"></a>Interne topologieboomaaa

De WebApp onderhoudt een intern gegevenswoordenboek met informatie over alle topologieknooppunten. De eigenschappen **Guid** en **OpcUri** worden gebruikt als sleutels om toegang te krijgen tot dit woordenboek en moeten uniek zijn.

### <a name="oeekpi-computation"></a>Berekening OEE/KPI

De OEE/KPI-cijfers voor de Connected Factory-simulatie worden geparameteriseerd door:

* De OPC UA-knooppuntwaarden die in de berekening moeten worden opgenomen.
* Hoe de figuur wordt berekend op basis van de telemetriewaarden.

Connected Factory maakt gebruik van de OEE-formules zoals gepubliceerd door de [http://www.oeefoundation.org](http://www.oeefoundation.org).

Opc UA-knooppuntobjecten in stations maken het mogelijk om te taggen voor gebruik in OEE/KPI-berekening. De eigenschap **Relevantie** geeft aan voor welke OEE/KPI de OPC UA-knooppuntwaarde moet worden gebruikt. De eigenschap **OpCode** definieert hoe de waarde is opgenomen in de berekening.

### <a name="alert-handling"></a>Afhandeling van waarschuwingen

Connected Factory ondersteunt een eenvoudig mechanisme voor het genereren van waarschuwingen op basis van een minimale/maximale drempelwaarde. Er zijn een aantal vooraf gedefinieerde acties die u configureren als reactie op deze waarschuwingen. Met de volgende eigenschappen wordt dit mechanisme aangestuurd:

* Maximum
* Minimum
* MaximumAlertActies
* MinimumAlertActies

## <a name="correlating-to-telemetry-data"></a>Correleren met telemetriegegevens

Voor bepaalde bewerkingen, zoals het visualiseren van de laatste waarde of het maken van Time Series Insight-query's, heeft de WebApp een adresseringsschema nodig voor de ingenomen telemetriegegevens. De telemetrie die naar Connected Factory wordt verzonden, moet ook worden opgeslagen in interne gegevensstructuren. De twee eigenschappen die deze bewerkingen mogelijk maken, bevinden zich op het niveau van het station (OPC UA-server) en het OPC UA-knooppunt:

* **OpcUri (OpcUri)**

  Identificeert (wereldwijd uniek) de OPC UA-server waar telemetrie vandaan komt. In de ingenomen berichten wordt deze eigenschap verzonden als **ApplicationUri**.

* **NodeId**

  Hiermee wordt de knooppuntwaarde in de OPC UA-server geïdentificeerd. Het formaat van de eigenschap moet zijn zoals gespecificeerd in de OPC UA-specificatie. In de ingenomen berichten wordt deze eigenschap verzonden als **NodeId**.

Zie [Wat is OPC Publisher](overview-opc-publisher.md) voor meer informatie over hoe de telemetriegegevens worden ingenomen in Connected Factory.

## <a name="example-how-kpi1-is-calculated"></a>Voorbeeld: hoe KPI1 wordt berekend

De configuratie `ContosoTopologyDescription.json` in het bestand bepaalt hoe OEE/KPI-cijfers worden berekend. In het volgende voorbeeld ziet u hoe eigenschappen in dit bestand de berekening van KPI1 bepalen.

In Connected Factory wordt KPI1 gebruikt om het aantal succesvol vervaardigde producten in het afgelopen uur te meten. Elk station (OPC UA-server) in de Connected Factory-simulatie`NodeId: "ns=2;i=385"`biedt een OPC UA-knooppunt ( ), dat de telemetrie biedt om deze KPI te berekenen.

De configuratie voor dit OPC UA-knooppunt ziet eruit als het volgende fragment:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Met deze configuratie u de telemetriewaarden van dit knooppunt opvragen met behulp van Time Series Insights. De query Time Series Insights wordt opgehaald:

* Het aantal waarden.
* De minimale waarde.
* De maximale waarde.
* Het gemiddelde van alle waarden.
* De som van alle waarden voor alle unieke **OpcUri** (**ApplicationUri**), **NodeId** paren in een bepaalde tijdspanne.

Een kenmerk van de **nodewaarde van De Fabrikant** is dat deze alleen maar toeneemt. Om het aantal producten te berekenen dat in de tijdspanne is vervaardigd, gebruikt Connected Factory de **OpCode** **SubMaxMin**. De berekening haalt de minimumwaarde op aan het begin van de tijdspanne en de maximale waarde aan het einde van de tijdspanne.

De **OpCode** in de configuratie configureert de berekeningslogica om het resultaat van het verschil van maximale en minimale waarde te berekenen. Deze resultaten worden vervolgens van onder naar het hoofdniveau (globaal) geaccumuleerd en weergegeven in het dashboard.

## <a name="next-steps"></a>Volgende stappen

Een voorgestelde volgende stap is om te leren hoe [u de Connected Factory-oplossing aanpassen.](iot-accelerators-connected-factory-customize.md)

---
title: Wat is Azure Digital Twins?
titleSuffix: Azure Digital Twins
description: Overzicht van wat u kunt doen met Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: 11ec9a4294cb1d5895d1bbc4e75c6b1a9b071f65
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613314"
---
# <a name="what-is-azure-digital-twins"></a>Wat is Azure Digital Twins?

**Azure Digital Twins** is een IoT-platform dat het maken van uitgebreide digitale modellen van hele omgevingen mogelijk maakt. Deze omgevingen kunnen gebouwen, fabrieken, boerderijen, energienetwerken, spoorwegen, stadiums en meer zijn, zelfs steden. Deze digitale modellen kunnen worden gebruikt om inzichten te verkrijgen die betere producten, geoptimaliseerde bewerkingen, lagere kosten en baanbrekende klantervaringen opleveren.

Maak gebruik van uw domeinexpertise bovenop Azure Digital Twins om aangepaste, verbonden oplossingen te bouwen die:
* Elke omgeving maken en Digital Twins op een schaalbare en veilige manier realiseren
* Assets koppelen, zoals IoT-apparaten en bestaande bedrijfssystemen
* Een robuust gebeurtenissysteem gebruiken voor het bouwen van dynamische bedrijfslogica en gegevensverwerking
* Met Azure Data, Analytics en AI-services integreren om het verleden bij te houden en de toekomst te voorspellen

## <a name="azure-digital-twins-capabilities"></a>Azure Digital Twins-mogelijkheden

Hier volgt een overzicht van de functies van Azure Digital Twins.

### <a name="open-modeling-language"></a>Modelleringstaal openen

In Azure Digital Twins definieert u de digitale entiteiten die de personen, plaatsen en dingen in uw fysieke omgeving vertegenwoordigen met aangepaste dubbele typen, die [**modellen**](concepts-models.md) worden genoemd. 

U kunt deze modeldefinities beschouwen als een gespecialiseerde woordenlijst om uw bedrijf te beschrijven. Voor een oplossing voor gebouwbeheer kunt u bijvoorbeeld modellen definiëren zoals gebouw, verdieping en lift. U kunt vervolgens **apparaatdubbels** maken op basis van deze modellen om uw specifieke omgeving weer te geven.

Modellen worden gedefinieerd in een JSON-achtige taal met de naam [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md), waarin apparaatdubbels worden beschreven met betrekking tot hun statuseigenschappen, telemetrie-gebeurtenissen, opdrachten, onderdelen en relaties.
* Modellen definiëren semantische **relaties** tussen uw entiteiten, zodat u uw apparaatdubbels kunt verbinden in een kennisgrafiek die de interacties aangeeft. U kunt de modellen zien als een zelfstandig naamwoord in een beschrijving van uw wereld en de relaties als werkwoorden.
* U kunt apparaatdubbels ook specialiseren met behulp van een modelovername. Een model kan informatie overnemen van een ander model.

DTDL wordt gebruikt voor gegevensmodellen in andere Azure IoT-services, waaronder [IoT-Plug en Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) en [Time Series Insights (TSI)](../time-series-insights/time-series-insights-update-overview.md). Zo kunt u ervoor zorgen dat uw Azure Digital Twins-oplossing met andere onderdelen van het Azure-ecosysteem is verbonden en compatibel is.

### <a name="live-execution-environment"></a>Live uitvoeringsomgeving

Digitale modellen in Azure Digital Twins zijn live, actuele weergaven van de echte wereld. Met de relaties in uw aangepaste DTDL-modellen verbindt u apparaatdubbels met een **live grafiek** die uw omgeving weergeeft.

Azure Digital Twins voorziet in een uitgebreid **gebeurtenissysteem** om de grafiek actueel te houden met gegevensverwerking en bedrijfslogica. U kunt verbinding maken met externe rekenresources, zoals [Azure Functions](../azure-functions/functions-overview.md), om deze gegevensverwerking flexibel en aangepast uit te voeren.

U kunt ook inzichten uit de live uitvoeringsomgeving halen met behulp van de krachtige **query-API** van Azure Digital Twins. Met de API kunt u een query uitvoeren met uitgebreide zoekvoorwaarden, waaronder eigenschapswaarden, relaties, relatie-eigenschappen, modelgegevens en meer. U kunt ook query's combineren, een breed scala aan inzichten over uw omgeving verzamelen en aangepaste vragen beantwoorden die belangrijk voor u zijn.

### <a name="input-from-iot-and-business-systems"></a>Invoer van IoT- en business-systemen

Als u de live uitvoeringsomgeving van Azure Digital Twins actueel wilt houden met de echte wereld, kunt u [IoT Hub](../iot-hub/about-iot-hub.md) gebruiken om uw oplossing te verbinden met IoT- en IoT Edge-apparaten. Deze door hub beheerde apparaten worden weergegeven als onderdeel van uw dubbele grafiek en verstrekken de gegevens die uw model aansturen.

U kunt een nieuwe IoT-hub voor dit doeleinde maken met Azure Digital Twins of een bestaande IoT-hub verbinden met de apparaten die al worden beheerd.

U kunt ook Azure Digital Twins van andere gegevensbronnen afhandelen met REST API's of connectoren naar andere services, zoals [Logic Apps](../logic-apps/logic-apps-overview.md).

### <a name="output-to-tsi-storage-and-analytics"></a>Uitvoer naar TSI, opslag en analyse

De gegevens in uw Azure Digital Twins-model kunnen worden doorgestuurd naar downstream Azure-services voor extra analyse of opslag. Dit wordt mogelijk gemaakt door **gebeurtenisroutes** die gebruikmaken van [Event Hub](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md) of [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) om uw gewenste gegevensstromen aan te sturen.

Wat u kunt doen met gebeurtenisroutes:
* Azure Digital Twins-gegevens in [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md) opslaan
* Azure Digital Twins-gegevens analyseren met [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)of andere Microsoft-hulpprogramma's voor gegevensanalyse
* Grotere werkstromen integreren met Logic Apps
* Azure Digital Twins verbinden met Time Series Insights om de tijdreeksgeschiedenis van elke apparaatdubbel te volgen
* Een tijdreeksmodel in Time Series Insights uitlijnen met een bron in azure Digital Twins

Dit is een andere manier waarop Azure Digital Twins verbinding kan maken met een grotere oplossing en uw aangepaste behoeften kan ondersteunen voor voortgezet werk met deze inzichten.

## <a name="azure-digital-twins-in-a-solution-context"></a>Azure Digital Twins in de context van een oplossing

Azure Digital Twins wordt vaak gebruikt in combinatie met andere Azure-services als onderdeel van een grotere IoT-oplossing. 

Een volledige oplossing met Azure Digital Twins kan de volgende onderdelen bevatten:
* De Azure Digital Twins-service-instantie. Hiermee worden uw dubbele modellen en de dubbele grafiek met de status ervan opgeslagen en wordt de verwerking van gebeurtenissen gemodelleerd.
* Een of meer client-apps die de Azure Digital Twins-instantie aansturen door modellen te configureren, een topologie te maken en inzichten te halen uit de dubbele grafiek.
* Een of meer externe rekenresources voor het verwerken van gebeurtenissen die zijn gegenereerd door Azure Digital Twins of verbonden gegevensbronnen, zoals apparaten. Een veelgebruikte manier om rekenresources te leveren is via [Azure Functions](../azure-functions/functions-overview.md).
* Een IoT-hub om mogelijkheden voor apparaatbeheer en IoT-gegevensstromen te bieden.
* Downstreamservices voor het afhandelen van taken zoals werkstroomintegratie (zoals [Logic Apps](../logic-apps/logic-apps-overview.md), koude opslag, integratie van tijdreeksen of analyse. 

In de volgende grafiek ziet u waar Azure Digital Twins zich bevindt in de context van een grotere Azure IoT-oplossing.

:::image type="content" source="media/overview/solution-context.png" alt-text="Diagram met de invoerbronnen, uitvoerservices en communicatie in twee richtingen met zowel client-apps als externe rekenresources." border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits-in-public-preview"></a>Servicebeperkingen van de openbare preview

> [!IMPORTANT]
> Azure Digital Twins is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Voor een lijst van de beperkingen van Azure Digital Twins tijdens een openbare preview, zie [Referentie: Servicebeperkingen van de openbare preview](reference-service-limits.md).

## <a name="next-steps"></a>Volgende stappen

Als u met de vorige preview-versie van Azure Digital Twins hebt gewerkt, leest u hier wat er is gewijzigd:
* [Overzicht: Verschillen met de vorige versie](overview-differences.md)

U kunt ook direct aan de slag met Azure Digital Twins door de eerste zelfstudie te volgen:

> [!div class="nextstepaction"]
> [Zelfstudie: Een client-app coderen](tutorial-code.md)

---
title: Een oplossing voor realtime-en stroom verwerking kiezen in azure
description: Meer informatie over het kiezen van de juiste realtime analyse en streaming-verwerkings technologie om uw toepassing op Azure te bouwen.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 3bde2964c40553d02a56f57f9c459cc6afa3f660
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924897"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Kies een real-time analyse-en streaming-verwerkings technologie in azure

Er zijn verschillende services beschikbaar voor het verwerken van real-time analyse en streaming op Azure. In dit artikel vindt u de informatie die u nodig hebt om te bepalen welke technologie het meest geschikt is voor uw toepassing.

## <a name="when-to-use-azure-stream-analytics"></a>Wanneer moet ik Azure Stream Analytics gebruiken?

Azure Stream Analytics is de aanbevolen Service voor stream Analytics op Azure. Het is bedoeld voor een breed scala aan scenario's die zijn inbegrepen, maar niet beperkt zijn tot:

* Dash boards voor gegevens visualisatie
* Real-time [waarschuwingen](stream-analytics-set-up-alerts.md) van tijdelijke en ruimtelijke patronen of afwijkingen
* Extraction, Transformation, Loading (ETL)
* [Gebeurtenis bronnen patroon](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Het toevoegen van een Azure Stream Analytics taak aan uw toepassing is de snelste manier om streaming Analytics te verkrijgen in azure, met behulp van de SQL-taal die u al kent. Azure Stream Analytics is een taak service, dus u hoeft geen tijd te best Eden aan het beheer van clusters en u hoeft zich geen zorgen te maken over downtime met een SLA van 99,9% op het taak niveau. Facturering wordt ook uitgevoerd op het niveau van de opstart kosten laag (één streaming-eenheid), maar schaalbaar (Maxi maal 192 streaming-eenheden). Het is veel rendabeler om enkele Stream Analytics taken uit te voeren dan het is om een cluster uit te voeren en te onderhouden.

Azure Stream Analytics heeft een veelzijdige out-of-the-box-ervaring. U kunt onmiddellijk profiteren van de volgende functies zonder aanvullende instellingen:

* Ingebouwde tijdelijke Opera Tors, zoals statistische functies in [Vensters](stream-analytics-window-functions.md), tijdelijke samen voegingen en tijdelijke analytische functionaliteiten.
* Systeem eigen Azure- [invoer](stream-analytics-add-inputs.md) en- [uitvoer](stream-analytics-define-outputs.md) adapters
* Ondersteuning voor het langzaam wijzigen van [referentie gegevens](stream-analytics-use-reference-data.md) (ook wel opzoek tabellen genoemd), inclusief het samen voegen met georuimtelijke referentie gegevens voor geoomheining.
* Geïntegreerde oplossingen, zoals [afwijkings detectie](stream-analytics-machine-learning-anomaly-detection.md)
* Meerdere tijd Vensters in dezelfde query
* De mogelijkheid om meerdere tijdelijke Opera tors in wille keurige volg orde op te stellen.
* Onder 100-MS-end-to-end latentie van invoer bij Event Hubs, naar uitvoer overloop in Event Hubs, met inbegrip van de netwerk vertraging van en naar Event Hubs, tegen een duurzame hoge door Voer

## <a name="when-to-use-other-technologies"></a>Wanneer u andere technologieën wilt gebruiken

### <a name="you-need-to-input-from-or-output-to-kafka"></a>U moet een invoer uitvoeren vanuit of uitvoer naar Kafka

Azure Stream Analytics heeft geen Apache Kafka invoer-of uitvoer adapter. Als u gebeurtenissen hebt geaanvoer in of moet worden verzonden naar Kafka en u geen vereiste hebt om uw eigen Kafka-cluster uit te voeren, kunt u Stream Analytics blijven gebruiken door gebeurtenissen te verzenden naar Event Hubs met behulp van de Event Hubs Kafka-API zonder de afzender van de gebeurtenis te wijzigen. Als u uw eigen Kafka-cluster moet uitvoeren, kunt u gebruikmaken van Spark Structured streaming, dat volledig wordt ondersteund op [Azure Databricks](../azure-databricks/index.yml), of Storm op [Azure HDInsight](../hdinsight/storm/apache-storm-overview.md).

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>U wilt Udf's-, Uda's-en aangepaste deserializers schrijven in een andere taal dan Java script ofC#

Azure Stream Analytics ondersteunt door de gebruiker gedefinieerde functies (UDF) of door de gebruiker gedefinieerde aggregaties (UDA) in Java script voor C# Cloud taken en voor IOT Edge taken. C#door de gebruiker gedefinieerde deserialers worden ook ondersteund. Als u een gedeserialiseerd, een UDF of een UDA in andere talen wilt implementeren, zoals Java of python, kunt u gebruikmaken van Spark Structured streaming. U kunt ook de Event Hubs **EventProcessorHost** uitvoeren op uw eigen virtuele machines om wille keurige streaming te verwerken.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Uw oplossing bevindt zich in een multi-Cloud-of on-premises omgeving

Azure Stream Analytics is de bedrijfs technologie van micro soft en is alleen beschikbaar in Azure. Als u wilt dat uw oplossing kan worden overdraagbaar in verschillende Clouds of on-premises, kunt u open-source technologieën zoals Spark Structured streaming of Storm overwegen.

## <a name="next-steps"></a>Volgende stappen

* [Een Stream Analytics taak maken met behulp van de Azure Portal](stream-analytics-quick-create-portal.md)
* [Een Stream Analytics-taak maken met behulp van Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Een Stream Analytics taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Een Stream Analytics taak maken met behulp van Visual Studio code](quick-create-vs-code.md)
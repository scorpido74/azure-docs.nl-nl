---
title: 'Concepten: gegevens stroom in IoT connector (preview)-functie van Azure API voor FHIR'
description: De gegevens stroom van een IoT-connector begrijpen. IoT-connector neemt gegevens op, normaliseert, groepeert, transformeert en bewaart IoMT-data naar Azure API voor FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: punagpal
ms.openlocfilehash: c2d150fcd35bc51478a1d7f4a0407abce1446c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097333"
---
# <a name="iot-connector-preview-data-flow"></a>Gegevens stroom van IoT-connector (preview-versie)

Dit artikel bevat een overzicht van de gegevens stroom in IoT connector. U leert over verschillende gegevens verwerkings fasen in IoT-connector waarmee apparaatgegevens worden getransformeerd in op FHIR gebaseerde [observatie](https://www.hl7.org/fhir/observation.html) resources.

![Gegevensstroom van IoT-connector](media/concepts-iot-data-flow/iot-connector-data-flow.png)

In het bovenstaande diagram ziet u verschillende gegevensstroom fasen binnen IoT-connector. 

## <a name="ingest"></a>Opnemen
Opname is de eerste fase waarin apparaatgegevens worden ontvangen in IoT-connector. Het opname-eind punt voor apparaatgegevens wordt gehost op een [Azure Event hub](https://docs.microsoft.com/azure/event-hubs/). Het Azure Event hub-platform ondersteunt hoge schaal en door Voer met de mogelijkheid om miljoenen berichten per seconde te ontvangen en verwerken. Daarnaast kan IoT-connector berichten asynchroon gebruiken, waardoor apparaten niet hoeven te worden gewacht wanneer de gegevens van het apparaat worden verwerkt.

> [!NOTE]
> JSON is op dit moment de enige indeling die wordt ondersteund voor apparaatgegevens.

## <a name="normalize"></a>Normaliseren
Normaliseren is de volgende fase waarin apparaatgegevens worden opgehaald uit de bovenstaande Azure Event hub en worden verwerkt met toewijzings sjablonen voor apparaten. Dit toewijzings proces resulteert in het transformeren van apparaatgegevens in een genormaliseerd schema. 

Het normalisatie proces vereenvoudigt gegevens verwerking in latere fasen, maar biedt ook de mogelijkheid om één invoer bericht te projecteren in meerdere genormaliseerde berichten. Een apparaat kan bijvoorbeeld meerdere vitale tekens voor de lichaams temperatuur, het Pulse-Rate, de bloed druk en het respiration-tempo per bericht verzenden. Dit invoer bericht zou vier afzonderlijke FHIR-resources maken. Elke resource zou een ander vitale teken vertegenwoordigen, waarbij het invoer bericht is geprojecteerd in vier verschillende genormaliseerde berichten.

## <a name="group"></a>Groep
Groep is de volgende fase waarin de genormaliseerde berichten die beschikbaar zijn in de vorige fase worden gegroepeerd met drie verschillende para meters: apparaat-id, metings type en tijds periode.

De groepering van de apparaat-id en het meet type maken gebruik van het meting type [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) . Dit type biedt een beknopte manier om een op tijd gebaseerde reeks metingen van een apparaat in FHIR weer te geven. En de tijd periode bepaalt de latentie waarmee waarnemingen die zijn gegenereerd door IoT-connector, worden geschreven naar Azure API voor FHIR.

> [!NOTE]
> De waarde van de tijds periode wordt standaard ingesteld op 15 minuten en kan niet worden geconfigureerd voor de preview-versie.

## <a name="transform"></a>Transformeren
In het transformatie stadium worden gegroepeerde genormaliseerde berichten verwerkt via FHIR-toewijzings sjablonen. Berichten die overeenkomen met een sjabloon type worden getransformeerd in FHIR-waarnemingen die zijn opgegeven via de toewijzing.

Op dit moment wordt de bron van het [apparaat](https://www.hl7.org/fhir/device.html) , samen met de bijbehorende [patiënt](https://www.hl7.org/fhir/patient.html) resource, ook opgehaald van de FHIR-server met behulp van de apparaat-id die aanwezig is in het bericht. Deze resources worden toegevoegd als een verwijzing naar de observatie resource die wordt gemaakt.

> [!NOTE]
> Alle identiteits kijken ups worden opgeslagen in de cache zodra deze zijn opgelost om de belasting op de FHIR-server te verlagen. Als u van plan bent apparaten met meerdere patiënten te gebruiken, wordt u geadviseerd om een bron van een virtueel apparaat te maken die specifiek is voor de patiënt en de id van het virtuele apparaat in de bericht lading te verzenden. Het virtuele apparaat kan worden gekoppeld aan de werkelijke apparaatnaam als bovenliggend item.

Als er geen apparaatnaam voor een bepaalde apparaat-id in de FHIR-server aanwezig is, is de uitkomst afhankelijk van de waarde die is `Resolution Type` ingesteld op het moment dat deze wordt gemaakt. Als deze instelling `Lookup` is ingesteld op, wordt het specifieke bericht genegeerd en blijven andere inkomende berichten door de pijp lijn worden verwerkt. Als deze is ingesteld op `Create` , maakt IOT connector een bare bones-apparaat en patiënten-resources op de FHIR-server.  

## <a name="persist"></a>Behouden
Zodra de FHIR-resource wordt gegenereerd in het transformatie stadium, wordt de resource opgeslagen in de Azure-API voor FHIR. Als de FHIR-resource nieuw is, wordt deze op de server gemaakt. Als de FHIR-resource al bestaat, wordt deze bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

Klik hieronder op de volgende stap voor meer informatie over het maken van FHIR.

>[!div class="nextstepaction"]
>[Toewijzingssjablonen voor IoT-connector](iot-mapping-templates.md)


FHIR is het gedeponeerde handelsmerk van HL7 en wordt gebruikt met de toestemming van HL7.

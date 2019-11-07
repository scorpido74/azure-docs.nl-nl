---
title: Webhook-activiteit in Azure Data Factory
description: De webhook-activiteit blijft de uitvoering van de pijp lijn voortzetten totdat de gekoppelde gegevensset wordt gevalideerd met bepaalde criteria die de gebruiker opgeeft.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 3734472d9026d4e355c08b36d5ba58974288daac
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678230"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-activiteit in Azure Data Factory
U kunt een web Hook-activiteit gebruiken om de uitvoering van pijp lijnen via uw aangepaste code te beheren. Met de webhook-activiteit kunnen klanten een eind punt aanroepen en een call back-URL door geven. De pijplijn uitvoering wacht totdat de retour aanroep is aangeroepen voordat u verdergaat met de volgende activiteit.

## <a name="syntax"></a>Syntaxis

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>Type-eigenschappen



Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
naam | Naam van de activiteit van de webhook | Tekenreeks | Ja |
type | Moet worden ingesteld op **webhook**. | Tekenreeks | Ja |
method | Rest API-methode voor het doel eindpunt. | tekenreeksexpressie. Ondersteunde typen: ' POST ' | Ja |
url | Doel eindpunt en-pad | Teken reeks (of expressie met het resultType van de teken reeks). | Ja |
koppen | Kopteksten die naar de aanvraag worden verzonden. U kunt bijvoorbeeld de taal en het type van een aanvraag instellen: "headers": {"Accept-Language": "nl-nl", "content-type": "application/json"}. | Teken reeks (of expressie met het resultType van de teken reeks) | Ja, content-type-header is vereist. "headers": {"content-type": "application/json"} |
organen | Vertegenwoordigt de nettolading die naar het eind punt wordt verzonden. | De hoofd tekst die is teruggestuurd naar de URI van de aanroepen, moet een geldige JSON zijn. Zie het schema van de sectie aanvraag lading in schema voor de lading van de [aanvraag](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) . | Ja |
verificatie | De verificatie methode die wordt gebruikt voor het aanroepen van het eind punt. De ondersteunde typen zijn "Basic" of "ClientCertificate". Zie de sectie [verificatie](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) voor meer informatie. Als verificatie niet is vereist, sluit u deze eigenschap. | Teken reeks (of expressie met het resultType van de teken reeks) | Nee |
timeout | Hoe lang de activiteit wacht totdat de &#39;callBackUri&#39; wordt aangeroepen. Hoe lang de activiteit wacht voordat de callBackUri wordt aangeroepen. De standaard waarde is 10mins ("00:10:00"). De notatie is time span, d.w.z. d. uu: mm: SS | Tekenreeks | Nee |

## <a name="additional-notes"></a>Aanvullende opmerkingen

Azure Data Factory wordt een extra eigenschap "callBackUri" in de hoofd tekst door gegeven aan het URL-eind punt en verwacht dat deze URI wordt aangeroepen voordat de opgegeven time-outwaarde. Als de URI niet wordt aangeroepen, mislukt de activiteit met de status out.

De Web Hook-activiteit zelf mislukt alleen wanneer het aanroepen van het aangepaste eind punt mislukt. Elk fout bericht kan worden toegevoegd aan de hoofd tekst van de call back en wordt gebruikt in een volgende activiteit.

## <a name="next-steps"></a>Volgende stappen
Zie andere controle stroom activiteiten die door Data Factory worden ondersteund:

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Execute Pipeline Activity](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Get Metadata Activity](control-flow-get-metadata-activity.md)
- [Lookup Activity](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
- [Until Activity](control-flow-until-activity.md)

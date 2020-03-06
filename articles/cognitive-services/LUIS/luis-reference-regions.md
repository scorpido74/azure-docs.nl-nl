---
title: "&-Eind punten voor het publiceren van regio's-LUIS"
description: De regio die in de Azure Portal is opgegeven, is hetzelfde als waar u de LUIS-app publiceert en er wordt een eind punt-URL gegenereerd voor deze regio.
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 097f3078b54b014dc646d144f3532e20236a9e4f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393793"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Ontwerpen en publiceren van regio's en de bijbehorende sleutels

Drie ontwerp regio's worden ondersteund door de bijbehorende LUIS-portals. Als u wilt een LUIS-app publiceren in meer dan één regio, moet u ten minste één sleutel per regio.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS-ontwerp regio's
Er zijn drie LUIS voor het ontwerpen van portals, gebaseerd op de regio. U moet maken en publiceren in dezelfde regio.

|LUIS|Regio ontwerpen|Naam van Azure-regio|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|V.S.<br>niet-Europa<br>niet-Australië| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Australië| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Europa|`westeurope`|

Voor het ontwerpen van regio's zijn er [gepaarde failover-regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publicatie regio's en Azure-resources
De app wordt gepubliceerd naar alle regio's die zijn gekoppeld aan de LUIS-resources in de portal LUIS toegevoegd. Als u bijvoorbeeld een app die is gemaakt op [www.Luis.ai][www.luis.ai], een Luis-of cognitieve service resource in **westus** maakt en [deze toevoegt aan de app als resource](luis-how-to-azure-subscription.md), wordt de app gepubliceerd in die regio.

## <a name="public-apps"></a>Openbare apps
Een openbare app is gepubliceerd in alle regio's, zodat een gebruiker met een sleutel op basis van een regio LUIS resource toegang heeft tot de app in welke regio gekoppeld aan de resource-sleutel is.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publicatie regio's zijn gekoppeld aan ontwerp regio's

De ontwerphandleiding regio app kan alleen worden gepubliceerd naar een bijbehorende regio publiceren. Als uw app zich momenteel in de verkeerde regio voor schrijven bevindt, de app exporteren en importeren in de juiste authoring regio voor uw regio publiceren.

LUIS-apps die zijn gemaakt op https://www.luis.ai kunnen worden gepubliceerd voor alle eind punten, met uitzonde ring van de [Europese](#publishing-to-europe) en [Australische](#publishing-to-australia) regio's.

## <a name="publishing-to-europe"></a>Publiceren naar Europa

Als u wilt publiceren naar de Europese regio's, maakt u LUIS-apps alleen op https://eu.luis.ai. Als u probeert te publiceren ergens anders met behulp van een sleutel in de regio Europa, weergegeven LUIS een waarschuwingsbericht. Gebruik in plaats daarvan https://eu.luis.ai. LUIS-apps die zijn gemaakt op [https://eu.luis.ai][eu.luis.ai] , worden niet automatisch gemigreerd naar andere regio's. Exporteren en vervolgens de LUIS-app importeren als u wilt deze migreren.

## <a name="europe-publishing-regions"></a>Europa-publicatie regio's

 Globale regio | Ontwerpen van API-regio & website ontwerpen| Publiceren en uitvoeren van query's regio<br>`API region name`   |  Eindpunt-URL-indeling   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Frankrijk - centraal<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa - noord<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa -west<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Verenigd Koninkrijk Zuid<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publiceren naar Australië

Als u wilt publiceren naar de Australische regio's, maakt u LUIS-apps alleen op https://au.luis.ai. Als u probeert te publiceren ergens anders met behulp van een sleutel in de Australische regio, weergegeven LUIS een waarschuwingsbericht. Gebruik in plaats daarvan https://au.luis.ai. LUIS-apps die zijn gemaakt op [https://au.luis.ai][au.luis.ai] , worden niet automatisch gemigreerd naar andere regio's. Exporteren en vervolgens de LUIS-app importeren als u wilt deze migreren.

## <a name="australia-publishing-regions"></a>Australië-publicatie regio's

 Globale regio | Ontwerpen van API-regio & website ontwerpen| Publiceren en uitvoeren van query's regio<br>`API region name`   |  Eindpunt-URL-indeling   |
|-----|------|------|------|
| [Australië](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australië - oost<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publiceren naar andere regio's

Als u wilt publiceren naar de andere regio's, maakt u LUIS-apps alleen op [https://www.luis.ai](https://www.luis.ai) .

## <a name="other-publishing-regions"></a>Andere publicatie regio's

 Globale regio | Ontwerpen van API-regio & website ontwerpen| Publiceren en uitvoeren van query's regio<br>`API region name`   |  Eindpunt-URL-indeling   |
|-----|------|------|------|
| Africa | `westus`<br>[www.luis.ai][www.luis.ai]| Zuid-Afrika - noord<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| India - centraal<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Azië - oost<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Japan - oost<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Japan - west<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Korea - centraal<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Azië - zuidoost<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Canada - midden<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - centraal<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - oost<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - oost 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - noord-centraal<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - zuid-centraal<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - west-centraal<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - west<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - west 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Zuid-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brazilië - zuid<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Eindpunten

Meer informatie over de [ontwerp-en Voorspellings eindpunten](developer-reference-resource.md).

## <a name="failover-regions"></a>Failover-regio's

Elke regio heeft een secundaire regio waarvoor een failover moet worden uitgevoerd. In Europa wordt een failover uitgevoerd binnen Europa en Australië in Australië.

Voor het ontwerpen van regio's zijn er [gepaarde failover-regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslag informatie voor vooraf gemaakte entiteiten](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai

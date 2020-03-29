---
title: Regio's publiceren & eindpunten - LUIS
description: Het gebied dat is opgegeven in de Azure-portal is hetzelfde waar u de LUIS-app publiceert en een url voor eindpunt en voor dezelfde regio wordt gegenereerd.
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: a7709d15a51637772d7a4775cd71c307dc21c52b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292086"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Ontwerp- en publicatiegebieden en de bijbehorende sleutels

Drie ontwerpregio's worden ondersteund door bijbehorende LUIS-portalen. Als u een LUIS-app naar meer dan één regio wilt publiceren, hebt u ten minste één sleutel per regio nodig.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS-ontwerpgebieden
Er zijn drie LUIS authoring portals, gebaseerd op regio. U moet in één en dezelfde regio maken en publiceren.

|LUIS|Ontwerpgebied|Azure-regionaam|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|U.S.<br>niet Europa<br>niet Australië| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Australië| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Europa|`westeurope`|

Ontwerpgebieden hebben [fail-over-regio's gekoppeld](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publicatieregio's en Azure-bronnen
De app wordt gepubliceerd in alle regio's die zijn gekoppeld aan de LUIS-resources die zijn toegevoegd aan de LUIS-portal. Voor een app die op [www.luis.ai][www.luis.ai]is gemaakt , als u bijvoorbeeld een LUIS- of Cognitive Service-bron in **westus** maakt en [deze als bron aan de app toevoegt,](luis-how-to-azure-subscription.md)wordt de app in die regio gepubliceerd.

## <a name="public-apps"></a>Openbare apps
Een openbare app wordt in alle regio's gepubliceerd, zodat een gebruiker met een op een regio gebaseerde LUIS-bronsleutel toegang heeft tot de app in welke regio dan ook is gekoppeld aan zijn resourcesleutel.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publicatieregio's zijn gekoppeld aan ontwerpgebieden

De app voor het ontwerpgebied kan alleen worden gepubliceerd naar een overeenkomstigpublicatiegebied. Als uw app zich momenteel in het verkeerde ontwerpgebied bevindt, exporteert u de app en importeert u deze in het juiste ontwerpgebied voor uw publicatiegebied.

LUIS-apps https://www.luis.ai die zijn gemaakt, kunnen worden gepubliceerd op alle eindpunten, behalve de [Europese](#publishing-to-europe) en [Australische](#publishing-to-australia) regio's.

## <a name="publishing-to-europe"></a>Publiceren naar Europa

Als u wilt publiceren naar de https://eu.luis.ai Europese regio's, maakt u alleen LUIS-apps. Als u ergens anders probeert te publiceren met een sleutel in de regio Europa, geeft LUIS een waarschuwingsbericht weer. Gebruik https://eu.luis.aiin plaats daarvan . LUIS-apps [https://eu.luis.ai][eu.luis.ai] die zijn gemaakt, migreren niet automatisch naar andere regio's. Exporteer en importeer vervolgens de LUIS-app om deze te migreren.

## <a name="europe-publishing-regions"></a>Europa dat regio's publiceert

 Regio wereldwijd | Auteur API-gebied & auteurwebsite| & querygebied publiceren<br>`API region name`   |  URL-indeling eindpunt   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Frankrijk - centraal<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa - noord<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa -west<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Verenigd Koninkrijk Zuid<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publiceren naar Nederland

Als u wilt publiceren naar de https://au.luis.ai Australische regio's, maakt u alleen LUIS-apps. Als u ergens anders probeert te publiceren met een sleutel in de Australische regio, geeft LUIS een waarschuwingsbericht weer. Gebruik https://au.luis.aiin plaats daarvan . LUIS-apps [https://au.luis.ai][au.luis.ai] die zijn gemaakt, migreren niet automatisch naar andere regio's. Exporteer en importeer vervolgens de LUIS-app om deze te migreren.

## <a name="australia-publishing-regions"></a>Australië publishing regio's

 Regio wereldwijd | Auteur API-gebied & auteurwebsite| & querygebied publiceren<br>`API region name`   |  URL-indeling eindpunt   |
|-----|------|------|------|
| [Australië](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australië - oost<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Publiceren naar andere regio's

Als u wilt publiceren naar de [https://www.luis.ai](https://www.luis.ai) andere regio's, maakt u alleen LUIS-apps.

## <a name="other-publishing-regions"></a>Andere publicatieregio's

 Regio wereldwijd | Auteur API-gebied & auteurwebsite| & querygebied publiceren<br>`API region name`   |  URL-indeling eindpunt   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Zuid-Afrika Noord<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| India - centraal<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Azië - oost<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Japan - oost<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Japan - west<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Korea - centraal<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Azië - zuidoost<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Canada - midden<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - centraal<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - oost<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - oost 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - noord-centraal<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - zuid-centraal<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - west-centraal<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - west<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - west 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Zuid-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brazilië - zuid<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Eindpunten

Meer informatie over de [doel- en voorspellingseindpunten](developer-reference-resource.md).

## <a name="failover-regions"></a>Failover-regio's

Elke regio heeft een secundaire regio om niet over te gaan. Europa faalt binnen Europa en Australië faalt in Australië.

Ontwerpgebieden hebben [fail-over-regio's gekoppeld](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verwijzing naar vooraf gebouwde entiteiten](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai

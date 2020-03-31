---
title: Azure Functions Premium-abonnement
description: Details en configuratieopties (VNet, geen koude start, onbeperkte uitvoeringsduur) voor het Azure Functions Premium-abonnement.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276905"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions Premium-abonnement

Het Azure Functions Premium-abonnement (ook wel Elastic Premium-abonnement genoemd) is een hostingoptie voor functie-apps. Het Premium-abonnement biedt functies zoals VNet-connectiviteit, geen koude start en premium hardware.  Meerdere functie-apps kunnen worden geïmplementeerd in hetzelfde Premium-abonnement en met het abonnement u de grootte van rekeninstantie, de grootte van het basisplan en de maximale grootte van het abonnement configureren.  Voor een vergelijking van het Premium-abonnement en andere typen plannen en hosting, [raadpleegt u functieschaal- en hostingopties.](functions-scale.md)

## <a name="create-a-premium-plan"></a>Een Premium-abonnement maken

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

U ook een Premium-abonnement maken met [het AZ-functieapp-plan maken](/cli/azure/functionapp/plan#az-functionapp-plan-create) in de Azure CLI. In het volgende voorbeeld wordt een _Elastic Premium 1-laagplan_ gemaakt:

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

Vervang in dit `<RESOURCE_GROUP>` voorbeeld uw `<PLAN_NAME>` resourcegroep en een naam voor uw plan die uniek is in de resourcegroep. Geef een [ondersteunde `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). Als u een Premium-abonnement wilt `--is-linux` maken dat Linux ondersteunt, neemt u de optie op.

Met het gemaakte plan kun je [de AZ-functieapp maken](/cli/azure/functionapp#az-functionapp-create) gebruiken om je functie-app te maken. In de portal worden zowel het plan als de app tegelijkertijd gemaakt. Zie [Een functie-app maken in een Premium-abonnement](scripts/functions-cli-create-premium-plan.md)voor een voorbeeld van een compleet Azure CLI-script .

## <a name="features"></a>Functies

De volgende functies zijn beschikbaar voor functie-apps die zijn geïmplementeerd in een Premium-abonnement.

### <a name="pre-warmed-instances"></a>Vooraf verwarmde exemplaren

Als er vandaag geen gebeurtenissen en uitvoeringen plaatsvinden in het verbruiksplan, kan uw app worden geschaald naar nul exemplaren. Wanneer er nieuwe gebeurtenissen binnenkomen, moet een nieuw exemplaar worden gespecialiseerd met uw app die erop wordt uitgevoerd.  Het specialiseren van nieuwe exemplaren kan enige tijd in beslag nemen, afhankelijk van de app.  Deze extra latentie op het eerste gesprek wordt vaak app cold start genoemd.

In het Premium-abonnement u uw app vooraf laten opwarmen op een bepaald aantal exemplaren, tot uw minimale grootte van het abonnement.  Met voorverwarmde exemplaren u een app ook vooraf schalen vóór hoge belasting. Als de app wordt uitgeschaald, wordt deze eerst geschaald naar de vooraf verwarmde exemplaren. Extra exemplaren blijven bufferen en warmen onmiddellijk in voorbereiding op de volgende schaaloperatie. Door het hebben van een buffer van voorverwarmde instanties, u effectief koude start latencies voorkomen.  Vooraf verwarmde exemplaren is een functie van het Premium-abonnement en u moet ten minste één instantie actief en beschikbaar houden te allen tijde het plan actief is.

U het aantal vooraf opgewarmde exemplaren in de Azure-portal configureren door uw **functie-app**te selecteren, naar het tabblad **Platformonderdelen** te gaan en de opties **Voor uitschalen** te selecteren. In het bewerkingsvenster voor functie-apps zijn vooraf opgewarmde exemplaren specifiek voor die app, maar de minimale en maximale exemplaren zijn van toepassing op uw hele abonnement.

![Instellingen voor elastische schaal](./media/functions-premium-plan/scale-out.png)

U ook vooraf opgewarmde exemplaren voor een app configureren met de Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Privénetwerkconnectiviteit

Azure Functions die is geïmplementeerd in een Premium-abonnement maakt gebruik van [nieuwe VNet-integratie voor web-apps.](../app-service/web-sites-integrate-with-vnet.md)  Wanneer geconfigureerd, kan uw app communiceren met bronnen binnen uw VNet of beveiligd via service eindpunten.  IP-beperkingen zijn ook beschikbaar op de app om binnenkomend verkeer te beperken.

Wanneer u een subnet toewijst aan uw functie-app in een Premium-abonnement, hebt u een subnet nodig met voldoende IP-adressen voor elk mogelijk exemplaar. We hebben een IP-blok nodig met ten minste 100 beschikbare adressen.

Zie uw [functie-app integreren met een VNet](functions-create-vnet.md)voor meer informatie.

### <a name="rapid-elastic-scale"></a>Snelle elastische schaal

Extra rekeninstanties worden automatisch toegevoegd voor uw app met dezelfde snelle schalingslogica als het verbruiksplan.  Zie [Functieschaal en hosting voor](./functions-scale.md#how-the-consumption-and-premium-plans-work)meer informatie over hoe schalen werkt.

### <a name="longer-run-duration"></a>Langere looptijd

Azure-functies in een verbruiksplan zijn beperkt tot 10 minuten voor één uitvoering.  In het Premium-abonnement wordt de duur van de uitvoering standaard ingesteld op 30 minuten om op hol geslagen uitvoeringen te voorkomen. U echter [de configuratie host.json wijzigen](./functions-host-json.md#functiontimeout) om deze onbegrensd te maken voor Premium-apps (gegarandeerd 60 minuten).

## <a name="plan-and-sku-settings"></a>Instellingen voor plannen en SKU

Wanneer u het abonnement maakt, configureert u twee instellingen: het minimumaantal exemplaren (of de grootte van het abonnement) en de maximale burstlimiet.  Minimum exemplaren zijn gereserveerd en altijd actief.

> [!IMPORTANT]
> Er worden kosten in rekening gebracht voor elk exemplaar dat wordt toegewezen in het minimumaantal instance's, ongeacht of functies worden uitgevoerd of niet.

Als uw app exemplaren vereist die verder zijn dan uw abonnementsgrootte, kan deze blijven uitschalen totdat het aantal exemplaren de maximale burst-limiet bereikt.  U wordt alleen gefactureerd voor gevallen die uw abonnementsgrootte niet groter zijn wanneer ze aan u worden uitgevoerd en verhuurd.  We zullen ons uiterste best doen om uw app te schalen naar de gedefinieerde maximumlimiet, terwijl de minimale planexemplaren gegarandeerd zijn voor uw app.

U de grootte en maxima van het abonnement in de Azure-portal configureren door de **opties voor uitschalen** in het plan of een functie-app te selecteren die is geïmplementeerd in dat abonnement (onder **Platformfuncties).**

U ook de maximale burstlimiet van de Azure CLI verhogen:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Beschikbare instantie SKU's

Wanneer u uw abonnement maakt of schaalt, u kiezen uit drie instantiegroottes.  U wordt gefactureerd voor het totale aantal cores en geheugen verbruikt per seconde.  Uw app kan automatisch worden uitgeschaald naar meerdere exemplaren als dat nodig is.  

|SKU|Kernen|Geheugen|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

### <a name="memory-utilization-considerations"></a>Overwegingen voor geheugengebruik
Draaien op een machine met meer geheugen betekent niet altijd dat uw functie-app al het beschikbare geheugen zal gebruiken.

Een JavaScript-functie-app wordt bijvoorbeeld beperkt door de standaardgeheugenlimiet in Node.js. Als u deze limiet voor vast `languageWorkers:node:arguments` geheugen wilt `--max-old-space-size=<max memory in MB>`verhogen, voegt u de app-instelling toe met een waarde van .

## <a name="region-max-scale-out"></a>Regio Max uitschalen

Hieronder vindt u de momenteel ondersteunde maximale scale out-waarden voor één plan in elke regio en osconfiguratie. Open een ondersteuningsticket om een verhoging aan te vragen.

Bekijk hier de volledige regionale beschikbaarheid van functies: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Regio| Windows | Linux |
|--| -- | -- |
|Australië - centraal| 20 | Niet beschikbaar |
|Australië - centraal 2| 20 | Niet beschikbaar |
|Australië - oost| 100 | 20 |
|Australië - zuidoost | 100 | 20 |
|Brazilië - zuid| 60 | 20 |
|Canada - midden| 100 | 20 |
|VS - centraal| 100 | 20 |
|Azië - oost| 100 | 20 |
|VS - oost | 100 | 20 |
|VS - oost 2| 100 | 20 |
|Frankrijk - centraal| 100 | 20 |
|Duitsland West Centraal| 100 | Niet beschikbaar |
|Japan - oost| 100 | 20 |
|Japan - west| 100 | 20 |
|Korea - centraal| 100 | 20 |
|VS - noord-centraal| 100 | 20 |
|Europa - noord| 100 | 20 |
|Noorwegen-Oost| 20 | 20 |
|VS - zuid-centraal| 100 | 20 |
|India - zuid | 100 | Niet beschikbaar |
|Azië - zuidoost| 100 | 20 |
|Verenigd Koninkrijk Zuid| 100 | 20 |
|Verenigd Koninkrijk West| 100 | 20 |
|Europa -west| 100 | 20 |
|India - west| 100 | 20 |
|VS - west-centraal| 20 | 20 |
|VS - west| 100 | 20 |
|VS - west 2| 100 | 20 |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Inzicht in schaal- en hostingopties voor Azure-functies](functions-scale.md)

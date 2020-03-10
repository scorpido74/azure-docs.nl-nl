---
title: Azure Functions Premium-abonnement
description: Details en configuratie opties (VNet, geen koude start, onbeperkte uitvoerings duur) voor het Azure Functions Premium-abonnement.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: 1d9f148351e4ce12d6f6bcd699cdd74e94ba09ef
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356926"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions Premium-abonnement

Het Premium-abonnement van Azure Functions (ook wel elastisch Premium-abonnement genoemd) is een hosting optie voor functie-apps. Het Premium-abonnement biedt functies als VNet-connectiviteit, geen koud start en Premium-hardware.  Meerdere functie-apps kunnen worden geïmplementeerd in hetzelfde Premium-abonnement en met het plan kunt u de grootte van het reken exemplaar, de grootte van het basis plan en de maximale plan grootte configureren.  Zie [functie schaal en hosting opties](functions-scale.md)voor een vergelijking van het Premium-abonnement en andere typen plannen en hosting.

## <a name="create-a-premium-plan"></a>Een Premium-abonnement maken

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

U kunt ook een Premium-abonnement maken met behulp van [AZ functionapp plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) in de Azure cli. In het volgende voor beeld wordt een _elastisch Premium 1_ -laag abonnement gemaakt:

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

In dit voor beeld moet u `<RESOURCE_GROUP>` vervangen door de resource groep en `<PLAN_NAME>` met een naam voor uw abonnement dat uniek is in de resource groep. Geef een [ondersteund `<REGION>`](#regions)op. Als u een Premium-abonnement wilt maken dat Linux ondersteunt, neemt u de optie `--is-linux` op.

Met het plan dat u hebt gemaakt, kunt u [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) gebruiken om uw functie-app te maken. In de portal worden zowel het abonnement als de app tegelijkertijd gemaakt. Zie [een functie-app maken in een Premium-abonnement](scripts/functions-cli-create-premium-plan.md)voor een voor beeld van een volledig Azure CLI-script.

## <a name="features"></a>Functies

De volgende functies zijn beschikbaar voor het werken met apps die zijn geïmplementeerd in een Premium-abonnement.

### <a name="pre-warmed-instances"></a>Vooraf gewarmde instanties

Als er vandaag geen gebeurtenissen en uitvoeringen in het verbruiks abonnement worden uitgevoerd, kan uw app worden geschaald naar nul instanties. Wanneer er nieuwe gebeurtenissen binnenkomen in, moet er een nieuw exemplaar worden gespecialiseerd in de app die erop wordt uitgevoerd.  Het maken van nieuwe instanties kan enige tijd duren, afhankelijk van de app.  Deze extra latentie bij de eerste aanroep wordt vaak app koude start genoemd.

In het Premium-abonnement kunt u uw app vooraf laten opwarmen op een opgegeven aantal exemplaren, tot aan de minimale plan grootte.  Met vooraf gewarmde instanties kunt u een app vooraf schalen voor een hoge belasting. Wanneer de app wordt geschaald, wordt deze voor het eerst geschaald naar de vooraf gewarmde instanties. Aanvullende instanties blijven in de voor bereiding van de volgende schaal bewerking direct in de buffer worden geplaatst en warme. Door een buffer van vooraf gewarmde instanties te hebben, kunt u de latentie van koude start effectief vermijden.  Voor bereide instanties is een functie van het Premium-abonnement en u moet ten minste één instantie actief houden en op elk moment dat het abonnement actief is.

U kunt het aantal vooraf gewarmde instanties in de Azure Portal configureren door uw **functie-app**te selecteren, naar het tabblad **platform functies** te gaan en de opties voor **uitschalen** te selecteren. In het venster functie-app bewerken zijn vooraf gewarmde exemplaren specifiek voor die app, maar de minimum-en maximum exemplaren zijn van toepassing op uw hele abonnement.

![Instellingen voor Elastic Scale](./media/functions-premium-plan/scale-out.png)

U kunt ook vooraf gewarmde instanties configureren voor een app met de Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Connectiviteit van particuliere netwerken

Azure Functions geïmplementeerd in een Premium-abonnement maakt gebruik van de [nieuwe VNet-integratie voor web-apps](../app-service/web-sites-integrate-with-vnet.md).  Als uw app is geconfigureerd, kan deze communiceren met resources binnen uw VNet of worden beveiligd via service-eind punten.  Er zijn ook IP-beperkingen beschikbaar op de app om inkomend verkeer te beperken.

Wanneer u een subnet aan uw functie-app toewijst in een Premium-abonnement, hebt u een subnet met voldoende IP-adressen nodig voor elk mogelijk exemplaar. Er is een IP-blok met ten minste 100 beschik bare adressen vereist.

Zie [uw functie-app integreren met een VNet](functions-create-vnet.md)voor meer informatie.

### <a name="rapid-elastic-scale"></a>Snel elastisch schalen

Er worden automatisch extra reken instanties toegevoegd voor uw app met dezelfde snelle schaal logica als het verbruiks abonnement.  Zie [functie schaal en hosting](./functions-scale.md#how-the-consumption-and-premium-plans-work)voor meer informatie over de werking van schalen.

### <a name="longer-run-duration"></a>Langere duur van de uitvoering

Azure Functions in een verbruiks abonnement zijn beperkt tot 10 minuten voor één uitvoering.  In het Premium-abonnement wordt de uitvoerings duur standaard ingesteld op 30 minuten om overmatige uitvoeringen te voor komen. U kunt echter [de host. json-configuratie wijzigen](./functions-host-json.md#functiontimeout) om deze niet-gebonden te maken voor apps voor Premium-abonnementen (gegarandeerd 60 minuten).

## <a name="plan-and-sku-settings"></a>Plannings-en SKU-instellingen

Wanneer u het plan maakt, configureert u twee instellingen: het minimum aantal exemplaren (of de plan grootte) en de maximale burst-limiet.  Minimum exemplaren worden gereserveerd en worden altijd uitgevoerd.

> [!IMPORTANT]
> Er worden kosten in rekening gebracht voor elke instantie die wordt toegewezen in het minimum aantal exemplaren, ongeacht of de functies worden uitgevoerd.

Als uw app exemplaren vereist die groter zijn dan de grootte van uw abonnement, kan deze worden uitgeschaald totdat het aantal exemplaren de maximale burst-limiet bereikt.  Er worden alleen exemplaren van uw abonnement in rekening gebracht wanneer ze worden uitgevoerd en aan u worden gehuurd.  We maken het beste om uw app te schalen naar de gedefinieerde maximum limiet, terwijl de minimale plan exemplaren gegarandeerd zijn voor uw app.

U kunt de plan grootte en maximum waarden in de Azure Portal configureren door de opties voor **Uitschalen** te selecteren in het plan of een functie-app die is geïmplementeerd in dat plan (onder **platform functies**).

U kunt ook de maximale burst-limiet van de Azure CLI verhogen:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Beschik bare exemplaar-Sku's

Bij het maken of schalen van uw plan kunt u kiezen uit drie instantie grootten.  Er worden kosten in rekening gebracht voor het totale aantal kernen en het geheugen dat per seconde wordt verbruikt.  Uw app kan automatisch uitschalen naar meerdere exemplaren als dat nodig is.  

|SKU|Kerngeheugens|Geheugen|Opslag|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|14GB|250 GB|

### <a name="memory-utilization-considerations"></a>Overwegingen voor geheugen gebruik
Als u een computer met meer geheugen gebruikt, betekent dat niet altijd dat de functie-app alle beschik bare geheugen zal gebruiken.

Een Java script-functie-app is bijvoorbeeld beperkt door de standaard limiet voor geheugen in node. js. Als u deze limiet wilt verhogen, voegt u de app-instelling `languageWorkers:node:arguments` met de waarde `--max-old-space-size=<max memory in MB>`toe.

## <a name="regions"></a>Regio's

Hieronder ziet u de regio's die momenteel worden ondersteund voor elk besturings systeem.

|Regio| Windows | Linux |
|--| -- | -- |
|Australië - centraal| ✔<sup>1</sup> | |
|Australië - centraal 2| ✔<sup>1</sup> | |
|Australië - oost| ✔ | ✔<sup>1</sup> |
|Australië - zuidoost | ✔ | ✔<sup>1</sup> |
|Brazilië - zuid| ✔<sup>2</sup> | ✔<sup>1</sup> |
|Canada - midden| ✔ | ✔<sup>1</sup> |
|VS - centraal| ✔ | ✔<sup>1</sup> |
|Azië - oost| ✔ | ✔<sup>1</sup> |
|VS - oost | ✔ | ✔<sup>1</sup> |
|VS - oost 2| ✔ | ✔<sup>1</sup> |
|Frankrijk - centraal| ✔ | ✔<sup>1</sup> |
|Duitsland - west-centraal| ✔ | |
|Japan - oost| ✔ | ✔<sup>1</sup> |
|Japan - west| ✔ | ✔<sup>1</sup> |
|Korea - centraal| ✔ | ✔<sup>1</sup> |
|VS - noord-centraal| ✔ | ✔<sup>1</sup> |
|Europa - noord| ✔ | ✔<sup>1</sup> |
|Noor wegen-Oost| ✔<sup>1</sup> | ✔<sup>1</sup> |
|VS - zuid-centraal| ✔ | ✔<sup>1</sup> |
|India - zuid | ✔ | |
|Azië - zuidoost| ✔ | ✔<sup>1</sup> |
|Verenigd Koninkrijk Zuid| ✔ | ✔<sup>1</sup> |
|Verenigd Koninkrijk West| ✔ | ✔<sup>1</sup> |
|Europa -west| ✔ | ✔<sup>1</sup> |
|India - west| ✔ | ✔<sup>1</sup> |
|VS - west-centraal| ✔<sup>1</sup> | ✔<sup>1</sup> |
|VS - west| ✔ | ✔<sup>1</sup> |
|VS - west 2| ✔ | ✔<sup>1</sup> |

<sup>1</sup> Maxi maal aantal uitschalen is beperkt tot 20 exemplaren.  
<sup>2</sup> Maxi maal aantal uitschalen beperkt tot 60 exemplaren.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Opties voor het schalen en hosten van Azure Functions begrijpen](functions-scale.md)

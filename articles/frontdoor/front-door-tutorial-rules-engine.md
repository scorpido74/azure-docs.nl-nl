---
title: Azure Front Door
description: Dit artikel bevat een zelf studie over het configureren van de engine voor regels in zowel de Azure Portal als de CLI.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: a931a12889cec67baf6ef2db09091c8ec581ef08
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85321559"
---
# <a name="configure-your-rules-engine"></a>Uw regels-Engine configureren

Dit artikel bevat stappen voor het maken van een configuratie van een regel engine en uw eerste regel in Azure Portal en CLI. 

## <a name="configure-rules-engine-in-azure-portal"></a>De engine voor regels configureren in Azure Portal
1. Voordat u een configuratie van een regel Engine maakt, [maakt u een voor deur](quickstart-create-front-door.md).

2. Ga in de voor deur van uw resource naar **instellingen** en selecteer **regel engine configuratie**. Klik op **toevoegen**, geef uw configuratie een naam en begin met het maken van de configuratie van de eerste regel engine.

    ![Engine voor regels zoeken](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

3. Klik op **regel toevoegen** om uw eerste regel te maken. Vervolgens kunt u de regel definiëren door te klikken op **voor waarde toevoegen** of **actie toevoegen** .
    
    > [!NOTE]
    >- Als u een voor waarde of actie van regel wilt verwijderen, kunt u aan de rechter kant van de specifieke voor waarde of actie de prullen mand gebruiken.
    > - Als u een regel wilt maken die van toepassing is op alle binnenkomend verkeer, geeft u geen voor waarden op.
    > - Als u wilt stoppen met het evalueren van regels zodra de eerste match-voor waarde is voldaan, raadpleegt u **stoppen met evalueren van resterende regel**. Als dit selectie vakje is ingeschakeld en aan alle match-voor waarden van een bepaalde regel wordt voldaan, worden de resterende regels in de configuratie niet uitgevoerd.  

    ![Engine voor regels zoeken](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

4. Bepaal de prioriteit van de regels binnen uw configuratie met behulp van de knoppen omhoog, omlaag en naar boven verplaatsen. De prioriteit is in oplopende volg orde, wat betekent dat de regel die het eerst wordt vermeld, de belangrijkste regel is.

5. Wanneer u een of meer regels hebt gemaakt, drukt u op **Opslaan**. Met deze actie wordt de configuratie van de engine voor regels gemaakt.

6. Wanneer u een of meer configuraties hebt gemaakt, koppelt u een configuratie van een regel Engine aan een route regel. Hoewel één configuratie kan worden toegepast op veel route regels, mag een route regel slechts één engine configuratie voor regels bevatten. Als u de koppeling wilt maken, gaat **Front Door designer**u naar de  >  **routerings regels**van de front deur Designer. Selecteer de route regel waarvan u de configuratie van de engine voor regels wilt toevoegen, gaat u naar **router Details**  >  **regel engine configuratie**en selecteert u de configuratie die u wilt koppelen.

    ![Engine voor regels zoeken](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Regels-Engine configureren in azure CLI

1. Als u dat nog niet hebt gedaan, installeert u [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Voeg de extensie ' front-deur ' toe:-AZ extension add--name front-deur. Meld u vervolgens aan en schakel over naar uw abonnement AZ account set--Subscription <name_or_Id>.

2. Begin met het maken van een regel Engine: in dit voor beeld ziet u een regels met één actie op basis van een koptekst en één match-voor waarde. 

```azurecli-interactive
az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
```

3. Alle regels weer geven. 

```azurecli-interactive
az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
```

4. Voeg een actie voor het overschrijven van een doorstuur route toe. 

```azurecli-interactive
az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
```

5. Alle acties in een regel weer geven. 

```azurecli-interactive
az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
```

6. Een engine configuratie voor regels koppelen aan een routerings regel.  

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
```

7. Regels-engine ontkoppelen. 

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
```

Voor meer informatie kunt u [hier](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)een volledige lijst met engine-opdrachten voor de afd-regel vinden.   

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de engine voor de [afd-regels](front-door-rules-engine.md). 
- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
- Meer informatie vindt u in de [Naslag Gids](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)voor de engine van de afd-regels. 
- Raadpleeg de [Power shell-Naslag Gids](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0)voor afd-regels voor meer informatie. 

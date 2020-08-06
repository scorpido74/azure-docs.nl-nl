---
title: 'Zelfstudie: Regelengine configureren - Azure Front Door'
description: Dit artikel bestaat uit een zelfstudie voor het configureren van de regelengine met zowel de Azure-portal als de CLI.
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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ab9bc72aa30ddb16d0719827b6d7baeab86c057
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87491303"
---
# <a name="configure-your-rules-engine"></a>Regelengine configureren

Dit artikel bevat stappen voor het maken van een configuratie van de regelengine en uw eerste regel in de Azure-portal en CLI. 

## <a name="configure-rules-engine-in-azure-portal"></a>Regelengine configureren in de Azure-portal
1. Voordat u de regelengine gaat configureren, moet u [een front door maken](quickstart-create-front-door.md).

2. Ga in de resource van de front door naar **Instellingen** en selecteer **Configuratie van regelengine**. Klik op **Toevoegen**, geef een naam op voor de configuratie en begin met het maken van uw eerste configuratie van de regelengine.

    ![Locatie van regelengine](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

3. Klik op **Regel toevoegen** om uw eerste regel te maken. Klik vervolgens op **Voorwaarde toevoegen** of **Actie toevoegen** om de regel te definiëren.
    
    > [!NOTE]
    >- Als u een voorwaarde of actie wilt verwijderen uit een regel, gebruikt u het pictogram Prullenbak rechts van de voorwaarde of actie die u wilt verwijderen.
    > - Als u een regel wilt maken die van toepassing is op alle binnenkomend verkeer, geeft u geen voorwaarden op.
    > - Als u wilt stoppen met het evalueren van regels zodra aan de eerste voorwaarde is voldaan, selecteert u **Stoppen met evalueren van resterende regels**. Als deze optie is ingeschakeld en aan alle voorwaarden van een bepaalde regel wordt voldaan, worden de resterende regels in de configuratie niet uitgevoerd.  

    ![Locatie van regelengine](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

4. Bepaal de prioriteit van de regels binnen uw configuratie met behulp van de knoppen Omhoog, Omlaag en Bovenaan. De prioriteit werkt in oplopende volgorde, wat betekent dat de regel die het eerst wordt vermeld, de belangrijkste regel is.

5. Als u een of meer regels hebt gemaakt, selecteert u **Opslaan**. De configuratie van de regelengine wordt nu gemaakt.

6. Wanneer u een of meer configuraties hebt gemaakt, koppelt u een configuratie van de regelengine aan een routeregel. Hoewel één configuratie kan worden toegepast op verschillende routeregels, mag een routeregel slechts één configuratie van de regelengine bevatten. Als u de koppeling wilt maken, gaat u naar de **ontwerpfunctie van Front Door** > **Routeregels**. Selecteer de routeregel waaraan u de configuratie van de regelengine wilt toevoegen, ga naar **Routedetails** > **Configuratie van regelengine**en selecteer de configuratie die u wilt koppelen.

    ![Locatie van regelengine](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Regelengine configureren in de Azure CLI

1. Installeer [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) als u dat nog niet hebt gedaan. Voeg de extensie 'front-door' toe: -az extension add --name front-door. Meld u vervolgens aan en schakel over naar uw abonnement met az account set --subscription <naam_of_id>.

2. Begin met het maken van een regelengine. In dit voorbeeld maakt u één regel met één actie op basis van de header en één match-voorwaarde. 

```azurecli-interactive
az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
```

3. Geef alle regels weer. 

```azurecli-interactive
az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
```

4. Voeg een actie toe voor het overschrijven van een doorstuurroute. 

```azurecli-interactive
az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
```

5. Geef alle acties in een regel weer. 

```azurecli-interactive
az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
```

6. Koppel een configuratie van de regelengine aan een routeregel.  

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
```

7. Koppel de regelengine los. 

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
```

Voor meer informatie vindt u [hier](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest) een volledige lijst met opdrachten voor de AFD-regelengine.   

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [AFD-regelengine](front-door-rules-engine.md). 
- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
- Meer informatie vindt u in de [CLI-referentie](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest) van de AFD-regelengine. 
- Meer informatie vindt u in de [PowerShell-referentie](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0) van de AFD-regelengine. 

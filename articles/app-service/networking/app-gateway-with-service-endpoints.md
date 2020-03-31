---
title: Application Gateway-integratie met serviceeindpunten - Azure App Service | Microsoft Documenten
description: Beschrijft hoe Application Gateway integreert met Azure App Service beveiligd met serviceeindpunten.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 5e32baa10e98f0f57a861f8cebfb7506ad615631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980060"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Application Gateway-integratie met serviceeindpunten
Er zijn drie varianten van App Service die een iets andere configuratie van de integratie met Azure Application Gateway vereisen. De variaties omvatten reguliere App Service - ook wel bekend als multi-tenant, Internal Load Balancer (ILB) App Service Environment (ASE) en Externe ASE. In dit artikel wordt uitgelegd hoe u het configureren met App Service (multi-tenant) en overwegingen over ILB en Externe ASE bespreken.

## <a name="integration-with-app-service-multi-tenant"></a>Integratie met App Service (multi-tenant)
App Service (multi-tenant) heeft een openbaar internet geconfronteerd eindpunt. Met [serviceeindpunten](../../virtual-network/virtual-network-service-endpoints-overview.md) u alleen verkeer van een specifiek subnet binnen een Azure Virtual Network toestaan en al het andere blokkeren. In het volgende scenario gebruiken we deze functionaliteit om ervoor te zorgen dat een app-service-instantie alleen verkeer kan ontvangen van een specifieke instantie van application gateway.

![Application Gateway-integratie met App Service](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

Er zijn twee delen aan deze configuratie naast het creëren van de App Service en de Application Gateway. Het eerste deel is het inschakelen van serviceeindpunten in het subnet van het virtuele netwerk waar de Application Gateway wordt geïmplementeerd. Serviceeindpunten zorgen ervoor dat al het netwerkverkeer dat het subnet naar de App-service verlaat, wordt getagd met de specifieke subnet-id. Het tweede deel is het instellen van een toegangsbeperking van de specifieke web-app om ervoor te zorgen dat alleen verkeer gelabeld met deze specifieke subnet-ID is toegestaan. U het configureren met behulp van verschillende tools, afhankelijk van de voorkeur.

## <a name="using-azure-portal"></a>Azure Portal gebruiken
Met Azure-portal volgt u vier stappen om de installatie in te richten en te configureren. Als u bestaande bronnen hebt, u de eerste stappen overslaan.
1. Een app-service maken met een van de Quickstarts in de documentatie van de appservice, bijvoorbeeld [.Net Core Quickstart](../../app-service/app-service-web-get-started-dotnet.md)
2. Maak een toepassingsgateway met de [portal Quickstart,](../../application-gateway/quick-create-portal.md)maar sla de sectie Backend-doelen toevoegen over.
3. Configureer [App Service als backend in Application Gateway,](../../application-gateway/configure-web-app-portal.md)maar sla de sectie Toegang beperken over.
4. Maak ten slotte de [toegangsbeperking met serviceeindpunten](../../app-service/app-service-ip-restrictions.md#service-endpoints).

U nu toegang krijgen tot de App-service via Application Gateway, maar als u rechtstreeks toegang probeert te krijgen tot de App-service, ontvangt u een 403 HTTP-fout die aangeeft dat de website is gestopt.

![Application Gateway-integratie met App Service](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager-sjabloon gebruiken
In [de implementatiesjabloon Resourcemanager][template-app-gateway-app-service-complete] wordt een compleet scenario gemaakt. Het scenario bestaat uit een App Service-exemplaar dat is vergrendeld met serviceeindpunten en toegangsbeperking om alleen verkeer van Application Gateway te ontvangen. De sjabloon bevat veel Smart Defaults en unieke postfixes toegevoegd aan de resource namen voor het eenvoudig te zijn. Als u ze wilt overschrijven, moet u de repo klonen of de sjabloon downloaden en bewerken. 

Als u de sjabloon wilt toepassen, u de knop Implementeren naar Azure gebruiken in de beschrijving van de sjabloon of u de juiste PowerShell/CLI gebruiken.

## <a name="using-azure-command-line-interface"></a>Azure Command Line Interface gebruiken
In het [Azure CLI-voorbeeld](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) wordt een App-service ingericht die is vergrendeld met serviceeindpunten en toegangsbeperking om alleen verkeer van Application Gateway te ontvangen. Als u alleen verkeer naar een bestaande App-service hoeft te isoleren van een bestaande Application Gateway, volstaat de volgende opdracht.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

In de standaardconfiguratie zorgt de opdracht ervoor dat zowel de instelling van de serviceeindpuntconfiguratie in het subnet als de toegangsbeperking in de App-service worden ingesteld.

## <a name="considerations-for-ilb-ase"></a>Overwegingen voor ILB ASE
ILB ASE is niet blootgesteld aan het internet en het verkeer tussen de instantie en een Application Gateway is daarom al geïsoleerd naar het virtuele netwerk. De volgende [handleiding configureert](../environment/integrate-with-application-gateway.md) een ILB ASE en integreert deze met een Application Gateway met Azure-portal. 

Als u ervoor wilt zorgen dat alleen verkeer van het subnet Application Gateway de ASE bereikt, u een NSG (Network security group) configureren die van invloed zijn op alle web-apps in de ASE. Voor de NSG u het subnet IP-bereik en eventueel de poorten (80/443) opgeven. Zorg ervoor dat u de [vereiste NSG-regels](../environment/network-info.md#network-security-groups) niet overschrijft zodat ASE correct functioneert.

Als u verkeer naar een afzonderlijke web-app wilt isoleren, moet u ip-gebaseerde toegangsbeperkingen gebruiken, omdat serviceeindpunten niet werken voor ASE. Het IP-adres moet het privé-IP-adres van de instantie Application Gateway zijn.

## <a name="considerations-for-external-ase"></a>Overwegingen voor externe ASE
Externe ASE heeft een openbare geconfronteerd load balancer zoals multi-tenant App Service. Serviceeindpunten werken niet voor ASE en daarom moet u ip-gebaseerde toegangsbeperkingen gebruiken met behulp van het openbare IP-ip van de instantie Application Gateway. Als u een externe ASE wilt maken met de Azure-portal, u deze [Quickstart volgen](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Azure Resource Manager-sjabloon voor voltooid scenario"

## <a name="considerations-for-kuduscm-site"></a>Overwegingen voor kudu/scm site
De scm site, ook bekend als kudu, is een admin site, die bestaat voor elke web-app. Het is niet mogelijk om de proxy van de scm-site om te keren en u wilt deze waarschijnlijk ook vergrendelen naar afzonderlijke IP-adressen of een specifiek subnet.

Als u dezelfde toegangsbeperkingen wilt gebruiken als de hoofdsite, u de instellingen overnemen met de volgende opdracht.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Als u individuele toegangsbeperkingen voor de scm-site wilt instellen, u toegangsbeperkingen toevoegen met behulp van de --scm-sitevlag zoals hieronder weergegeven.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Volgende stappen
Zie [Documentatie app-serviceomgeving](https://docs.microsoft.com/azure/app-service/environment)voor meer informatie over de app-serviceomgeving.

Om uw web-app verder te beveiligen, vindt u informatie over Web Application Firewall op Application Gateway in de [azure webapplicationfirewall-documentatie.](../../web-application-firewall/ag/ag-overview.md)
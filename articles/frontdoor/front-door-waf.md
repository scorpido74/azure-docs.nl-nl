---
title: Schaal en bescherm een webtoepassing snel met Azure Front Door en Azure Web Application Firewall (WAF) | Microsoft Docs
description: In deze zelfstudie wordt uitgelegd hoe u Web Application Firewall gebruikt met uw Azure Front Door Service
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 1958481193b66c8cec2cb6a1ac6648a6900d70ac
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531199"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Zelfstudie: Schaal en bescherm een webtoepassing snel met Azure Front Door en Azure Web Application Firewall (WAF)

Veel webtoepassingen hebben in de afgelopen weken een snelle toename van verkeer ervaren door COVID-19. Bovendien zien deze webtoepassingen ook een toename in schadelijk verkeer, waaronder in Denial of Service-aanvallen. Een efficiënte manieren om aan deze beide behoeften te voldoen, uitschalen voor verkeerstoename en bescherming tegen aanvallen, is om Azure Front Door in te stellen met Azure WAF als versnellings-, caching- en beveiligingslaag voor uw webtoepassing. In dit artikel wordt uitgelegd hoe u snel Azure Front Door instelt met Azure WAF voor elke webtoepassing die binnen of buiten Azure wordt uitgevoerd. 

In deze zelfstudie gebruiken we Azure CLI om de WAF in te stellen, maar al deze stappen worden ook volledig ondersteund in Azure Portal, Azure PowerShell, Azure ARM en Azure REST API's. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een Front Door maken.
> - Een Azure WAF-beleid maken.
> - Regelsets instellen voor WAF-beleid.
> - WAF-beleid koppelen aan Front Door
> - Aangepast domein configureren

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

De instructies in dit blog maken gebruik van de Azure Command Line Interface (CLI). Raadpleeg deze handleiding [om aan de slag te gaan met Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Tip: een eenvoudige en snelle manier om aan de slag te gaan met Azure CLI is met [Bash in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Zorg ervoor dat de Front Door-extensie is toegevoegd aan uw Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

Opmerking: Raadpleeg [Azure CLI-verwijzing voor Front Door](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest) voor meer informatie over de hieronder vermelde opdrachten.

## <a name="create-an-azure-front-door-afd-resource"></a>Een Azure Front Door-resource (AFD) maken

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-address**: Het back-endadres is de Fully Qualified Domain Name (FQDN) van de toepassing die u wilt beschermen. Bijvoorbeeld myapplication.contoso.com

**--accepted-protocols**: De geaccepteerde protocollen specificeren welke protocollen AFD moet ondersteunen voor uw webtoepassing. Een voorbeeld zou zijn --accepted-protocols Http Https.

**--name**: Geef een naam op voor uw AFD-resource

**--resource-group**: De resourcegroep waarin u deze AFD-resource wilt plaatsen.  Raadpleeg resourcegroepen beheren in Azure voor meer informatie over resourcegroepen

Zoek in het antwoord van de succesvol uitgevoerde opdracht naar de sleutel "hostName" en schrijf deze waarde op voor een latere stap. De hostName is de DNS-naam van de AFD-resource die u hebt gemaakt

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Een Azure WAF-profiel maken voor gebruik met Azure Front Door-resources

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--name Geef een naam op voor uw Azure WAF-beleid

--resource-group De resourcegroep waarin u deze WAF-resource wilt plaatsen. 

De bovenstaande CLI-code maakt een WAF-beleid dat is ingeschakeld en zich in de preventiemodus bevindt. 

Opmerking: u wilt mogelijk ook de WAF in de detectiemodus maken en bekijken hoe die schadelijke verzoeken detecteert en registreert (en niet blokkeert) voordat u besluit om de beschermingsmodus aan te zetten.

Zoek in het antwoord van de succesvol uitgevoerde opdracht naar de sleutel "ID" en schrijf deze waarde op voor een latere stap. Het ID-veld zou de volgende indeling moeten hebben

/abonnementen/**abonnement-id**/resourcegroepen/**resourcegroepnaam**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF-beleidsnaam**

## <a name="add-managed-rulesets-to-this-waf-policy"></a>Beheerde regelsets toevoegen aan dit WAF-beleid

In een WAF-beleid kunt u beheerde regelsets toevoegen die een set regels zijn die gebouwd en beheerd worden door Microsoft en externe bescherming bieden tegen hele klassen aan bedreigingen. In dit voorbeeld voegen we regelsets toe: (1) Standaardregelset die bescherming biedt tegen algemene webbedreigingen en (2) Bot-beschermingsregelset, die u beschermt tegen schadelijke bots

(1) De standaardregelset toevoegen

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) De botbeheerdersregelset toevoegen

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--policy-name De naam die u aan uw Azure WAF-resource hebt gegeven

--resource-group De resourcegroep waarin u deze WAF-resource hebt geplaatst.

## <a name="associate-the-waf-policy-with-the-afd-resource"></a>Het WAF-beleid koppelen aan de AFD-resource

In deze stap koppelen we het WAF-beleid dat we hebben gemaakt met de AFD-resource die zich voorin uw webtoepassing bevindt.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--name De naam die u hebt opgegeven voor uw AFD-resource

--resource-group De resourcegroep waarin u de Azure Front Door-resource hebt geplaatst.

--set Dit is waar u het kenmerk WebApplicationFirewallPolicyLink bijwerkt voor de frontendEndpoint dat is gekoppeld aan uw AFD-resource met het nieuw gemaakte WAF-beleid. De id van het WAF-beleid vindt u in de reactie die u in de hierboven genoemde stap 2 hebt ontvangen

Opmerking: het bovenstaande voorbeeld is voor het geval waar u geen aangepast domein gebruikt, indien u dat wel doet

Als u geen aangepast domein gebruikt voor toegang tot uw webtoepassingen, kunt u stap 5 overslaan. In dat geval geeft u bij uw eindgebruikers de hostName op die u in stap 1 hebt gekregen om naar uw webtoepassing te navigeren

## <a name="configure-custom-domain-for-your-web-application"></a>Aangepast domein configureren voor uw webtoepassing

De aangepast domeinnaam van uw webtoepassing (die klanten gebruiken om naar uw webtoepassing te verwijzen, bijvoorbeeld www.contose.com) richtte zich in eerste instantie op de plek waar die werd uitgevoerd voordat AFD werd geïntroduceerd. Na deze architectuurwijziging waarbij AFD+WAF werd toegevoegd aan de toepassing, moet de DNS-invoer die bij die aangepaste domeinnaam hoort nu verwijzen naar deze AFD-resource. Dit doet u door deze invoer in uw DNS-server opnieuw toe te wijzen aan de AFD-hostnaam die u hebt genoteerd in stap 1.

Specifieke stappen om uw DNS-records bij te werken hangen af van uw DNS-serviceprovider, maar als u Azure DNS gebruikt om uw DNS-naam te hosten, kunt u de documentatie raadplegen voor [stappen om een DNS-record bij te werken](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) en verwijzen naar de AFD-hostName. 

Een belangrijke opmerking hier is dat als uw gebruikers naar uw website moeten navigeren met de zone-apex, bijvoorbeeld www.contoso.com, u Azure DNS moet gebruiken en de [recordtype ALIAS](https://docs.microsoft.com/azure/dns/dns-alias) uw DNS-naam moet laten hosten. 

Bovendien moet u uw AFD-configuratie bijwerken naar [dit aangepast domein toevoegen](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain), zodat AFD deze toewijzing begrijpt.

Ten slotte moet u de [certificaten voor uw aangepaste domein in AFD hebben ingesteld](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https) als u een aangepast domein gebruikt om uw webtoepassing te bereiken en u het HTTPS-protocol wilt inschakelen. 

## <a name="lock-down-your-web-application"></a>Uw webtoepassing vergrendelen

Een optionele best practice is om ervoor te zorgen dat alleen AFD-edges kunnen communiceren met uw webtoepassing. Met deze actie zorgt u ervoor dat niemand rond de AFD-bescherming kan en uw toepassingen rechtstreeks kan bereiken. U kunt deze vergrendeling bereik door [het gedeelte met veelgestelde vragen voor AFD](https://docs.microsoft.com/azure/frontdoor/front-door-faq) te bezoeken en te verwijzen naar de vraag met betrekking tot de vergrendeling van back-ends voor toegang door alleen AFD.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources in deze zelfstudie niet langer nodig hebt, gebruikt u de opdracht [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) om de resourcegroep, Front Door en het WAF-beleid te verwijderen.

```azurecli-interactive
  az group delete \
    --name <>
```
--name De naam van de resourcegroep voor alle resources die in deze zelfstudie zijn gebruikt.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen van uw Front Door, gaat u verder met de instructiegidsen.

> [!div class="nextstepaction"]
> [Algemene routeringsproblemen oplossen](front-door-troubleshoot-routing.md)

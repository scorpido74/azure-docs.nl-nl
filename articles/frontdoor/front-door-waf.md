---
title: Snel schalen en beveiligen van een webtoepassing met behulp van Azure front deur en Azure Web Application firewall (WAF) | Microsoft Docs
description: Dit artikel helpt u inzicht in het gebruik van Web Application Firewall met uw gereserveerde front-deur service
services: frontdoor
documentationcenter: ''
author: tremansdoerfer
ms.service: frontdoor
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: rimansdo
ms.openlocfilehash: 6f91a98372aa85a52a6013a121235ca354004a84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743538"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Snel schalen en beveiligen van een webtoepassing met behulp van Azure front deur en Azure Web Application firewall (WAF)

Veel webtoepassingen hebben een snelle toename van het verkeer in recente weken met betrekking tot COVID-19. Daarnaast wordt in deze webtoepassingen ook een piek in schadelijk verkeer met inbegrip van denial of service-aanvallen geobserveerd. Een efficiënte manier voor het afhandelen van beide behoeften, uitschalen voor verkeer en bescherming tegen aanvallen, is het instellen van de voor deur van Azure met Azure WAF als een versnelling, caching en beveiligings laag voor uw webtoepassing. Dit artikel bevat richt lijnen voor het snel ophalen van deze Azure-voor deur met Azure WAF Setup voor alle webtoepassingen die in of buiten Azure worden uitgevoerd. 

We gaan Azure CLI gebruiken om de WAF in deze zelf studie in te stellen, maar al deze stappen worden ook volledig ondersteund in Azure Portal, Azure PowerShell, Azure ARM en Azure REST Api's. 

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

De instructies in deze blog gebruiken de Azure-opdracht regel interface (CLI). Bekijk deze hand leiding om aan de [slag te gaan met Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Tip: een eenvoudige & snelle manier om aan de slag te gaan met Azure CLI is met [bash in azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Zorg ervoor dat de front-deur extensie wordt toegevoegd aan uw Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

Opmerking: voor meer informatie over de hieronder vermelde opdrachten raadpleegt u de [Azure cli-referentie voor de voor deur](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>Stap 1: een resource voor een Azure-deur (AFD) maken


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--back-end-adres**: het back-end-adres is de FQDN-naam (Fully Qualified Domain Name) van de toepassing die u wilt beveiligen. Bijvoorbeeld myapplication.contoso.com

**--geaccepteerd-protocollen**: de geaccepteerde protocollen geeft aan welke protocollen door afd moeten worden ondersteund voor uw webtoepassing. Een voor beeld is geaccepteerd--protocollen HTTP HTTPS.

**--naam**: Geef een naam op voor de afd-resource

**--Resource-Group**: de resource groep waarin u deze afd-resource wilt plaatsen.  Ga voor meer informatie over resource groepen naar resource groepen beheren in azure

In het antwoord dat u ontvangt, zoekt u naar de sleutel ' hostName ' en noteert u de waarde ervan voor gebruik in een latere stap. De hostnaam is de DNS-naam van de AFD-resource die u hebt gemaakt

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Stap 2: een Azure WAF-profiel maken voor gebruik met Azure front-deur bronnen

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--naam Geef een naam op voor uw Azure WAF-beleid

--resource: groep de resource groep waar u deze WAF-resource in wilt plaatsen. 

Met de CLI-code hierboven wordt een WAF-beleid gemaakt dat is ingeschakeld en zich in de preventie modus bevindt. 

Opmerking: u kunt ook de WAF in de detectie modus maken en nagaan hoe er wordt gedetecteerd & schadelijke aanvragen te registreren (en niet worden geblokkeerd) voordat u besluit om de beveiligings modus te wijzigen.

In het antwoord dat u ontvangt, zoekt u naar de sleutel ' ID ' en noteert u de waarde ervan voor gebruik in een latere stap. Het ID-veld moet de indeling hebben

/Subscriptions/**-abonnements-id/ResourceGroups/-** naam van de**resource groep**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF-beleids naam**

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>Stap 3: beheerde Rules-regels toevoegen aan dit WAF-beleid

In een WAF-beleid kunt u beheerde Rules toevoegen die een set regels zijn die door micro soft zijn gebouwd en beheerd, en die de beveiliging van het vak voor de hele bedreigingen van dreigingen afgeeft. In dit voor beeld voegen we twee dergelijke regel-instellingen (1) toe die zijn beveiligd tegen veelvoorkomende webbedreigingen en (2) bot Protection ruleer, die bescherming biedt tegen schadelijke bots

(1) de standaardruleset toevoegen

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) de ruleset van bot Manager toevoegen

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--beleids naam de naam die u voor uw Azure WAF-resource hebt opgegeven

--resource: groep de resource groep waar u deze WAF-resource in hebt geplaatst.

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>Stap 4: het WAF-beleid koppelen aan de AFD-resource

In deze stap koppelen we het WAF-beleid dat we hebben gemaakt met de AFD-resource die zich voor de webtoepassing bevindt.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--Geef de naam op die u voor uw AFD-resource hebt opgegeven

--resource: groep de resource groep waar u de Azure front-deur hebt geplaatst in.

--Stel dit in wanneer u het kenmerk WebApplicationFirewallPolicyLink voor de frontendEndpoint die aan uw AFD-resource is gekoppeld, bijwerkt met het zojuist gemaakte WAF-beleid. De ID van het WAF-beleid kan worden gevonden in het antwoord dat u hebt ontvangen van stap #2 hierboven

Opmerking: het bovenstaande voor beeld is voor het geval waarin u geen aangepast domein gebruikt, als u

Als u geen aangepaste domeinen gebruikt om toegang te krijgen tot uw webtoepassingen, kunt u stap #5 overs Laan. In dat geval geeft u uw eind gebruikers de hostnaam die u hebt verkregen in stap #1 om naar uw webtoepassing te navigeren

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>Stap 5: aangepast domein voor uw webtoepassing configureren

In eerste instantie is de aangepaste domein naam van uw webtoepassing (die klanten gebruiken om te verwijzen naar uw toepassing, bijvoorbeeld www.contoso.com) gericht op de plaats waar u deze had uitgevoerd voordat AFD werd geïntroduceerd. Na deze wijziging van de architectuur AFD + WAF toevoegen aan de voor grond van de toepassing, moet de DNS-vermelding die overeenkomt met dat aangepaste domein nu verwijzen naar deze AFD-resource. U kunt dit doen door deze vermelding in de DNS-server te koppelen aan de AFD-hostnaam die u hebt genoteerd in stap #1.

Specifieke stappen voor het bijwerken van uw DNS-records zijn afhankelijk van uw DNS-service provider, maar als u Azure DNS gebruikt om uw DNS-naam te hosten, kunt u de documentatie voor stappen gebruiken om [een DNS-record](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) bij te werken en naar de afd-hostnaam te verwijzen. 

Een belang rijke ding hier is dat als u uw gebruikers nodig hebt om naar uw website te navigeren met behulp van de zone Apex, voor voor beeld, contoso.com, u Azure DNS en het [alias record type](https://docs.microsoft.com/azure/dns/dns-alias) moet gebruiken om uw DNS-naam te hosten. 

Daarnaast moet u ook uw AFD-configuratie bijwerken om [dit aangepaste domein toe te voegen](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) , zodat afd deze toewijzing begrijpt.

Ten slotte, als u een aangepast domein gebruikt om uw webtoepassing te bereiken en u het HTTPS-protocol wilt inschakelen, moet u de [certificaten voor uw aangepaste domein installatie in afd](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https)hebben. 

## <a name="step-6-lock-down-your-web-application"></a>Stap 6: de webtoepassing vergren delen

Een optionele best practice die u moet volgen, is om ervoor te zorgen dat alleen AFD-randen kunnen communiceren met uw webtoepassing. Met deze actie wordt ervoor gezorgd dat niemand de AFD-beveiliging kan omzeilen en rechtstreeks toegang heeft tot uw toepassingen. U kunt deze vergren deling volt ooien door de [sectie Veelgestelde vragen van afd](https://docs.microsoft.com/azure/frontdoor/front-door-faq) te bezoeken en te verwijzen naar de vraag over het vergren delen van back-ends voor toegang alleen door afd.

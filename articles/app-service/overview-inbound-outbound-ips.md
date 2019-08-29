---
title: Inkomende/uitgaande IP-adressen-Azure App Service | Microsoft Docs
description: Hierin wordt beschreven hoe binnenkomende en uitgaande IP-adressen worden gebruikt in App Service en hoe u er informatie over kunt vinden voor uw app.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2508090fa8831c8fefb0e710c28e512ec0c94c6e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074143"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Binnenkomende en uitgaande IP-adressen in Azure App Service

[Azure app service](overview.md) is een service met meerdere tenants, met uitzonde ring van [app service omgevingen](environment/intro.md). Apps die zich niet in een App Service omgeving bevinden (niet in de [geïsoleerde laag](https://azure.microsoft.com/pricing/details/app-service/)) delen netwerk infrastructuur met andere apps. Als gevolg hiervan kunnen de inkomende en uitgaande IP-adressen van een app afwijken en kunnen zelfs in bepaalde situaties worden gewijzigd. 

[App service omgevingen](environment/intro.md) gebruiken specifieke netwerk infrastructuren, zodat apps die in een app service omgeving worden uitgevoerd, statische, specifieke IP-adressen voor binnenkomende en uitgaande verbindingen krijgen.

## <a name="when-inbound-ip-changes"></a>Wanneer inkomende IP-wijzigingen

Ongeacht het aantal uitgeschaalde instanties heeft elke app één inkomend IP-adres. Het binnenkomende IP-adres kan veranderen wanneer u een van de volgende acties uitvoert:

- Verwijder een app en maak deze opnieuw in een andere resource groep.
- Verwijder de laatste app in een combi natie van resource groep _en_ regio en maak deze opnieuw.
- Een bestaande SSL-binding verwijderen, bijvoorbeeld tijdens het vernieuwen van het certificaat (Zie [certificaten vernieuwen](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="find-the-inbound-ip"></a>Het inkomende IP-adres zoeken

Voer de volgende opdracht uit in een lokale terminal:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Een statisch inkomend IP-adres ophalen

Soms wilt u mogelijk een toegewezen, statisch IP-adres voor uw app. Als u een statisch inkomend IP-adres wilt ophalen, moet u een [SSL-binding op basis van IP](app-service-web-tutorial-custom-ssl.md#secure-a-custom-domain)configureren. Als u niet echt SSL-functionaliteit nodig hebt om uw app te beveiligen, kunt u zelfs een zelfondertekend certificaat voor deze binding uploaden. In een SSL-binding op basis van IP is het certificaat gebonden aan het IP-adres zelf, dus App Service een statisch IP-adres in te stellen om het uit te voeren. 

## <a name="when-outbound-ips-change"></a>Wanneer uitgaande Ip's worden gewijzigd

Ongeacht het aantal uitgeschaalde instanties heeft elke app op elk gewenst moment een bepaald aantal uitgaande IP-adressen. Elke uitgaande verbinding van de App Service-app, zoals een back-enddatabase, gebruikt een van de uitgaande IP-adressen als het IP-bron adres. U kunt niet vooraf weten welk IP-adres een gegeven app-exemplaar gebruikt voor het maken van de uitgaande verbinding. Daarom moet uw back-end-service zijn firewall openen voor alle uitgaande IP-adressen van uw app.

De set met uitgaande IP-adressen voor uw app verandert wanneer u uw app schaalt tussen de lagere lagen (**Basic**, **Standard**en **Premium**) en de **Premium v2** -laag.

U kunt de set met alle mogelijke uitgaande IP-adressen die uw app kan gebruiken, ongeacht de prijs categorieën, door te zoeken naar de `possibleOutboundIPAddresses` eigenschap of in het veld **extra uitgaande IP-adressen** op de Blade **Eigenschappen** in de Azure Portal. Zie [uitgaande Ip's zoeken](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Uitgaande Ip's zoeken

Als u de uitgaande IP-adressen wilt vinden die momenteel worden gebruikt door uw app in de Azure Portal, klikt u op **Eigenschappen** in de linkernavigatiebalk van de app. Deze worden weer gegeven in het veld **uitgaande IP-adressen** .

U kunt dezelfde informatie vinden door de volgende opdracht uit te voeren in de [Cloud shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Klik op **Eigenschappen** in de linkernavigatiebalk van uw app om _alle_ mogelijke uitgaande IP-adressen voor uw app te vinden, ongeacht de prijs categorie. Deze worden weer gegeven in het veld **extra uitgaande IP-adressen** .

U kunt dezelfde informatie vinden door de volgende opdracht uit te voeren in de [Cloud shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beperken van inkomend verkeer op bron-IP-adressen.

> [!div class="nextstepaction"]
> [Statische IP-beperkingen](app-service-ip-restrictions.md)

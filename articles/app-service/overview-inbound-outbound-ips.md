---
title: Binnenkomende/uitgaande IP-adressen
description: Lees hoe inkomende en uitgaande IP-adressen worden gebruikt in Azure App Service, wanneer ze worden gewijzigd en hoe u de adressen voor uw app vinden.
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: aebce04fe2f1b055a4d498021dcd25144cd122a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279206"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Binnenkomende en uitgaande IP-adressen in Azure App Service

[Azure App Service](overview.md) is een multi-tenantservice, met uitzondering van [App-serviceomgevingen.](environment/intro.md) Apps die zich niet in een App-serviceomgeving bevinden (niet in de [geïsoleerde laag)](https://azure.microsoft.com/pricing/details/app-service/)delen netwerkinfrastructuur met andere apps. Als gevolg hiervan kunnen de inkomende en uitgaande IP-adressen van een app verschillend zijn en zelfs in bepaalde situaties veranderen. 

[App-serviceomgevingen](environment/intro.md) maken gebruik van speciale netwerkinfrastructuren, zodat apps die in een App Service-omgeving worden uitgevoerd, statische, specifieke IP-adressen krijgen voor zowel inkomende als uitgaande verbindingen.

## <a name="when-inbound-ip-changes"></a>Bij binnenkomende IP-wijzigingen

Ongeacht het aantal uitgeschaalde exemplaren heeft elke app één binnenkomend IP-adres. Het binnenkomende IP-adres kan veranderen wanneer u een van de volgende acties uitvoert:

- Verwijder een app en maak deze opnieuw in een andere brongroep.
- Verwijder de laatste app in een resourcegroep _en_ regiocombinatie en maak deze opnieuw.
- Een bestaande SSL-binding verwijderen, bijvoorbeeld tijdens certificaatverlenging (zie [Certificaat verlengen).](configure-ssl-certificate.md#renew-certificate)

## <a name="find-the-inbound-ip"></a>Het binnenkomende IP zoeken

Voer gewoon de volgende opdracht uit in een lokale terminal:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Een statisch binnenkomend IP-adres

Soms wilt u misschien een specifiek, statisch IP-adres voor uw app. Als u een statisch binnenkomend IP-adres wilt krijgen, moet u een [IP-gebaseerde SSL-binding](configure-ssl-bindings.md#secure-a-custom-domain)configureren. Als u eigenlijk geen SSL-functionaliteit nodig hebt om uw app te beveiligen, u zelfs een zelfondertekend certificaat uploaden voor deze binding. In een IP-gebaseerde SSL-binding is het certificaat gebonden aan het IP-adres zelf, dus App Service voorziet in een statisch IP-adres om dit mogelijk te maken. 

## <a name="when-outbound-ips-change"></a>Wanneer uitgaande IP's veranderen

Ongeacht het aantal uitgeschaalde exemplaren heeft elke app op een bepaald moment een vast aantal uitgaande IP-adressen. Elke uitgaande verbinding van de App Service-app, zoals een back-enddatabase, gebruikt een van de uitgaande IP-adressen als het IP-adres van de oorsprong. U niet van tevoren weten welk IP-adres een bepaalde app-instantie zal gebruiken om de uitgaande verbinding te maken, dus uw back-endservice moet de firewall openen voor alle uitgaande IP-adressen van uw app.

De set uitgaande IP-adressen voor uw app verandert wanneer u uw app schaalt tussen de lagere lagen **(Basic**, **Standard**en **Premium)** en de **Premium V2-laag.**

U de set van alle mogelijke uitgaande IP-adressen die uw app kan gebruiken, ongeacht de prijsniveaus, vinden door te zoeken naar de `possibleOutboundIpAddresses` eigenschap of in het veld Extra uitgaande **IP-adressen** in het eigenschappenblad in de Azure-portal. **Properties** Zie [Uitgaande IP's zoeken](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Outbound IP's zoeken

Als u de uitgaande IP-adressen wilt vinden die momenteel door uw app in de Azure-portal worden gebruikt, klikt u op **Eigenschappen** in de linkernavigatie van uw app. Ze worden weergegeven in het veld **Uitgaande IP-adressen.**

U dezelfde informatie vinden door de volgende opdracht uit te voeren in de [Cloud Shell.](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Als u _alle_ mogelijke uitgaande IP-adressen voor uw app wilt vinden, ongeacht de prijsniveaus, klikt u op **Eigenschappen** in de linkernavigatie van uw app. Ze worden weergegeven in het veld **Extra uitgaande IP-adressen.**

U dezelfde informatie vinden door de volgende opdracht uit te voeren in de [Cloud Shell.](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beperken van binnenkomend verkeer op bron-IP-adressen.

> [!div class="nextstepaction"]
> [Statische IP-beperkingen](app-service-ip-restrictions.md)

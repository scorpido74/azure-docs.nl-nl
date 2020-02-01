---
title: Safelist de Azure Portal Url's op uw firewall of proxy server
description: Deze Url's toevoegen aan de proxy server bypass om te communiceren met de Azure Portal en de bijbehorende services
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900653"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist de Azure Portal Url's op uw firewall of proxy server

U kunt on-premises beveiligings apparaten configureren om beveiligings beperkingen voor de Azure Portal Url's over te slaan. Deze configuratie kan de prestaties en connectiviteit tussen uw lokale of Wide Area netwerk en de Azure-Cloud verbeteren.

Netwerk beheerders implementeren vaak proxy servers, firewalls of andere apparaten. Deze apparaten helpen beveiligen en bepalen hoe gebruikers toegang krijgen tot internet. Regels die zijn ontworpen om gebruikers te beschermen, kunnen het legitieme bedrijfsgerelateerde Internet verkeer soms blok keren of vertragen. Dit verkeer bevat communicatie tussen u en Azure. Voor het optimaliseren van de connectiviteit tussen uw netwerk en de Azure Portal en de bijbehorende services, raden we u aan Azure Portal-Url's toe te voegen aan uw Safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure Portal Url's voor het overs laan van een proxy

De URL-eind punten naar Safelist voor de Azure Portal zijn specifiek voor de Azure-Cloud waar uw organisatie is geïmplementeerd. Selecteer uw Cloud om netwerk verkeer naar deze eind punten toe te staan om beperkingen over te slaan. Voeg vervolgens de lijst met Url's toe aan uw proxy server of firewall.

#### <a name="public-cloudtabpublic-cloud"></a>[Open bare Cloud](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloudtabus-government-cloud"></a>[Cloud van de Amerikaanse overheid](#tab/us-government-cloud)

```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloudtabchina-government-cloud"></a>[Overheids Cloud voor China](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Verkeer naar deze eind punten maakt gebruik van standaard TCP-poorten voor HTTP (80) en HTTPS (443).
>
>
## <a name="next-steps"></a>Volgende stappen

Moet u IP-adressen safelisten? Down load de lijst met IP-adresbereiken voor Microsoft Azure Data Center voor uw Cloud:

* [Wijd](https://www.microsoft.com/download/details.aspx?id=56519)
* [Amerikaanse overheid](https://www.microsoft.com/download/details.aspx?id=57063)
* [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064)
* [China](https://www.microsoft.com/download/details.aspx?id=57062)

Andere micro soft-Services gebruiken extra Url's en IP-adressen voor connectiviteit. Zie [uw netwerk instellen voor Office 365 om de](/office365/enterprise/set-up-network-for-office-365)netwerk verbinding voor Microsoft 365 services te optimaliseren.

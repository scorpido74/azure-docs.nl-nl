---
title: Lijst met veilige Azure-portal-URL's op uw firewall of proxyserver maken
description: Deze Url's toevoegen aan de proxy server bypass om te communiceren met de Azure Portal en de bijbehorende services
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 16acedc8fad97c1752d71c8643b1655015484e5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330856"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Lijst met veilige Azure-portal-URL's op uw firewall of proxyserver maken

U kunt on-premises beveiligings apparaten configureren om beveiligings beperkingen voor de Azure Portal Url's over te slaan. Deze configuratie kan de prestaties en connectiviteit tussen uw lokale of Wide Area netwerk en de Azure-Cloud verbeteren.

Netwerk beheerders implementeren vaak proxy servers, firewalls of andere apparaten. Deze apparaten helpen beveiligen en bepalen hoe gebruikers toegang krijgen tot internet. Regels die zijn ontworpen om gebruikers te beschermen, kunnen het legitieme bedrijfsgerelateerde Internet verkeer soms blok keren of vertragen. Dit verkeer bevat communicatie tussen u en Azure. Voor het optimaliseren van de connectiviteit tussen uw netwerk en de Azure Portal en de bijbehorende services, raden we u aan Azure Portal-Url's toe te voegen aan uw Safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure Portal Url's voor het overs laan van een proxy

De URL-eind punten naar Safelist voor de Azure Portal zijn specifiek voor de Azure-Cloud waar uw organisatie is geïmplementeerd. Selecteer uw Cloud om netwerk verkeer naar deze eind punten toe te staan om beperkingen over te slaan. Voeg vervolgens de lijst met Url's toe aan uw proxy server of firewall.

#### <a name="public-cloud"></a>[Open bare Cloud](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azurefd.net
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

#### <a name="us-government-cloud"></a>[Cloud van de Amerikaanse overheid](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Overheids Cloud voor China](#tab/china-government-cloud)

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

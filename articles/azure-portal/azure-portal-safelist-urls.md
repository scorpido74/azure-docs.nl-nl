---
title: De URL's van de Azure-portal op uw firewall of proxyserver safelist
description: Deze URL's toevoegen aan de bypass van proxyservers om te communiceren met de Azure-portal en de bijbehorende services
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900653"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>De URL's van de Azure-portal op uw firewall of proxyserver safelist

U on-premises beveiligingsapparaten configureren om beveiligingsbeperkingen voor de URL's van de Azure-portal te omzeilen. Deze configuratie kan de prestaties en connectiviteit tussen uw lokale of brede netwerk en de Azure-cloud verbeteren.

Netwerkbeheerders implementeren vaak proxyservers, firewalls of andere apparaten. Deze apparaten helpen bij het beveiligen en geven controle over hoe gebruikers toegang hebben tot het internet. Regels die zijn ontworpen om gebruikers te beschermen, kunnen soms legitiem zakelijk internetverkeer blokkeren of vertragen. Dit verkeer omvat communicatie tussen u en Azure. Om de connectiviteit tussen uw netwerk en de Azure-portal en de bijbehorende services te optimaliseren, raden we u aan Azure-portal-URL's toe te voegen aan uw safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL's van Azure-portal en proxy-bypass

De URL-eindpunten voor safelist voor de Azure-portal zijn specifiek voor de Azure-cloud waar uw organisatie is geïmplementeerd. Als u het netwerkverkeer naar deze eindpunten wilt toestaan beperkingen te omzeilen, selecteert u uw cloud. Voeg vervolgens de lijst met URL's toe aan uw proxyserver of firewall.

#### <a name="public-cloud"></a>[Openbare cloud](#tab/public-cloud)

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

#### <a name="us-government-cloud"></a>[Cloud van de Amerikaanse overheid](#tab/us-government-cloud)

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

#### <a name="china-government-cloud"></a>[China Government Cloud](#tab/china-government-cloud)

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
> Verkeer naar deze eindpunten maakt gebruik van standaard TCP-poorten voor HTTP (80) en HTTPS (443).
>
>
## <a name="next-steps"></a>Volgende stappen

Wilt u IP-adressen safelist? Download de lijst met IP-bereiken van Microsoft Azure-datacenter voor uw cloud:

* [Wereldwijd](https://www.microsoft.com/download/details.aspx?id=56519)
* [Amerikaanse regering](https://www.microsoft.com/download/details.aspx?id=57063)
* [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064)
* [China](https://www.microsoft.com/download/details.aspx?id=57062)

Andere Microsoft-services gebruiken extra URL's en IP-adressen voor connectiviteit. Zie [Uw netwerk instellen voor Office 365](/office365/enterprise/set-up-network-for-office-365)voor het optimaliseren van de netwerkconnectiviteit voor Microsoft 365-services.

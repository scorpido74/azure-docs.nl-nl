---
title: De URL's van de Azure-portal op uw firewall of proxyserver safelist
description: Deze URL's toevoegen aan de bypass van proxyservers om te communiceren met de Azure-portal en de bijbehorende services
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 399c64c88e78079432fcf7c09dafd199da83358b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255044"
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

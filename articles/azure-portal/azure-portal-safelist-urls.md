---
title: Safelist de Azure Portal Url's | Microsoft Docs
description: Deze Url's toevoegen aan de proxy server bypass om te communiceren met de Azure Portal en de bijbehorende services
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3f81d41bc6d8ce07ea4e7b11c7c48f9b68d70466
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310562"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist de Azure Portal Url's op uw firewall of proxy server

Voor goede prestaties en connectiviteit tussen uw lokale of Wide Area netwerk en de Azure-Cloud configureert u on-premises beveiligings apparaten om beveiligings beperkingen voor de Azure Portal Url's over te slaan. Netwerk beheerders implementeren vaak proxy servers, firewalls of andere apparaten om te helpen beveiligen en de controle te geven over de manier waarop gebruikers toegang krijgen tot internet. Regels die zijn ontworpen om gebruikers te beschermen, kunnen er echter toe leiden dat het zakelijke verkeer dat betrekking heeft op het Internet, inclusief communicatie tussen u en Azure, wordt geblokkeerd of vertraagd. Voor het optimaliseren van de connectiviteit tussen uw netwerk en de Azure Portal en de bijbehorende services, raden we u aan Azure Portal-Url's toe te voegen aan uw Safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure Portal Url's voor het overs laan van een proxy

De URL-eind punten naar Safelist voor de Azure Portal zijn specifiek voor de Azure-Cloud waar uw organisatie is geïmplementeerd. Selecteer uw Cloud en voeg vervolgens de lijst met Url's toe aan uw proxy server of firewall om netwerk verkeer naar deze eind punten toe te staan om beperkingen over te slaan.

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

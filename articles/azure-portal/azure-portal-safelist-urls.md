---
title: Safelist de Azure Portal Url's | Microsoft Docs
description: Deze Url's toevoegen aan de proxy server bypass om te communiceren met de Azure Portal en de bijbehorende services
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3747ca7504e1a8a6bbeb6237c1b3cb2e5e4afb5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667473"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist de Azure Portal Url's op uw firewall of proxy server

Voor goede prestaties en connectiviteit tussen uw lokale of Wide Area netwerk en de Azure-Cloud configureert u on-premises beveiligings apparaten om beveiligings beperkingen voor de Azure Portal Url's over te slaan. Netwerk beheerders implementeren vaak proxy servers, firewalls of andere apparaten om te helpen beveiligen en de controle te geven over de manier waarop gebruikers toegang krijgen tot internet. Regels die zijn ontworpen om gebruikers te beschermen, kunnen er echter toe leiden dat het zakelijke verkeer dat betrekking heeft op het Internet, inclusief communicatie tussen u en Azure, wordt geblokkeerd of vertraagd. Voor het optimaliseren van de connectiviteit tussen uw netwerk en de Azure Portal en de bijbehorende services, raden we u aan Azure Portal-Url's toe te voegen aan uw Safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure Portal Url's voor het overs laan van een proxy

Voeg de volgende lijst met Url's toe aan uw proxy server of firewall om netwerk verkeer naar deze eind punten toe te staan voor het overs laan van beperkingen:

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *.portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* *.windows.net
* *.wpc.azureedge.net

> [!NOTE]
> Verkeer naar deze eind punten maakt gebruik van standaard TCP-poorten voor HTTP (80) en HTTPS (443).
>
>
## <a name="next-steps"></a>Volgende stappen

* Moet u IP-adressen safelisten? Down load de lijst met [IP-adresbereiken van Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653).
* Andere micro soft-Services gebruiken extra Url's en IP-adressen voor connectiviteit. Zie [uw netwerk instellen voor Office 365 om de](/office365/enterprise/set-up-network-for-office-365)netwerk verbinding voor Microsoft 365 services te optimaliseren.

---
title: Proxyconnectors voor foutopsporingstoepassingen - Azure Active Directory | Microsoft Documenten
description: Foutopsporing problemen met Azure Active Directory (Azure AD) Application Proxy connectors.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72311845"
---
# <a name="debug-application-proxy-connector-issues"></a>Fouten opsporen in connector van toepassingsproxy 

Met dit artikel u problemen met Azure Active Directory-connectoren (Azure AD) application proxy oplossen. Als u de application proxy-service gebruikt voor externe toegang tot een on-premises webtoepassing, maar u problemen ondervindt bij het maken van verbinding met de toepassing, gebruikt u dit stroomdiagram om problemen met de connector te debuggen. 

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u de application proxy-connector hebt geïnstalleerd en een probleem hebt. Wanneer u problemen met toepassingsproxy oplost, raden we u aan te beginnen met deze stroom van probleemoplossing om te bepalen of toepassingsproxyconnectors correct zijn geconfigureerd. Als u nog steeds problemen ondervindt bij het maken van verbinding met de toepassing, volgt u de stroom voor het oplossen van problemen in problemen met de [toepassingsproblemen van de foutopsporingstoepassing.](application-proxy-debug-apps.md)  


Zie voor meer informatie over Application Proxy en het gebruik van de connectors:

- [Toegang op afstand tot on-premises toepassingen via Application Proxy](application-proxy.md)
- [Toepassingsproxyconnectors](application-proxy-connectors.md)
- [Een connector installeren en registreren](application-proxy-add-on-premises-application.md)
- [Problemen en foutmeldingen van toepassingsproxy oplossen](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Stroomdiagram voor verbindingsproblemen

Deze stroomdiagram leidt u door de stappen voor het opsporen van enkele van de meest voorkomende verbindingsproblemen. Zie de tabel na het stroomdiagram voor meer informatie over elke stap.

![Stroomdiagram met stappen voor het opsporen van een connector](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Actie | Beschrijving | 
|---------|---------|---------|
|1 | De aan de app toegewezen verbindingsgroep zoeken | U hebt waarschijnlijk een connector geïnstalleerd op meerdere servers, in welk geval de connectors moeten worden [toegewezen aan connectorgroepen.](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups) Zie Toepassingen publiceren op [afzonderlijke netwerken en locaties met behulp van verbindingsgroepen](application-proxy-connector-groups.md)voor meer informatie over verbindingsgroepen. |
|2 | De connector installeren en een groep toewijzen | Zie [Een connector installeren en registreren als](application-proxy-add-on-premises-application.md#install-and-register-a-connector)u geen connector hebt geïnstalleerd.<br></br> Zie Probleem met het installeren van de connector als u problemen ondervindt bij het installeren [van de connector.](application-proxy-connector-installation-problem.md)<br></br> Zie De connector toewijzen aan een [groep](application-proxy-connector-groups.md#create-connector-groups)als de connector niet aan een groep is toegewezen.<br></br>Zie [De toepassing toewijzen aan een verbindingsgroep](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)als de toepassing niet is toegewezen aan een verbindingsgroep.|
|3 | Een poorttest uitvoeren op de connectorserver | Voer op de connectorserver een poorttest uit met [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) of een ander poorttestprogramma om te controleren of poorten 443 en 80 zijn geopend.|
|4 | De domeinen en poorten configureren | [Zorg ervoor dat uw domeinen en poorten correct zijn geconfigureerd](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Om de connector goed te laten werken, zijn er bepaalde poorten die open moeten zijn en URL's waartoe uw server toegang moet hebben. |
|5 | Controleren of er een back-endproxy in gebruik is | Controleer of de connectors back-end proxyservers gebruiken of omzeilen. Zie Problemen [met de proxy van de connector oplossen voor](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)meer informatie en problemen met de serviceconnectiviteit . |
|6 | De connector en updater bijwerken om de back-endproxy te gebruiken | Als er een back-endproxy in gebruik is, wilt u ervoor zorgen dat de connector dezelfde proxy gebruikt. Zie [Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)voor meer informatie over het oplossen van problemen en het configureren van connectors voor het werken met proxyservers. |
|7 | De interne URL van de app laden op de connectorserver | Laad op de connectorserver de interne URL van de app. |
|8 | Interne netwerkconnectiviteit controleren | Er is een verbindingsprobleem in uw interne netwerk dat deze foutopsporingsstroom niet kan diagnosticeren. De toepassing moet intern toegankelijk zijn om de connectoren te laten werken. U logboeken voor connectorgebeurtenissen in- en weergeven zoals beschreven in [toepassingsproxyconnectors.](application-proxy-connectors.md#under-the-hood) |
|9 | Verleng de time-outwaarde op de back-end | Wijzig in de **aanvullende instellingen** voor uw toepassing de **time-outinstelling Backend-toepassing** in **Lang**. Zie [Een on-premises app toevoegen aan Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Als er problemen blijven bestaan, richt u specifieke stroomproblemen, controleert u de foutopsporingsstromen van apps en SSO | Gebruik de [probleemoplossingstoepassing Proxy-toepassingsproblemen](application-proxy-debug-apps.md) voor probleemproblemen. |

## <a name="next-steps"></a>Volgende stappen


* [Toepassingen publiceren op afzonderlijke netwerken en locaties met behulp van verbindingsgroepen](application-proxy-connector-groups.md)
* [Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)
* [Problemen met toepassingsproxy- en connectorfouten oplossen](application-proxy-troubleshoot.md)
* [De Azure AD Application Proxy Connector in stilte installeren](application-proxy-register-connector-powershell.md)

---
title: Proxytoepassingen voor foutopsporingstoepassingen voor toepassingen - Azure Active Directory | Microsoft Documenten
description: Foutopsporing problemen met Azure Active Directory (Azure AD) Application Proxy-toepassingen.
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
ms.openlocfilehash: 575891d99c077299f5e7abf008c1ebb2b158373f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382080"
---
# <a name="debug-application-proxy-application-issues"></a>Toepassingsproblemen met toepassingsproxy oplossen 

Met dit artikel u problemen met Azure Active Directory-toepassingsproxytoepassingen (Azure Active Directory) oplossen. Als u de application proxy-service gebruikt voor externe toegang tot een on-premises webtoepassing, maar u problemen ondervindt bij het maken van verbinding met de toepassing, gebruikt u dit stroomdiagram om toepassingsproblemen te debuggen. 

## <a name="before-you-begin"></a>Voordat u begint

Wanneer u problemen met toepassingsproxy oplost, raden we u aan met de connectors te beginnen. Volg eerst de probleemoplossingsstroom in problemen met de proxyconnector voor [foutopsporingstoepassingen](application-proxy-debug-connectors.md) om ervoor te zorgen dat toepassingsproxyconnectors correct zijn geconfigureerd. Als u nog steeds problemen ondervindt, gaat u terug naar dit artikel om problemen op te lossen.  

Zie voor meer informatie over Application Proxy:

- [Toegang op afstand tot on-premises toepassingen via Application Proxy](application-proxy.md)
- [Toepassingsproxyconnectors](application-proxy-connectors.md)
- [Een connector installeren en registreren](application-proxy-add-on-premises-application.md)
- [Problemen en foutmeldingen van toepassingsproxy oplossen](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Stroomdiagram voor toepassingsproblemen

Deze stroomdiagram leidt u door de stappen voor het debuggen van enkele van de meest voorkomende problemen met het verbinden met de toepassing. Zie de tabel na het stroomdiagram voor meer informatie over elke stap.

![Stroomdiagram met stappen voor het opsporen van een toepassing](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Actie | Beschrijving | 
|---------|---------|---------|
|1 | Een browser openen, de app openen en uw referenties invoeren | Probeer uw referenties te gebruiken om u aan te melden bij de app en controleer op gebruikersgerelateerde fouten, zoals [deze bedrijfsapp kan niet worden geopend.](application-proxy-sign-in-bad-gateway-timeout-error.md) |
|2 | Gebruikerstoewijzing naar de app verifiëren | Zorg ervoor dat uw gebruikersaccount toestemming heeft om de app vanuit het bedrijfsnetwerk te openen en test vervolgens het aanmelden bij de app door de stappen te volgen in [De toepassing testen.](application-proxy-add-on-premises-application.md#test-the-application) Als aanmeldingsproblemen blijven bestaan, raadpleegt u [Aanmeldingsfouten oplossen](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Een browser openen en proberen toegang te krijgen tot de app | Als er onmiddellijk een fout wordt weergegeven, controleert u of de toepassingsproxy correct is geconfigureerd. Zie [Problemen met toepassingsproxy oplossen en foutberichten voor](application-proxy-troubleshoot.md)meer informatie over specifieke foutberichten.  |
|4 | Controleer de aangepaste domeininstelling of los de fout op | Als de pagina helemaal niet wordt weergegeven, controleert u of uw aangepaste domein correct is geconfigureerd door [Werken met aangepaste domeinen](application-proxy-configure-custom-domain.md)te controleren.<br></br>Als de pagina niet wordt geladen en er een foutbericht wordt weergegeven, lost u de fout op door te verwijzen naar [problemen met toepassingsproxy en foutberichten](application-proxy-troubleshoot.md)oplossen. <br></br>Als het langer duurt dan 20 seconden voordat een foutbericht wordt weergegeven, kan er een verbindingsprobleem zijn. Ga naar het artikel voor het oplossen van problemen met de [proxyvan de bugtoepassingsproxy.](application-proxy-debug-connectors.md)  |
|5 | Als er problemen blijven bestaan, gaat u naar het debuggen van connectoren | Er kan een verbindingsprobleem zijn tussen de proxy en de connector of tussen de connector en de back-end. Ga naar het artikel voor het oplossen van problemen met de [proxyvan de bugtoepassingsproxy.](application-proxy-debug-connectors.md) |
|6 | Alle bronnen publiceren, hulpprogramma's voor browserontwikkelaars controleren en koppelingen herstellen | Zorg ervoor dat het publicatiepad alle benodigde afbeeldingen, scripts en stijlbladen voor uw toepassing bevat. Zie Een [on-premises app toevoegen aan Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)voor meer informatie. <br></br>Gebruik de hulpprogramma's voor ontwikkelaars van de browser (F12-hulpprogramma's in Internet Explorer of Microsoft Edge) en controleer of publicatieproblemen zoals beschreven in [de pagina Toepassing niet correct worden weergegeven.](application-proxy-page-appearance-broken-problem.md) <br></br>Bekijk de opties voor het oplossen van [verbroken](application-proxy-page-links-broken-problem.md)koppelingen in Koppelingen op de pagina werken niet . |
|7 | Controleren op netwerklatentie | Als de pagina langzaam wordt geladen, leest u meer over manieren om de netwerklatentie te minimaliseren in [Overwegingen voor het verminderen van latentie.](application-proxy-network-topology.md#considerations-for-reducing-latency) | 
|8 | Aanvullende help voor probleemoplossing bekijken | Als er problemen blijven bestaan, vindt u aanvullende probleemoplossingsartikelen in de [documentatie voor het oplossen van problemen met de toepassingsproxy.](application-proxy-troubleshoot.md) |

## <a name="next-steps"></a>Volgende stappen


* [Toepassingen publiceren op afzonderlijke netwerken en locaties met behulp van verbindingsgroepen](application-proxy-connector-groups.md)
* [Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)
* [Problemen met toepassingsproxy- en connectorfouten oplossen](application-proxy-troubleshoot.md)
* [De Azure AD Application Proxy Connector in stilte installeren](application-proxy-register-connector-powershell.md)

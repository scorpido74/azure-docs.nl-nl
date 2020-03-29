---
title: 'Azure AD-toepassingsproxy: releasedatumversie | Microsoft Documenten'
description: In dit artikel worden alle releases van Azure AD Application Proxy weergegeven en worden nieuwe functies en vaste problemen beschreven
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9ecd193282ed9b7333df44689530b4d057ad7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68693904"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD-toepassingsproxy: releasedatum versie
In dit artikel worden de versies en functies van Azure Active Directory (Azure AD) Application Proxy weergegeven die zijn uitgebracht. Het Azure AD-team werkt application proxy regelmatig bij met nieuwe functies en functionaliteit. Application Proxy connectors worden automatisch bijgewerkt wanneer een nieuwe versie wordt uitgebracht. 

We raden u aan ervoor te zorgen dat automatische updates zijn ingeschakeld voor uw connectors om ervoor te zorgen dat u over de nieuwste functies en bugfixes beschikt. Microsoft biedt directe ondersteuning voor de nieuwste connectorversie en één versie daarvoor.

Hier is een lijst met gerelateerde bronnen:

Resource |  Details
--------- | --------- |
Toepassingsproxy inschakelen | In deze [zelfstudie](application-proxy-add-on-premises-application.md)worden vereisten beschreven voor het inschakelen van Application Proxy en het installeren en registreren van een connector.
Informatie over Azure AD-toepassingsproxyconnectors | Meer informatie over [connectorbeheer](application-proxy-connectors.md) en de automatische [upgrade van](application-proxy-connectors.md#automatic-updates)connectoren.
Downloaden van Azure AD-toepassingsproxyconnector |  [Download de nieuwste connector.](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Status van de release

20 september 2018: Vrijgegeven voor download

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

- WebSocket-ondersteuning toegevoegd voor de QlikSense-toepassing. Zie deze [walkthrough](application-proxy-qlik.md)voor meer informatie over het integreren van QlikSense met Application Proxy. 
- Verbeterde de installatiewizard om het eenvoudiger te maken om een uitgaande proxy te configureren. 
- Stel TLS 1.2 in als het standaardprotocol voor connectors. 
- Een nieuwe licentieovereenkomst voor eindgebruikers (EULA) toegevoegd.  

### <a name="fixed-issues"></a>Problemen opgelost

- Fixed a bug that caused some memory leaks in the connector.
- De Azure Service Bus-versie bijgewerkt, die een bugoplossing bevat voor problemen met de time-out van de connector.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Status van de release

19 januari 2018: Vrijgegeven om te downloaden

### <a name="fixed-issues"></a>Problemen opgelost

- Ondersteuning toegevoegd voor aangepaste domeinen die domeinvertaling nodig hebben in de cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Status van de release 

25 mei 2017: Vrijgegeven om te downloaden 

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 

Verbeterde controle over de uitgaande verbindingslimieten van connectors. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Status van de release

15 april 2017: Vrijgegeven voor download

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

- Vereenvoudigde onboarding en beheer met minder vereiste poorten. Application Proxy vereist nu het openen van slechts twee standaard uitgaande poorten: 443 en 80. Application Proxy blijft alleen uitgaande verbindingen gebruiken, dus u hebt nog steeds geen componenten in een DMZ nodig. Zie voor meer informatie onze [configuratiedocumentatie.](application-proxy-add-on-premises-application.md)  
- Als u wordt ondersteund door uw externe proxy of firewall, u uw netwerk nu openen via DNS in plaats van ip-bereik. Application Proxy services vereisen alleen verbindingen met *.msappproxy.net en *.servicebus.windows.net.


## <a name="earlier-versions"></a>Eerdere versies

Als u een application proxy-connectorversie eerder dan 1.5.36.0 gebruikt, werkt u bij naar de nieuwste versie om ervoor te zorgen dat u over de nieuwste volledig ondersteunde functies beschikt.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [externe toegang tot on-premises toepassingen via Azure AD Application Proxy](application-proxy.md).
- Zie [Zelfstudie: Een on-premises toepassing toevoegen voor externe toegang via Application Proxy](application-proxy-add-on-premises-application.md)als u Application Proxy wilt gebruiken.

---
title: 'Azure AD Pass-through Authentication: Releasegeschiedenis versie | Microsoft Documenten'
description: In dit artikel worden alle releases van de Azure AD Pass-through Authentication-agent weergegeven
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 11/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c5d0efe8e662544dc69356c6b17dd7eca6f3a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786449"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD Pass-through Authentication-agent: releasegeschiedenis versie 
 
De agents die on-premises zijn geïnstalleerd en pass-through-verificatie inschakelen, worden regelmatig bijgewerkt om nieuwe mogelijkheden te bieden. In dit artikel worden de versies en functies weergegeven die worden toegevoegd wanneer nieuwe functionaliteit wordt geïntroduceerd. Doorgeefverificatiemedewerkers worden automatisch bijgewerkt wanneer een nieuwe versie wordt uitgebracht. 

Hier zijn gerelateerde onderwerpen: 

- [Aanmelden door gebruikers met Azure AD Pass-through-verificatie](how-to-connect-pta.md) 
- [Installatie van Azure AD Pass-through Authentication-agent](how-to-connect-pta-quick-start.md) 



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Status van de release 
1/22/2019: Vrij gegeven om te downloaden  
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 
- Ondersteuning toegevoegd voor betrouwbare kanalen van Service Bus om een extra laag verbindingstolerantie toe te voegen voor uitgaande verbindingen 
- TLS 1.2 afdwingen tijdens de registratie van agenten 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Status van de release 
4/10/2018: Vrijgegeven om te downloaden  
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 
- Ondersteuning voor websocketverbindingen 
- TLS 1.2 instellen als het standaardprotocol voor de agent 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Status van de release 
1/31/2018: Vrijgegeven om te downloaden  
### <a name="fixed-issues"></a>Problemen opgelost 

- Fixed a bug that caused some memory leaks in the agent. 
- De Azure Service Bus-versie bijgewerkt, die een bugoplossing bevat voor problemen met de time-out van de connector. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Status van de release 
11/26/2017: Vrijgegeven om te downloaden  
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 
- Ondersteuning toegevoegd voor websocket-gebaseerde verbindingen tussen de agent en Azure AD-services om de tolerantie voor de verbinding te verbeteren 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Status van de release 
11/25/2017: Vrijgegeven om te downloaden  
### <a name="fixed-issues"></a>Problemen opgelost 
- Fixed bugs related to the DNS cache for default proxy scenarios Fixed bugs related to the DNS cache for default proxy scenarios Fixed bugs related to the DNS cache for default proxy scenarios Fixed bugs 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Status van de release 
10/17/2017: Vrijgegeven om te downloaden  
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 
- Dns-cachefunctionaliteit toegevoegd voor uitgaande verbindingen om tolerantie toe te voegen voor DNS-fouten 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Status van de release 
08/31/2017: Vrijgegeven om te downloaden  
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 
- GA-versie van de Azure AD Pass-through-verificatieagent 

## <a name="next-steps"></a>Volgende stappen

- [Gebruikersaanmelding met Pass Through-verificatie in Azure Active Directory](how-to-connect-pta.md)
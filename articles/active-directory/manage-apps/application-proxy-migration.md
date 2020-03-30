---
title: Upgraden naar Azure AD-toepassingsproxy | Microsoft Documenten
description: Kies welke proxyoplossing het beste is als u een upgrade uitvoert vanuit Microsoft Forefront of Unified Access Gateway.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4790dc7ebeeee3407e89bcf38d7e3f25699ed328
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108415"
---
# <a name="compare-remote-access-solutions"></a>Oplossingen voor externe toegang vergelijken

Azure Active Directory Application Proxy is een van de twee oplossingen voor externe toegang die Microsoft aanbiedt. De andere is Web Application Proxy, de on-premises versie. Deze twee oplossingen vervangen eerdere producten die Microsoft aanbood: Microsoft Forefront Threat Management Gateway (TMG) en Unified Access Gateway (UAG). Gebruik dit artikel om te begrijpen hoe deze vier oplossingen zich tot elkaar verhouden. Voor degenen onder u die nog steeds gebruik maken van de afgeschafte TMG- of UAG-oplossingen, gebruikt u dit artikel om uw migratie naar een van de toepassingsproxy te plannen. 


## <a name="feature-comparison"></a>Vergelijking van functies

Gebruik deze tabel om te begrijpen hoe Threat Management Gateway (TMG), Unified Access Gateway (UAG), Web Application Proxy (WAP) en Azure AD Application Proxy (AP) zich tot elkaar verhouden.

| Functie | Tmg | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Verificatie via certificaat | Ja | Ja | - | - |
| Browser-apps selectief publiceren | Ja | Ja | Ja | Ja |
| Preauthenticatie en eenmalige aanmelding | Ja | Ja | Ja | Ja | 
| Laag 2/3 firewall | Ja | Ja | - | - |
| Proxy-mogelijkheden doorsturen | Ja | - | - | - |
| VPN-mogelijkheden | Ja | Ja | - | - |
| Ondersteuning voor rich-protocollen | - | Ja | Ja, als u http loopt | Ja, als u http of extern bureaublad-gateway loopt |
| Fungeert als ADFS-proxyserver | - | Ja | Ja | - |
| Eén portal voor toegang tot toepassingen | - | Ja | - | Ja |
| Vertaling van de koppeling van de antwoord-instantie | Ja | Ja | - | Ja | 
| Verificatie met kopteksten | - | Ja | - | Ja, met PingAccess | 
| Beveiliging op cloudschaal | - | - | - | Ja | 
| Voorwaardelijke toegang | - | Ja | - | Ja |
| Geen componenten in de gedemilitariseerde zone (DMZ) | - | - | - | Ja |
| Geen binnenkomende verbindingen | - | - | - | Ja |

Voor de meeste scenario's raden we Azure AD Application Proxy aan als de moderne oplossing. Webtoepassingsproxy heeft alleen de voorkeur in scenario's waarvoor een proxyserver voor AD FS vereist is en u geen aangepaste domeinen gebruiken in Azure Active Directory. 

Azure AD Application Proxy biedt unieke voordelen in vergelijking met vergelijkbare producten, waaronder:

- Azure AD uitbreiden naar on-premises resources
   - Beveiliging en beveiliging op cloudschaal
   - Functies zoals voorwaardelijke toegang en multi-factor authenticatie zijn eenvoudig in te schakelen
- Geen componenten in de gedemilitariseerde zone
- Geen inkomende verbindingen vereist
- Eén toegangspaneel waar uw gebruikers terecht kunnen voor al hun toepassingen, waaronder O365, Azure AD-geïntegreerde SaaS-apps en uw on-premises webapps. 


## <a name="next-steps"></a>Volgende stappen

- [Azure AD-toepassing gebruiken om veilige externe toegang tot on-premises toepassingen te bieden](application-proxy.md)

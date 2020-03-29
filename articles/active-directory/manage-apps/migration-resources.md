---
title: Bronnen voor het migreren van apps naar Azure Active Directory | Microsoft Documenten
description: Bronnen waarmee u toepassingstoegang en verificatie migreren naar Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: mimart
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: b30469858a5dd83f7f5f707f74466302b3000510
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968732"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Bronnen voor het migreren van toepassingen naar Azure Active Directory

Bronnen waarmee u toepassingstoegang en verificatie migreren naar Azure Active Directory (Azure AD). Neem deze kortehttps://aka.ms/AppsMigrationFeedback) enquête (om feedback te geven over het migreren van apps naar Azure AD (inclusief blokkers naar migratie, noodzaak voor tooling / richtlijnen of redenen om uw on-premises IDP te behouden). 

| Resource  | Beschrijving  |
|:-----------|:-------------|
|[Uw apps migreren naar Azure AD](https://aka.ms/migrateapps/whitepaper) | Deze whitepaper presenteert de voordelen van migratie en beschrijft hoe u migratie plannen in vier duidelijk geschetste fasen: ontdekking, classificatie, migratie en doorlopend beheer. U wordt begeleid door hoe u over het proces nadenken en uw project opsplitsen in gemakkelijk te consumeren stukken. In het hele document zijn links naar belangrijke bronnen die u onderweg zullen helpen. |
|[Oplossingshandleiding: apps migreren van Active Directory Federation Services (AD FS) naar Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Deze oplossingsgids leidt u door dezelfde vier fasen van het plannen en uitvoeren van een toepassingsmigratieproject dat op een hoger niveau in de migratiewhitepaper wordt beschreven. In deze handleiding leert u hoe u deze fasen toepassen op het specifieke doel om een toepassing te verplaatsen van Azure Directory Federated Services (AD FS) naar Azure AD.|
| [Gereedschap: Active Directory Federation Services Migration Readiness Script](https://aka.ms/migrateapps/adfstools) | Dit is een script dat u uitvoeren op uw on-premises Active Directory Federation Services (AD FS)-server om de gereedheid van apps voor migratie naar Azure AD te bepalen.|
| [Implementatieplan: migreren van AD FS naar wachtwoordhashsynchronisatie](https://aka.ms/ADFSTOPHSDPDownload) | Met wachtwoordhashsynchronisatie worden hashes van gebruikerswachtwoorden gesynchroniseerd van on-premises Active Directory naar Azure AD. Hierdoor kan Azure AD gebruikers verifiëren zonder interactie met de on-premises Active Directory.| 
| [Implementatieplan: migreren van AD FS naar pass-through-verificatie](https://aka.ms/ADFSTOPTADPDownload)|Azure AD-pass-through-verificatie helpt gebruikers zich aan te melden bij zowel on-premises als cloudtoepassingen met hetzelfde wachtwoord. Deze functie biedt uw gebruikers een betere ervaring omdat ze één wachtwoord minder hebben om te onthouden. Het vermindert ook de kosten van de IT-helpdesk omdat gebruikers minder snel vergeten hoe ze zich moeten aanmelden wanneer ze slechts één wachtwoord hoeven te onthouden. Als iemand zich aanmeldt bij Azure AD, worden met deze functie de wachtwoorden rechtstreeks gecontroleerd tegen de on-premises Active Directory.|
| [Implementatieplan: Eenmalig aanmelden inschakelen voor een SaaS-app met Azure AD](https://aka.ms/SSODPDownload) | Met eenmalige aanmelding (SSO) u toegang krijgen tot alle apps en bronnen die u nodig hebt om zaken te doen, terwijl u zich slechts één keer aanmeldt met één gebruikersaccount. Nadat een gebruiker zich bijvoorbeeld heeft aangemeld, kan de gebruiker van Microsoft Office naar SalesForce naar Box gaan zonder een tweede keer een wachtwoord te verifiëren (bijvoorbeeld een wachtwoord typen). 
| [Implementatieplan: apps uitbreiden naar Azure AD met toepassingsproxy](https://aka.ms/AppProxyDPDownload)| Het bieden van toegang van werknemerslaptops en andere apparaten tot on-premises toepassingen heeft traditioneel betrekking op virtuele privénetwerken (VPN's) of gedemilitariseerde zones (DMZ's). Dit is niet alleen ingewikkeld en moeilijk te beveiligen, maar het instellen en beheren ervan is duur. Azure AD Application Proxy maakt het eenvoudiger om toegang te krijgen tot on-premises toepassingen. |
| [Implementatieplannen](../fundamentals/active-directory-deployment-plans.md) | Vind meer implementatieplannen voor het implementeren van functies zoals multi-Factor authenticatie, Voorwaardelijke toegang, gebruikersinrichting, naadloze SSO, self-service wachtwoordreset en meer! |



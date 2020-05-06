---
title: Gebruikers naam opzoeken tijdens aanmelden Azure Active Directory | Microsoft Docs
description: Hoe scherm berichten de gebruikers naam opzoeken weer gegeven tijdens het aanmelden in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1f27c7b91a78da8944c23fd353d3b6791b3e015
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582549"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Home realm-detectie voor Azure Active Directory aanmeldings pagina's

We wijzigen ons aanmeldings gedrag van Azure Active Directory (Azure AD) om ruimte te maken voor nieuwe verificatie methoden en om de bruikbaarheid te verbeteren. Tijdens het aanmelden bepaalt Azure AD waar een gebruiker moet worden geverifieerd. Azure AD maakt intelligente beslissingen door het lezen van de organisatie-en gebruikers instellingen voor de gebruikers naam die is ingevoerd op de aanmeldings pagina. Dit is een stap naar een toekomstig wacht woord dat aanvullende referenties mogelijk maakt, zoals FIDO 2,0.

## <a name="home-realm-discovery-behavior"></a>Gedrag van detectie van thuis realm

In het verleden is de detectie van de Home-realm onderhevig aan het domein dat wordt meegeleverd bij het aanmelden of door een beleid voor de introductie van realm-detectie voor sommige oudere toepassingen. Zo kan een Azure Active Directory gebruiker in ons detectie gedrag de gebruikers naam van een fout typen, maar wel op het scherm van de referentie verzameling van het bedrijf arriveren. Dit gebeurt wanneer de gebruiker de domein naam ' contoso.com ' van de organisatie correct levert. Dit gedrag staat de granulariteit niet toe om de ervaringen voor een afzonderlijke gebruiker aan te passen.

Als u een breder scala aan referenties wilt ondersteunen en de bruikbaarheid wilt verg Roten, wordt het gedrag van de gebruikers naam voor het zoeken van Azure Active Directory tijdens het aanmeldings proces nu bijgewerkt. Het nieuwe gedrag maakt intelligente beslissingen door instellingen op organisatie niveau en gebruikers niveau te lezen op basis van de gebruikers naam die op de aanmeldings pagina is opgegeven. Om dit mogelijk te maken, controleert Azure Active Directory of de gebruikers naam die is ingevoerd op de aanmeldings pagina bestaat in het opgegeven domein of de gebruiker wordt omgeleid om zijn of haar referenties op te geven.

Een extra voor deel van dit werk is het verbeteren van de fout berichten. Hier volgen enkele voor beelden van de verbeterde fout berichten bij het aanmelden bij een toepassing die alleen Azure Active Directory gebruikers ondersteunt.

- De gebruikers naam is onjuist getypt of de gebruikers naam is nog niet gesynchroniseerd met Azure AD:
  
    ![de gebruikers naam is onjuist getypt of niet gevonden](./media/signin-realm-discovery/typo-username.png)
  
- De domein naam is niet goed getypt:
  
    ![de domein naam is onjuist getypt of niet gevonden](./media/signin-realm-discovery/typo-domain.png)
  
- De gebruiker probeert zich aan te melden met een bekend consument domein:
  
    ![aanmelden met een bekend consument domein](./media/signin-realm-discovery/consumer-domain.png)
  
- Het wacht woord is onjuist getypt, maar de gebruikers naam is nauw keurig:  
  
    ![het wacht woord is niet juist getypt met een goede gebruikers naam](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Deze functie kan van invloed zijn op federatieve domeinen die afhankelijk zijn van de oude basis realm-detectie op domein niveau om de Federatie af te dwingen. Raadpleeg [Home realm Discovery tijdens het aanmelden voor Microsoft 365 Services](https://azure.microsoft.com/updates/signin-hrd/)voor updates over het toevoegen van federatieve domein ondersteuning. Ondertussen hebben sommige organisaties hun werk nemers getraind om zich aan te melden met een gebruikers naam die niet bestaat in Azure Active Directory, maar bevat de juiste domein naam, omdat de domein namen gebruikers naar het domein eindpunt van de organisatie routeren. Het nieuwe aanmeldings gedrag staat dit niet toe. De gebruiker wordt op de hoogte gesteld om de gebruikers naam te corrigeren en ze zijn niet gemachtigd om zich aan te melden met een gebruikers naam die niet voor komt in Azure Active Directory.
>
> Als u of uw organisatie procedures heeft die afhankelijk zijn van het oude gedrag, is het belang rijk dat beheerders zich aanmelden en verificatie documentatie bijwerken en werk nemers trainen om hun Azure Active Directory gebruikers naam te gebruiken om zich aan te melden.
  
Als u problemen hebt met het nieuwe gedrag, laat u uw opmerkingen in het gedeelte **feedback** van dit artikel staan.  

## <a name="next-steps"></a>Volgende stappen

[Uw huis stijl voor aanmelden aanpassen](../fundamentals/add-custom-domain.md)

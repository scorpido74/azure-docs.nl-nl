---
title: Opzoekadres gebruikersnaam tijdens aanmelding - Azure Active Directory | Microsoft Documenten
description: Hoe berichten op het scherm het opzoeken van gebruikersnaam weergeven tijdens het aanmelden in Azure Active Directory
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
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024253"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Thuisrealmdetectie voor aanmeldingspagina's van Azure Active Directory

We wijzigen ons azure active directory-aanmeldingsgedrag (Azure Active Directory) om ruimte te maken voor nieuwe verificatiemethoden en de bruikbaarheid te verbeteren. Tijdens het aanmelden bepaalt Azure AD waar een gebruiker moet verifiëren. Azure AD neemt intelligente beslissingen door organisatie- en gebruikersinstellingen te lezen voor de gebruikersnaam die is ingevoerd op de aanmeldingspagina. Dit is een stap in de richting van een wachtwoordvrije toekomst die aanvullende referenties zoals FIDO 2.0 mogelijk maakt.

## <a name="home-realm-discovery-behavior"></a>Het ontdekkingsgedrag van het huiskoninkrijk

Historisch gezien werd de detectie van het thuisrijk bepaald door het domein dat wordt geleverd bij aanmelding of door een Home Realm Discovery-beleid voor sommige oudere toepassingen. In ons detectiegedrag kan een Azure Active Directory-gebruiker bijvoorbeeld zijn gebruikersnaam verkeerd typen, maar toch op het scherm voor het verzamelen van referenties van zijn organisatie aankomen. Dit gebeurt wanneer de gebruiker de domeinnaam 'contoso.com' van de organisatie correct opgeeft. Met dit gedrag kan de granulariteit geen ervaringen voor een individuele gebruiker aanpassen.

Om een breder scala aan referenties te ondersteunen en de bruikbaarheid te vergroten, wordt het opzoekgedrag van de gebruikersnaam van Azure Active Directory tijdens het aanmeldingsproces nu bijgewerkt. Het nieuwe gedrag maakt intelligente beslissingen door tenant- en gebruikersniveauinstellingen te lezen op basis van de gebruikersnaam die is ingevoerd op de aanmeldingspagina. Om dit mogelijk te maken, controleert Azure Active Directory of de gebruikersnaam die is ingevoerd op de aanmeldingspagina in het opgegeven domein bestaat of wordt de gebruiker omgeleid om zijn of haar referenties op te geven.

Een bijkomend voordeel van dit werk is verbeterde foutmeldingen. Hier volgen enkele voorbeelden van de verbeterde foutmeldingen bij het aanmelden bij een toepassing die alleen Azure Active Directory-gebruikers ondersteunt.

- De gebruikersnaam is verkeerd getypt of de gebruikersnaam is nog niet gesynchroniseerd met Azure AD:
  
    ![de gebruikersnaam is verkeerd getypt of niet gevonden](./media/signin-realm-discovery/typo-username.png)
  
- De domeinnaam wordt verkeerd getypt:
  
    ![de domeinnaam verkeerd is getypt of niet wordt gevonden](./media/signin-realm-discovery/typo-domain.png)
  
- Gebruiker probeert in te loggen met een bekend consumentendomein:
  
    ![aanmelden met een bekend consumentendomein](./media/signin-realm-discovery/consumer-domain.png)
  
- Het wachtwoord is verkeerd getypt, maar de gebruikersnaam is juist:  
  
    ![wachtwoord is verkeerd getypt met een goede gebruikersnaam](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Deze functie kan een impact hebben op federatieve domeinen die afhankelijk zijn van de oude Home Realm Discovery op domeinniveau om federatie te forceren. Zie [Detectie van het thuisrijk tijdens de aanmelding voor Microsoft 365-services voor](https://azure.microsoft.com/updates/signin-hrd/)updates over wanneer federatieve domeinondersteuning wordt toegevoegd. In de tussentijd hebben sommige organisaties hun werknemers getraind om zich aan te melden met een gebruikersnaam die niet bestaat in Azure Active Directory, maar de juiste domeinnaam bevat, omdat de domeinnamen gebruikers die momenteel worden doorlijnen naar het domeineindpunt van hun organisatie. Het nieuwe aanmeldingsgedrag staat dit niet toe. De gebruiker wordt op de hoogte gesteld dat hij de gebruikersnaam corrigeert en mag zich niet aanmelden met een gebruikersnaam die niet bestaat in Azure Active Directory.
>
> Als u of uw organisatie praktijken hebben die afhankelijk zijn van het oude gedrag, is het belangrijk dat organisatiebeheerders aanmeldings- en verificatiedocumentatie voor werknemers bijwerken en werknemers trainen om hun Azure Active Directory-gebruikersnaam te gebruiken om zich aan te melden.
  
Als je je zorgen maakt over het nieuwe gedrag, laat je je opmerkingen achter in het gedeelte **Feedback** van dit artikel.  

## <a name="next-steps"></a>Volgende stappen

[Uw aanmeldingsmerking aanpassen](../fundamentals/add-custom-domain.md)

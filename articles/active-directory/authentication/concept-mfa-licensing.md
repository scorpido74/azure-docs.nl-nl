---
title: Azure MFA-versies en-verbruiks plannen-Azure Active Directory
description: Informatie over de multi-factor Authentication-client en de verschillende methoden en versies die beschikbaar zijn.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1528bffc613d2e8ab2c0150095d90791b649198a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979487"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication ophalen

Wanneer u uw accounts wilt beveiligen, moet verificatie in twee stappen standaard zijn in uw organisatie. Deze functie is vooral belang rijk voor accounts met uitgebreide toegang tot resources. Daarom biedt micro soft eenvoudige verificatie functies van twee stappen voor Office 365-en Azure Active Directory-beheerders (Azure AD) zonder extra kosten. Als u een upgrade wilt uitvoeren voor de functies van uw beheerders of als u verificatie in twee stappen wilt uitbreiden naar de overige gebruikers, kunt u Azure Multi-Factor Authentication op verschillende manieren kopen.

> [!IMPORTANT]
> Dit artikel is bedoeld als een hulp middel om u inzicht te geven in de verschillende manieren om Azure Multi-Factor Authentication te kopen. Voor specifieke informatie over prijzen en facturering moet u altijd verwijzen naar de [pagina met multi-factor Authentication prijzen](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Beschik bare versies van Azure Multi-Factor Authentication

In de volgende tabel worden de verschillen tussen versies van multi-factor Authentication beschreven:

| Versie | Beschrijving |
| --- | --- |
| Gratis optie | Klanten die gebruikmaken van de gratis voor delen van Azure AD, kunnen gebruikmaken van de [standaard instellingen](../fundamentals/concept-fundamentals-security-defaults.md) om multi-factor Authentication in hun omgeving in te scha kelen. |
| Verificatie met meerdere factoren (MFA) voor Office 365 | Deze versie wordt beheerd vanuit het Office 365-of Microsoft 365-Portal. Beheerders kunnen [Office 365-bronnen beveiligen met verificatie in twee stappen](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Deze versie maakt deel uit van een Office 365-abonnement. |
| Multi-Factor Authentication voor Azure AD-beheerders | Gebruikers die de rol van Azure AD Global-beheerder hebben toegewezen in azure AD-tenants, kunnen verificatie in twee stappen inschakelen zonder extra kosten. |
| Verificatie op basis van meerdere factoren van Azure | Azure Multi-Factor Authentication wordt ook wel de ' volledige ' versie genoemd en biedt de uitgebreide set mogelijkheden. Het biedt aanvullende configuratie opties via de [Azure Portal](https://portal.azure.com), geavanceerde rapportage en ondersteuning voor een groot aantal on-premises en Cloud toepassingen. Azure Multi-Factor Authentication is een functie van [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) en [Microsoft 365 Business](https://www.microsoft.com/microsoft-365/business). |

> [!NOTE]
> Nieuwe klanten kunnen Azure Multi-Factor Authentication niet meer kopen als zelfstandige aanbieding die vanaf 1 september 2018. Multi-factor Authentication blijft beschikbaar als onderdeel van Azure AD Premium-of Microsoft 365 Business-licenties.

## <a name="feature-comparison-of-versions"></a>Functie vergelijking van versies

De volgende tabel bevat een lijst met de functies die beschikbaar zijn in de verschillende versies van Azure Multi-Factor Authentication.

> [!NOTE]
> Deze vergelijkings tabel bevat een beschrijving van de functies die deel uitmaken van elke versie van Multi-Factor Authentication. Als u de volledige Azure Multi-Factor Authentication-service hebt, is het mogelijk dat sommige functies niet beschikbaar zijn, afhankelijk van of u [MFA in de Cloud of MFA on-premises](concept-mfa-whichversion.md)gebruikt.
>

| Functie | Verificatie met meerdere factoren (MFA) voor Office 365 | Multi-Factor Authentication voor Azure AD-beheerders | Verificatie op basis van meerdere factoren van Azure | Standaardinstellingen voor de beveiliging |
| --- |:---:|:---:|:---:|:---:|
| Azure AD-beheerders accounts beveiligen met MFA |● |● (Alleen Azure AD Global Administrator-accounts) |● |● |
| Mobiele app als een tweede factor |● |● |● |● |
| Telefoon gesprek als een tweede factor |● |● |● |   |
| SMS als een tweede factor |● |● |● |   |
| App-wacht woorden voor clients die geen ondersteuning bieden voor MFA |● |● |● |   |
| Beheer beheer via verificatie methoden |● |● |● |   |
| Niet-beheerders accounts beveiligen met MFA |● | |● |● |
| Pincodemodus | | |● |   |
| Melding bij fraude | | |● |   |
| MFA-rapporten | | |● |   |
| Eenmalige bypass | | |● |   |
| Aangepaste begroeting voor telefoongesprekken | | |● |   |
| Aangepaste beller-ID voor telefoon gesprekken | | |● |   |
| Goedgekeurde IP-adressen | | |● |   |
| MFA herinneren voor vertrouwde apparaten |● |● |● |   |
| MFA voor on-premises toepassingen | | |● |   |

> [!IMPORTANT]
> Vanaf maart 2019 zijn de opties voor telefoon gesprekken niet beschikbaar voor MFA-en SSPR-gebruikers in gratis/proef versie van Azure AD-tenants. SMS-berichten worden niet beïnvloed door deze wijziging. De telefoon oproep blijft beschikbaar voor gebruikers in betaalde Azure AD-tenants. Deze wijziging is alleen van invloed op de Azure AD-tenants gratis en proef versie.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Azure-Multi-Factor Authentication inschakelen voor Azure AD-beheerders

Gebruikers die de rol globale beheerder hebben toegewezen in azure AD-tenants, kunnen verificatie in twee stappen inschakelen voor hun globale beheerders accounts van Azure AD zonder extra kosten. Als u een micro soft-account gebruikt, kunt u zich registreren voor multi-factor Authentication met behulp van de richt lijnen in het Microsoft-account-ondersteunings artikel, [over verificatie in twee stappen](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Als u geen micro soft-account gebruikt, schakelt u multi-factor Authentication in voor globale beheerders met behulp van de richt lijnen in het artikel [een verificatie in twee stappen vereisen voor een gebruiker of groep](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication aanschaffen

Schaf licenties aan met Azure Multi-Factor Authentication, zoals Azure Active Directory Premium, of een licentie bundel die Azure AD Premium of voorwaardelijke toegang bevat en wijs deze toe aan uw gebruikers in Azure Active Directory.

### <a name="consumption-based-licensing"></a>Licentie verlening op basis van verbruik

Licentie verlening op basis van verbruik is niet langer beschikbaar voor nieuwe klanten vanaf 1 september 2018.

Met ingang van 1 september 2018 kunnen nieuwe verificatie providers niet meer worden gemaakt. Bestaande auth-providers kunnen blijven gebruiken en worden bijgewerkt. Multi-factor Authentication blijft een beschik bare functie in Azure AD Premium-licenties.

Wanneer u een Azure Multi-Factor Authentication-provider gebruikt, zijn er twee gebruiks modellen beschikbaar die worden gefactureerd via uw Azure-abonnement:

1. **Per ingeschakelde gebruiker** : voor bedrijven die verificatie in twee stappen willen inschakelen voor een vast aantal werk nemers die regel matig verificatie nodig hebben. Facturering per gebruiker is gebaseerd op het aantal gebruikers dat is ingeschakeld voor MFA in uw Azure AD-Tenant en uw Azure MFA-server. Als gebruikers zijn ingeschakeld voor MFA in zowel Azure AD-als Azure MFA-server, en domein synchronisatie (Azure AD Connect) is ingeschakeld, tellen we de grotere set gebruikers. Als domein synchronisatie niet is ingeschakeld, tellen we de som van alle gebruikers die zijn ingeschakeld voor MFA in azure AD en Azure MFA server. Facturering wordt per dag naar het commerce-systeem gefactureerd.

   > [!NOTE]
   > Facturerings voorbeeld 1: u hebt nog 5.000 gebruikers ingeschakeld voor MFA. Het MFA-systeem deelt dat aantal op 31 en rapporteert 161,29 gebruikers voor die dag. Morgen kunt u 15 meer gebruikers inschakelen. het MFA-systeem rapporteert dus 161,77 gebruikers voor die dag. Aan het einde van de facturerings cyclus voegt het totale aantal gebruikers dat bij uw Azure-abonnement wordt gefactureerd toe tot ongeveer 5.000.
   >
   > Facturerings voorbeeld 2: u hebt een combi natie van gebruikers met licenties en gebruikers zonder, dus u hebt een Azure MFA-provider per gebruiker om het verschil op te nemen. Er zijn 4.500 Enterprise Mobility + Security licenties voor uw Tenant, maar 5.000 gebruikers zijn ingeschakeld voor MFA. Uw Azure-abonnement wordt gefactureerd voor 500 gebruikers, naar rato en dagelijks gerapporteerd als 16,13 gebruikers.
   >

1. **Per authenticatie** : voor bedrijven die verificatie in twee stappen willen inschakelen voor een grote groep gebruikers die weinig verificatie nodig hebben. De facturering is gebaseerd op het aantal verificatie aanvragen in twee stappen, ongeacht of deze verificaties slagen of worden geweigerd. Deze facturering wordt weer gegeven in de Azure-gebruiks instructie in packs van 10 verificaties en wordt dagelijks gerapporteerd.

   > [!NOTE]
   > Facturerings voorbeeld 3: vandaag, de Azure MFA-service heeft 3.105 2-stap verificatie aanvragen ontvangen. Uw Azure-abonnement wordt gefactureerd voor 310,5-verificatie pakketten.
   >

Het is belang rijk te weten dat u licenties kunt hebben, maar nog steeds wordt gefactureerd voor configuratie op basis van verbruik. Als u een Azure MFA-provider per verificatie instelt, wordt u gefactureerd voor elke verificatie aanvraag in twee stappen, zelfs die aanvragen die worden uitgevoerd door gebruikers die een licentie hebben. Als u een per gebruiker een Azure MFA-provider instelt op een domein dat niet is gekoppeld aan uw Azure AD-Tenant, wordt u per ingeschakelde gebruiker gefactureerd, zelfs als uw gebruikers licenties hebben op Azure AD.

## <a name="next-steps"></a>Volgende stappen

- Zie [prijzen voor Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)voor meer informatie over prijzen.

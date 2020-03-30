---
title: Aanmelden met verificatie met een werk- of schoolaccount - Azure AD
description: Meer informatie over hoe u zich aanmeldt bij uw werk- of schoolaccount met behulp van de verschillende tweestapsverificatiemethoden.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: 33cf9e284d2206ea497af7a5da7c3cf4a890cc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064084"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Meld u aan bij uw werk- of schoolaccount met uw tweestapsverificatiemethode

> [!NOTE]
> Het doel van dit artikel is om door een typische inlogervaring te lopen. Zie [Problemen met Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md)voor hulp bij het aanmelden of om problemen op te lossen.

## <a name="what-will-your-sign-in-experience-be"></a>Wat is uw aanmeldingservaring?
Uw aanmeldingservaring verschilt afhankelijk van wat u als tweede factor wilt gebruiken: een telefoongesprek, een verificatie-app of sms'en. Kies de optie die het beste beschrijft wat u doet:

| Hoe log je in? |
| --- |
| [Met een telefoontje naar mijn mobiele telefoon of kantoortelefoon](#signing-in-with-a-phone-call) |
| [Met een sms naar mijn mobiele telefoon](#signing-in-with-a-text-message)
| [Met meldingen van de Microsoft Authenticator-app](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Met verificatiecodes uit de Microsoft Authenticator-app |
| [Met een alternatieve methode, want ik kan mijn voorkeursmethode nu niet gebruiken](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Inloggen met een telefoongesprek
De volgende informatie beschrijft de verificatie-ervaring in twee stappen met een oproep naar uw mobiele telefoon of kantoortelefoon.

1. Meld u aan bij een toepassing of service zoals Office 365 met uw gebruikersnaam en wachtwoord.  
2. Microsoft belt u.  
3. Neem de telefoon op en druk op de # sleutel.  

## <a name="signing-in-with-a-text-message"></a>Aanmelden met een sms-bericht
De volgende informatie beschrijft de verificatie-ervaring in twee stappen met een sms-bericht naar uw mobiele telefoon:

1. Meld u aan bij een toepassing of service zoals Office 365 met uw gebruikersnaam en wachtwoord.
2. Microsoft stuurt u een sms-bericht met een nummercode.
3. Voer de code in het vak op de aanmeldingspagina in.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Aanmelden met de Microsoft Authenticator-app
De volgende informatie beschrijft de ervaring van het gebruik van de Microsoft Authenticator-app voor verificaties in twee stappen. Er zijn twee verschillende manieren om de app te gebruiken. U pushmeldingen ontvangen op uw apparaat of u de app openen om een verificatiecode te ontvangen.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Aanmelden met een melding vanuit de Microsoft Authenticator-app
1. Meld u aan bij een toepassing of service zoals Office 365 met uw gebruikersnaam en wachtwoord.
2. Microsoft stuurt een melding naar de Microsoft Authenticator-app op uw apparaat.

   ![Microsoft verzendt melding](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Open de melding op uw telefoon en selecteer de sleutel **Verifiëren.** Als uw bedrijf een pincode nodig heeft, voert u deze hier in.
4. Je moet nu ingelogd zijn.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Aanmelden met een verificatiecode met de Microsoft Authenticator-app

Als u de Microsoft Authenticator-app gebruikt om verificatiecodes te krijgen, ziet u bij het openen van de app een nummer onder uw accountnaam. Dit aantal verandert elke 30 seconden, zodat u niet twee keer hetzelfde nummer gebruikt. Wanneer u om een verificatiecode wordt gevraagd, opent u de app en gebruikt u het nummer dat momenteel wordt weergegeven.

1. Meld u aan bij een toepassing of service zoals Office 365 met uw gebruikersnaam en wachtwoord.
2. Microsoft vraagt u om een verificatiecode.

   ![Verificatiecode invoeren](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Open de Microsoft Authenticator-app op uw telefoon en voer de code in in het vak waarin u zich aanmeldt.

## <a name="signing-in-with-an-alternate-method"></a>Aanmelden met een alternatieve methode
Soms hebt u niet de telefoon of het apparaat dat u hebt ingesteld als verificatiemethode voor de voorkeur. Daarom raden we u aan back-upmethoden voor uw account in te stellen. In de volgende sectie ziet u hoe u zich aanmeldt met een alternatieve methode wanneer uw primaire methode mogelijk niet beschikbaar is.

1. Meld u aan bij een toepassing of service zoals Office 365 met uw gebruikersnaam en wachtwoord.
2. Selecteer **Een andere verificatieoptie gebruiken**. U ziet verschillende verificatieopties op basis van het aantal instellingen dat u hebt ingesteld.
3. Kies een alternatieve methode en meld u aan.

   ![Alternatieve methode gebruiken](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Volgende stappen
- Als u problemen ondervindt bij het aanmelden met verificatie in twee stappen, krijgt u meer informatie bij [Problemen met Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

- Meer informatie over het [beheren van uw verificatie-instellingen in twee stappen](multi-factor-authentication-end-user-manage-settings.md).

- Ontdek hoe u aan [de slag met de Microsoft Authenticator-app,](user-help-auth-app-download-install.md) zodat u meldingen gebruiken om u aan te melden in plaats van sms'jes en telefoongesprekken.

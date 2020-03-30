---
title: Eén aanmelding bij apps met Azure AD-toepassingsproxy | Microsoft Documenten
description: Schakel eenmalige aanmelding in voor uw gepubliceerde on-premises toepassingen met Azure AD Application Proxy in de Azure-portal.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0259a8d9fcb4c9c513ab2c31103c9a8488e90ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025738"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Wachtwoordkluisvoor eenmalige aanmelding met toepassingsproxy

Azure Active Directory Application Proxy helpt u de productiviteit te verbeteren door on-premises toepassingen te publiceren, zodat externe werknemers er ook veilig toegang toe hebben. In de Azure-portal u ook enkele aanmeldings (SSO) instellen voor deze apps. Uw gebruikers hoeven alleen te verifiëren met Azure AD en ze hebben toegang tot uw bedrijfstoepassing zonder dat ze zich opnieuw hoeven aan te melden.

Application Proxy ondersteunt verschillende [single sign-on modes](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Aanmelding op basis van wachtwoorden is bedoeld voor toepassingen die een combinatie van gebruikersnaam/wachtwoord gebruiken voor verificatie. Wanneer u aanmelding op basis van een wachtwoord voor uw toepassing configureert, moeten uw gebruikers zich één keer aanmelden bij de on-premises toepassing. Daarna slaat Azure Active Directory de aanmeldingsgegevens op en wordt deze automatisch aan de toepassing verstrekt wanneer uw gebruikers deze op afstand openen.

U had uw app al moeten publiceren en testen met Application Proxy. Als dit niet het zo is, volgt u de stappen in [Toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md) en komt u hier terug.

## <a name="set-up-password-vaulting-for-your-application"></a>Wachtwoordkluis instellen voor uw toepassing

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beheerder.
1. Selecteer Alle toepassingen van **Azure Active Directory** > **Enterprise-toepassingen** > **All applications**.
1. Selecteer in de lijst de app die u met SSO wilt instellen.  
1. Selecteer **Toepassingsproxy**. 
1. Wijzig het **type Pre-verificatie** in **Passthrough** en selecteer **Opslaan**. Later u weer overschakelen naar **Azure Active Directory-type!** 
1. Selecteer **Eenmalig aanmelden**.

   ![Eén aanmelding selecteren op de overzichtspagina van de app](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Kies voor de **SSO-modus Sign-on op wachtwoord gebaseerd**.
1. Voer voor de URL aanmelding de URL in voor de pagina waar gebruikers hun gebruikersnaam en wachtwoord invoeren om zich buiten het bedrijfsnetwerk bij uw app aan te melden. Dit kan de externe URL zijn die u hebt gemaakt toen u de app publiceerde via Application Proxy.

   ![Kies aanmelding op basis van een wachtwoord en voer uw URL in](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Selecteer **Opslaan**.
1. Selecteer **Toepassingsproxy**. 
1. Wijzig het **type Pre-verificatie** in **Azure Active Directory** en selecteer **Opslaan**. 
1. Selecteer **gebruikers en groepen**.
1. Gebruikers toewijzen aan de toepassing met de selectie **Gebruiker toevoegen**. 
1. Als u referenties voor een gebruiker vooraf wilt definiëren, schakelt u het selectievakje voorzijde van de gebruikersnaam in en selecteert **u Referenties bijwerken.**
1. Selecteer **Azure Active Directory** > **App-registraties** > **Alle toepassingen**.
1. Selecteer in de lijst de app die u hebt geconfigureerd met Password SSO.
1. Selecteer **Branding**. 
1. Werk de **URL van** de startpagina bij met de URL **aanmelden op** de pagina Password SSO en selecteer **Opslaan**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Uw app testen

Ga naar de mijn apps-portal. Meld u aan met uw referenties (of de referenties voor een testaccount dat u hebt ingesteld met toegang). Zodra u zich hebt aangemeld, klikt u op het pictogram van de app. Dit kan leiden tot de installatie van de browserextensie Mijn apps Secure Sign-in. Als uw gebruiker vooraf gedefinieerde referenties had, moet de verificatie naar de app automatisch plaatsvinden, anders moet u de gebruikersnaam of het wachtwoord voor de eerste keer opgeven. 

## <a name="next-steps"></a>Volgende stappen

- Lees meer over andere manieren om [single sign-on](what-is-single-sign-on.md) te implementeren
- Meer informatie over [beveiligingsoverwegingen voor het op afstand openen van apps met Azure AD-toepassingsproxy](application-proxy-security.md)

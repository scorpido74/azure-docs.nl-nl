---
title: Eenmalige aanmelding voor apps met Azure AD-toepassingsproxy | Microsoft Docs
description: Schakel eenmalige aanmelding voor uw gepubliceerde on-premises toepassingen met Azure AD-toepassingsproxy in de Azure-portal.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025738"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Wachtwoord voor eenmalige aanmelding met Application Proxy vaulting

Azure Active Directory-toepassingsproxy helpt u bij de productiviteit te verbeteren door het publiceren van on-premises toepassingen, zodat externe werknemers veilig toegang deze te tot. In de Azure-portal kunt u ook instellen van eenmalige aanmelding (SSO) tot deze apps. Er hoeft alleen uw gebruikers te verifiëren met Azure AD en ze toegang krijgen tot uw zakelijke toepassing zonder te hoeven aan te melden bij het opnieuw.

Application Proxy biedt ondersteuning voor diverse [eenmalige aanmelding modi](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Wachtwoord gebaseerde aanmelding is bedoeld voor toepassingen die gebruikmaken van een combinatie van gebruikersnaam en wachtwoord voor verificatie. Wanneer u op basis van wachtwoorden aanmelding voor uw toepassing configureren, hebben uw gebruikers zich aanmeldt bij de on-premises toepassing één keer. Hierna Azure Active Directory de gegevens worden opgeslagen en deze automatisch biedt aan de toepassing als uw gebruikers toegang hebben tot het op afstand.

U moet al hebt gepubliceerd en uw app met Application Proxy getest. Als dit niet het geval is, volg de stappen in [toepassingen publiceren die gebruikmaken van Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md) vervolgens hier terugkeren.

## <a name="set-up-password-vaulting-for-your-application"></a>Instellen van wachtwoord vaulting voor uw toepassing

1. Meld u als beheerder aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Azure Active Directory** > **bedrijfstoepassingen** > **alle toepassingen**.
1. Selecteer de app die u wilt instellen met eenmalige aanmelding in de lijst.  
1. Selecteer **toepassingsproxy**. 
1. Wijzig het **type verificatie vooraf** in **passthrough** en selecteer **Opslaan**. U kunt later weer overschakelen naar **Azure Active Directory** type. 
1. Selecteer **eenmalige aanmelding**.

   ![Eenmalige aanmelding selecteren op de overzichts pagina van de app](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Kies voor de SSO-modus, **op basis van wachtwoorden Sign-on**.
1. Voer de URL voor de aanmeldings-URL voor de pagina waar gebruikers hun gebruikersnaam en wachtwoord aanmelden bij uw app buiten het bedrijfsnetwerk invoeren. Dit kan zijn dat de externe URL die u hebt gemaakt toen u de app via de toepassingsproxy hebt gepubliceerd.

   ![Kies op basis van wachtwoorden Sign-on en voer de URL van uw](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Selecteer **Opslaan**.
1. Selecteer **toepassingsproxy**. 
1. Wijzig het **type verificatie vooraf** in **Azure Active Directory** en selecteer **Opslaan**. 
1. Selecteer **gebruikers en groepen**.
1. Gebruikers toewijzen aan de toepassing met de optie **gebruiker toevoegen**. 
1. Als u de referenties voor een gebruiker vooraf wilt definiëren, schakelt u het selectie vakje voor de gebruikers naam in en selecteert u **referenties bijwerken**.
1. Selecteer **Azure Active Directory** > **app-registraties** **alle toepassingen**te > .
1. Selecteer in de lijst de app die u hebt geconfigureerd met wacht woord-SSO.
1. Selecteer **huis stijl**. 
1. Werk de **URL van de start pagina** bij met de **aanmeldings-URL** op de SSO-pagina van het wacht woord en selecteer **Opslaan**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Uw app testen

Ga naar de portal mijn apps. Meld u aan met uw referenties (of de referenties voor een test account dat u hebt ingesteld met toegang). Nadat u zich hebt aangemeld, klikt u op het pictogram van de app. Hiermee wordt mogelijk de installatie van de extensie voor beveiligde aanmelding van mijn apps geactiveerd. Als uw gebruiker vooraf gedefinieerde referenties had, moet de verificatie voor de app automatisch plaatsvinden, anders moet u de gebruikers naam of het wacht woord voor de eerste keer opgeven. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over andere manieren om te implementeren [eenmalige aanmelding](what-is-single-sign-on.md)
- Meer informatie over [beveiligingsoverwegingen voor het openen van apps op afstand met Azure AD-toepassingsproxy](application-proxy-security.md)

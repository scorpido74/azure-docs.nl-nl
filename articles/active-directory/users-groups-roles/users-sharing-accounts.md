---
title: Accounts en referenties delen - Azure Active Directory | Microsoft Documenten
description: Beschrijft hoe Azure Active Directory organisaties in staat stelt om accounts voor on-premises apps en consumentencloudservices veilig te delen.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 987c3ee7c90eb0bb793b96eb2771efbb258f16a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565499"
---
# <a name="sharing-accounts-with-azure-ad"></a>Accounts delen met Azure AD

## <a name="overview"></a>Overzicht

Soms moeten organisaties één gebruikersnaam en wachtwoord voor meerdere personen gebruiken, wat meestal in twee gevallen gebeurt:

* Wanneer u toegang krijgt tot toepassingen waarvoor voor elke gebruiker een uniek aanmeldings- en wachtwoord vereist is, of het nu gaat om on-premises apps of consumentencloudservices (bijvoorbeeld zakelijke sociale media-accounts).
* Bij het maken van omgevingen voor meerdere gebruikers. Mogelijk hebt u één lokaal account dat verhoogde bevoegdheden heeft en wordt gebruikt voor kerninstellingen, beheer en herstelactiviteiten. Bijvoorbeeld het lokale account 'globale beheerder' voor Office 365 of het hoofdaccount in Salesforce.

Traditioneel worden deze accounts gedeeld door de referenties (gebruikersnaam en wachtwoord) te distribueren naar de juiste personen of ze op te slaan op een gedeelde locatie waar meerdere vertrouwde agents toegang tot hebben.

Het traditionele deelmodel heeft verschillende nadelen:

* Als u toegang tot nieuwe toepassingen inschakelt, moet u referenties distribueren naar iedereen die toegang nodig heeft.
* Elke gedeelde toepassing kan zijn eigen unieke set gedeelde referenties vereisen, waarbij gebruikers meerdere sets referenties moeten onthouden. Wanneer gebruikers veel referenties moeten onthouden, neemt het risico toe dat ze hun toevlucht nemen tot risicovolle praktijken. (bijvoorbeeld het opschrijven van wachtwoorden).
* Je niet zeggen wie toegang heeft tot een toepassing.
* U niet zeggen wie toegang heeft *tot* een toepassing.
* Wanneer u de toegang tot een toepassing wilt verwijderen, moet u de referenties bijwerken en opnieuw distribueren naar iedereen die toegang tot die toepassing nodig heeft.

## <a name="azure-active-directory-account-sharing"></a>Delen van Azure Active Directory-account

Azure AD biedt een nieuwe benadering voor het gebruik van gedeelde accounts die deze nadelen elimineert.

De Azure AD-beheerder configureert welke toepassingen een gebruiker kan openen met behulp van het Toegangspaneel en het type eenmalige aanmelding te kiezen dat het meest geschikt is voor die toepassing. Een van die typen, *met een wachtwoord gebaseerdeenmalig aanmelden,* laat Azure AD fungeren als een soort 'broker' tijdens het aanmeldingsproces voor die app.

Gebruikers melden zich één keer aan met hun organisatieaccount. Dit account is dezelfde account die ze regelmatig gebruiken om toegang te krijgen tot hun bureaublad of e-mail. Ze kunnen alleen de toepassingen ontdekken en openen waaraan ze zijn toegewezen. Met gedeelde accounts kan deze lijst met toepassingen een willekeurig aantal gedeelde referenties bevatten. De eindgebruiker hoeft de verschillende accounts die ze mogelijk gebruiken niet te onthouden of op te schrijven.

Gedeelde accounts vergroten niet alleen het toezicht en verbeteren de bruikbaarheid, ze verbeteren ook uw beveiliging. Gebruikers met machtigingen om de referenties te gebruiken, zien het gedeelde wachtwoord niet, maar krijgen machtigingen om het wachtwoord te gebruiken als onderdeel van een georkestreerde verificatiestroom. Verder bieden sommige wachtwoord SSO-toepassingen u de mogelijkheid om Azure AD te gebruiken om wachtwoorden periodiek te rolloveren (bijwerken). Het systeem maakt gebruik van grote, complexe wachtwoorden, wat de accountbeveiliging verhoogt. De beheerder kan eenvoudig toegang tot een toepassing verlenen of intrekken, weet wie toegang heeft tot het account en wie er in het verleden toegang toe heeft gehad.

Azure AD ondersteunt gedeelde accounts voor elk EMS-licentieplan (Enterprise Mobility Suite) of Azure AD Premium voor alle typen wachtwoordtoepassingen. U accounts voor duizenden vooraf geïntegreerde toepassingen in de toepassingsgalerie delen en uw eigen wachtwoordauthenticating-toepassing toevoegen met [aangepaste SSO-apps.](../manage-apps/configure-single-sign-on-non-gallery-applications.md)

Azure AD-functies die het delen van een account inschakelen, zijn onder andere:

* [Eenmalige aanmelding voor wachtwoorden](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Eenmalige aanmeldingsagent voor wachtwoorden
* [Groepstoewijzing](groups-self-service-management.md)
* Aangepaste wachtwoord-apps
* [Dashboard voor app-gebruik/rapporten](../active-directory-passwords-get-insights.md)
* Toegangsportalen voor eindgebruikers
* [App-proxy](../manage-apps/application-proxy.md)
* [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Een account delen

Als u Azure AD wilt gebruiken om een account te delen, moet u het:

* Een galerie voor [toepassings-apps](https://azure.microsoft.com/marketplace/active-directory/) of [aangepaste toepassing toevoegen](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* De toepassing voor wachtwoord Single Sign-On (SSO) configureren
* [Groepstoewijzing](groups-saasapps.md) gebruiken en de optie selecteren om een gedeelde referentie in te voeren

U uw gedeelde account ook veiliger maken met Multi-Factor Authentication (MFA) (meer informatie over [het beveiligen van toepassingen met Azure AD)](../authentication/concept-mfa-whichversion.md)en u de mogelijkheid delegeren om te beheren wie toegang heeft tot de toepassing met Azure AD [selfservicegroepbeheer.](groups-self-service-management.md)

## <a name="next-steps"></a>Volgende stappen

* [Application Management in Azure Active Directory](../manage-apps/what-is-application-management.md) (Toepassingsbeheer in Azure Active Directory)
* [Apps beveiligen met voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md)
* [Selfservice group management/SSAA](groups-self-service-management.md)

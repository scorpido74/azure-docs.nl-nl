---
title: Zelfservicewachtwoord voor licentie - Azure Active Directory
description: Licentievereisten voor azure AD-zelfservicewachtwoord opnieuw instellen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 598f3bd8500a59cd41cc4126915e6cccbd4fb2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848558"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Licentievereisten voor het opnieuw instellen van azure AD-zelfservicewachtwoorden

Azure Active Directory (Azure AD) wordt geleverd in verschillende edities: Gratis, Premium P1 en Premium P2. Er zijn verschillende functies die deel uitmaken van self-service wachtwoord resetten, met inbegrip van wijzigen, resetten, ontgrendelen en terugschrijven, die beschikbaar zijn in de verschillende edities van Azure AD. Dit artikel probeert de verschillen uit te leggen. Meer details over de functies in elke Azure AD-editie vindt u op de [prijspagina van Azure Active Directory.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="compare-editions-and-features"></a>Edities en functies vergelijken

Azure AD self-service password reset is gelicentieerd per gebruiker, om compliance organisaties te handhaven zijn verplicht om de juiste licentie toe te wijzen aan hun gebruikers.

* Selfservice wachtwoordwijziging voor cloudgebruikers
   * Ik ben een **cloud-only gebruiker** en weet mijn wachtwoord.
      * Ik wil mijn wachtwoord **wijzigen** in iets nieuws.
   * Deze functionaliteit is opgenomen in alle edities van Azure AD.

* Selfservice wachtwoordreset voor cloudgebruikers
   * Ik ben een **cloud-only gebruiker** en ben mijn wachtwoord vergeten.
      * Ik wil mijn wachtwoord **resetten** naar iets wat ik weet.
   * Deze functionaliteit is opgenomen in Azure AD Premium P1 of P2, Microsoft 365 Business of Office 365.

* Self-Service Wachtwoord opnieuw instellen/wijzigen/ontgrendelen **met on-premises terugschrijftekst**
   * Ik ben een **hybride gebruiker** Mijn on-premises Active Directory-gebruikersaccount wordt gesynchroniseerd met mijn Azure AD-account met Azure AD Connect. Ik wil mijn wachtwoord wijzigen, mijn wachtwoord vergeten of buitengesloten zijn.
      * Ik wil mijn wachtwoord wijzigen of opnieuw instellen op iets dat ik ken, of mijn account ontgrendelen **en** die wijziging weer synchroniseren met on-premises Active Directory.
   * Deze functionaliteit is opgenomen in Azure AD Premium P1 of P2 of Microsoft 365 Business.

> [!WARNING]
> Zelfstandige Office 365-licentieplannen *bieden geen ondersteuning voor 'Self-Service Password Reset/Change/Unlock with on-premises writeback'* en vereisen een abonnement dat Azure AD Premium P1, Premium P2 of Microsoft 365 Business bevat om deze functionaliteit te laten werken.
>

Aanvullende licentie-informatie, inclusief kosten, is te vinden op de volgende pagina's:

* [Prijssite azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory-functies en -mogelijkheden](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Zakelijk](https://www.microsoft.com/microsoft-365/enterprise)
* [Beschrijving van microsoft 365 Business-service](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Licenties op basis van groepen of gebruikers inschakelen

Azure AD ondersteunt nu groepslicenties. Beheerders kunnen licenties in bulk toewijzen aan een groep gebruikers, in plaats van ze één voor één toe te wijzen. Zie [Problemen met licenties toewijzen, verifiëren en oplossen voor](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)meer informatie.

Sommige services van Microsoft zijn niet op alle locaties beschikbaar. Voordat een licentie aan een gebruiker kan worden toegewezen, moet de beheerder de eigenschap **Gebruikslocatie** van de gebruiker opgeven. Toewijzing van licenties kan worden gedaan onder de sectie > **Gebruikersprofielinstellingen** > **Settings** in de Azure-portal. **User** *Wanneer u groepslicentietoewijzing gebruikt, erven gebruikers zonder opgegeven gebruikslocatie de locatie van de map.*

## <a name="next-steps"></a>Volgende stappen

* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wachtwoord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registreren voor de selfservice voor het opnieuw instellen van een wachtwoord](../user-help/active-directory-passwords-reset-register.md)
* [Welke gegevens worden gebruikt door selfservice voor wachtwoordherstel en welke gegevens moet u voor uw gebruikers invullen?](howto-sspr-authenticationdata.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat zijn de beleidsopties bij selfservice voor wachtwoordherstel?](concept-sspr-policy.md)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](howto-sspr-writeback.md)
* [Hoe maak ik rapporten van activiteit in selfservice voor wachtwoordherstel?](howto-sspr-reporting.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets kapot is. Hoe los ik SSPR op?](active-directory-passwords-troubleshoot.md)
* [Ik heb een vraag die nog niet is beantwoord](active-directory-passwords-faq.md)

---
title: Self-service voor wachtwoord herstel van licentie-Azure Active Directory
description: Licentie vereisten voor Azure AD self-service voor wacht woord opnieuw instellen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d7a23ed0a84ebd671c69744dfec145763ef6897
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622719"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Licentie vereisten voor Azure AD self-service voor wachtwoord herstel

Azure Active Directory (Azure AD) is verkrijgbaar in verschillende edities: Gratis, Premium P1 en Premium P2. Er zijn verschillende functies die selfservice voor het opnieuw instellen van wacht woorden maken, zoals wijzigen, opnieuw instellen, ontgrendelen en terugschrijven, die beschikbaar zijn in de verschillende edities van Azure AD. In dit artikel worden de verschillen uitgelegd. Meer informatie over de functies die deel uitmaken van elke Azure AD-editie kunt u vinden op de [pagina met Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Edities en functies vergelijken

Voor de selfservice voor wachtwoord herstel van Azure AD is een licentie verleend per gebruiker, om nalevings organisaties te onderhouden, moeten de juiste licentie aan hun gebruikers worden toegewezen.

* Wachtwoorden wijzigen via self-service voor cloudgebruikers
   * Ik ben een **alleen-Cloud gebruiker** en weet mijn wacht woord.
      * Ik wil mijn wacht woord **wijzigen** in iets nieuw.
   * Deze functionaliteit is opgenomen in alle edities van Azure AD.

* Wachtwoorden opnieuw instellen via self-service voor cloudgebruikers
   * Ik ben een **alleen-Cloud gebruiker** en ben mijn wacht woord verg eten.
      * Ik wil mijn wacht woord **opnieuw instellen** op iets dat ik weet.
   * Deze functionaliteit is opgenomen in Azure AD Premium P1, P2, Microsoft 365 Business of Office 365.

* Self-service voor wacht woord opnieuw instellen/wijzigen/ontgrendelen **met on-premises terugschrijven**
   * Ik ben een **hybride gebruiker** met mijn on-premises Active Directory gebruikers account is gesynchroniseerd met mijn Azure ad-account met behulp van Azure AD Connect. Ik wil mijn wacht woord wijzigen, mijn wacht woord verg eten of dit is vergrendeld.
      * Ik wil mijn wacht woord wijzigen of opnieuw instellen op een bekende of mijn account ontgrendelen, **en** deze wijziging wordt weer gesynchroniseerd naar on-premises Active Directory.
   * Deze functionaliteit is opgenomen in Azure AD Premium P1 of P2 of Microsoft 365 Business.

> [!WARNING]
> Zelfstandige Office 365-licentie plannen *bieden geen ondersteuning voor "selfservice voor wachtwoord herstel/wijzigen/ontgrendelen met on-premises terugschrijven"* en vereisen een abonnement met Azure AD Premium P1, een Premium P2 of Microsoft 365 Business om deze functionaliteit te laten werken.
>

Meer informatie over licenties, waaronder kosten, vindt u op de volgende pagina's:

* [Azure Active Directory-prijs site](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory functies en mogelijkheden](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Beschrijving van Microsoft 365 Business-Service](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Op groep of gebruiker gebaseerde licentie verlening inschakelen

Azure AD ondersteunt nu licentie verlening op basis van een groep. Beheerders kunnen licenties bulksgewijs toewijzen aan een groep gebruikers, in plaats van ze een voor een toe te wijzen. Zie [problemen met licenties toewijzen, controleren en oplossen](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)voor meer informatie.

Sommige services van Microsoft zijn niet op alle locaties beschikbaar. Voordat een licentie aan een gebruiker kan worden toegewezen, moet de beheerder de eigenschap **gebruiks locatie** opgeven voor de gebruiker. De toewijzing van licenties kan worden uitgevoerd in de sectie**instellingen** voor **gebruikers** > **profielen** > in de Azure Portal. *Wanneer u de toewijzing van een groeps licentie gebruikt, nemen alle gebruikers waarvoor geen gebruiks locatie is opgegeven, de locatie van de map over.*

## <a name="next-steps"></a>Volgende stappen

* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wachtwoord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registreren voor de selfservice voor wachtwoordherstel](../user-help/active-directory-passwords-reset-register.md)
* [Welke gegevens worden gebruikt door selfservice voor wachtwoordherstel en welke gegevens moet u voor uw gebruikers invullen?](howto-sspr-authenticationdata.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat zijn de beleidsopties bij selfservice voor wachtwoordherstel?](concept-sspr-policy.md)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](howto-sspr-writeback.md)
* [Hoe maak ik rapporten van activiteit in selfservice voor wachtwoordherstel?](howto-sspr-reporting.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets misgaat. Hoe los ik problemen in selfservice voor wachtwoordherstel op?](active-directory-passwords-troubleshoot.md)
* [Ik heb een vraag die nog niet is beantwoord](active-directory-passwords-faq.md)

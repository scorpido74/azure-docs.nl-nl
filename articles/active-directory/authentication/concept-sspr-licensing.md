---
title: Zelfservicewachtwoord voor licentie - Azure Active Directory
description: Meer informatie over het verschil met de licentievereisten voor het opnieuw instellen van azure Active Directory-zelfservicewachtwoorden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca11af061e37cf4f804ce2d7ceed72a9448294
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393068"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Licentievereisten voor zelfservice-wachtwoord van Azure Active Directory

Om helpdeskgesprekken en productiviteitsverlies te verminderen wanneer een gebruiker zich niet kan aanmelden bij zijn apparaat of toepassing, kunnen gebruikersaccounts in Azure Active Directory (Azure AD) worden ingeschakeld voor selfservice wachtwoordreset (SSPR). Functies die deel uitmaken van SSPR omvatten wachtwoordwijzigen, resetten, ontgrendelen en terugschrijven naar een on-premises directory. BasisFuncties voor SSPR zijn gratis beschikbaar voor Office 365 en alle Azure AD-gebruikers.

In dit artikel worden de verschillende manieren beschreven waarop selfservicewachtwoord opnieuw instellen kan worden gelicentieerd en gebruikt. Zie de [prijspagina van Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)voor specifieke informatie over prijzen en facturering.

## <a name="compare-editions-and-features"></a>Edities en functies vergelijken

SSPR heeft een licentie per gebruiker. Om de naleving te handhaven, moeten organisaties de juiste licentie toewijzen aan hun gebruikers.

In de volgende tabel worden de verschillende SSPR-scenario's beschreven voor wachtwoordwijziging, reset of on-premises terugschrijfinformatie en welke SKU's de functie bieden.

| Functie | Azure AD Free | Office 365 Business Premium | Microsoft 365 Business | Azure AD Premium P1 of P2 |
| --- |:---:|:---:|:---:|:---:|
| **Wachtwoordwijziging voor alleen in de cloud**<br />Wanneer een gebruiker in Azure AD zijn wachtwoord kent en het wil wijzigen in iets nieuws. | ● | ● | ● | ● |
| **Wachtwoord opnieuw instellen voor alleen in de cloud**<br />Wanneer een gebruiker in Azure AD zijn wachtwoord is vergeten en het moet resetten. | | ● | ● | ● |
| **Hybride gebruiker wachtwoord wijzigen of opnieuw instellen met on-prem writeback**<br />Wanneer een gebruiker in Azure AD die is gesynchroniseerd vanuit een on-premises directory met Azure AD Connect, zijn wachtwoord wil wijzigen of opnieuw instellen en het nieuwe wachtwoord ook terug schrijft naar on-prem. | | | ● | ● |

> [!WARNING]
> Zelfstandige Office 365-licentieplannen ondersteunen SSPR niet met on-premises terugschrijfgegevens. Voor deze Office 365-licentieplannen is Azure AD Premium P1, Premium P2 of Microsoft 365 Business vereist dat deze functionaliteit werkt.

Zie de volgende pagina's voor aanvullende licentiegegevens, inclusief kosten:

* [Azure Active Directory-prijzen](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory-functies en -mogelijkheden](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Zakelijk](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Licenties op basis van groepen of gebruikers inschakelen

Azure AD ondersteunt groepslicenties. Beheerders kunnen licenties in bulk toewijzen aan een groep gebruikers, in plaats van ze één voor één toe te wijzen. Zie [Problemen met licenties toewijzen, verifiëren en oplossen voor](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)meer informatie.

Sommige Microsoft-services zijn niet op alle locaties beschikbaar. Voordat een licentie aan een gebruiker kan worden toegewezen, moet de beheerder de eigenschap **Gebruikslocatie** van de gebruiker opgeven. Toewijzing van licenties kan worden gedaan onder de sectie > **Gebruikersprofielinstellingen** > **Settings** in de Azure-portal. **User** *Wanneer u groepslicentietoewijzing gebruikt, erven gebruikers zonder opgegeven gebruikslocatie de locatie van de map.*

## <a name="next-steps"></a>Volgende stappen

Voer de volgende zelfstudie in om aan de slag te gaan met SSPR:

> [!div class="nextstepaction"]
> [Zelfstudie: Self-service wachtwoordreset inschakelen (SSPR)](tutorial-enable-sspr.md)

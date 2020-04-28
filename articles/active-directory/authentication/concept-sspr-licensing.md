---
title: Self-service voor wachtwoord herstel van licentie-Azure Active Directory
description: Meer informatie over het verschil Azure Active Directory selfservice voor wachtwoord herstel
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393068"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Licentie vereisten voor Azure Active Directory self-service voor wachtwoord herstel

Gebruikers accounts in Azure Active Directory (Azure AD) kunnen worden ingeschakeld voor de self-service voor wachtwoord herstel (SSPR) om de helpdesk oproepen en productiviteits verlies te verminderen wanneer een gebruiker zich niet kan aanmelden bij hun apparaat of een toepassing. Functies die deel uitmaken van SSPR bevatten wachtwoord wijziging, opnieuw instellen, ontgrendelen en terugschrijven naar een on-premises Directory. De basis functies van SSPR zijn gratis beschikbaar voor Office 365 en alle Azure AD-gebruikers.

In dit artikel vindt u meer informatie over de verschillende manieren waarop self-service voor wachtwoord herstel kan worden gelicentieerd en gebruikt. Zie de [pagina met prijzen voor Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)voor specifieke informatie over prijzen en facturering.

## <a name="compare-editions-and-features"></a>Edities en functies vergelijken

SSPR wordt per gebruiker in licentie gegeven. Voor het behoud van de naleving moeten organisaties de juiste licentie toewijzen aan hun gebruikers.

De volgende tabel bevat een overzicht van de verschillende SSPR-scenario's voor het wijzigen van wacht woorden, resetten of on-premises terugschrijven, en welke Sku's de functie bieden.

| Functie | Azure AD Free | Office 365 Business Premium | Microsoft 365 Business | Azure AD Premium P1 of P2 |
| --- |:---:|:---:|:---:|:---:|
| **Gebruikers wachtwoord wijziging in de Cloud**<br />Wanneer een gebruiker in azure AD het wacht woord kent en wilt wijzigen in een nieuwe waarde. | ● | ● | ● | ● |
| **Alleen Cloud gebruikers wachtwoord opnieuw instellen**<br />Wanneer een gebruiker in azure AD het wacht woord verg eten is en opnieuw moet worden ingesteld. | | ● | ● | ● |
| **Gebruikers wachtwoord wijzigen of opnieuw instellen met on-premises terugschrijven**<br />Wanneer een gebruiker in azure AD die is gesynchroniseerd vanuit een on-premises map met Azure AD Connect het wacht woord wil wijzigen of opnieuw wilt instellen en het nieuwe wacht woord moet worden teruggeschreven naar on-premises. | | | ● | ● |

> [!WARNING]
> Zelfstandige Office 365-licentie plannen bieden geen ondersteuning voor SSPR met on-premises terugschrijven. Voor deze Office 365-licentie plannen is Azure AD Premium P1, Premium P2 of Microsoft 365 Business vereist voor een goede werking van deze functionaliteit.

Zie de volgende pagina's voor aanvullende informatie over licenties, inclusief kosten:

* [Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory functies en mogelijkheden](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Zakelijk](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Op groep of gebruiker gebaseerde licentie verlening inschakelen

Azure AD biedt ondersteuning voor op groepen gebaseerde licentie verlening. Beheerders kunnen licenties bulksgewijs toewijzen aan een groep gebruikers, in plaats van ze een voor een toe te wijzen. Zie [problemen met licenties toewijzen, controleren en oplossen](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)voor meer informatie.

Sommige micro soft-Services zijn niet op alle locaties beschikbaar. Voordat een licentie aan een gebruiker kan worden toegewezen, moet de beheerder de eigenschap **gebruiks locatie** opgeven voor de gebruiker. De toewijzing van licenties kan worden uitgevoerd in de sectie**instellingen** voor **gebruikers** > **profielen** > in de Azure Portal. *Wanneer u de toewijzing van een groeps licentie gebruikt, nemen alle gebruikers waarvoor geen gebruiks locatie is opgegeven, de locatie van de map over.*

## <a name="next-steps"></a>Volgende stappen

Voltooi de volgende zelf studie om aan de slag te gaan met SSPR:

> [!div class="nextstepaction"]
> [Zelf studie: Self-service voor wacht woord opnieuw instellen inschakelen (SSPR)](tutorial-enable-sspr.md)

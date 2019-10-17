---
title: Problemen oplossen met Azure AD-rechten beheer (preview)-Azure Active Directory
description: Meer informatie over sommige items die u moet controleren om u te helpen bij het oplossen van Azure Active Directory rechten beheer (preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea979731c27a8d332102c3215e80510994f2ab3f
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430232"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Problemen oplossen met Azure AD-rechten beheer (preview-versie)

> [!IMPORTANT]
> Azure Active Directory (Azure AD)-rechts beheer is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel worden enkele items beschreven die u moet controleren om te helpen bij het oplossen van het beheer van rechten van Azure Active Directory (Azure AD).

## <a name="checklist-for-entitlement-management-administration"></a>Controle lijst voor beheer van rechten

* Als u een bericht krijgt dat toegang is geweigerd bij het configureren van rechten beheer en u een globale beheerder bent, moet u ervoor zorgen dat uw Directory een [Azure AD Premium P2 (of EMS E5)-licentie](entitlement-management-overview.md#license-requirements)heeft.  
* Als u een bericht krijgt dat toegang wordt geweigerd bij het maken of weer geven van toegangs pakketten en u lid bent van een maker van een catalogus, moet u een catalogus maken voordat u uw eerste toegangs pakket maakt.

## <a name="checklist-for-adding-a-resource"></a>Controle lijst voor het toevoegen van een resource

* Een toepassing kan alleen een resource in een toegangs pakket zijn als deze ten minste één resource functie heeft die kan worden toegewezen. De rollen worden gedefinieerd door de toepassing zelf en worden beheerd in azure AD. Houd er rekening mee dat de Azure Portal ook service-principals kunnen weer geven voor services die niet als toepassingen kunnen worden geselecteerd.  Met name **Exchange Online** en **share point online** zijn services, geen toepassingen die resource rollen hebben in de Directory, zodat ze niet kunnen worden opgenomen in een toegangs pakket.  Gebruik in plaats daarvan op groep gebaseerde licenties om een juiste licentie te maken voor een gebruiker die toegang tot deze services nodig heeft.

* Een groep kan alleen een resource in een toegangs pakket zijn als deze kan worden gewijzigd in azure AD.  Groepen die afkomstig zijn van een on-premises Active Directory, kunnen niet worden toegewezen als resources omdat hun eigen kenmerken of lideigenschappen niet kunnen worden gewijzigd in azure AD.   Groepen die afkomstig zijn van Exchange Online als distributie groep kunnen niet worden gewijzigd in azure AD. 

* Share point online-document bibliotheken en afzonderlijke documenten kunnen niet worden toegevoegd als resources.  Maak in plaats daarvan een Azure AD-beveiligings groep, neem deze groep en een siterol op in het toegangs pakket, en gebruik in share point online die groep om de toegang tot de document bibliotheek of het document te beheren.

* Als er gebruikers zijn die al zijn toegewezen aan een resource die u met een toegangs pakket wilt beheren, moet u ervoor zorgen dat de gebruikers zijn toegewezen aan het toegangs pakket met een toepasselijk beleid. Het is bijvoorbeeld mogelijk dat u een groep wilt toevoegen in een toegangs pakket dat al gebruikers in de groep bevat. Als gebruikers in de groep voortdurende toegang vereisen, moeten ze beschikken over het juiste beleid voor de toegangs pakketten, zodat ze hun toegang tot de groep niet verliezen. U kunt het toegangs pakket toewijzen door de gebruikers te vragen het toegangs pakket met die bron te aanvragen of door ze rechtstreeks toe te wijzen aan het toegangs pakket. Zie [instellingen voor aanvraag en goed keuring wijzigen voor een toegangs pakket](entitlement-management-access-package-request-policy.md)voor meer informatie.

## <a name="checklist-for-providing-external-users-access"></a>Controle lijst voor het bieden van toegang tot externe gebruikers

* Als er een B2B- [acceptatie lijst](../b2b/allow-deny-list.md)is, kunnen gebruikers waarvan de mappen niet zijn toegestaan geen toegang aanvragen.

* Zorg ervoor dat er geen [beleid voor voorwaardelijke toegang](../conditional-access/require-managed-devices.md) is om te voor komen dat externe gebruikers toegang aanvragen of de toepassingen in de toegangs pakketten kunnen gebruiken.

## <a name="checklist-for-request-issues"></a>Controle lijst voor aanvraag problemen

* Wanneer een gebruiker de toegang tot een toegangs pakket wil aanvragen, moet u ervoor zorgen dat ze de **Portal-koppeling van mijn toegang** gebruiken voor het toegangs pakket. Zie [koppeling delen om een toegangs pakket aan te vragen](entitlement-management-access-package-settings.md)voor meer informatie.  Als een externe gebruiker een bezoek stuurt naar **myaccess.Microsoft.com**, zien ze de toegangs pakketten die voor hen beschikbaar zijn in hun eigen organisatie.

* Wanneer een gebruiker die nog niet in uw Directory zich aanmeldt bij de mijn Access-Portal om een toegangs pakket aan te vragen, moet u ervoor zorgen dat ze worden geverifieerd met hun organisatie account. Het organisatie account kan een account in de Resource Directory zijn of een map die is opgenomen in een van de beleids regels van het toegangs pakket. Als het account van de gebruiker geen organisatie account is, of de map waar de authenticatie niet is opgenomen in het beleid, wordt het toegangs pakket niet weer gegeven door de gebruiker. Zie [toegang tot een toegangs pakket aanvragen](entitlement-management-request-access.md)voor meer informatie.

* Als een gebruiker zich niet kan aanmelden bij de resource directory, kunnen ze geen toegang aanvragen in de portal van mijn toegang. Voordat de gebruiker toegang kan aanvragen, moet u het aanmeldings blok verwijderen uit het profiel van de gebruiker. Als u het aanmeldings blok wilt verwijderen, klikt u in het Azure Portal op **Azure Active Directory**, klikt u op **gebruikers**, klikt u op de gebruiker en klikt u vervolgens op **profiel**. Bewerk de sectie **instellingen** en wijzig de **blok aanmelding in** op **Nee**. Zie [de profiel gegevens van een gebruiker toevoegen of bijwerken met behulp van Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md)voor meer informatie.  U kunt ook controleren of de gebruiker is geblokkeerd vanwege een [beleid voor identiteits beveiliging](../identity-protection/howto-unblock-user.md).

* Als een gebruiker zowel een aanvrager als een fiatteur is, wordt in de portal mijn toegang de aanvraag voor een toegangs pakket niet weer geven op de pagina **goed keuringen** . Dit gedrag is opzettelijk: een gebruiker kan hun eigen aanvraag niet goed keuren. Zorg ervoor dat het toegangs pakket dat door de gebruikers wordt aangevraagd, extra goed keurders heeft geconfigureerd op het beleid. Zie [instellingen voor aanvraag en goed keuring wijzigen voor een toegangs pakket](entitlement-management-access-package-request-policy.md)voor meer informatie.

* Als een nieuwe externe gebruiker, die nog niet eerder in uw directory is ondertekend, een toegangs pakket met een share point online-site ontvangt, wordt het toegangs pakket weer gegeven als niet volledig geleverd tot het account is ingericht in share point online.

## <a name="next-steps"></a>Volgende stappen

- [Rapporten weer geven over hoe gebruikers toegang krijgen in het recht beheer](entitlement-management-reports.md)
- [Toegang tot externe gebruikers bepalen](entitlement-management-external-users.md)

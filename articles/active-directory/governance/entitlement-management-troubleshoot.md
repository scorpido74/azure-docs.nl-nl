---
title: Problemen met het beheer van rechten oplossen-Azure AD
description: Meer informatie over sommige items die u moet controleren om u te helpen bij het oplossen van Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: e291a032c1aac45ebc783126e69b524e1d0af95b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422480"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Het beheer van rechten voor Azure AD oplossen

In dit artikel worden enkele items beschreven die u moet controleren om te helpen bij het oplossen van het beheer van rechten van Azure Active Directory (Azure AD).

## <a name="administration"></a>Beheer

* Als u een bericht krijgt dat toegang is geweigerd bij het configureren van rechten beheer en u een globale beheerder bent, moet u ervoor zorgen dat uw Directory een [Azure AD Premium P2 (of EMS E5)-licentie](entitlement-management-overview.md#license-requirements)heeft.

* Als u een bericht krijgt dat toegang wordt geweigerd bij het maken of weer geven van toegangs pakketten en u lid bent van een maker van een catalogus, moet u [een catalogus maken](entitlement-management-catalog-create.md) voordat u uw eerste toegangs pakket maakt.

## <a name="resources"></a>Resources

* Rollen voor toepassingen worden gedefinieerd door de toepassing zelf en worden beheerd in azure AD. Als een toepassing geen resource rollen heeft, wijst het rechten beheer gebruikers toe aan een **standaard-Access** -rol.

    Houd er rekening mee dat de Azure Portal ook service-principals kunnen weer geven voor services die niet als toepassingen kunnen worden geselecteerd.  Met name **Exchange Online** en **share point online** zijn services, geen toepassingen die resource rollen hebben in de Directory, zodat ze niet kunnen worden opgenomen in een toegangs pakket.  Gebruik in plaats daarvan op groep gebaseerde licenties om een juiste licentie te maken voor een gebruiker die toegang tot deze services nodig heeft.

* Een groep kan alleen een resource in een toegangs pakket zijn als deze kan worden gewijzigd in azure AD.  Groepen die afkomstig zijn van een on-premises Active Directory, kunnen niet worden toegewezen als resources omdat hun eigen kenmerken of lideigenschappen niet kunnen worden gewijzigd in azure AD.   Groepen die afkomstig zijn van Exchange Online als distributie groep kunnen niet worden gewijzigd in azure AD. 

* Share point online-document bibliotheken en afzonderlijke documenten kunnen niet worden toegevoegd als resources.  Maak in plaats daarvan een [Azure AD-beveiligings groep](../fundamentals/active-directory-groups-create-azure-portal.md), neem deze groep en een siterol op in het toegangs pakket, en gebruik in share point online die groep om de toegang tot de document bibliotheek of het document te beheren.

* Als er gebruikers zijn die al zijn toegewezen aan een resource die u met een toegangs pakket wilt beheren, moet u ervoor zorgen dat de gebruikers zijn toegewezen aan het toegangs pakket met een toepasselijk beleid. Het is bijvoorbeeld mogelijk dat u een groep wilt toevoegen in een toegangs pakket dat al gebruikers in de groep bevat. Als gebruikers in de groep voortdurende toegang vereisen, moeten ze beschikken over het juiste beleid voor de toegangs pakketten, zodat ze hun toegang tot de groep niet verliezen. U kunt het toegangs pakket toewijzen door de gebruikers te vragen het toegangs pakket met die bron te aanvragen of door ze rechtstreeks toe te wijzen aan het toegangs pakket. Zie [instellingen voor aanvraag en goed keuring wijzigen voor een toegangs pakket](entitlement-management-access-package-request-policy.md)voor meer informatie.

* Wanneer u een lid van een team verwijdert, worden deze ook verwijderd uit de Office 365-groep. Het verwijderen van de chat functionaliteit van het team kan worden vertraagd. Zie [groepslid maatschap](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)voor meer informatie.

* Zorg ervoor dat uw Directory niet is geconfigureerd voor meerdere geografische locaties. Het rechten beheer biedt momenteel geen ondersteuning voor multi-geo-locaties voor share point online. Share point online-sites moeten zich in de standaard geo-locatie beheersen met het rechten beheer. Zie [multi-geo-mogelijkheden in OneDrive en share point online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365)voor meer informatie.

## <a name="external-users"></a>Externe gebruikers

* Wanneer een externe gebruiker de toegang tot een toegangs pakket wil aanvragen, moet u ervoor zorgen dat ze de **Portal-koppeling van mijn toegang** gebruiken voor het toegangs pakket. Zie [koppeling delen om een toegangs pakket aan te vragen](entitlement-management-access-package-settings.md)voor meer informatie. Als een externe gebruiker alleen **myaccess.Microsoft.com** bezoekt en niet de volledige mijn Access-Portal koppeling gebruikt, zien ze de toegangs pakketten die voor hen beschikbaar zijn in hun eigen organisatie en niet in uw organisatie.

* Als een externe gebruiker geen toegang kan aanvragen tot een toegangs pakket of geen toegang kan krijgen tot bronnen, controleert u de [instellingen voor externe gebruikers](entitlement-management-external-users.md#settings-for-external-users).

* Als een nieuwe externe gebruiker, die nog niet eerder in uw directory is ondertekend, een toegangs pakket met een share point online-site ontvangt, wordt het toegangs pakket weer gegeven als niet volledig geleverd tot het account is ingericht in share point online. Zie [uw externe share point Online-instellingen voor delen controleren](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings)voor meer informatie over het delen van instellingen.

## <a name="requests"></a>Aanvragen

* Wanneer een gebruiker de toegang tot een toegangs pakket wil aanvragen, moet u ervoor zorgen dat ze de **Portal-koppeling van mijn toegang** gebruiken voor het toegangs pakket. Zie [koppeling delen om een toegangs pakket aan te vragen](entitlement-management-access-package-settings.md)voor meer informatie.

* Als u de portal mijn toegang opent terwijl uw browser is ingesteld op de persoonlijke of Incognito-modus, kan dit conflicteren met het aanmeldings gedrag. U wordt aangeraden geen in-privé-of Incognito-modus voor uw browser te gebruiken wanneer u de portal van mijn toegang bezoekt.

* Wanneer een gebruiker die nog niet in uw Directory zich aanmeldt bij de mijn Access-Portal om een toegangs pakket aan te vragen, moet u ervoor zorgen dat ze worden geverifieerd met hun organisatie account. Het organisatie account kan een account in de Resource Directory zijn of een map die is opgenomen in een van de beleids regels van het toegangs pakket. Als het account van de gebruiker geen organisatie account is, of de map waar de authenticatie niet is opgenomen in het beleid, wordt het toegangs pakket niet weer gegeven door de gebruiker. Zie [toegang tot een toegangs pakket aanvragen](entitlement-management-request-access.md)voor meer informatie.

* Als een gebruiker zich niet kan aanmelden bij de resource directory, kunnen ze geen toegang aanvragen in de portal van mijn toegang. Voordat de gebruiker toegang kan aanvragen, moet u het aanmeldings blok verwijderen uit het profiel van de gebruiker. Als u het aanmeldings blok wilt verwijderen, klikt u in het Azure Portal op **Azure Active Directory**, klikt u op **gebruikers**, klikt u op de gebruiker en klikt u vervolgens op **profiel**. Bewerk de sectie **instellingen** en wijzig de **blok aanmelding in** op **Nee**. Zie [de profiel gegevens van een gebruiker toevoegen of bijwerken met behulp van Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md)voor meer informatie.  U kunt ook controleren of de gebruiker is geblokkeerd vanwege een [beleid voor identiteits beveiliging](../identity-protection/howto-unblock-user.md).

* Als een gebruiker zowel een aanvrager als een fiatteur is, wordt in de portal mijn toegang de aanvraag voor een toegangs pakket niet weer geven op de pagina **goed keuringen** . Dit gedrag is opzettelijk: een gebruiker kan hun eigen aanvraag niet goed keuren. Zorg ervoor dat het toegangs pakket dat door de gebruikers wordt aangevraagd, extra goed keurders heeft geconfigureerd op het beleid. Zie [instellingen voor aanvraag en goed keuring wijzigen voor een toegangs pakket](entitlement-management-access-package-request-policy.md)voor meer informatie.

### <a name="view-a-requests-delivery-errors"></a>De leverings fouten van een aanvraag weer geven

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Selecteer de aanvraag die u wilt weer geven.

    Als de aanvraag een leverings fout bevat, wordt de status van de aanvraag niet **bezorgd** of **gedeeltelijk bezorgd**.

    Als er bezorgings fouten zijn, wordt in het detail venster van de aanvraag een telling van de bezorgings fouten weer gegeven.

1. Klik op het aantal om alle bezorgings fouten van de aanvraag weer te geven.

### <a name="reprocess-a-request"></a>Een aanvraag opnieuw verwerken

Als een aanvraag een fout tegen komt, kunt u de aanvraag opnieuw verwerken om deze opnieuw te proberen. U kunt alleen een aanvraag met de status **bezorgd** of **gedeeltelijk** opnieuw verwerken, en een voltooide datum van minder dan een week.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Klik op de aanvraag die u opnieuw wilt verwerken.

1. Klik in het deel venster aanvraag Details op **aanvraag opnieuw verwerken**.

    ![Een mislukte aanvraag opnieuw verwerken](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Een aanvraag in behandeling annuleren

U kunt een aanvraag die in behandeling is, alleen annuleren als deze nog niet is bezorgd of waarvoor de bezorging is mislukt.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Klik op de aanvraag die u wilt annuleren.

1. Klik in het deel venster aanvraag Details op **Aanvraag annuleren**.

## <a name="multiple-policies"></a>Meerdere beleids regels

* Voor het beheer van rechten worden de best practices voor de minimale bevoegdheid gevolgd. Wanneer een gebruiker toegang vraagt tot een toegangs pakket dat meerdere beleids regels heeft, is het beheer van rechten de logica om ervoor te zorgen dat strengere of meer specifieke beleids regels worden toegepast via algemeen beleid. Als een beleid algemeen is, kan het beheer van rechten het beleid niet weer geven voor de aanvrager of kan er automatisch een strikter beleid worden geselecteerd.

* Denk bijvoorbeeld aan een toegangs pakket met twee beleids regels voor interne werk nemers waarin beide beleids regels van toepassing zijn op de aanvrager. Het eerste beleid is voor specifieke gebruikers die de aanvrager bevatten. Het tweede beleid geldt voor alle gebruikers in een map waarvan de aanvrager lid is. In dit scenario wordt het eerste beleid automatisch geselecteerd voor de aanvrager omdat het strikter is. De aanvrager krijgt niet de optie om het tweede beleid te selecteren.

* Als er meerdere beleids regels van toepassing zijn, is het beleid dat automatisch wordt geselecteerd of het beleid dat wordt weer gegeven voor de aanvrager gebaseerd op de volgende prioriteits logica:

    | Beleids prioriteit | Scope |
    | --- | --- |
    | P1 | Specifieke gebruikers en groepen in uw Directory of specifieke verbonden organisaties |
    | P2 | Alle leden in uw directory (exclusief gasten) |
    | P3 | Alle gebruikers in uw directory (inclusief gasten) of specifieke verbonden organisaties |
    | P4 | Alle verbonden organisaties of alle gebruikers (alle verbonden organisaties en alle nieuwe externe gebruikers) |
    
    Als een beleid zich in een categorie met hogere prioriteit bevindt, worden de categorieën met lagere prioriteit genegeerd. Zie [een beleid selecteren](entitlement-management-request-access.md#select-a-policy)voor een voor beeld van hoe er meerdere beleids regels met dezelfde prioriteit worden weer gegeven voor de aanvrager.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot externe gebruikers bepalen](entitlement-management-external-users.md)
- [Rapporten weer geven over hoe gebruikers toegang krijgen in het recht beheer](entitlement-management-reports.md)

---
title: Beheer van rechten oplossen - Azure AD
description: Meer informatie over bepaalde items die u moet controleren om problemen op te lossen bij Azure Active Directory-rechtenbeheer.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c38e1a61827da547bb39a699a0e92043e63466c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128477"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Azure AD-rechtenbeheer oplossen

In dit artikel worden enkele items beschreven die u moet controleren om problemen op te lossen bij Azure Active Directory (Azure AD) rechtenbeheer.

## <a name="administration"></a>Beheer

* Als u een bericht met geweigerde toegang krijgt bij het configureren van beheer van rechten en u een globale beheerder bent, moet u ervoor zorgen dat uw directory een [Azure AD Premium P2-licentie (of EMS E5) heeft.](entitlement-management-overview.md#license-requirements)

* Als u een bericht met toegang wordt geweigerd bij het maken of weergeven van toegangspakketten en u bent lid van een makergroep catalogus, moet u [een catalogus maken](entitlement-management-catalog-create.md) voordat u uw eerste toegangspakket maakt.

## <a name="resources"></a>Resources

* Rollen voor toepassingen worden gedefinieerd door de toepassing zelf en worden beheerd in Azure AD. Als een toepassing geen resourcerollen heeft, wijst het rechtbeheer gebruikers toe aan een **standaardtoegangsrol.**

    Houd er rekening mee dat de Azure-portal ook serviceprincipals kan weergeven voor services die niet als toepassingen kunnen worden geselecteerd.  **Exchange Online** en **SharePoint Online** zijn met name services, geen toepassingen met resourcerollen in de map, zodat ze niet in een toegangspakket kunnen worden opgenomen.  Gebruik in plaats daarvan op groepen gebaseerde licenties om een geschikte licentie te maken voor een gebruiker die toegang nodig heeft tot deze services.

* Een groep kan alleen een resource in een toegangspakket zijn als deze kan worden gewijzigd in Azure AD.  Groepen die afkomstig zijn van een on-premises Active Directory, kunnen niet worden toegewezen als resources omdat hun eigenaar- of lidkenmerken niet kunnen worden gewijzigd in Azure AD.   Groepen die afkomstig zijn van Exchange Online als distributiegroep kunnen evenmin in Azure AD worden gewijzigd. 

* SharePoint Online-documentbibliotheken en afzonderlijke documenten kunnen niet worden toegevoegd als resources.  Maak in plaats daarvan een [Azure AD-beveiligingsgroep,](../fundamentals/active-directory-groups-create-azure-portal.md)neem die groep en een siterol op in het toegangspakket en gebruik die groep in SharePoint Online om de toegang tot de documentbibliotheek of het document te beheren.

* Als er gebruikers zijn die al zijn toegewezen aan een resource die u met een toegangspakket wilt beheren, moet u ervoor zorgen dat de gebruikers met een toepasselijk beleid worden toegewezen aan het toegangspakket. Het is bijvoorbeeld mogelijk dat u een groep wilt opnemen in een toegangspakket dat al gebruikers in de groep bevat. Als die gebruikers in de groep voortdurende toegang nodig hebben, moeten ze beschikken over het juiste beleid voor de toegangspakketten, zodat ze hun toegang tot de groep niet verliezen. U kunt het toegangspakket toewijzen door de gebruikers te vragen het toegangspakket met die resource aan te vragen, of door ze rechtstreeks toe te wijzen aan het toegangspakket. Zie [Aanvraag- en goedkeuringsinstellingen voor een toegangspakket wijzigen voor](entitlement-management-access-package-request-policy.md)meer informatie.

* Wanneer u een lid van een team verwijdert, wordt deze persoon ook verwijderd uit de Office 365-groep. Het verwijderen uit de chatfunctionaliteit van het team kan worden uitgesteld. Zie [Groepslidmaatschap](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)voor meer informatie .

* Zorg ervoor dat uw directory niet is geconfigureerd voor Multi-Geo. Rechtenbeheer biedt momenteel geen ondersteuning voor Multi-Geo-locaties voor SharePoint Online. SharePoint Online-sites moeten zich in de standaard geo-locatie bevinden om te worden beheerd met rechtenbeheer. Zie [Multi-Geo-mogelijkheden in OneDrive en SharePoint Online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365)voor meer informatie.

## <a name="access-packages"></a>Toegangspakketten

* Als u een toegangspakket of -beleid probeert te verwijderen en een foutbericht ziet waarin staat dat er actieve toewijzingen zijn, controleert u of onlangs verwijderde gebruikers nog opdrachten hebben als u geen gebruikers met toewijzingen ziet. Tijdens het 30-dagenvenster nadat een gebruiker is verwijderd, kan het gebruikersaccount worden hersteld.   

## <a name="external-users"></a>Externe gebruikers

* Wanneer een externe gebruiker toegang tot een toegangspakket wil aanvragen, controleert u of deze de **koppeling Mijn toegang-portal** voor het toegangspakket gebruikt. Zie [Koppeling delen om een toegangspakket aan te vragen](entitlement-management-access-package-settings.md)voor meer informatie. Als een externe gebruiker gewoon **myaccess.microsoft.com** bezoekt en niet de volledige My Access-portalkoppeling gebruikt, ziet hij de toegangspakketten die voor hen beschikbaar zijn in hun eigen organisatie en niet in uw organisatie.

* Als een externe gebruiker geen toegang kan vragen tot een toegangspakket of geen toegang heeft tot bronnen, controleert u de [instellingen voor externe gebruikers.](entitlement-management-external-users.md#settings-for-external-users)

* Als een nieuwe externe gebruiker die zich nog niet eerder bij uw directory heeft aangemeld een toegangspakket ontvangt met een SharePoint Online site, wordt het toegangspakket weergegeven als niet volledig geleverd totdat het account van de gebruiker in SharePoint Online is ingericht. Zie Instellingen voor extern delen van [SharePoint Online](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings)controleren voor meer informatie over instellingen voor delen.

## <a name="requests"></a>Aanvragen

* Wanneer een gebruiker toegang tot een toegangspakket wil aanvragen, moet u ervoor zorgen dat hij de **koppeling Mijn toegang-portal** voor het toegangspakket gebruikt. Zie [Koppeling delen om een toegangspakket aan te vragen](entitlement-management-access-package-settings.md)voor meer informatie.

* Als u de portal Mijn toegang opent met de browser in privé- of incognitomodus, kan dit conflicteren met het aanmeldingsgedrag. U wordt aangeraden geen in-privé-of incognito-modus voor uw browser te gebruiken wanneer u de portal Mijn toegang bezoekt.

* Wanneer een gebruiker die zich nog niet in uw directory bevindt zich aanmeldt bij de portal Mijn toegang om een toegangspakket aan te vragen, moet deze zich authenticeren met zijn/haar organisatieaccount. Het organisatieaccount kan een account in de resourcedirectory zijn, of in een directory die is opgenomen in een van de beleidsregels van het toegangspakket. Als het account van de gebruiker geen organisatieaccount is, of de directory waar deze zich authenticeert niet is opgenomen in het beleid, kan de gebruiker het toegangspakket niet zien. Zie [Toegang tot een toegangspakket aanvragen voor](entitlement-management-request-access.md)meer informatie .

* Als een gebruiker zich niet kan aanmelden bij de resourcedirectory, kan deze geen toegang aanvragen in de portal Mijn toegang. Voordat de gebruiker toegang kan aanvragen, moet u de aanmeldingsblokkering verwijderen uit het profiel van de gebruiker. Als u het aanmeldingsblok wilt verwijderen, klikt u in de **Azure-portal**op Azure Active Directory , klikt u op **Gebruikers,** klikt u op de gebruiker en klikt u vervolgens op **Profiel**. Bewerk de sectie **Instellingen** en wijzig **het aanmelding voor blokkeren in** **Nee**. Zie [De profielgegevens van een gebruiker toevoegen of bijwerken met Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md)voor meer informatie.  U ook controleren of de gebruiker is geblokkeerd vanwege een [beleid voor identiteitsbescherming.](../identity-protection/howto-unblock-user.md)

* Als een gebruiker in de My Access-portal zowel een aanvrager als een fiatteur is, ziet hij of zij zijn aanvraag voor een toegangspakket niet op de pagina **Goedkeuringen.** Dit gedrag is opzettelijk: een gebruiker kan zijn/haar eigen aanvraag niet goedkeuren. Zorg ervoor dat er meerdere fiatteurs zijn geconfigureerd in het beleid voor het toegangspakket dat de gebruiker aanvraagt. Zie [Aanvraag- en goedkeuringsinstellingen voor een toegangspakket wijzigen voor](entitlement-management-access-package-request-policy.md)meer informatie.

### <a name="view-a-requests-delivery-errors"></a>De leveringsfouten van een aanvraag weergeven

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik **op Aanvragen**.

1. Selecteer het verzoek dat u wilt weergeven.

    Als er leveringsfouten in de aanvraag zijn, wordt de aanvraagstatus **niet geleverd** of **gedeeltelijk geleverd.**

    Als er leveringsfouten zijn, wordt in het detailvenster van de aanvraag een aantal leveringsfouten weergegeven.

1. Klik op het aantal om alle leveringsfouten van de aanvraag te bekijken.

### <a name="reprocess-a-request"></a>Een aanvraag opnieuw verwerken

Als er een fout optreedt bij een aanvraag, u het verzoek opnieuw verwerken om het opnieuw te proberen. U alleen een aanvraag met een status van **levering als mislukt** of gedeeltelijk **geleverd** en een voltooide datum van minder dan een week opnieuw verwerken.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik **op Aanvragen**.

1. Klik op het verzoek dat u opnieuw wilt verwerken.

1. Klik in het deelvenster Details van de aanvraag op **Aanvraag opnieuw verwerken**.

    ![Een mislukte aanvraag opnieuw verwerken](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Een aanvraag in behandeling annuleren

U alleen een aanvraag in behandeling annuleren die nog niet is geleverd of waarvan de levering is mislukt.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik **op Aanvragen**.

1. Klik op het verzoek dat u wilt annuleren.

1. Klik in het deelvenster Details van het verzoek op **Aanvraag annuleren**.

## <a name="multiple-policies"></a>Meerdere beleidsregels

* Het beheer van rechten volgt de beste procedures voor de minste bevoegdheden. Wanneer een gebruiker toegang vraagt tot een toegangspakket met meerdere beleidsregels die van toepassing zijn, bevat het beheer van rechten logica om ervoor te zorgen dat strengere of meer specifieke beleidsregels voorrang krijgen boven algemeen beleid. Als een beleid generiek is, wordt het beleid mogelijk niet aan de aanvrager weergegeven of wordt er mogelijk automatisch een strenger beleid geselecteerd.

* Denk bijvoorbeeld aan een toegangspakket met twee beleidsregels voor interne werknemers waarin beide beleidsregels van toepassing zijn op de aanvrager. Het eerste beleid is voor specifieke gebruikers die de aanvrager bevatten. Het tweede beleid is voor alle gebruikers in een directory waarvan de aanvrager lid is. In dit scenario wordt het eerste beleid automatisch geselecteerd voor de aanvrager of omdat het strenger is. De aanvrager krijgt niet de mogelijkheid om het tweede beleid te selecteren.

* Wanneer meerdere beleidsregels van toepassing zijn, is het beleid dat automatisch wordt geselecteerd of het beleid dat wordt weergegeven aan de aanvrager gebaseerd op de volgende prioriteitslogica:

    | Beleidsprioriteit | Bereik |
    | --- | --- |
    | P1 | Specifieke gebruikers en groepen in uw directory of specifieke verbonden organisaties |
    | P2 | Alle leden in uw directory (met uitzondering van gasten) |
    | P3 | Alle gebruikers in uw directory (inclusief gasten) of specifieke verbonden organisaties |
    | P4 | Alle verbonden organisaties OF Alle gebruikers (alle verbonden organisaties + nieuwe externe gebruikers) |
    
    Als een beleid zich in een categorie met een hogere prioriteit bevindt, worden de categorieën met een lagere prioriteit genegeerd. Zie [Een beleid selecteren](entitlement-management-request-access.md#select-a-policy)voor een voorbeeld van hoe meerdere beleidsregels met dezelfde prioriteit worden weergegeven aan de aanvrager .

## <a name="next-steps"></a>Volgende stappen

- [Toegang voor externe gebruikers beheren](entitlement-management-external-users.md)
- [Rapporten weergeven over hoe gebruikers toegang kregen in het beheer van rechten](entitlement-management-reports.md)

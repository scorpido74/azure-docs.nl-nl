---
title: bestand opnemen
description: bestand opnemen
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: aba3dd34383edbfb555adcc2063e4a2b76af1959
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389387"
---
## <a name="for-users-in-your-directory"></a>Voor gebruikers in uw Directory

Volg deze stappen als u wilt toestaan dat gebruikers in uw directory dit toegangs pakket kunnen aanvragen. Wanneer u het aanvraag beleid definieert, kunt u afzonderlijke gebruikers of meer vaak groepen gebruikers opgeven. Uw organisatie kan bijvoorbeeld al een groep hebben, zoals **alle werk nemers**.  Als deze groep wordt toegevoegd aan het beleid voor gebruikers die toegang kunnen aanvragen, kan een lid van die groep vervolgens toegang vragen.

1. Selecteer in de lijst **gebruikers die toegang kunnen aanvragen** , **de optie voor gebruikers in uw directory**.

    Wanneer u deze optie selecteert, worden er nieuwe opties weer gegeven om te verfijnen wie in uw directory dit toegangs pakket kan aanvragen.

    ![Toegang tot pakket aanvragen: voor gebruikers in uw Directory](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Selecteer een van de volgende opties:

    |  |  |
    | --- | --- |
    | **Specifieke gebruikers en groepen** | Kies deze optie als u wilt dat alleen de gebruikers en groepen in uw directory die u opgeeft, dit toegangs pakket kunnen aanvragen. |
    | **Alle leden (exclusief gasten)** | Kies deze optie als u wilt dat alle gebruikers in uw directory dit toegangs pakket kunnen aanvragen. Deze optie bevat geen gast gebruikers die u mogelijk hebt uitgenodigd voor uw Directory. |
    | **Alle gebruikers (inclusief gasten)** | Selecteer deze optie als u wilt dat alle leden van gebruikers en gast gebruikers in uw directory dit toegangs pakket kunnen aanvragen. |

    Gast gebruikers verwijzen naar externe gebruikers die zijn uitgenodigd voor uw directory met [Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md). Zie [Wat zijn de standaard machtigingen voor gebruikers in azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md)voor informatie over de verschillen tussen gebruikers van het lid en gast gebruikers.

1. Als u **specifieke gebruikers en groepen**hebt geselecteerd, klikt u op **gebruikers en groepen toevoegen**.

1. Selecteer in het deel venster gebruikers en groepen selecteren de gebruikers en groepen die u wilt toevoegen.

    ![Toegangs pakket-aanvragen-gebruikers en groepen selecteren](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Klik op **selecteren** om de gebruikers en groepen toe te voegen.

1. De sectie [goed keuring](#approval) overs Laan.

## <a name="for-users-not-in-your-directory"></a>Voor gebruikers die niet in uw Directory voor komt

Volg deze stappen als u wilt toestaan dat gebruikers in uw directory dit toegangs pakket niet kunnen aanvragen. De **gebruikers die zich niet in uw directory** bevinden, verwijzen naar gebruikers in een andere Azure AD-Directory of een ander domein en zijn mogelijk nog niet in uw directory uitgenodigd. Azure AD-mappen moeten zodanig worden geconfigureerd dat uitnodigingen in **samenwerkings beperkingen**worden toegestaan. Zie [externe B2B-samen werking inschakelen en beheren wie gasten kan uitnodigen](../articles/active-directory/b2b/delegate-invitations.md)voor meer informatie.

> [!NOTE]
> Er wordt een gast gebruikers account voor een gebruiker gemaakt dat nog niet in uw directory is waarvan de aanvraag is goedgekeurd of automatisch goedgekeurd. De gast wordt uitgenodigd, maar ontvangt geen uitnodigings-e-mail. In plaats daarvan ontvangen ze een e-mail bericht wanneer hun toegangs pakket toewijzing wordt bezorgd. Later, wanneer deze gast gebruiker geen toegangs pakket toewijzingen meer heeft, omdat de laatste toewijzing is verlopen of is geannuleerd, wordt het gast gebruikers account geblokkeerd om zich aan te melden en vervolgens te verwijderen. Als u wilt dat gast gebruikers voor onbepaalde tijd in uw Directory blijven, zelfs als ze geen toegangs pakket toewijzingen hebben, kunt u de instellingen voor uw rechten beheer configuratie wijzigen. Zie [Eigenschappen van een Azure Active Directory B2B-samenwerkings gebruiker](../articles/active-directory/b2b/user-properties.md)voor meer informatie over het gast gebruikers object.

1. Selecteer in de **gebruikers die toegang kunnen aanvragen** , de optie **voor gebruikers die zich niet in uw adres**lijst bevindt.

    Wanneer u deze optie selecteert, worden er nieuwe opties weer gegeven.

    ![Toegang tot pakket aanvragen: voor gebruikers die niet in uw Directory voor komt](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Selecteer een van de volgende opties:

    |  |  |
    | --- | --- |
    | **Specifieke verbonden organisaties** | Kies deze optie als u een selectie wilt selecteren in een lijst met organisaties die de beheerder eerder heeft toegevoegd. Alle gebruikers van de geselecteerde organisaties kunnen dit toegangs pakket aanvragen. |
    | **Alle verbonden organisaties** | Kies deze optie als u wilt dat alle gebruikers van alle verbonden organisaties dit toegangs pakket kunnen aanvragen. |

    Een verbonden organisatie is een extern Azure AD-adres lijst of-domein waarmee u regel matig samenwerkt.

1. Als u **specifieke verbonden organisaties**hebt geselecteerd, klikt u op **mappen toevoegen** om te selecteren in een lijst met verbonden organisaties die de beheerder eerder heeft toegevoegd.

1. Voer een domein naam in om te zoeken naar een verbonden organisatie met die domein naam.

    ![Toegangs pakket-aanvragen-mappen selecteren](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Als de organisatie waarmee u wilt samen werken, zich niet in de lijst bevindt, kunt u uw beheerder vragen om deze toe te voegen als een verbonden organisatie. 

1. Wanneer u alle verbonden organisaties hebt geselecteerd, klikt u op **selecteren**.

    > [!NOTE]
    > Alle gebruikers van de geselecteerde verbonden organisaties kunnen dit toegangs pakket aanvragen. Dit omvat gebruikers uit alle subdomeinen die zijn gekoppeld aan de verbonden organisaties.

1. De sectie [goed keuring](#approval) overs Laan.

## <a name="none-administrator-direct-assignments-only"></a>Geen (alleen directe toewijzingen voor beheerders)

Volg deze stappen als u toegangs aanvragen wilt overs Laan en wilt toestaan dat beheerders rechtstreeks specifieke gebruikers aan dit toegangs pakket toewijzen. Gebruikers hoeven het toegangs pakket niet aan te vragen. U kunt nog steeds levenscyclus instellingen instellen, maar er zijn geen aanvraag instellingen.

1. Selecteer in de **gebruikers die toegang kunnen aanvragen** , de optie **geen (alleen directe toewijzingen beheerder**.

    ![Toegang tot pakket-requests-geen alleen directe toewijzingen voor beheerders](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Nadat u het toegangs pakket hebt gemaakt, kunt u rechtstreeks specifieke interne en externe gebruikers aan het toegangs pakket toewijzen. Als u een externe gebruiker opgeeft, wordt er een gast gebruikers account in uw directory gemaakt. Zie [toewijzingen voor een toegangs pakket weer geven en wijzigen](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)voor meer informatie over het rechtstreeks toewijzen van een gebruiker.

1. Ga verder naar de sectie [aanvragen inschakelen](#enable-requests) .

## <a name="approval"></a>Goedkeuring

In de sectie goed keuring geeft u op of een goed keuring is vereist wanneer gebruikers dit toegangs pakket aanvragen. De goedkeurings instellingen werken op de volgende manier:

- Slechts een van de geselecteerde goed keurders of terugval fiatteurs moet een aanvraag goed keuren. Goed keuring van alle goed keurders is niet vereist.
- De goedkeurings beslissing is gebaseerd op welke fiatteur de aanvraag eerst bekijkt.

1. Als u goed keuring wilt vereisen voor aanvragen van de geselecteerde gebruikers, stelt u de schakel optie **goed keuring vereisen** in op **Ja**. Als u wilt dat aanvragen automatisch worden goedgekeurd, stelt u de wissel knop in op **Nee**.

    ![Toegangs aanvragen-goedkeurings instellingen voor het pakket](./media/active-directory-entitlement-management-request-policy/approval.png)

1. Als u wilt dat gebruikers een reden opgeven om het toegangs pakket aan te vragen, stelt u de optie uitschakeling van **aanvrager vereisen** in op **Ja**.

1. Bepaal of de aanvraag één of meerdere fasen moet goed keuren. Stel in hoeveel **fasen** **moeten worden in-** of uitgeschakeld voor één fase.

1. Voor goed keurders selecteert u **Manager als fiatteur** of **kiest u specifieke goed keurders**.

    De Manager wordt bepaald door het kenmerk **Manager** in het profiel van de gebruiker van Azure AD. Zie [de profiel gegevens van een gebruiker toevoegen of bijwerken met behulp van Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)voor meer informatie.

    ![Azure Active Directory gebruikers profiel-kenmerk Manager](./media/active-directory-entitlement-management-request-policy/profile-manager.png)

1. Als u Manager als fiatteur hebt geselecteerd, klikt u op **terugval toevoegen** om een of meer gebruikers of groepen in uw directory te selecteren als terugval fiatteur in het geval van rechten beheer kan de Manager niet vinden.

1. Als u specifieke goed keurders kiezen hebt geselecteerd, klikt u op goed keurders **toevoegen** om een of meer gebruikers of groepen in uw directory te selecteren voor goed keurders.

1. In de **beslissing moet worden bepaald hoeveel dagen?** het aantal dagen dat een fiatteur een aanvraag voor dit toegangs pakket heeft gecontroleerd.

    Als een aanvraag niet binnen deze tijds periode wordt goedgekeurd, wordt deze automatisch geweigerd. De gebruiker moet een andere aanvraag indienen voor het toegangs pakket.

1. Als u wilt dat gebruikers een reden opgeven om het toegangs pakket aan te vragen, stelt u in **dat** moet worden ingesteld op **Ja**.

    Een motivering is zichtbaar voor andere goed keurders en de aanvrager.

## <a name="enable-requests"></a>Aanvragen inschakelen

1. Als u wilt dat het toegangs pakket onmiddellijk beschikbaar wordt gesteld voor gebruikers in het aanvraag beleid om aan te vragen, klikt u op **Ja** om in te scha kelen.

    U kunt deze altijd in de toekomst inschakelen nadat u klaar bent met het maken van het toegangs pakket.

    Als u geen hebt geselecteerd **(alleen voor beheerders directe toewijzingen)** en u inschakelen op **Nee**, kunnen beheerders dit toegangs pakket niet rechtstreeks toewijzen.

    ![Toegangs pakket-beleids instelling beleid inschakelen](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Klik op **Volgende**.

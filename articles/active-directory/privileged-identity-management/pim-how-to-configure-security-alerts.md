---
title: Beveiligingswaarschuwingen voor Azure AD-rollen in PIM - Azure AD | Microsoft Documenten
description: Beveiligingswaarschuwingen configureren voor Azure AD-rollen Privileged Identity Management in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86dbcdc24c90ba8b161b041af96cbdd0665ad827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253310"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Beveiligingswaarschuwingen configureren voor Azure AD-rollen in Privileged Identity Management

Pim (Privileged Identity Management) genereert waarschuwingen wanneer er verdachte of onveilige activiteiten zijn in uw Azure AD-organisatie (Azure Directory). Wanneer een waarschuwing wordt geactiveerd, wordt deze weergegeven op het dashboard Privileged Identity Management. Selecteer de waarschuwing om een rapport te zien met de gebruikers of rollen die de waarschuwing hebben geactiveerd.

## <a name="determine-your-version-of-pim"></a>Bepaal uw versie van PIM

Vanaf november 2019 wordt het gedeelte Azure AD-rollen van Privileged Identity Management bijgewerkt naar een nieuwe versie die overeenkomt met de ervaringen voor Azure-bronrollen. Dit zorgt voor extra functies en [wijzigingen in de bestaande API.](azure-ad-roles-features.md#api-changes) Terwijl de nieuwe versie wordt uitgerold, zijn welke procedures u in dit artikel volgt, afhankelijk van de versie van Privileged Identity Management die u momenteel hebt. Volg de stappen in deze sectie om te bepalen welke versie van Privileged Identity Management u hebt. Nadat u uw versie van Privileged Identity Management hebt weten, u de procedures in dit artikel selecteren die overeenkomen met die versie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een gebruiker die zich in de rol [Van Privileged role administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) bevindt.
1. Azure **AD Privileged Identity Management openen**. Als u boven aan de overzichtspagina een banner hebt staan, volgt u de instructies op het tabblad **Nieuwe versie** van dit artikel. Volg anders de instructies op het tabblad **Vorige versie.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Volg de stappen in dit artikel om beveiligingswaarschuwingen voor Azure AD-rollen te onderzoeken.

# <a name="new-version"></a>[Nieuwe versie](#tab/new)

![Azure AD-rollen - Waarschuwingen voor waarschuwingen en de ernst](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Beveiligingswaarschuwingen

In deze sectie worden alle beveiligingswaarschuwingen voor Azure AD-rollen weergegeven, samen met hoe u oplossen en hoe u deze voorkomen. Ernst heeft de volgende betekenis:

- **Hoog**: Vereist onmiddellijke actie vanwege een beleidsschending.
- **Medium:** Vereist geen onmiddellijke actie, maar signaleert een mogelijke schending van het beleid.
- **Laag**: Vereist geen onmiddellijke actie, maar suggereert een voorkeursbeleidswijziging.

### <a name="administrators-arent-using-their-privileged-roles"></a>Beheerders gebruiken hun bevoorrechte rollen niet

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | Gebruikers die bevoorrechte rollen hebben toegewezen die ze niet nodig hebben, vergroot de kans op een aanval. Het is ook gemakkelijker voor aanvallers om onopgemerkt te blijven in accounts die niet actief worden gebruikt. |
| **Hoe op te lossen?** | Bekijk de gebruikers in de lijst en verwijder ze uit bevoorrechte rollen die ze niet nodig hebben. |
| **Preventie** | Wijs bevoorrechte rollen alleen toe aan gebruikers die een zakelijke rechtvaardiging hebben. </br>Plan regelmatig [toegangsbeoordelingen](pim-how-to-start-security-review.md) om te controleren of gebruikers nog steeds toegang nodig hebben. |
| **In-portal mitigatieactie** | Hiermee verwijdert u het account uit hun bevoorrechte rol. |
| **Trigger** | Geactiveerd als een gebruiker een bepaald aantal dagen doorloopt zonder een rol te activeren. |
| **Aantal dagen** | Met deze instelling geeft u het maximum aantal dagen aan, van 0 tot 100, dat een gebruiker kan gaan zonder een rol te activeren.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Rollen vereisen geen meervoudige verificatie voor activering

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | Zonder meervoudige verificatie kunnen gecompromitteerde gebruikers bevoorrechte rollen activeren. |
| **Hoe op te lossen?** | Bekijk de lijst met rollen en [vereist multi-factor authenticatie](pim-how-to-change-default-settings.md) voor elke rol. |
| **Preventie** | [Vereisen MFA](pim-how-to-change-default-settings.md) voor elke rol.  |
| **In-portal mitigatieactie** | Maakt multi-factor authenticatie vereist voor activering van de bevoorrechte rol. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>De organisatie heeft geen Azure AD Premium P2

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | De huidige Azure AD-organisatie heeft geen Azure AD Premium P2. |
| **Hoe op te lossen?** | Bekijk informatie over [Azure AD-edities](../fundamentals/active-directory-whatis.md). Upgrade naar Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potentiële verouderde accounts in een bevoorrechte rol

| | |
| --- | --- |
| **Ernst** | Middelgroot |
| **Waarom krijg ik deze waarschuwing?** | Accounts in een bevoorrechte rol hebben hun wachtwoord in de afgelopen 90 dagen niet gewijzigd. Deze accounts kunnen service- of gedeelde accounts zijn die niet worden onderhouden en kwetsbaar zijn voor aanvallers. |
| **Hoe op te lossen?** | Bekijk de accounts in de lijst. Als ze geen toegang meer nodig hebben, verwijder ze dan uit hun bevoorrechte rollen. |
| **Preventie** | Zorg ervoor dat accounts die worden gedeeld sterke wachtwoorden roteren wanneer er een wijziging is in de gebruikers die het wachtwoord kennen. </br>Controleer regelmatig accounts met bevoorrechte rollen met [toegangsbeoordelingen](pim-how-to-start-security-review.md) en verwijder roltoewijzingen die niet langer nodig zijn. |
| **In-portal mitigatieactie** | Hiermee verwijdert u het account uit hun bevoorrechte rol. |
| **Aanbevolen procedures** | Accounts voor gedeelde, service- en noodtoegangsaccounts die zich verifiëren met een wachtwoord en zijn toegewezen aan zeer bevoorrechte beheerdersrollen, zoals globale beheerder of beveiligingsbeheerder, moeten hun wachtwoorden laten roteren voor de volgende gevallen:<ul><li>Na een beveiligingsincident met misbruik of compromis van administratieve toegangsrechten</li><li>Nadat de bevoegdheden van een gebruiker zijn gewijzigd, zodat deze geen beheerder meer is (bijvoorbeeld nadat een werknemer die een beheerder was, IT verlaat of de organisatie verlaat)</li><li>Op regelmatige tijdstippen (bijvoorbeeld drie- of jaarlijks), zelfs als er geen bekende inbreuk of wijziging in IT-personeel</li></ul>Aangezien meerdere personen toegang hebben tot de referenties van deze accounts, moeten de referenties worden geroteerd om ervoor te zorgen dat mensen die hun rol hebben verlaten, geen toegang meer hebben tot de accounts. [Meer informatie over het beveiligen van accounts](../users-groups-roles/directory-admin-roles-secure.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Rollen worden toegewezen buiten Privileged Identity Management

| | |
| --- | --- |
| **Ernst** | Hoog |
| **Waarom krijg ik deze waarschuwing?** | Bevoorrechte roltoewijzingen die buiten Privileged Identity Management worden uitgevoerd, worden niet goed gecontroleerd en kunnen duiden op een actieve aanval. |
| **Hoe op te lossen?** | Controleer de gebruikers in de lijst en verwijder deze uit bevoorrechte rollen die buiten Privileged Identity Management zijn toegewezen. |
| **Preventie** | Onderzoek waar gebruikers bevoorrechte rollen toegewezen krijgen buiten Privileged Identity Management en verbied toekomstige toewijzingen vanaf daar. |
| **In-portal mitigatieactie** | Hiermee verwijdert u de gebruiker uit zijn of haar bevoorrechte rol. |

### <a name="there-are-too-many-global-administrators"></a>Er zijn te veel globale beheerders

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | Globale beheerder is de hoogste bevoorrechte rol. Als een globale beheerder wordt gecompromitteerd, krijgt de aanvaller toegang tot al zijn machtigingen, waardoor uw hele systeem in gevaar komt. |
| **Hoe op te lossen?** | Bekijk de gebruikers in de lijst en verwijder gebruikers die de rol globale beheerder niet absoluut nodig hebben. </br>Wijs in plaats daarvan lagere bevoorrechte rollen toe aan deze gebruikers. |
| **Preventie** | Wijs gebruikers de minst bevoorrechte rol toe die ze nodig hebben. |
| **In-portal mitigatieactie** | Hiermee verwijdert u het account uit hun bevoorrechte rol. |
| **Trigger** | Geactiveerd als aan twee verschillende criteria wordt voldaan en u ze allebei configureren. Eerst moet u een bepaalde drempel van globale beheerders bereiken. Ten tweede moet een bepaald percentage van uw totale roltoewijzingen globale beheerders zijn. Als u slechts aan één van deze metingen voldoet, wordt de waarschuwing niet weergegeven. |
| **Minimumaantal globale beheerders** | Met deze instelling geeft u het aantal globale beheerders op, van 2 tot 100, dat u als te weinig beschouwt voor uw Azure AD-organisatie. |
| **Percentage globale beheerders** | Met deze instelling geeft u het minimumpercentage beheerders op dat globale beheerders zijn, van 0% tot 100%, waaronder u niet wilt dat uw Azure AD-organisatie ondergaat. |

### <a name="roles-are-being-activated-too-frequently"></a>Rollen worden te vaak geactiveerd

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | Meerdere activeringen naar dezelfde bevoorrechte rol door dezelfde gebruiker is een teken van een aanval. |
| **Hoe op te lossen?** | Controleer de gebruikers in de lijst en zorg ervoor dat de [activeringsduur](pim-how-to-change-default-settings.md) voor hun bevoorrechte rol lang genoeg is ingesteld om hun taken uit te voeren. |
| **Preventie** | Controleer of de [activeringsduur](pim-how-to-change-default-settings.md) voor bevoorrechte rollen lang genoeg is ingesteld zodat gebruikers hun taken kunnen uitvoeren.</br>[Vereist meervoudige verificatie](pim-how-to-change-default-settings.md) voor bevoorrechte rollen waarvoor accounts zijn gedeeld door meerdere beheerders. |
| **In-portal mitigatieactie** | N.v.t. |
| **Trigger** | Geactiveerd als een gebruiker dezelfde bevoorrechte rol meerdere keren activeert binnen een bepaalde periode. U zowel de periode als het aantal activeringen configureren. |
| **Tijdsperiode activeringsvernieuwing** | Deze instelling geeft in dagen, uren, minuten en ten tweede de tijdsperiode op die u wilt gebruiken om verdachte verlengingen bij te houden. |
| **Aantal activeringsverlengingen** | Met deze instelling geeft u het aantal activeringen op, van 2 tot 100, waarop u binnen het door u gekozen tijdsbestek een melding wilt ontvangen. U deze instelling wijzigen door de schuifregelaar te verplaatsen of een getal in het tekstvak te typen. |

## <a name="configure-security-alert-settings"></a>Instellingen voor beveiligingswaarschuwingen configureren

Ga op de pagina Waarschuwingen naar **Instellingen**.

![Pagina Waarschuwingen met gemarkeerde instellingen](media/pim-how-to-configure-security-alerts/alert-settings.png)

Pas instellingen aan voor de verschillende waarschuwingen om te werken met uw omgeving en beveiligingsdoelen.

![Pagina instellen voor een waarschuwing om instellingen in te schakelen en te configureren](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

# <a name="previous-version"></a>[Vorige versie](#tab/previous)

![Azure AD-rollen - Waarschuwingen voor waarschuwingen en de ernst](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Beveiligingswaarschuwingen

In deze sectie worden alle beveiligingswaarschuwingen voor Azure AD-rollen weergegeven, samen met hoe u oplossen en hoe u deze voorkomen. Ernst heeft de volgende betekenis:

- **Hoog**: Vereist onmiddellijke actie vanwege een beleidsschending.
- **Medium:** Vereist geen onmiddellijke actie, maar signaleert een mogelijke schending van het beleid.
- **Laag**: Vereist geen onmiddellijke actie, maar suggereert een voorkeursbeleidswijziging.

### <a name="administrators-arent-using-their-privileged-roles"></a>Beheerders gebruiken hun bevoorrechte rollen niet

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | Gebruikers die bevoorrechte rollen hebben toegewezen die ze niet nodig hebben, vergroot de kans op een aanval. Het is ook gemakkelijker voor aanvallers om onopgemerkt te blijven in accounts die niet actief worden gebruikt. |
| **Hoe op te lossen?** | Bekijk de gebruikers in de lijst en verwijder ze uit bevoorrechte rollen die ze niet nodig hebben. |
| **Preventie** | Wijs bevoorrechte rollen alleen toe aan gebruikers die een zakelijke rechtvaardiging hebben. </br>Plan regelmatig [toegangsbeoordelingen](pim-how-to-start-security-review.md) om te controleren of gebruikers nog steeds toegang nodig hebben. |
| **In-portal mitigatieactie** | Hiermee verwijdert u het account uit hun bevoorrechte rol. |
| **Trigger** | Geactiveerd als een gebruiker een bepaald aantal dagen doorloopt zonder een rol te activeren. |
| **Aantal dagen** | Met deze instelling geeft u het maximum aantal dagen aan, van 0 tot 100, dat een gebruiker kan gaan zonder een rol te activeren.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Rollen vereisen geen meervoudige verificatie voor activering

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | Zonder meervoudige verificatie kunnen gecompromitteerde gebruikers bevoorrechte rollen activeren. |
| **Hoe op te lossen?** | Bekijk de lijst met rollen en [vereist multi-factor authenticatie](pim-how-to-change-default-settings.md) voor elke rol. |
| **Preventie** | [Vereisen MFA](pim-how-to-change-default-settings.md) voor elke rol.  |
| **In-portal mitigatieactie** | Maakt multi-factor authenticatie vereist voor activering van de bevoorrechte rol. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>De organisatie heeft geen Azure AD Premium P2

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | De huidige Azure AD-organisatie heeft geen Azure AD Premium P2. |
| **Hoe op te lossen?** | Bekijk informatie over [Azure AD-edities](../fundamentals/active-directory-whatis.md). Upgrade naar Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potentiële verouderde accounts in een bevoorrechte rol

| | |
| --- | --- |
| **Ernst** | Middelgroot |
| **Waarom krijg ik deze waarschuwing?** | Accounts in een bevoorrechte rol hebben hun wachtwoord in de afgelopen 90 dagen niet gewijzigd. Deze accounts kunnen service- of gedeelde accounts zijn die niet worden onderhouden en kwetsbaar zijn voor aanvallers. |
| **Hoe op te lossen?** | Bekijk de accounts in de lijst. Als ze geen toegang meer nodig hebben, verwijder ze dan uit hun bevoorrechte rollen. |
| **Preventie** | Zorg ervoor dat accounts die worden gedeeld sterke wachtwoorden roteren wanneer er een wijziging is in de gebruikers die het wachtwoord kennen. </br>Controleer regelmatig accounts met bevoorrechte rollen met [toegangsbeoordelingen](pim-how-to-start-security-review.md) en verwijder roltoewijzingen die niet langer nodig zijn. |
| **In-portal mitigatieactie** | Hiermee verwijdert u het account uit hun bevoorrechte rol. |
| **Aanbevolen procedures** | Accounts voor gedeelde, service- en noodtoegangsaccounts die zich verifiëren met een wachtwoord en zijn toegewezen aan zeer bevoorrechte beheerdersrollen, zoals globale beheerder of beveiligingsbeheerder, moeten hun wachtwoorden laten roteren voor de volgende gevallen:<ul><li>Na een beveiligingsincident met misbruik of compromis van administratieve toegangsrechten</li><li>Nadat de bevoegdheden van een gebruiker zijn gewijzigd, zodat deze geen beheerder meer is (bijvoorbeeld nadat een werknemer die een beheerder was, IT verlaat of de organisatie verlaat)</li><li>Op regelmatige tijdstippen (bijvoorbeeld drie- of jaarlijks), zelfs als er geen bekende inbreuk of wijziging in IT-personeel</li></ul>Aangezien meerdere personen toegang hebben tot de referenties van deze accounts, moeten de referenties worden geroteerd om ervoor te zorgen dat mensen die hun rol hebben verlaten, geen toegang meer hebben tot de accounts. [Meer informatie](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Rollen worden toegewezen buiten Privileged Identity Management

| | |
| --- | --- |
| **Ernst** | Hoog |
| **Waarom krijg ik deze waarschuwing?** | Bevoorrechte roltoewijzingen die buiten Privileged Identity Management worden uitgevoerd, worden niet goed gecontroleerd en kunnen duiden op een actieve aanval. |
| **Hoe op te lossen?** | Controleer de gebruikers in de lijst en verwijder deze uit bevoorrechte rollen die buiten Privileged Identity Management zijn toegewezen. |
| **Preventie** | Onderzoek waar gebruikers bevoorrechte rollen toegewezen krijgen buiten Privileged Identity Management en verbied toekomstige toewijzingen vanaf daar. |
| **In-portal mitigatieactie** | Hiermee verwijdert u de gebruiker uit zijn of haar bevoorrechte rol. |

### <a name="there-are-too-many-global-administrators"></a>Er zijn te veel globale beheerders

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | Globale beheerder is de hoogste bevoorrechte rol. Als een globale beheerder wordt gecompromitteerd, krijgt de aanvaller toegang tot al zijn machtigingen, waardoor uw hele systeem in gevaar komt. |
| **Hoe op te lossen?** | Bekijk de gebruikers in de lijst en verwijder gebruikers die de rol globale beheerder niet absoluut nodig hebben. </br>Wijs in plaats daarvan lagere bevoorrechte rollen toe aan deze gebruikers. |
| **Preventie** | Wijs gebruikers de minst bevoorrechte rol toe die ze nodig hebben. |
| **In-portal mitigatieactie** | Hiermee verwijdert u het account uit hun bevoorrechte rol. |
| **Trigger** | Geactiveerd als aan twee verschillende criteria wordt voldaan en u ze allebei configureren. Eerst moet u een bepaalde drempel van globale beheerders bereiken. Ten tweede moet een bepaald percentage van uw totale roltoewijzingen globale beheerders zijn. Als u slechts aan één van deze metingen voldoet, wordt de waarschuwing niet weergegeven. |
| **Minimumaantal globale beheerders** | Met deze instelling geeft u het aantal globale beheerders op, van 2 tot 100, dat u als te weinig beschouwt voor uw Azure AD-organisatie. |
| **Percentage globale beheerders** | Met deze instelling geeft u het minimumpercentage beheerders op dat globale beheerders zijn, van 0% tot 100%, waaronder u niet wilt dat uw Azure AD-organisatie ondergaat. |

### <a name="roles-are-being-activated-too-frequently"></a>Rollen worden te vaak geactiveerd

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | Meerdere activeringen naar dezelfde bevoorrechte rol door dezelfde gebruiker is een teken van een aanval. |
| **Hoe op te lossen?** | Controleer de gebruikers in de lijst en zorg ervoor dat de [activeringsduur](pim-how-to-change-default-settings.md) voor hun bevoorrechte rol lang genoeg is ingesteld om hun taken uit te voeren. |
| **Preventie** | Controleer of de [activeringsduur](pim-how-to-change-default-settings.md) voor bevoorrechte rollen lang genoeg is ingesteld zodat gebruikers hun taken kunnen uitvoeren.</br>[Vereist meervoudige verificatie](pim-how-to-change-default-settings.md) voor bevoorrechte rollen waarvoor accounts zijn gedeeld door meerdere beheerders. |
| **In-portal mitigatieactie** | N.v.t. |
| **Trigger** | Geactiveerd als een gebruiker dezelfde bevoorrechte rol meerdere keren activeert binnen een bepaalde periode. U zowel de periode als het aantal activeringen configureren. |
| **Tijdsperiode activeringsvernieuwing** | Deze instelling geeft in dagen, uren, minuten en ten tweede de tijdsperiode op die u wilt gebruiken om verdachte verlengingen bij te houden. |
| **Aantal activeringsverlengingen** | Met deze instelling geeft u het aantal activeringen op, van 2 tot 100, waarop u binnen het door u gekozen tijdsbestek een melding wilt ontvangen. U deze instelling wijzigen door de schuifregelaar te verplaatsen of een getal in het tekstvak te typen. |

## <a name="configure-security-alert-settings"></a>Instellingen voor beveiligingswaarschuwingen configureren

U een aantal beveiligingswaarschuwingen in Privileged Identity Management aanpassen om te werken met de behoeften en beveiligingsdoelen van uw organisatie. Voer de volgende stappen uit om de instellingen voor beveiligingswaarschuwingen te openen:

1. Open **Privileged Identity Management** in Azure AD.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Instellingen** en vervolgens **Waarschuwingen**.

    ![Azure AD-rollen - Instellingen met waarschuwingen geselecteerd](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Selecteer een waarschuwingsnaam om de instelling voor die waarschuwing te configureren.

    ![Voor de geselecteerde waarschuwing, deelvenster beveiligingswaarschuwingen](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rolinstellingen configureren in Privileged Identity Management](pim-how-to-change-default-settings.md)

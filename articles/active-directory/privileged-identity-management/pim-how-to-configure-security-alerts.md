---
title: Beveiligings waarschuwingen configureren voor Azure AD-rollen in PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het configureren van beveiligings waarschuwingen voor Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3984d3276590357866c824d01ea8c51cf5b28fd7
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804384"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-pim"></a>Beveiligings waarschuwingen configureren voor Azure AD-rollen in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) genereert waarschuwingen wanneer er verdachte of onveilige activiteiten in uw omgeving zijn. Wanneer een waarschuwing wordt geactiveerd, wordt deze weer gegeven op het PIM-dash board. Selecteer de waarschuwing om een rapport weer te geven met een lijst met de gebruikers of rollen die de waarschuwing hebben geactiveerd.

![Azure AD-rollen: waarschuwingen in het deel venster waarschuwingen en de ernst](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Beveiligingswaarschuwingen

In deze sectie vindt u alle beveiligings waarschuwingen voor Azure AD-rollen, samen met informatie over hoe u kunt voor komen. Ernst heeft de volgende betekenis:

* **Hoog**: Vereist direct actie vanwege een beleids schending.
* **Gemiddeld**: Vereist geen onmiddellijke actie, maar signaleert een mogelijke beleids schending.
* **Laag**: Vereist geen onmiddellijke actie, maar stelt een voor keur voor een wijziging van het beleid voor.

### <a name="administrators-arent-using-their-privileged-roles"></a>Beheerders gebruiken hun geprivilegieerde rollen niet

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | Gebruikers aan wie een Privileged is toegewezen, zijn niet meer nodig om de kans op een aanval te verhogen. Het is ook gemakkelijker voor kwaadwillende personen om niet te worden opgemerkt in accounts die niet actief worden gebruikt. |
| **Hoe kunt u het probleem oplossen?** | Controleer de gebruikers in de lijst en verwijder ze uit geprivilegieerde rollen die ze niet nodig hebben. |
| **Preventie** | Wijs alleen geprivilegieerde rollen toe aan gebruikers die een zakelijke reden hebben. </br>Plan regel matige [toegangs beoordelingen](pim-how-to-start-security-review.md) om te controleren of gebruikers nog steeds hun toegang nodig hebben. |
| **Actie voor het verkleinen van de portal** | Hiermee verwijdert u het account uit hun geprivilegieerde rol. |
| **Trigger** | Wordt geactiveerd als een gebruiker een bepaalde hoeveelheid tijd weggaat zonder een rol te activeren. |
| **Aantal dagen** | Met deze instelling bepaalt u het aantal dagen van 0 tot en met 100 dat een gebruiker kan starten zonder een rol te activeren.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Voor rollen is multi-factor Authentication niet vereist voor activering

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | Zonder MFA kunnen gemanipuleerde gebruikers bevoegde rollen activeren. |
| **Hoe kunt u het probleem oplossen?** | Bekijk de lijst met rollen en vereis [MFA](pim-how-to-change-default-settings.md) voor elke rol. |
| **Preventie** | [Vereis MFA vereisen](pim-how-to-change-default-settings.md) voor elke rol.  |
| **Actie voor het verkleinen van de portal** | Maakt MFA vereist voor de activering van de bevoorrechte rol. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>De Tenant heeft geen Azure AD Premium P2

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | De huidige Tenant heeft geen Azure AD Premium P2. |
| **Hoe kunt u het probleem oplossen?** | Lees de informatie over [Azure AD-edities](../fundamentals/active-directory-whatis.md). Voer een upgrade uit naar Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Mogelijke verlopen accounts in een geprivilegieerde rol

| | |
| --- | --- |
| **Ernst** | Gemiddeld |
| **Waarom krijg ik deze waarschuwing?** | Accounts in een geprivilegieerde rol waarvan het wacht woord in de afgelopen 90 dagen niet is gewijzigd. Deze accounts zijn mogelijk service of gedeelde accounts die niet worden onderhouden en kwetsbaar zijn voor aanvallen. |
| **Hoe kunt u het probleem oplossen?** | Controleer de accounts in de lijst. Als ze geen toegang meer nodig hebben, moet u ze verwijderen uit hun geprivilegieerde rollen. |
| **Preventie** | Zorg ervoor dat accounts die worden gedeeld sterke wacht woorden draaien wanneer er een wijziging is in de gebruikers die het wacht woord kennen. </br>Controleer regel matig accounts met geprivilegieerde rollen met [toegangs beoordelingen](pim-how-to-start-security-review.md) en verwijder roltoewijzingen die niet meer nodig zijn. |
| **Actie voor het verkleinen van de portal** | Hiermee verwijdert u het account uit hun geprivilegieerde rol. |
| **Aanbevolen procedures** | Gedeelde, service-en toegangs accounts voor toegang die worden geverifieerd met een wacht woord en die zijn toegewezen aan streng beschermde administratieve rollen, zoals globale beheerder of beveiligings beheerder, moeten hun wacht woorden draaien voor de volgende gevallen:<ul><li>Na een beveiligings incident met betrekking tot misbruik of inbreuk op beheerders toegangs rechten</li><li>Nadat de bevoegdheden van een gebruiker zijn gewijzigd, zodat ze geen beheerder meer zijn (bijvoorbeeld nadat een werk nemer die een beheerder is, de organisatie verlaat of verlaat)</li><li>Met regel matige intervallen (bijvoorbeeld elk kwar taal of per jaar), zelfs als er geen sprake is van een onbekende schending of wijziging van IT-personeel</li></ul>Omdat meerdere personen toegang hebben tot de referenties van deze accounts, moeten de referenties worden geroteerd om ervoor te zorgen dat mensen die hun rollen hebben verlaten, geen toegang meer hebben tot de accounts. [Meer informatie](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-pim"></a>Rollen worden buiten PIM toegewezen

| | |
| --- | --- |
| **Ernst** | Hoog |
| **Waarom krijg ik deze waarschuwing?** | Geprivilegieerde roltoewijzingen buiten PIM worden niet op de juiste wijze gecontroleerd en kunnen duiden op een actieve aanval. |
| **Hoe kunt u het probleem oplossen?** | Controleer de gebruikers in de lijst en verwijder ze uit bevoorrechte rollen die buiten PIM zijn toegewezen. |
| **Preventie** | Onderzoek waar gebruikers geprivilegieerde rollen worden toegewezen buiten PIM en verdere toewijzingen van daaruit verbieden. |
| **Actie voor het verkleinen van de portal** | Hiermee verwijdert u het account uit hun geprivilegieerde rol. |

### <a name="there-are-too-many-global-administrators"></a>Er zijn te veel globale beheerders

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | De globale beheerder is de rol met de hoogste bevoegdheden. Als een globale beheerder is aangetast, krijgt de aanvaller toegang tot alle machtigingen, waardoor het hele systeem risico loopt. |
| **Hoe kunt u het probleem oplossen?** | Controleer de gebruikers in de lijst en verwijder alle die de rol globale beheerder niet absoluut nodig hebben. </br>Wijs deze gebruikers minder privilegede rollen toe. |
| **Preventie** | Wijs gebruikers de minst bevoegde rol toe die ze nodig hebben. |
| **Actie voor het verkleinen van de portal** | Hiermee verwijdert u het account uit hun geprivilegieerde rol. |
| **Trigger** | Wordt geactiveerd als aan twee verschillende criteria wordt voldaan en u deze beide kunt configureren. Eerst moet u een bepaalde drempel waarde van globale beheerders bereiken. Ten tweede moet een bepaald percentage van uw totale roltoewijzingen globale beheerders zijn. Als u slechts een van deze metingen voldoet, wordt de waarschuwing niet weer gegeven. |
| **Minimum aantal globale beheerders** | Met deze instelling geeft u het aantal globale beheerders, van 2 tot 100, aan dat u een onveilig bedrag beschouwt. |
| **Percentage van globale beheerders** | Met deze instelling bepaalt u het minimum percentage van beheerders die globale beheerders zijn, van 0% tot 100%, dat veilig is in uw omgeving. |

### <a name="roles-are-being-activated-too-frequently"></a>Rollen worden te vaak geactiveerd

| | |
| --- | --- |
| **Ernst** | Laag |
| **Waarom krijg ik deze waarschuwing?** | Meerdere activeringen naar dezelfde bevoorrechte rol door dezelfde gebruiker is een aantekening van een aanval. |
| **Hoe kunt u het probleem oplossen?** | Controleer de gebruikers in de lijst en zorg ervoor dat de [activerings duur](pim-how-to-change-default-settings.md) voor de bijbehorende rol lang genoeg is ingesteld om hun taken uit te voeren. |
| **Preventie** | Zorg ervoor dat de [activerings duur](pim-how-to-change-default-settings.md) voor geprivilegieerde rollen lang genoeg is ingesteld voor gebruikers om hun taken uit te voeren.</br>[Vereis MFA vereist](pim-how-to-change-default-settings.md) voor geprivilegieerde rollen met accounts die worden gedeeld door meerdere beheerders. |
| **Actie voor het verkleinen van de portal** | N/A |
| **Trigger** | Wordt geactiveerd als een gebruiker dezelfde geprivilegieerde rol meerdere keren binnen een opgegeven periode activeert. U kunt zowel de tijds periode als het aantal activeringen configureren. |
| **Periode activering verlenging** | Met deze instelling geeft u in dagen, uren, minuten en seconden de periode op die u wilt gebruiken voor het bijhouden van verdachte vernieuwingen. |
| **Aantal activerings verlengingen** | Met deze instelling bepaalt u het aantal activeringen, van 2 tot 100, waarmee u de betrouw bare van de waarschuwing kunt controleren binnen het tijds bestek dat u hebt gekozen. U kunt deze instelling wijzigen door de schuif regelaar te verplaatsen of door een getal in het tekstvak te typen. |

## <a name="configure-security-alert-settings"></a>Instellingen voor beveiligings waarschuwingen configureren

U kunt enkele van de beveiligings waarschuwingen in PIM aanpassen om te werken met uw omgeving en beveiligings doelen. Volg deze stappen om de instellingen voor beveiligings waarschuwingen te openen:

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure AD-rollen**.

1. Klik op **instellingen** en vervolgens op **waarschuwingen**.

    ![Azure AD-rollen: instellingen waarvoor waarschuwingen zijn geselecteerd](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Klik op de naam van een waarschuwing om de instelling voor die waarschuwing te configureren.

    ![Voor de geselecteerde waarschuwing, het deel venster instellingen voor beveiligings waarschuwingen](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure AD-rollen configureren in PIM](pim-how-to-change-default-settings.md)

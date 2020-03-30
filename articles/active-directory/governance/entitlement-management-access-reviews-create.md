---
title: Een toegangsbeoordeling van een toegangspakket maken in Azure AD-rechtenbeheer
description: Meer informatie over het maken van een toegangsbeoordelingsbeleid voor toegangspakketten voor rechtenbeheer in Azure Active Directory-toegangsbeoordelingen (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a862bbb1f574e4adab2f7d8e59a1abe8e5a5fa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608837"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Een toegangsbeoordeling van een toegangspakket maken in Azure AD-rechtenbeheer

Om het risico op verouderde toegang te verminderen, moet u periodieke beoordelingen inschakelen van gebruikers die actieve toewijzingen hebben voor een toegangspakket in Azure AD-rechtenbeheer. U beoordelingen inschakelen wanneer u een nieuw toegangspakket maakt of een bestaand toegangspakket bewerkt. In dit artikel wordt beschreven hoe u toegangsbeoordelingen van toegangspakketten inschakelen.

## <a name="prerequisites"></a>Vereisten

Als u beoordelingen van toegangspakketten wilt inschakelen, moet u voldoen aan de vereisten voor het maken van een toegangspakket:
- Azure AD Premium P2
- Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

Zie [Licentievereisten voor](entitlement-management-overview.md#license-requirements)meer informatie.


## <a name="create-an-access-review-of-an-access-package"></a>Een toegangsbeoordeling van een toegangspakket maken

U toegangsbeoordelingen inschakelen bij [het maken van een nieuw toegangspakket](entitlement-management-access-package-create.md) of het bewerken van een bestaand [toegangspakketbeleid.](entitlement-management-access-package-lifecycle-policy.md) Volg deze stappen om toegangsbeoordelingen van een toegangspakket in te schakelen:

1. Open het tabblad **Levenscyclus** voor een toegangspakket en blader omlaag naar **Toegangsbeoordelingen**.

1. Verplaats de **omschakelingsbeoordelingen vereisen** naar **Ja**.

    ![De toegangscontrole toevoegen](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Geef de datum op waarop de beoordelingen worden gestart naast **Starten op**.

1. Stel vervolgens de **frequentie Van de beoordeling** in op **Jaarlijks**, **Tweejaarlijks**, **Per kwartaal** of **maandelijks**.
Deze instelling bepaalt hoe vaak toegangsbeoordelingen plaatsvinden.

1. Stel de **duur** in om te bepalen hoeveel dagen elke beoordeling van de terugkerende reeks wordt geopend voor invoer van revisoren. U bijvoorbeeld een jaaroverzicht plannen dat begint op 1 januari en 30 dagen lang kan worden beoordeeld, zodat revisoren tot het einde van de maand de tijd hebben om te reageren.

1. Selecteer naast **Revisoren** **Self-review** als u wilt dat gebruikers hun eigen toegangscontrole uitvoeren of selecteer **Specifieke revisor(s)** als u een revisor wilt aanwijzen.

    ![Revisoren toevoegen selecteren](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Als u **Specifieke revisor(s)** hebt geselecteerd, geeft u op welke gebruikers de toegangscontrole zullen doen:
    1. Selecteer **Revisoren toevoegen**.
    1. Zoek in het deelvenster **Revisoren** naar en selecteer de gebruiker(s) die u revisor wilt zijn.
    1. Wanneer u uw revisor(s) hebt geselecteerd, klikt u op de knop **Selecteren.**

    ![De revisoren opgeven](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Klik onder aan de pagina op **Controleren + Maken** als u een nieuw toegangspakket maakt of **Update** als u een toegangspakket bewerkt.

## <a name="view-the-status-of-the-access-review"></a>De status van de toegangscontrole weergeven

Na de begindatum wordt een toegangscontrole weergegeven in de sectie **Toegangsrecensies.** Volg de volgende stappen om de status van een toegangscontrole weer te geven:

1. Klik **in Identity Governance**op **Access-pakketten** en selecteer vervolgens het toegangspakket met de status van toegangscontrole die u wilt controleren.   

1. Zodra u in het overzicht van het toegangspakket staat, klikt u op **Beoordelingen openen** in het linkermenu.
    
    ![Toegangsbeoordelingen selecteren](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Er verschijnt een lijst met alle beleidsregels met toegangsbeoordelingen die eraan zijn gekoppeld. Klik op de beoordeling om het rapport te bekijken.

    ![Lijst met toegangsbeoordelingen](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Wanneer u het rapport bekijkt, wordt het aantal beoordeelde gebruikers weergegeven en de acties die de revisor heeft uitgevoerd.

    ![Bekijk de beoordelingsstatus](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Access reviews e-mailmeldingen
U revisoren aanwijzen of gebruikers kunnen hun toegang zelf controleren. Azure AD stuurt standaard een e-mail naar revisoren of zelfbeoordelaars kort nadat de beoordeling is gestart.

De e-mail bevat instructies over het controleren van de toegang tot toegangspakketten. Als de beoordeling is voor gebruikers om hun toegang te controleren, laat hen de instructies over hoe een self-review van hun toegangspakketten uit te voeren.
  
Als u gastgebruikers hebt toegewezen als revisoren en ze hun uitnodiging voor Azure AD-gasten niet hebben geaccepteerd, ontvangen ze geen e-mails van Azure AD-toegangsbeoordelingen. Ze moeten eerst de uitnodiging accepteren en een account maken met Azure AD voordat ze de e-mails kunnen ontvangen. 

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot toegangspakketten controleren](entitlement-management-access-reviews-review-access.md)
- [Zelfbeoordeling van toegangspakketten](entitlement-management-access-reviews-self-review.md)

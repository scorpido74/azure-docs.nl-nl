---
title: Instellingen voor aanvraag-en goed keuring wijzigen voor een toegangs pakket in azure AD-recht beheer-Azure Active Directory
description: Meer informatie over het wijzigen van de aanvraag-en goedkeurings instellingen voor een toegangs pakket in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ef2faf2a1d1a131dc5f2a01d3fa46cc61a06fb6
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85078804"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Instellingen voor aanvraag-en goed keuring wijzigen voor een toegangs pakket in het beheer van rechten van Azure AD

Als Access Package Manager kunt u de gebruikers die op elk gewenst moment een toegangs pakket aanvragen, wijzigen door het beleid te bewerken of een nieuw beleid toe te voegen. U kunt ook de goedkeurings instellingen wijzigen.

In dit artikel wordt beschreven hoe u de aanvraag-en goedkeurings instellingen voor een bestaand toegangs pakket wijzigt.

## <a name="choose-between-one-or-multiple-polices"></a>Kiezen tussen een of meer beleids regels

De manier waarop u opgeeft wie een toegangs pakket kan aanvragen, is met een beleid. Wanneer u een toegangs pakket maakt, geeft u de aanvraag en goedkeurings instelling op waarmee een beleid wordt gemaakt. Voor de meeste toegangs pakketten geldt één beleid, maar één toegangs pakket kan meerdere beleids regels bevatten. U maakt meerdere beleids regels voor een toegangs pakket als u wilt toestaan dat verschillende sets gebruikers toewijzingen krijgen met verschillende aanvraag-en goedkeurings instellingen. Eén beleid kan bijvoorbeeld niet worden gebruikt om interne en externe gebruikers toe te wijzen aan hetzelfde toegangs pakket. U kunt echter twee beleids regels maken in hetzelfde toegangs pakket: een voor interne gebruikers en één voor externe gebruikers. Als er meerdere beleids regels zijn die van toepassing zijn op een gebruiker, wordt deze gevraagd op het tijdstip van de aanvraag om het beleid te selecteren waaraan ze zijn toegewezen. In het volgende diagram ziet u een toegangs pakket met twee beleids regels.

![Meerdere beleids regels in een toegangs pakket](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Scenario | Aantal beleids regels |
| --- | --- |
| Ik wil dat alle gebruikers in mijn Directory dezelfde aanvraag-en goedkeurings instellingen voor een toegangs pakket hebben | Eén |
| Ik wil dat alle gebruikers in bepaalde verbonden organisaties een toegangs pakket kunnen aanvragen | Eén |
| Ik wil gebruikers in mijn Directory en gebruikers buiten mijn Directory toestaan om een toegangs pakket aan te vragen | Meerdere |
| Ik wil andere goedkeurings instellingen opgeven voor sommige gebruikers | Meerdere |
| Ik wil dat sommige gebruikers pakket toewijzingen gebruiken om te verlopen terwijl andere gebruikers hun toegang kunnen uitbreiden | Meerdere |

Zie [meerdere beleids regels](entitlement-management-troubleshoot.md#multiple-policies
)voor informatie over de prioriteits logica die wordt gebruikt wanneer er meerdere beleids regels van toepassing zijn.

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Een bestaand beleid voor aanvraag-en goedkeurings instellingen openen

Als u de aanvraag-en goedkeurings instellingen voor een toegangs pakket wilt wijzigen, moet u het bijbehorende beleid openen. Volg deze stappen om de aanvraag-en goedkeurings instellingen voor een toegangs pakket te openen.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **beleids regels** en klik vervolgens op het beleid dat u wilt bewerken.

    Het deel venster beleids Details wordt geopend onder aan de pagina.

    ![Toegangs venster pakket-Details van beleid](./media/entitlement-management-shared/policy-details.png)

1. Klik op **bewerken** om het beleid te bewerken.

    ![Toegangs beleid voor pakket bewerken](./media/entitlement-management-shared/policy-edit.png)

1. Klik op het tabblad **aanvragen** om de instellingen voor aanvraag en goed keuring te openen.

1. Voer de stappen in een van de volgende aanvraag secties uit.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Een nieuw beleid voor aanvraag-en goedkeurings instellingen toevoegen

Als u een set gebruikers hebt met verschillende aanvraag-en goedkeurings instellingen, moet u waarschijnlijk een nieuw beleid maken. Volg deze stappen om een nieuw beleid toe te voegen aan een bestaand toegangs pakket.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **beleid** en vervolgens op **beleid toevoegen**.

1. Voer een naam en beschrijving voor het beleid in.

    ![Beleid maken met de naam en beschrijving](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Klik op **volgende** om het tabblad **aanvragen** te openen.

1. Voer de stappen in een van de volgende aanvraag secties uit.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Als u een beleid bewerkt, klikt u op **bijwerken**. Als u een nieuw beleid toevoegt, klikt u op **maken**.

## <a name="next-steps"></a>Volgende stappen

- [Levenscyclus instellingen voor een toegangs pakket wijzigen](entitlement-management-access-package-lifecycle-policy.md)
- [Aanvragen voor een toegangs pakket weer geven](entitlement-management-access-package-requests.md)
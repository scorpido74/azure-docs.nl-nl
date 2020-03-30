---
title: Aanvraag- en goedkeuringsinstellingen wijzigen voor een toegangspakket in Azure AD-rechtenbeheer - Azure Active Directory
description: Meer informatie over het wijzigen van aanvraag- en goedkeuringsinstellingen voor een toegangspakket in Azure Active Directory-rechtenbeheer.
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
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 493ba6396a7ceb11b917fbda5dd6c37c070f2fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261929"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Aanvraag- en goedkeuringsinstellingen wijzigen voor een toegangspakket in Azure AD-rechtenbeheer

Als toegangspakketbeheerder u de gebruikers wijzigen die op elk gewenst moment een toegangspakket kunnen aanvragen door het beleid te bewerken of een nieuw beleid toe te voegen. U ook de goedkeuringsinstellingen wijzigen.

In dit artikel wordt beschreven hoe u de aanvraag- en goedkeuringsinstellingen voor een bestaand toegangspakket wijzigen.

## <a name="choose-between-one-or-multiple-polices"></a>Kies tussen één of meerdere politiekorpsen

De manier waarop u aangeeft wie een toegangspakket kan aanvragen, is met een beleid. Wanneer u een toegangspakket maakt, geeft u de aanvraag- en goedkeuringsinstelling op waarmee een beleid wordt gemaakt. De meeste toegangspakketten hebben één beleid, maar één toegangspakket kan meerdere beleidsregels hebben. U maakt meerdere beleidsregels voor een toegangspakket als u wilt toestaan dat verschillende sets gebruikers toewijzingen met verschillende aanvraag- en goedkeuringsinstellingen krijgen. Eén beleid kan bijvoorbeeld niet worden gebruikt om interne en externe gebruikers aan hetzelfde toegangspakket toe te wijzen. U echter twee beleidsregels maken in hetzelfde toegangspakket: een voor interne gebruikers en een voor externe gebruikers. Als er meerdere beleidsregels zijn die van toepassing zijn op een gebruiker, wordt deze gevraagd op het moment van hun verzoek om het beleid te selecteren waaraan hij of zij zou willen worden toegewezen. In het volgende diagram ziet u een toegangspakket met twee beleidsregels.

![Meerdere beleidsregels in een toegangspakket](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Scenario | Aantal polissen |
| --- | --- |
| Ik wil dat alle gebruikers in mijn directory dezelfde aanvraag- en goedkeuringsinstellingen hebben voor een toegangspakket | Eén |
| Ik wil dat alle gebruikers in bepaalde verbonden organisaties een toegangspakket kunnen aanvragen | Eén |
| Ik wil gebruikers in mijn directory en ook gebruikers buiten mijn directory toestaan om een toegangspakket aan te vragen | Meerdere |
| Ik wil verschillende goedkeuringsinstellingen opgeven voor sommige gebruikers | Meerdere |
| Ik wil dat sommige gebruikers toegang krijgen tot pakkettoewijzingen, terwijl andere gebruikers hun toegang kunnen verlengen | Meerdere |

Zie [Meerdere beleidsregels](entitlement-management-troubleshoot.md#multiple-policies
)voor informatie over de prioriteitlogica die wordt gebruikt wanneer meerdere beleidsregels van toepassing zijn.

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Een bestaand beleid van aanvraag- en goedkeuringsinstellingen openen

Als u de aanvraag- en goedkeuringsinstellingen voor een toegangspakket wilt wijzigen, moet u het bijbehorende beleid openen. Volg deze stappen om de aanvraag- en goedkeuringsinstellingen voor een toegangspakket te openen.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik **op Beleid** en klik vervolgens op het beleid dat u wilt bewerken.

    Het deelvenster Details van het beleid wordt onder aan de pagina geopend.

    ![Toegangspakket - deelvenster Beleidsdetails](./media/entitlement-management-shared/policy-details.png)

1. Klik **op Bewerken** om het beleid te bewerken.

    ![Toegangspakket - Beleid bewerken](./media/entitlement-management-shared/policy-edit.png)

1. Klik op het tabblad **Aanvragen** om de aanvraag- en goedkeuringsinstellingen te openen.

1. Voer de stappen uit in een van de volgende aanvraagsecties.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Een nieuw beleid van aanvraag- en goedkeuringsinstellingen toevoegen

Als u een set gebruikers hebt die verschillende aanvraag- en goedkeuringsinstellingen moeten hebben, moet u waarschijnlijk een nieuw beleid maken. Volg deze stappen om een nieuw beleid toe te voegen aan een bestaand toegangspakket.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik **op Beleid** en voeg vervolgens beleid **toe**.

1. Typ een naam en een beschrijving voor het beleid.

    ![Beleid maken met naam en beschrijving](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Klik **op Volgende** om het tabblad Aanvragen te **openen.**

1. Voer de stappen uit in een van de volgende aanvraagsecties.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Als u een beleidsbewerking bewerkt, klikt u op **Bijwerken**. Als u een nieuw beleid toevoegt, klikt u op **Maken**.

## <a name="next-steps"></a>Volgende stappen

- [Levenscyclusinstellingen voor een toegangspakket wijzigen](entitlement-management-access-package-lifecycle-policy.md)
- [Aanvragen voor een toegangspakket weergeven](entitlement-management-access-package-requests.md)
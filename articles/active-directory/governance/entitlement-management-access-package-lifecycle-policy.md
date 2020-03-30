---
title: Levenscyclusinstellingen wijzigen voor een toegangspakket in Azure AD-rechtenbeheer - Azure Active Directory
description: Meer informatie over het wijzigen van levenscyclusinstellingen voor een toegangspakket in Azure Active Directory-rechtenbeheer.
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
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261981"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Levenscyclusinstellingen wijzigen voor een toegangspakket in Azure AD-rechtenbeheer

Als toegangspakketbeheerder u de levenscyclusinstellingen voor een toegangspakket op elk gewenst moment wijzigen door een bestaand beleid te bewerken. Als u de vervaldatum voor een beleid wijzigt, wordt de vervaldatum voor aanvragen die al in behandeling zijn, of goedgekeurde status, niet gewijzigd.

In dit artikel wordt beschreven hoe u de levenscyclusinstellingen voor een bestaand toegangspakket wijzigen.

## <a name="open-lifecycle-settings"></a>Instellingen voor open levenscyclus

Als u de levenscyclusinstellingen voor een toegangspakket wilt wijzigen, moet u het bijbehorende beleid openen. Volg deze stappen om de levenscyclusinstellingen voor een toegangspakket te openen.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik **op Beleid** en klik vervolgens op het beleid met de levenscyclusinstellingen die u wilt bewerken.

    Het deelvenster Details van het beleid wordt onder aan de pagina geopend.

    ![Toegangspakket - deelvenster Beleidsdetails](./media/entitlement-management-shared/policy-details.png)

1. Klik **op Bewerken** om het beleid te bewerken.

    ![Toegangspakket - Beleid bewerken](./media/entitlement-management-shared/policy-edit.png)

1. Klik op het tabblad **Levenscyclus** om de levenscyclusinstellingen te openen.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Volgende stappen

- [Aanvraag- en goedkeuringsinstellingen voor een toegangspakket wijzigen](entitlement-management-access-package-request-policy.md)
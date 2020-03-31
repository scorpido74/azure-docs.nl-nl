---
title: Beleid voor voorwaardelijke toegang migreren - Azure Active Directory
description: In dit artikel ziet u hoe u een klassiek beleid migreert waarvoor multifactorauthenticatie vereist is in de Azure-portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d637ac464b689a25ce5d5a79cf47da0c85d38d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74846017"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Een klassiek beleid migreren in de Azure-portal

In dit artikel ziet u hoe u een klassiek beleid migreert waarvoor **multi-factor authenticatie** voor een cloud-app vereist is. Hoewel dit geen voorwaarde is, raden we u aan klassieke beleidsregels migreren te lezen [in de Azure-portal](policy-migration.md) voordat u begint met het migreren van uw klassieke beleidsregels.

![Klassieke beleidsdetails die MFA vereisen voor Salesforce-app](./media/policy-migration/33.png)

Het migratieproces bestaat uit de volgende stappen:

1. [Open het klassieke beleid](#open-a-classic-policy) om de configuratie-instellingen op te halen.
1. Maak een nieuw Azure AD Conditional Access-beleid ter vervanging van uw klassieke beleid. 
1. Schakel het klassieke beleid uit.

## <a name="open-a-classic-policy"></a>Een klassiek beleid openen

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteren, **Klassiek beleid**.

   ![Klassieke beleidsweergave](./media/policy-migration-mfa/12.png)

1. Selecteer in de lijst met klassieke beleidsregels het beleid dat u wilt migreren. Documenteer de configuratie-instellingen, zodat u opnieuw maken met een nieuw beleid voor voorwaardelijke toegang.

## <a name="create-a-new-conditional-access-policy"></a>Nieuw beleid voor voorwaardelijke toegang maken

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Als u een nieuw beleid voor voorwaardelijke toegang wilt maken, selecteert u **Nieuw beleid**.
1. Typ op de pagina **Nieuw** in het tekstvak **Naam** een naam voor uw beleid.
1. Klik in de sectie **Toewijzingen** op **Gebruikers en groepen**.
   1. Als u alle gebruikers hebt geselecteerd in uw klassieke beleid, klikt u op **Alle gebruikers**. 
   1. Als u groepen hebt geselecteerd in uw klassieke beleid, klikt u op **Gebruikers en groepen selecteren**en selecteert u vervolgens de vereiste gebruikers en groepen.
   1. Als u de uitgesloten groepen hebt, klikt u op het tabblad **Uitsluiten** en selecteert u de vereiste gebruikers en groepen. 
   1. Gereed **doen selecteren**
1. Klik **in** de sectie Toewijzing op **Cloud-apps of -acties**.
1. Voer op de pagina **Cloud-apps of acties** de volgende stappen uit:
   1. Klik **op Apps selecteren**.
   1. Klik **op Selecteren**.
   1. Selecteer **op** de pagina Selecteren de cloud-app en klik op **Selecteren**.
   1. Klik op de pagina **Cloud-apps** op **Gereed**.
1. Als **u multifactorauthenticatie hebt** geselecteerd:
   1. Klik in de sectie **Toegangsbesturingselementen** op **Verlenen**.
   1. Klik op de pagina **Subsidie** op **Toegang verlenen**en klik vervolgens op Meervoudige **verificatie vereisen**.
   1. Klik **op Selecteren**.
1. Klik **op Aan** om uw beleid in te schakelen en selecteer **Opslaan**.

   ![Het maken van beleid voor voorwaardelijke toegang](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Het klassieke beleid uitschakelen

Als u uw klassieke beleid wilt uitschakelen, klikt u op **Uitschakelen** in de weergave **Details.**

![Klassiek beleid uitschakelen](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [Klassieke beleidsregels migreren in de Azure-portal](policy-migration.md)voor meer informatie over de klassieke beleidsmigratie.
- [Gebruik de modus alleen-rapport voor voorwaardelijke toegang om de impact van nieuwe beleidsbeslissingen te bepalen.](concept-conditional-access-report-only.md)
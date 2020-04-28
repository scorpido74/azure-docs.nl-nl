---
title: Externe B2B-samenwerkings instellingen inschakelen-Azure AD
description: Meer informatie over het inschakelen van Active Directory B2B externe samen werking en het beheren van wie gast gebruikers kunnen uitnodigen. Gebruik de rol gast uitnodiging voor het delegeren van uitnodigingen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fb10863334392b207c7cfd2172dc9260cf15e2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272903"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Externe B2B-samen werking inschakelen en beheren wie gasten kan uitnodigen

In dit artikel wordt beschreven hoe u de samen werking van Azure Active Directory (Azure AD) inschakelt en bepaalt wie gasten kunnen uitnodigen. Standaard kunnen alle gebruikers en gasten in uw directory gasten uitnodigen, zelfs als ze niet zijn toegewezen aan een beheerdersrol. Met instellingen voor externe samen werking kunt u uitnodigingen voor gasten in-of uitschakelen voor verschillende soorten gebruikers in uw organisatie. U kunt uitnodigingen ook overdragen aan afzonderlijke gebruikers door rollen toe te wijzen waarmee gasten kunnen worden uitgenodigd.

## <a name="configure-b2b-external-collaboration-settings"></a>Externe B2B-samenwerkings instellingen configureren

Met Azure AD B2B Collaboration kan een Tenant beheerder het volgende uitnodigings beleid instellen:

- Uitnodigingen uitschakelen
- Alleen beheerders en gebruikers in de rol van de gast-uitnodiging kunnen uitnodigen
- Beheerders, de rol van de gast-uitnodiging en leden kunnen uitnodigen
- Alle gebruikers, inclusief gasten, kunnen uitnodigen

Standaard kunnen alle gebruikers, inclusief gasten, gast gebruikers uitnodigen.

### <a name="to-configure-external-collaboration-settings"></a>Externe samenwerkings instellingen configureren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een Tenant beheerder.
2. Selecteer **Azure Active Directory** >  > **gebruikers****instellingen**.
3. Onder **externe gebruikers**selecteert u **externe instellingen voor samen werking beheren**.
   > [!NOTE]
   > De **instellingen voor externe samen werking** zijn ook beschikbaar op de pagina relaties van de **organisatie** . Ga in azure Active Directory naar de**instellingen**van **organisatie relaties** > onder **beheren**.
4. Kies op de pagina **instellingen voor externe samen werking** de beleids regels die u wilt inschakelen.

   ![Instellingen voor externe samenwerking](./media/delegate-invitations/control-who-to-invite.png)

  - **Machtigingen voor gast gebruikers zijn beperkt**: dit beleid bepaalt de machtigingen voor gasten in uw Directory. Selecteer **Ja** om gasten van bepaalde directory taken te blok keren, zoals het opsommen van gebruikers, groepen of andere Directory bronnen. Selecteer **Nee** om gasten dezelfde toegang te geven tot Directory gegevens als gewone gebruikers in uw Directory.
   - **Beheerders en gebruikers in de rol van de gast-uitnodiging kunnen uitnodigen**: als u beheerders en gebruikers in de rol ' gast uitnodiging ' wilt toestaan om gasten uit te nodigen, stelt u dit beleid in op **Ja**.
   - **Leden kunnen uitnodigen**: Stel dit beleid in op **Ja**als u niet-beheerders leden van uw directory toestemming wilt geven gasten uit te nodigen.
   - **Gasten kunnen uitnodigen**: als u gasten wilt toestaan andere gasten uit te nodigen, stelt u dit beleid in op **Ja**.
   - **Eenmalige e-mail wachtwoord voor gasten inschakelen (preview-versie)**: Zie [eenmalige verificatie per e-mail (preview)](one-time-passcode.md)voor meer informatie over de functie voor eenmalige wachtwoord code.
   - **Samenwerkings beperkingen**: voor meer informatie over het toestaan of blok keren van uitnodigingen voor specifieke domeinen raadpleegt [u uitnodigingen voor B2B-gebruikers van specifieke organisaties toestaan of blok keren](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>De rol van de gast-uitnodiging toewijzen aan een gebruiker

Met de rol gast uitnodiging kunt u afzonderlijke gebruikers de mogelijkheid geven om gasten uit te nodigen zonder hen een globale beheerder of andere beheerdersrol toe te wijzen. Wijs de rol van de gast-uitnodiging toe aan personen. Zorg er vervolgens voor dat u **beheerders en gebruikers instelt in de rol gast uitnodiging. Dit kan worden uitgenodigd** voor **Ja**.

Hier volgt een voor beeld waarin wordt uitgelegd hoe u Power shell gebruikt om een gebruiker toe te voegen aan de rol van de gast-uitnodiging:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over Azure AD B2B-samen werking:

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Gast gebruikers voor B2B-samen werking toevoegen zonder uitnodiging](add-user-without-invite.md)
- [Een B2B-samenwerkings gebruiker toevoegen aan een rol](add-guest-to-role.md)



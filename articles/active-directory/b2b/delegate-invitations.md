---
title: Instellingen voor externe samenwerking in Schakelen In B2B - Azure AD
description: Meer informatie over het inschakelen van externe samenwerking in Active Directory B2B en het beheren van wie gastgebruikers kan uitnodigen. Gebruik de rol Gastuitnodigingom uitnodigingen te delegeren.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272903"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Externe samenwerking met B2B inschakelen en beheren wie gasten kan uitnodigen

In dit artikel wordt beschreven hoe u Azure Active Directory (Azure AD) B2B-samenwerking inschakelt en bepaalt wie gasten kan uitnodigen. Standaard kunnen alle gebruikers en gasten in uw map gasten uitnodigen, zelfs als ze niet zijn toegewezen aan een beheerdersrol. Met externe samenwerkingsinstellingen u uitnodigingen voor gasten in- of uitschakelen voor verschillende typen gebruikers in uw organisatie. U ook uitnodigingen delegeren aan individuele gebruikers door rollen toe te wijzen waarmee ze gasten kunnen uitnodigen.

## <a name="configure-b2b-external-collaboration-settings"></a>Instellingen voor externe b2b-samenwerking configureren

Met Azure AD B2B-samenwerking kan een tenantbeheerder het volgende uitnodigingsbeleid instellen:

- Uitnodigingen uitschakelen
- Alleen beheerders en gebruikers in de rol Gastgenodigden kunnen uitnodigen
- Beheerders, de rol Gastgenodigden en leden kunnen uitnodigen
- Alle gebruikers, inclusief gasten, kunnen

Standaard kunnen alle gebruikers, inclusief gasten, gastgebruikers uitnodigen.

### <a name="to-configure-external-collaboration-settings"></a>Ga als u externe samenwerkingsinstellingen configureren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als tenantbeheerder.
2. Selecteer **gebruikersinstellingen van Azure Active Directory** > **Users** > **User settings**.
3. Selecteer **onder Externe gebruikers**de optie Instellingen voor externe samenwerking **beheren**.
   > [!NOTE]
   > De **instellingen voor externe samenwerking** zijn ook beschikbaar op de pagina **Organisatierelaties.** Ga in Azure Active Directory onder **Beheren**naar **Instellingen voor organisatierelaties** > **.**
4. Kies op de pagina **Instellingen voor externe samenwerking** het beleid dat u wilt inschakelen.

   ![Instellingen voor externe samenwerking](./media/delegate-invitations/control-who-to-invite.png)

  - **Machtigingen voor gastgebruikers zijn beperkt:** dit beleid bepaalt machtigingen voor gasten in uw directory. Selecteer **Ja** om gasten te blokkeren uit bepaalde directorytaken, zoals het opsommen van gebruikers, groepen of andere directorybronnen. Selecteer **Nee** om gasten dezelfde toegang te geven tot directorygegevens als gewone gebruikers in uw directory.
   - **Beheerders en gebruikers in de gastinviterrol kunnen uitnodigen:** Beheerders en gebruikers in de rol 'Gastgenodigder' toestaan om gasten uit te nodigen, stelt dit beleid in op **Ja**.
   - **Leden kunnen uitnodigen:** Als niet-beheerders van uw directory gasten kunnen uitnodigen, stelt u dit beleid in op **Ja**.
   - **Gasten kunnen uitnodigen:** Om gasten toe te staan andere gasten uit te nodigen, stelt u dit beleid in **op Ja.**
   - **E-maileentoegangscode voor gasten inschakelen (Voorbeeld)**: Zie Verificatie van [eenmalige toegangscode (voorbeeld)](one-time-passcode.md)voor meer informatie over de eenmalige toegangscodefunctie.
   - **Samenwerkingsbeperkingen:** Zie [Uitnodigingen voor B2B-gebruikers van specifieke organisaties toestaan of](allow-deny-list.md)blokkeren voor meer informatie over het toestaan of blokkeren van uitnodigingen voor specifieke domeinen.

## <a name="assign-the-guest-inviter-role-to-a-user"></a>De rol Gastuitnodigingsfunctie toewijzen aan een gebruiker

Met de rol Gastuitnodigingkunt u individuele gebruikers de mogelijkheid bieden om gasten uit te nodigen zonder hen een globale beheerder of andere beheerdersrol toe te kennen. Wijs de rol van gastgenodigden toe aan personen. Zorg er vervolgens voor dat **beheerders en gebruikers in de gastinviter-rol kunnen uitnodigen** voor **Ja.**

Hier is een voorbeeld dat laat zien hoe u PowerShell gebruiken om een gebruiker toe te voegen aan de rol Gastgenodigden:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen over Azure AD B2B-samenwerking:

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Gastgebruikers voor B2B-samenwerking toevoegen zonder uitnodiging](add-user-without-invite.md)
- [Een B2B-samenwerkingsgebruiker toevoegen aan een rol](add-guest-to-role.md)



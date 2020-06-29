---
title: Registreren voor Microsoft 365 met een Azure-account
description: Meer informatie over het maken van een Microsoft 365-abonnement met behulp van een Azure-account
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: banders
ms.openlocfilehash: 006f8edc81ec19647ff6173a4c786c6d7e93fadc
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84810377"
---
# <a name="sign-up-for-a-microsoft--365-subscription-with-your-azure-account"></a>Registreren voor een Microsoft 365-abonnement met uw Azure-account

Als u een Azure-abonnement hebt, kunt u uw Azure-account gebruiken om u te registreren voor een Microsoft 365-abonnement. Als u deel uitmaakt van een organisatie die een Azure-abonnement heeft, kunt u Microsoft 365-abonnementen maken voor gebruikers in uw bestaande Azure Active Directory (Azure AD). Registreer u voor Microsoft 365 met een account met de machtiging Globale beheerder of Factureringsbeheerder in uw Azure Active Directory-tenant. Zie [Mijn accountmachtigingen in Azure AD controleren](#RoleInAzureAD) en [Beheerdersrollen toewijzen in Azure Active Directory](../../active-directory/users-groups-roles/directory-assign-admin-roles.md) voor meer informatie.

Als u al een Microsoft 365-account en een Azure-abonnement hebt, kunt u [Een Microsoft 365-tenant koppelen aan een Azure-abonnement](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-a-microsoft-365-subscription-by-using-your-azure-account"></a>Een Microsoft 365-abonnement nemen met uw Azure-account

1. Ga naar de [Microsoft 365-productpagina](https://www.microsoft.com/microsoft-365/business/all-business) en selecteer een abonnement.
2. Klik in de rechterbovenhoek van de pagina op **Aanmelden**.

    ![schermopname van de pagina voor de Microsoft 365-proefversie](./media/azure-account-for-microsoft-365-subscription/12-office-365-trial-page.png)
3. Meld u aan met de referenties van uw Azure-account. Als u een abonnement voor uw organisatie wilt maken, gebruikt u een Azure-account met de rol Globale beheerder of Factureringsbeheerder in uw Azure Active Directory-tenant.

    ![Schermopname van aanmelding bij Microsoft](./media/azure-account-for-microsoft-365-subscription/13-office-365-sign-in.png)
4. Klik op **Nu proberen**.

    ![Schermopname met bevestiging van uw bestelling voor Microsoft 365.](./media/azure-account-for-microsoft-365-subscription/14-office-365-confirm-your-order.png)
5. Klik op de pagina met het ontvangstbewijs van de bestelling op **Doorgaan**.

    ![Schermopname van het ontvangstbewijs van de bestelling voor Microsoft 365](./media/azure-account-for-microsoft-365-subscription/15-office-365-order-receipt.png)

Dat is alles. Als u het Microsoft 365-abonnement voor uw organisatie hebt gemaakt, gebruikt u de volgende stappen om te controleren of uw Azure AD-gebruikers nu zijn opgenomen in Microsoft 365.

1. Open het Microsoft 365-beheercentrum.
2. Vouw **Gebruikers**uit en klik vervolgens op **Actieve gebruikers**.

    ![Schermopname van gebruikers in het Microsoft 365-beheercentrum](./media/azure-account-for-microsoft-365-subscription/16-microsoft-365-admin-center-users.png)

Nadat u zich hebt geregistreerd, wordt het Microsoft 365-abonnement toegevoegd aan hetzelfde exemplaar van Azure Active Directory als uw Azure-abonnement. Zie [Meer informatie over Azure en Microsoft 365-abonnementen](microsoft-365-account-for-azure-subscription.md#more-about-subs) en [Hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) voor meer informatie.

## <a name="check-my-account-permissions-in-azure-ad"></a><a id="RoleInAzureAD"></a>Accountmachtigingen controleren in azure AD
1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Klik op **Alle services** en zoek naar **Active Directory**.

    ![Schermopname van Active Directory in het Azure-portal](./media/azure-account-for-microsoft-365-subscription/billing-more-services-active-directory.png)
3. Klik op **Gebruikers en groepen** > **Alle gebruikers**.
4. Selecteer de gebruikersnaam.

    ![Schermopname met Azure Active Directory gebruikers](./media/azure-account-for-microsoft-365-subscription/billing-users-groups.png)

5. Klik op **Maprol**.

    ![Schermopname met maprol in Azure-portal](./media/azure-account-for-microsoft-365-subscription/billing-user-directory-role.png)
6.  De rol **Globale beheerder** of **Beperkte beheerder** > **Factureringsbeheerder** is vereist voor het maken van een Microsoft 365-abonnement voor gebruikers in uw bestaande Azure Active Directory.

    ![Schermopname met maprol Beperkte beheerder in Azure-portal](./media/azure-account-for-microsoft-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Een Microsoft 365-tenant koppelen aan een Azure-abonnement](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

---
title: Azure-abonnementsbeheerders toevoegen of wijzigen | Microsoft Docs
description: Hierin wordt beschreven hoe u een Azure-abonnementsbeheerder kunt toevoegen of wijzigen met behulp van op rollen gebaseerd toegangsbeheer (RBAC).
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 000315a2d751a05d3e401ee1bb9f593c6e321194
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "64922902"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Azure-abonnementsbeheerders toevoegen of wijzigen

Om de toegang tot Azure-resources te beheren, moet u over de juiste beheerdersrol beschikken. Azure heeft een autorisatiesysteem met op rollen gebaseerd toegangsbeheer (RBAC) met verschillende ingebouwde rollen waaruit u kunt kiezen. U kunt deze rollen toewijzen aan verschillende bereiken, zoals de beheergroep, het abonnement of de resourcegroep.

Microsoft raadt u aan om de toegang tot resources te beheren met RBAC. Als u echter nog steeds gebruikmaakt van het klassieke implementatiemodel en de klassieke resources beheert met behulp van [de Power shell-module van Azure Service Management](https://docs.microsoft.com/powershell/module/servicemanagement/azure), moet u een klassieke beheerder gebruiken. 

> [!TIP]
> Als u de Azure-portal alleen gebruikt voor het beheren van de klassieke resources, hoeft u de klassieke beheerder niet te gebruiken.

Zie [Azure Resource Manager vs. klassieke implementatie](../azure-resource-manager/resource-manager-deployment-model.md) en [Beheerders van het klassieke Azure-abonnement](../role-based-access-control/classic-administrators.md) voor meer informatie.

In dit artikel wordt beschreven hoe u de beheerdersrol voor een gebruiker toevoegt of wijzigt met behulp van RBAC in het abonnementsbereik.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Een gebruiker toewijzen als beheerder van een abonnement

Als u een gebruiker wilt toewijzen als beheerder van een Azure-abonnement, geeft u deze persoon de rol van [Eigenaar](../role-based-access-control/built-in-roles.md#owner) (een RBAC-rol) voor het abonnementsbereik. De rol van eigenaar geeft de gebruiker volledige toegang tot alle resources in het abonnement, waaronder het recht om toegang aan anderen te delegeren. Deze stappen zijn hetzelfde als die voor elke andere functietoewijzing.

1. Open [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure-portal.

1. Klik op het abonnement waartoe u toegang wilt verlenen.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen voor dit abonnement weer te geven.

    ![Schermopname met roltoewijzingen](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Klik op **Toevoegen** > **Roltoewijzing toevoegen** om het deelvenster **Roltoewijzing toevoegen** te openen.

    Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie uitgeschakeld.

1. Selecteer in de vervolgkeuzelijst **Rol** de rol **Eigenaar**.

1. Selecteer een gebruiker in de lijst **Selecteren**. Als u de gebruiker niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om in de map te zoeken naar weergavenamen en e-mailadressen.

    ![Schermopname met de rol Eigenaar geselecteerd](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Klik op **Opslaan** om de rol toe te wijzen.

    Na enkele ogenblikken wordt de rol van eigenaar op abonnementsniveau toegewezen aan de gebruiker.

## <a name="next-steps"></a>Volgende stappen

* [Wat is toegangsbeheer op basis van rollen (RBAC)?](../role-based-access-control/overview.md)
* [Inzicht in de verschillende rollen](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Procedure: Een Azure-abonnement aan Azure Active Directory koppelen of toevoegen](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Machtigingen voor beheerrol in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u nog steeds hulp nodig hebt, neemt u [contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

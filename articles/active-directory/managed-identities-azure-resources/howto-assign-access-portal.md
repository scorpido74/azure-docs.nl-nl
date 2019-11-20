---
title: Een beheerde identiteits toegang tot een resource toewijzen met behulp van de Azure Portal-Azure AD
description: Stapsgewijze instructies voor het toewijzen van een beheerde identiteit op één toegang tot bronnen naar een andere resource met behulp van de Azure-portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8660d5539d9cfc218667bc7bb077f9f6d1191eec
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184143"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Een beheerde identiteit toegang tot een resource toewijzen met behulp van Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Nadat u hebt een Azure-resource geconfigureerd met een beheerde identiteit, kunt u de beheerde identiteit-toegang verlenen tot een andere resource, net als bij elke beveiligings-principal. Dit artikel ziet u hoe u een virtuele Azure-machine of VM-schaalset van beheerde identiteit toegang geven tot een Azure storage-account met behulp van de Azure-portal.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend met beheerde identiteiten voor Azure-resources bent, lees de [overzichtssectie](overview.md). **Lees de [verschil tussen een beheerde identiteit door het systeem is toegewezen en de gebruiker toegewezen](overview.md#how-does-it-work)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>RBAC gebruiken voor het toewijzen van een beheerde identiteit toegang naar een andere resource

Wanneer u inschakelt de beheerde identiteit op een Azure-resource, zoals een [virtuele Azure-machine](qs-configure-portal-windows-vm.md) of [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Aanmelden bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement waarin u de beheerde identiteit hebt geconfigureerd.

2. Navigeer naar de gewenste resource waarop u wilt wijzigen van toegangsbeheer. In dit voorbeeld wij zijn een virtuele Azure-machine die toegang geeft tot een storage-account, zodat we gaat u naar het opslagaccount.

3. Selecteer de **toegangsbeheer (IAM)** pagina van de resource en selecteer **+ roltoewijzing toevoegen**. Geef vervolgens de **rol**, **toegang toewijzen aan**, en geeft u de bijbehorende **abonnement**. Onder het zoekgebied criteria ziet u de resource. Selecteer de resource en selecteer **opslaan**. 

   ![Schermafbeelding van de Access control (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Volgende stappen

- [Beheerde identiteit voor Azure-resources-overzicht](overview.md)
- Zie voor het inschakelen van beheerde identiteiten op een Azure-machine [configureren beheerde identiteiten voor een Azure-resources op een virtuele machine met behulp van de Azure-portal](qs-configure-portal-windows-vm.md).
- Zie voor het inschakelen van beheerde identiteiten op een schaalset voor virtuele Azure-machine [configureren beheerde identiteiten voor een Azure-resources op een VM-schaalset met behulp van de Azure-portal](qs-configure-portal-windows-vmss.md).



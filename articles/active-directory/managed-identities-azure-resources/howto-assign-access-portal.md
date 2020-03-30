---
title: Een beheerde identiteitstoegang toewijzen aan een bron met behulp van de Azure-portal - Azure AD
description: Stapsgewijze instructies voor het toewijzen van een beheerde identiteit aan de ene brontoegang tot een andere bron, met behulp van de Azure-portal.
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
ms.openlocfilehash: e24c97909870c4d76b07ec837e5f624a509bd1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547290"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Een beheerde identiteitstoegang toewijzen aan een bron met behulp van de Azure-portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Nadat u een Azure-bron met een beheerde identiteit hebt geconfigureerd, u de beheerde identiteit toegang geven tot een andere bron, net als elke beveiligingsprincipal. In dit artikel ziet u hoe u de beheerde identiteitstoegang van een Azure-virtuele machine of virtuele machineschaalset geeft tot een Azure-opslagaccount met behulp van de Azure-portal.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>RBAC gebruiken om een beheerde identiteitstoegang toe te wijzen aan een andere bron

Nadat u beheerde identiteit hebt ingeschakeld op een [Azure-bron,](qs-configure-portal-windows-vm.md) zoals een Azure VM of [Azure VMSS:](qs-configure-portal-windows-vmss.md)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement waaronder u de beheerde identiteit hebt geconfigureerd.

2. Navigeer naar de gewenste bron waarop u het toegangscontrolebeheer wilt wijzigen. In dit voorbeeld geven we een Azure virtuele machine toegang tot een opslagaccount, zodat we naar het opslagaccount gaan.

3. Selecteer de pagina **IAM (Access control)** van de bron en selecteer **+ Roltoewijzing toevoegen**. Geef vervolgens de **rol op**, **wijs toegang toe tot**en geef het bijbehorende abonnement **op**. Onder het zoekcriteriagebied moet u de resource zien. Selecteer de resource en selecteer **Opslaan**. 

   ![Schermafbeelding van het toegangsbeheer (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Volgende stappen

- [Overzicht beheerde identiteit voor Azure-bronnen](overview.md)
- Zie [Beheerde identiteitconfigureren voor Azure-resources op een vm configureren met behulp van de Azure-portal](qs-configure-portal-windows-vm.md)als u beheerde identiteit op een virtuele Azure-machine wilt inschakelen.
- Zie [Beheerde identiteit configureren voor Azure-resources op een virtuele machineschaalset met behulp van de Azure-portal](qs-configure-portal-windows-vmss.md)als u beheerde identiteit wilt inschakelen op een azure-schaalset voor virtuele machines.



---
title: Een beheerde identiteits toegang toewijzen aan een resource met behulp van Azure CLI-Azure AD
description: Stapsgewijze instructies voor het toewijzen van een beheerde identiteit aan één resource, toegang tot een andere resource met behulp van Azure CLI.
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
ms.date: 12/06/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b241ac223fd1eb9df2b0a914726d8f37df5f4d88
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547362"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Een beheerde identiteits toegang tot een resource toewijzen met behulp van Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zodra u een Azure-resource met een beheerde identiteit hebt geconfigureerd, kunt u de beheerde identiteit toegang geven tot een andere resource, net zoals elke beveiligingsprincipal. In dit voor beeld ziet u hoe u met Azure CLI een Azure-virtuele machine of beheerde identiteits toegang voor virtuele machines kunt geven aan een Azure-opslag account.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de CLI-script voorbeelden wilt uitvoeren, hebt u drie opties:
    - Gebruik [Azure Cloud shell](../../cloud-shell/overview.md) van de Azure Portal (zie volgende sectie).
    - Gebruik de Inge sloten Azure Cloud Shell via de knop ' Probeer het ', in de rechter bovenhoek van elk code blok.
    - [Installeer de nieuwste versie van Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) als u liever een lokale cli-console gebruikt. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>RBAC gebruiken om een beheerde identiteits toegang toe te wijzen aan een andere resource

Nadat u de beheerde identiteit hebt ingeschakeld voor een Azure-resource, zoals een [virtuele machine van Azure](qs-configure-cli-windows-vm.md) of een [schaalset voor virtuele machines van Azure](qs-configure-cli-windows-vmss.md): 

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waaronder u de virtuele machine of VM-schaalset wilt implementeren:

   ```azurecli-interactive
   az login
   ```

2. In dit voor beeld geven we een virtuele Azure-machine toegang tot een opslag account. Eerst gebruiken we [AZ Resource List](/cli/azure/resource/#az-resource-list) om de Service-Principal op te halen voor de virtuele machine met de naam myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Voor een schaalset voor virtuele Azure-machines is de opdracht hetzelfde, maar u krijgt de service-principal voor de virtuele-machine schaalset ' DevTestVMSS ':
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Wanneer u de Service-Principal-ID hebt, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) om de virtuele machine of de virtuele-machine schaalset ' lezer ' toegang te geven tot een opslag account met de naam ' myStorageAcct ':

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Als u beheerde identiteit wilt inschakelen op een virtuele machine van Azure, raadpleegt u [beheerde identiteiten voor Azure-resources configureren op een Azure-VM met behulp van Azure cli](qs-configure-cli-windows-vm.md).
- Zie [beheerde identiteiten voor Azure-resources configureren op een schaalset voor virtuele machines met behulp van Azure cli](qs-configure-cli-windows-vmss.md)om beheerde identiteit in te scha kelen op een virtuele machine schaalset van Azure.

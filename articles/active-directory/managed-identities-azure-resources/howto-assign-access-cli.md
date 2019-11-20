---
title: Een beheerde identiteits toegang toewijzen aan een resource met behulp van Azure CLI-Azure AD
description: Stapsgewijze toegang instructies voor het toewijzen van een beheerde identiteit op een resource, tot een andere bron, met behulp van Azure CLI.
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
ms.openlocfilehash: 4153e038228d7ec4631fc5fec81303966a12b01b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184089"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Een beheerde identiteit toegang toewijzen aan een resource met behulp van Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Wanneer u een Azure-resource hebt geconfigureerd met een beheerde identiteit, kunt u de toegang tot de beheerde identiteit geven naar een andere resource, net als bij elke beveiligings-principal. In dit voorbeeld ziet u hoe u een virtuele Azure-machine of VM-schaalset van beheerde identiteit toegang geven tot Azure storage-account met behulp van Azure CLI.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend met beheerde identiteiten voor Azure-resources bent, lees de [overzichtssectie](overview.md). **Lees de [verschil tussen een beheerde identiteit door het systeem is toegewezen en de gebruiker toegewezen](overview.md#how-does-it-work)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u wilt de CLI-scriptvoorbeelden uitvoeren, hebt u drie opties:
    - Gebruik [Azure Cloud Shell](../../cloud-shell/overview.md) vanuit Azure portal (Zie volgende sectie).
    - Gebruik de ingesloten Azure Cloud Shell via het 'Try It' de knop, zich in de rechterbovenhoek van elk codeblok.
    - [Installeer de nieuwste versie van Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) als u liever een lokale CLI-console. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>RBAC gebruiken voor het toewijzen van een beheerde identiteit toegang naar een andere resource

Wanneer u inschakelt de beheerde identiteit op een Azure-resource, zoals een [virtuele Azure-machine](qs-configure-cli-windows-vm.md) of [Azure virtuele-machineschaalset](qs-configure-cli-windows-vmss.md): 

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waarin u wilt implementeren van de virtuele machine of VM-schaalset:

   ```azurecli-interactive
   az login
   ```

2. In dit voorbeeld zijn we geeft u de toegang van een virtuele machine van Azure naar een opslagaccount. Eerst gebruiken we [az resource list](/cli/azure/resource/#az-resource-list) om op te halen van de service-principal voor de virtuele machine met de naam myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Een schaalset voor virtuele Azure-machine, de opdracht is hetzelfde, behalve hier, krijgt u de service-principal voor de virtuele-machineschaalset met de naam 'DevTestVMSS':
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Zodra u de service-principal-ID hebt, gebruikt u [az roltoewijzing maken](/cli/azure/role/assignment#az-role-assignment-create) instellen om te geven van de virtuele machine of virtuele-machineschaalset 'Lezer' toegang tot een opslagaccount met de naam 'myStorageAcct':

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Volgende stappen

- [Beheerde identiteiten voor een overzicht van Azure-resources](overview.md)
- Zie voor het inschakelen van beheerde identiteiten op een Azure-machine [configureren beheerde identiteiten voor een Azure-resources op een Azure-VM met behulp van Azure CLI](qs-configure-cli-windows-vm.md).
- Zie voor het inschakelen van beheerde identiteiten op een schaalset voor virtuele Azure-machine [configureren beheerde identiteiten voor een Azure-resources op een VM-schaalset met behulp van Azure CLI](qs-configure-cli-windows-vmss.md).

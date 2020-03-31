---
title: Een beheerde identiteitstoegang toewijzen aan een bron met Azure CLI - Azure AD
description: Stapsgewijze instructies voor het toewijzen van een beheerde identiteit op de ene bron, toegang tot een andere bron, met behulp van Azure CLI.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547362"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Een beheerde identiteitstoegang toewijzen aan een bron met Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zodra u een Azure-bron met een beheerde identiteit hebt geconfigureerd, u de beheerde identiteit toegang geven tot een andere bron, net als elke beveiligingsprincipal. In dit voorbeeld ziet u hoe u de beheerde identiteitstoegang van een Azure-virtuele machine of een virtuele machineschaalset geeft tot een Azure-opslagaccount met Azure CLI.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de voorbeelden van CLI-scripten wilt uitvoeren, hebt u drie opties:
    - [Azure Cloud Shell gebruiken](../../cloud-shell/overview.md) vanuit de Azure-portal (zie volgende sectie).
    - Gebruik de ingesloten Azure Cloud Shell via de knop 'Probeer het' in de rechterbovenhoek van elk codeblok.
    - [Installeer de nieuwste versie van Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) als u liever een lokale CLI-console gebruikt. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>RBAC gebruiken om een beheerde identiteitstoegang toe te wijzen aan een andere bron

Nadat u beheerde identiteit hebt ingeschakeld op een Azure-bron, zoals een [azure-virtuele machine](qs-configure-cli-windows-vm.md) of [azure-set voor virtuele machines](qs-configure-cli-windows-vmss.md)voor virtuele machines: 

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waaronder u de vm- of virtuele machineschaalset wilt implementeren:

   ```azurecli-interactive
   az login
   ```

2. In dit voorbeeld geven we een Virtuele Azure-machine toegang tot een opslagaccount. Eerst gebruiken we [az resource lijst](/cli/azure/resource/#az-resource-list) om de service principal voor de virtuele machine genaamd myVM te krijgen:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Voor een Azure-set voor virtuele machineschaal is de opdracht hetzelfde, behalve hier, u krijgt de serviceprincipal voor de virtuele machineschaalset met de naam 'DevTestVMSS':
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Zodra u de serviceprincipal-ID hebt, gebruikt u [az-roltoewijzingom](/cli/azure/role/assignment#az-role-assignment-create) de virtuele machine of virtuele machineschaalset "Reader" toegang te geven tot een opslagaccount met de naam "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-bronnen](overview.md)
- Zie [Beheerde identiteitconfigureren voor Azure-resources op een Azure VM configureren met Azure CLI](qs-configure-cli-windows-vm.md).
- Zie [Beheerde identiteit configureren voor Azure-resources op een virtuele machineschaalset met Azure CLI](qs-configure-cli-windows-vmss.md)als u beheerde identiteit wilt inschakelen op een azure-schaalset.

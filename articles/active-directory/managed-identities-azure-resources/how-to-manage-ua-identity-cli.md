---
title: Door de gebruiker toegewezen beheerde identiteit beheren-Azure CLI-Azure AD
description: Stapsgewijze instructies voor het maken, weer geven en verwijderen van een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1e8587562ff452373fe2ee3b98fa20309e77cc7
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547500"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Een door de gebruiker toegewezen beheerde identiteit maken, weer geven of verwijderen met behulp van de Azure CLI


Beheerde identiteiten voor Azure-resources bieden Azure-Services met een beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie, zonder dat er referenties in uw code nodig zijn. 

In dit artikel leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt maken, weer geven en verwijderen met behulp van Azure CLI.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de CLI-script voorbeelden wilt uitvoeren, hebt u drie opties:
    - Gebruik [Azure Cloud shell](../../cloud-shell/overview.md) van de Azure Portal (zie volgende sectie).
    - Gebruik de Inge sloten Azure Cloud Shell via de knop ' Probeer het ', in de rechter bovenhoek van elk code blok.
    - [Installeer de nieuwste versie van de Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 of hoger) als u liever een lokale cli-console gebruikt. Meld u met `az login`aan bij Azure met behulp van een account dat is gekoppeld aan het Azure-abonnement waaronder u de door de gebruiker toegewezen beheerde identiteit wilt implementeren.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken 

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Gebruik de opdracht [AZ Identity Create](/cli/azure/identity#az-identity-create) om een door de gebruiker toegewezen beheerde identiteit te maken. Met de para meter `-g` geeft u de resource groep op waar de door de gebruiker toegewezen beheerde identiteit moet worden gemaakt, en de para meter `-n` de naam ervan opgeeft. Vervang de `<RESOURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` parameter waarden door uw eigen waarden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Door de gebruiker toegewezen beheerde identiteiten weer geven

Als u een door de gebruiker toegewezen beheerde identiteit wilt weer geven/lezen, moet uw account beschikken over de rol [Managed Identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) of [Managed id contributor](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Als u door de gebruiker toegewezen beheerde identiteiten wilt weer geven, gebruikt u de opdracht [AZ ID List](/cli/azure/identity#az-identity-list) . Vervang de `<RESOURCE GROUP>` door uw eigen waarde:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
In het JSON-antwoord hebben door de gebruiker toegewezen beheerde identiteiten `"Microsoft.ManagedIdentity/userAssignedIdentities"` geretourneerde waarde voor sleutel `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, gebruikt u de opdracht [AZ Identity delete](/cli/azure/identity#az-identity-delete) .  De para meter-n specificeert de naam en de-g para meter geeft de resource groep op waarin de door de gebruiker toegewezen beheerde identiteit is gemaakt. Vervang de waarden voor `<USER ASSIGNED IDENTITY NAME>` en `<RESOURCE GROUP>` para meters door uw eigen waarden:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Als u een door de gebruiker toegewezen beheerde identiteit verwijdert, wordt de verwijzing niet verwijderd uit een resource waaraan deze is toegewezen. Verwijder deze uit de VM-VMSS met behulp van de opdracht `az vm/vmss identity remove`

## <a name="next-steps"></a>Volgende stappen

Zie [AZ Identity](/cli/azure/identity)voor een volledige lijst met Azure cli-identiteits opdrachten.

Voor informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit aan een Azure-VM raadpleegt u [beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met behulp van Azure cli](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 

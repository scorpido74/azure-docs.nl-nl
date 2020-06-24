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
ms.date: 04/17/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f9bfe8c9e0dc9aa6711ce46096e7a4df695bf18
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84694515"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Een door de gebruiker toegewezen beheerde identiteit maken, weer geven of verwijderen met behulp van de Azure CLI


Beheerde identiteiten voor Azure-resources bieden Azure-Services met een beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie, zonder dat er referenties in uw code nodig zijn. 

In dit artikel leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt maken, weer geven en verwijderen met behulp van Azure CLI.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de CLI-script voorbeelden wilt uitvoeren, hebt u drie opties:
    - Gebruik [Azure Cloud shell](../../cloud-shell/overview.md) van de Azure Portal (zie volgende sectie).
    - Gebruik de Inge sloten Azure Cloud Shell via de knop ' Probeer het ', in de rechter bovenhoek van elk code blok.
    - [Installeer de nieuwste versie van de Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 of hoger) als u liever een lokale cli-console gebruikt. Meld u aan bij Azure met met `az login` behulp van een account dat is gekoppeld aan het Azure-abonnement waaronder u de door de gebruiker toegewezen beheerde identiteit wilt implementeren.


> [!NOTE]
> Als u de gebruikers machtigingen wilt wijzigen wanneer u een app servivce Principal gebruikt met CLI, moet u de Service-Principal extra machtigingen opgeven in azure AD Graph API als delen van CLI GET-aanvragen uitvoeren voor de Graph API. Als dat niet het geval is, kunt u het bericht ' onvoldoende bevoegdheden om de bewerking te volt ooien ' ontvangen. Als u dit wilt doen, gaat u naar de registratie van de app in Azure Active Directory, selecteert u uw app, klikt u op API-machtigingen, schuift u omlaag en selecteert u Azure Active Directory Graph. Van daaruit selecteert u toepassings machtigingen en voegt u vervolgens de juiste machtigingen toe. 



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken 

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Gebruik de opdracht [AZ Identity Create](/cli/azure/identity#az-identity-create) om een door de gebruiker toegewezen beheerde identiteit te maken. Met de `-g` para meter wordt de resource groep opgegeven waarin de door de gebruiker toegewezen beheerde identiteit moet worden gemaakt `-n` . de para meter geeft de naam op. Vervang de `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` para meters en door uw eigen waarden:

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
In het JSON-antwoord hebben door de gebruiker toegewezen beheerde identiteiten een `"Microsoft.ManagedIdentity/userAssignedIdentities"` waarde geretourneerd voor de sleutel, `type` .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, gebruikt u de opdracht [AZ Identity delete](/cli/azure/identity#az-identity-delete) .  De para meter-n specificeert de naam en de-g para meter geeft de resource groep op waarin de door de gebruiker toegewezen beheerde identiteit is gemaakt. Vervang de `<USER ASSIGNED IDENTITY NAME>` `<RESOURCE GROUP>` para meters en waarden door uw eigen waarden:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Als u een door de gebruiker toegewezen beheerde identiteit verwijdert, wordt de verwijzing niet verwijderd uit een resource waaraan deze is toegewezen. Verwijder deze uit de VM-VMSS met behulp van de `az vm/vmss identity remove` opdracht

## <a name="next-steps"></a>Volgende stappen

Zie [AZ Identity](/cli/azure/identity)voor een volledige lijst met Azure cli-identiteits opdrachten.

Voor informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit aan een Azure-VM raadpleegt u [beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met behulp van Azure cli](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 

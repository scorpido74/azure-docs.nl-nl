---
title: Beheerde identiteiten configureren voor virtuele-machine schaal sets-Azure CLI-Azure AD
description: Stapsgewijze instructies voor het configureren van door het systeem en de gebruiker toegewezen beheerde identiteiten op een virtuele-machine schaalset van Azure met behulp van Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: MarkusVi
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969307070d23f9892105b2f620ee839356f46330
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609168"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Beheerde identiteiten configureren voor Azure-resources op een schaalset voor virtuele machines met behulp van Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel leert u hoe u de volgende beheerde identiteiten voor Azure-bronnen bewerkingen kunt uitvoeren op een virtuele machine schaalset van Azure, met behulp van Azure CLI:
- De door het systeem toegewezen beheerde identiteit inschakelen en uitschakelen op een virtuele-machine schaalset van Azure
- Een door de gebruiker toegewezen beheerde identiteit toevoegen aan en verwijderen uit een Azure virtual machine-schaalset


## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de beheer bewerkingen in dit artikel wilt uitvoeren, moet uw account beschikken over de volgende toegangs beheer toewijzingen van Azure op basis van rollen:

    > [!NOTE]
    > Er zijn geen aanvullende Azure AD Directory-roltoewijzingen vereist.

    - [Inzender voor virtuele machines](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) voor het maken van een schaalset voor virtuele machines en het inschakelen en verwijderen van door het systeem en/of gebruiker toegewezen beheerde identiteit uit een schaalset voor virtuele machines.
    - Rol van [beheerde identiteit Inzender](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) om een door de gebruiker toegewezen beheerde identiteit te maken.
    - De rol [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) voor het toewijzen en verwijderen van een door de gebruiker toegewezen beheerde identiteit van en aan een virtuele-machine schaalset.
- Als u de CLI-script voorbeelden wilt uitvoeren, hebt u drie opties:
    - Gebruik [Azure Cloud shell](../../cloud-shell/overview.md) van de Azure Portal (zie volgende sectie).
    - Gebruik de Inge sloten Azure Cloud Shell via de knop ' Probeer het ', in de rechter bovenhoek van elk code blok.
    - [Installeer de nieuwste versie van de Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 of hoger) als u liever een lokale cli-console gebruikt. 
      
      > [!NOTE]
      > De opdrachten zijn bijgewerkt om de nieuwste versie van de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli)weer te geven.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u de door het systeem toegewezen beheerde identiteit voor een virtuele-machine schaalset van Azure kunt in-en uitschakelen met behulp van Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een schaalset voor virtuele Azure-machines

Een schaalset voor virtuele machines maken waarbij de door het systeem toegewezen beheerde identiteit is ingeschakeld:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waaronder u de schaalset voor virtuele machines wilt implementeren:

   ```azurecli-interactive
   az login
   ```

2. Maak een [resource groep](../../azure-resource-manager/management/overview.md#terminology) voor insluiting en implementatie van uw schaalset voor virtuele machines en de bijbehorende gerelateerde resources met behulp van [AZ Group Create](/cli/azure/group/#az-group-create). U kunt deze stap overs Laan als u al een resource groep hebt die u in plaats daarvan wilt gebruiken:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Een schaalset voor virtuele machines [maken](/cli/azure/vmss/#az-vmss-create) . In het volgende voor beeld wordt een schaalset voor virtuele machines gemaakt met de naam *myVMSS* met een door het systeem toegewezen beheerde identiteit, zoals aangevraagd door de `--assign-identity` para meter. Met de parameters `--admin-username` en `--admin-password` worden de naam van de gebruiker met beheerdersrechten en het wachtwoord van het account voor aanmelding bij de virtuele machine opgegeven. Werk deze waarden bij met waarden die geschikt zijn voor uw omgeving: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande schaalset voor virtuele Azure-machines

Als u de door het systeem toegewezen beheerde identiteit wilt inschakelen op een bestaande schaalset voor virtuele Azure-machines:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de schaalset voor virtuele machines bevat.

   ```azurecli-interactive
   az login
   ```

2. Een door het systeem toegewezen beheerde identiteit [inschakelen](/cli/azure/vmss/identity/#az-vmss-identity-assign) voor een bestaande virtuele machine:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit uitschakelen vanuit een schaalset voor virtuele Azure-machines

Als u een schaalset voor virtuele machines hebt die de door het systeem toegewezen beheerde identiteit niet meer nodig heeft, maar nog steeds door de gebruiker toegewezen beheerde identiteiten nodig hebt, gebruikt u de volgende opdracht:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Als u een virtuele machine hebt die door het systeem toegewezen beheerde identiteit niet meer nodig heeft en geen door de gebruiker toegewezen beheerde identiteiten heeft, gebruikt u de volgende opdracht:

> [!NOTE]
> De waarde `none` is hoofdletter gevoelig. Het moet een kleine letter zijn. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>door de gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt inschakelen en verwijderen met behulp van Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een schaalset voor virtuele machines

In deze sectie wordt uitgelegd hoe u een schaalset voor virtuele machines en toewijzing van een door de gebruiker toegewezen beheerde identiteit kunt maken aan de schaalset van de virtuele machine. Als u al een schaalset voor virtuele machines hebt die u wilt gebruiken, kunt u deze sectie overs Laan en door gaan met de volgende.

1. U kunt deze stap overs Laan als u al een resource groep hebt die u wilt gebruiken. Maak een [resource groep](~/articles/azure-resource-manager/management/overview.md#terminology) voor insluiting en implementatie van uw door de gebruiker toegewezen beheerde identiteit met behulp van [AZ Group Create](/cli/azure/group/#az-group-create). Vervang de parameterwaarden `<RESOURCE GROUP>` en `<LOCATION>` door uw eigen waarden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Maak een door de gebruiker toegewezen beheerde identiteit met [az identity create](/cli/azure/identity#az-identity-create).  De parameter `-g` geeft de resourcegroep aan waarin de door de gebruiker toegewezen beheerde identiteit wordt gemaakt en de parameter `-n` geeft de naam ervan aan. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Het antwoord bevat Details voor de door de gebruiker toegewezen beheerde identiteit, vergelijkbaar met de volgende. De resource `id` waarde die is toegewezen aan de door de gebruiker toegewezen beheerde identiteit, wordt in de volgende stap gebruikt.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Een schaalset voor virtuele machines [maken](/cli/azure/vmss/#az-vmss-create) . In het volgende voor beeld wordt een schaalset voor virtuele machines gemaakt die is gekoppeld aan de nieuwe door de gebruiker toegewezen beheerde identiteit, zoals opgegeven door de `--assign-identity` para meter. Vervang de parameterwaarden `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` en `<USER ASSIGNED IDENTITY>` door uw eigen waarden. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande schaalset voor virtuele machines

1. Maak een door de gebruiker toegewezen beheerde identiteit met [az identity create](/cli/azure/identity#az-identity-create).  De parameter `-g` geeft de resourcegroep aan waarin de door de gebruiker toegewezen beheerde identiteit wordt gemaakt en de parameter `-n` geeft de naam ervan aan. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Het antwoord bevat Details voor de door de gebruiker toegewezen beheerde identiteit, vergelijkbaar met de volgende.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [Wijs](/cli/azure/vmss/identity) de door de gebruiker toegewezen beheerde identiteit toe aan de schaalset van de virtuele machine. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VIRTUAL MACHINE SCALE SET NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY>` is de bron eigenschap van de door de gebruiker toegewezen identiteit `name` , zoals deze is gemaakt in de vorige stap:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een schaalset voor virtuele Azure-machines

Een door de gebruiker toegewezen beheerde identiteit [verwijderen](/cli/azure/vmss/identity#az-vmss-identity-remove) uit een gebruik van een virtuele-machine schaalset `az vmss identity remove` . Als dit de enige door de gebruiker toegewezen beheerde identiteit is toegewezen aan de schaalset van de virtuele machine, `UserAssigned` wordt deze verwijderd uit de waarde voor het identiteits type.  Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VIRTUAL MACHINE SCALE SET NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY>` is de door de gebruiker toegewezen beheerde identiteits `name` eigenschap, die u kunt vinden in de sectie identiteit van de virtuele-machine schaalset met behulp van `az vmss identity show` :

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Als uw schaalset voor virtuele machines geen door het systeem toegewezen beheerde identiteit heeft en u alle door de gebruiker toegewezen beheerde identiteiten wilt verwijderen, gebruikt u de volgende opdracht:

> [!NOTE]
> De waarde `none` is hoofdletter gevoelig. Het moet een kleine letter zijn.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Als uw schaalset voor virtuele machines zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten heeft, kunt u alle door de gebruiker toegewezen identiteiten verwijderen door te scha kelen naar alleen door het systeem toegewezen beheerde identiteit te gebruiken. Gebruik de volgende opdracht:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Zie voor de volledige Snelstartgids voor het maken van virtuele machines in Azure. 

  - [Een Schaalset voor virtuele machines maken met CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)


















---
title: Beheerde identiteiten configureren op de set van virtuele machinesschaal - Azure CLI - Azure AD
description: Stapsgewijze instructies voor het configureren van systeem- en door de gebruiker toegewezen beheerde identiteiten op een Azure-set voor virtuele machines, met Azure CLI.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: MarkusVi
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2832a8c584c0fbe707f22501809d772c6ffb970b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430096"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Beheerde identiteiten configureren voor Azure-resources op een virtuele machineschaalset met Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben. 

In dit artikel leert u hoe u de volgende beheerde identiteiten uitvoert voor Azure-resourcesbewerkingen op een Azure-set voor virtuele machines met behulp van Azure CLI:
- De door het systeem toegewezen beheerde identiteit in- en uitschakelen op een azure-schaalset voor virtuele machines
- Een door de gebruiker toegewezen beheerde identiteit toevoegen en verwijderen op een azure-schaalset voor virtuele machines


## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de beheerbewerkingen in dit artikel wilt uitvoeren, heeft uw account de volgende op Azure-rollen gebaseerde toegangsbeheertoewijzingen nodig:

    > [!NOTE]
    > Er zijn geen extra Azure AD-maproltoewijzingen vereist.

    - [Virtuele machinebijdrager](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) om een virtuele machineschaalset te maken en systeem- en/of door de gebruiker toegewezen beheerde identiteit in te schakelen en te verwijderen uit een virtuele machineschaalset.
    - [Functie Beheerde identiteitsbijdrager](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) om een door de gebruiker toegewezen beheerde identiteit te maken.
    - [Functie Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) om een door de gebruiker toegewezen beheerde identiteit toe te wijzen en te verwijderen van en naar een virtuele machineschaalset.
- Als u de voorbeelden van CLI-scripten wilt uitvoeren, hebt u drie opties:
    - [Azure Cloud Shell gebruiken](../../cloud-shell/overview.md) vanuit de Azure-portal (zie volgende sectie).
    - Gebruik de ingesloten Azure Cloud Shell via de knop 'Probeer het' in de rechterbovenhoek van elk codeblok.
    - [Installeer de nieuwste versie van de Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 of hoger) als u liever een lokale CLI-console gebruikt. 
      
      > [!NOTE]
      > De opdrachten zijn bijgewerkt om de nieuwste versie van de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)weer te geven.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u de door het systeem toegewezen beheerde identiteit in- en uitschakelt voor een Azure-set voor virtuele machines met Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Beheerde identiteit met systeemtoegewezen status inschakelen tijdens het maken van een Azure-schaalset voor virtuele machines

Ga als lid van het nieuwe bedrijf naar een virtuele machineschaalset met de door het systeem toegewezen beheerde identiteit:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waaronder u de virtuele machineschaalset wilt implementeren:

   ```azurecli-interactive
   az login
   ```

2. Maak een [resourcegroep](../../azure-resource-manager/management/overview.md#terminology) voor het indammen en implementeren van uw virtuele machineschaalset en de bijbehorende resources met behulp van [de AZ-groep maken.](/cli/azure/group/#az-group-create) U deze stap overslaan als u al een resourcegroep hebt die u in plaats daarvan wilt gebruiken:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Maak een virtuele machine schaal set met behulp [van az vmss maken](/cli/azure/vmss/#az-vmss-create) . In het volgende voorbeeld wordt een virtuele machineschaalset met de naam *myVMSS met* een door het systeem toegewezen beheerde identiteit gemaakt, zoals gevraagd door de `--assign-identity` parameter. Met de parameters `--admin-username` en `--admin-password` worden de naam van de gebruiker met beheerdersrechten en het wachtwoord van het account voor aanmelding bij de virtuele machine opgegeven. Werk deze waarden bij met waarden die geschikt zijn voor uw omgeving: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Beheerde identiteit met systeem toegewezen op een bestaande Azure-schaalset voor virtuele machines inschakelen

Als u de door het systeem toegewezen beheerde identiteit moet inschakelen op een bestaande Azure-schaalset voor virtuele machines:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machineschaalset bevat.

   ```azurecli-interactive
   az login
   ```

2. Gebruik [de opdracht AZ VMSS-identiteit toewijzen](/cli/azure/vmss/identity/#az-vmss-identity-assign) om een door het systeem toegewezen beheerde identiteit in te schakelen op een bestaande VM:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Beheerde identiteit met systeemtoegewezen uitschakelen vanuit een Azure-schaalset voor virtuele machines

Als u een virtuele machineschaalset hebt die de door het systeem toegewezen beheerde identiteit niet meer nodig heeft, maar nog steeds door de gebruiker toegewezen beheerde identiteiten nodig heeft, gebruikt u de volgende opdracht:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Als u een virtuele machine hebt die geen door het systeem toegewezen beheerde identiteit nodig heeft en geen door de gebruiker toegewezen beheerde identiteiten heeft, gebruikt u de volgende opdracht:

> [!NOTE]
> De `none` waarde is hoofdlettergevoelig. Het moet een kleine letters zijn. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>Door de gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit in- en verwijdert met Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een virtuele machineschaalset

In deze sectie wordt u door het maken van een virtuele machineschaalset en toewijzing van een door de gebruiker toegewezen beheerde identiteit naar de virtuele machineschaalset geleid. Als u al een virtuele machineschaalset hebt die u wilt gebruiken, slaat u deze sectie over en gaat u door naar de volgende.

1. U deze stap overslaan als u al een resourcegroep hebt die u wilt gebruiken. Maak een [resourcegroep](~/articles/azure-resource-manager/management/overview.md#terminology) voor insluiting en implementatie van uw door de gebruiker toegewezen beheerde identiteit met behulp van [de AZ-groep maken](/cli/azure/group/#az-group-create). Vervang de parameterwaarden `<RESOURCE GROUP>` en `<LOCATION>` door uw eigen waarden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Maak een door de gebruiker toegewezen beheerde identiteit met [az identity create](/cli/azure/identity#az-identity-create).  De parameter `-g` geeft de resourcegroep aan waarin de door de gebruiker toegewezen beheerde identiteit wordt gemaakt en de parameter `-n` geeft de naam ervan aan. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Het antwoord bevat details voor de door de gebruiker toegewezen beheerde identiteit die is gemaakt, vergelijkbaar met het volgende. De `id` resourcewaarde die is toegewezen aan de door de gebruiker toegewezen beheerde identiteit wordt in de volgende stap gebruikt.

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

3. Maak een virtuele machine schaal set met behulp [van az vmss maken](/cli/azure/vmss/#az-vmss-create). In het volgende voorbeeld wordt een virtuele machineschaalset gemaakt die is `--assign-identity` gekoppeld aan de nieuwe door de gebruiker toegewezen beheerde identiteit, zoals opgegeven door de parameter. Vervang de parameterwaarden `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` en `<USER ASSIGNED IDENTITY>` door uw eigen waarden. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande virtuele machineschaalset

1. Maak een door de gebruiker toegewezen beheerde identiteit met [az identity create](/cli/azure/identity#az-identity-create).  De parameter `-g` geeft de resourcegroep aan waarin de door de gebruiker toegewezen beheerde identiteit wordt gemaakt en de parameter `-n` geeft de naam ervan aan. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Het antwoord bevat details voor de door de gebruiker toegewezen beheerde identiteit die is gemaakt, vergelijkbaar met het volgende.

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

2. Wijs de door de gebruiker toegewezen beheerde identiteit toe aan uw virtuele machineschaalset met [az vmss-identiteit.](/cli/azure/vmss/identity) Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VIRTUAL MACHINE SCALE SET NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY>` resourceeigenschap van de gebruiker `name` is de resourceeigenschap van de gebruiker, zoals gemaakt in de vorige stap:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een Azure-schaalset voor virtuele machines

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen uit een schaalset van een virtuele machine, moet [u de az vmss-identiteit verwijderen.](/cli/azure/vmss/identity#az-vmss-identity-remove) Als dit de enige door de gebruiker toegewezen beheerde `UserAssigned` identiteit is die is toegewezen aan de virtuele machineschaalset, wordt deze verwijderd uit de waarde van het identiteitstype.  Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VIRTUAL MACHINE SCALE SET NAME>` door uw eigen waarden. Het `<USER ASSIGNED IDENTITY>` zal de door de gebruiker `name` toegewezen beheerde identiteit eigenschap, die kan worden `az vmss identity show`gevonden in de identiteit sectie van de virtuele machine schaal ingesteld met behulp van:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Als uw virtuele machineschaalset geen door het systeem toegewezen beheerde identiteit heeft en u alle door de gebruiker toegewezen beheerde identiteiten wilt verwijderen, gebruikt u de volgende opdracht:

> [!NOTE]
> De `none` waarde is hoofdlettergevoelig. Het moet een kleine letters zijn.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Als uw virtuele machineschaalset zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten heeft, u alle door de gebruiker toegewezen identiteiten verwijderen door over te schakelen naar alleen door het systeem toegewezen beheerde identiteit. Gebruik de volgende opdracht:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-bronnen](overview.md)
- Zie voor de volledige Azure-set voor het maken van virtuele machinevoor stel Quickstart: 

  - [Een virtuele machineschaalset maken met CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)


















---
title: Beheerde identiteiten configureren op Azure VM met Azure CLI - Azure AD
description: Stapsgewijze instructies voor het configureren van systeem- en door de gebruiker toegewezen beheerde identiteiten op een Azure VM met Azure CLI.
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
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f2efaceefc53b3c0b5dfd899baf9fd30fdf9a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244145"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Beheerde identiteiten configureren voor Azure-resources op een Azure VM met Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben. 

In dit artikel leert u met de Azure CLI hoe u de volgende beheerde identiteiten uitvoert voor Azure-resourcesbewerkingen op een Azure VM:

- De door het systeem toegewezen beheerde identiteit op een Azure VM in- en uitschakelen
- Een door de gebruiker toegewezen beheerde identiteit toevoegen en verwijderen op een Azure VM

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de voorbeelden van CLI-scripten wilt uitvoeren, hebt u drie opties:
    - [Azure Cloud Shell gebruiken](../../cloud-shell/overview.md) vanuit de Azure-portal (zie volgende sectie).
    - Gebruik de ingesloten Azure Cloud Shell via de knop 'Probeer het' in de rechterbovenhoek van elk codeblok.
    - [Installeer de nieuwste versie van de Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) als u liever een lokale CLI-console gebruikt. 
      
      > [!NOTE]
      > De opdrachten zijn bijgewerkt om de nieuwste versie van de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)weer te geven.     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u de door het systeem toegewezen beheerde identiteit op een Azure VM in- en uitschakelt met Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Beheerde identiteit met systeemtoegewezen status inschakelen tijdens het maken van een Azure VM

Als u een Azure VM wilt maken met de door het systeem toegewezen beheerde identiteit ingeschakeld, heeft uw account de roltoewijzing [voor virtuele machineinzenderbijdrage](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) nodig.  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waaronder u de VM wilt implementeren:

   ```azurecli-interactive
   az login
   ```

2. Maak met [az group create](/cli/azure/group/#az-group-create) een [resourcegroep](../../azure-resource-manager/management/overview.md#terminology) voor insluiting en implementatie van uw VM en de bijbehorende bronnen. U kunt deze stap overslaan als u al een resourcegroep hebt die u in plaats daarvan wilt gebruiken:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Maak een VM met [az vm create](/cli/azure/vm/#az-vm-create). In het volgende voorbeeld wordt een VM met de naam *myVM* met een door het systeem toegewezen beheerde identiteit, zoals gevraagd door de `--assign-identity` parameter. Met de parameters `--admin-username` en `--admin-password` worden de naam van de gebruiker met beheerdersrechten en het wachtwoord van het account voor aanmelding bij de virtuele machine opgegeven. Werk deze waarden bij met waarden die geschikt zijn voor uw omgeving: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Beheerde identiteit met systeemtoegewezen op een bestaande Azure-vm inschakelen

Als u beheerde identiteit met systeemtoewijzing op een virtuele apparaat wilt inschakelen, heeft uw account de roltoewijzing [voor virtuele machineinzendernodig.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.

   ```azurecli-interactive
   az login
   ```

2. Gebruik [az vm-identiteit toewijzen](/cli/azure/vm/identity/) met de `identity assign` opdracht schakel de systeem-toegewezen identiteit in een bestaande VM:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Systeemtoegewezen identiteit uitschakelen vanaf een Azure VM

Als u de door het systeem toegewezen beheerde identiteit op een virtuele apparaat wilt uitschakelen, heeft uw account de roltoewijzing [voor virtuele machineinzender nodig.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

Als u een virtuele machine hebt die de door het systeem toegewezen identiteit niet meer nodig heeft, maar nog steeds door de gebruiker toegewezen identiteiten nodig heeft, gebruikt u de volgende opdracht:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Als u een virtuele machine hebt die geen door het systeem toegewezen identiteit nodig heeft en geen door de gebruiker toegewezen identiteiten heeft, gebruikt u de volgende opdracht:

> [!NOTE]
> De `none` waarde is hoofdlettergevoelig. Het moet een kleine letters zijn. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit toevoegt en verwijdert uit een Azure VM met Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een Azure VM

Als u tijdens het maken van een door de gebruiker toegewezen identiteit aan een vm wilt toewijzen, heeft uw account de roltoewijzingen [Virtual Machine Contributor](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en Managed Identity [Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nodig. Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. U deze stap overslaan als u al een resourcegroep hebt die u wilt gebruiken. Maak een [resourcegroep](~/articles/azure-resource-manager/management/overview.md#terminology) voor insluiting en implementatie van uw door de gebruiker toegewezen beheerde identiteit met behulp van [de AZ-groep maken](/cli/azure/group/#az-group-create). Vervang de parameterwaarden `<RESOURCE GROUP>` en `<LOCATION>` door uw eigen waarden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Maak een door de gebruiker toegewezen beheerde identiteit met [az identity create](/cli/azure/identity#az-identity-create).  De parameter `-g` geeft de resourcegroep aan waarin de door de gebruiker toegewezen beheerde identiteit wordt gemaakt en de parameter `-n` geeft de naam ervan aan.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Het antwoord bevat details voor de door de gebruiker toegewezen beheerde identiteit die is gemaakt, vergelijkbaar met het volgende. De resource-id-waarde die is toegewezen aan de door de gebruiker toegewezen beheerde identiteit, wordt in de volgende stap gebruikt.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
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

3. Maak een VM met [az vm create](/cli/azure/vm/#az-vm-create). In het volgende voorbeeld wordt een VM gemaakt die is `--assign-identity` gekoppeld aan de nieuwe door de gebruiker toegewezen identiteit, zoals opgegeven door de parameter. Vervang de parameterwaarden `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande Azure-vm

Als u een door de gebruiker toegewezen identiteit aan een vm wilt toewijzen, heeft uw account de roltoewijzingen [Virtual Machine Contributor](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en Managed Identity [Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nodig. Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Maak een door de gebruiker toegewezen identiteit met [az identity create](/cli/azure/identity#az-identity-create).  De `-g` parameter geeft de resourcegroep op waar de `-n` door de gebruiker toegewezen identiteit wordt gemaakt en de parameter geeft de naam op. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden:

    > [!IMPORTANT]
    > Het maken van door de gebruiker toegewezen beheerde identiteiten met speciale tekens (d.w.z. onderstrepen) in de naam wordt momenteel niet ondersteund. Gebruik alfanumerieke tekens. Controleer later op updates.  Zie [veelgestelde vragen en bekende problemen voor](known-issues.md) meer informatie

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Het antwoord bevat details voor de door de gebruiker toegewezen beheerde identiteit die is gemaakt, vergelijkbaar met het volgende. 

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

2. Wijs de door de gebruiker toegewezen identiteit toe aan uw VM met behulp van [az vm-identiteit toewijzen](/cli/azure/vm). Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VM NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY NAME>` resourceeigenschap van de gebruiker `name` is de resourceeigenschap van de gebruiker toegewezen, zoals gemaakt in de vorige stap:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een Azure-vm

Als u een door de gebruiker toegewezen identiteit aan een vm wilt verwijderen, heeft uw account de roltoewijzing [voor virtuele machineinzender](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) nodig. 

Als dit de enige door de gebruiker toegewezen `UserAssigned` beheerde identiteit is die aan de virtuele machine is toegewezen, wordt deze verwijderd uit de waarde van het identiteitstype.  Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VM NAME>` door uw eigen waarden. Het `<USER ASSIGNED IDENTITY>` zal de eigenschap van `name` de gebruiker toegewezen identiteit, die kan worden `az vm identity show`gevonden in de identiteit sectie van de virtuele machine met behulp van:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Als uw VM geen door het systeem toegewezen beheerde identiteit heeft en u alle door de gebruiker toegewezen identiteiten wilt verwijderen, gebruikt u de volgende opdracht:

> [!NOTE]
> De `none` waarde is hoofdlettergevoelig. Het moet een kleine letters zijn.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Als uw VM zowel door het systeem toegewezen als door de gebruiker toegewezen identiteiten heeft, u alle door de gebruiker toegewezen identiteiten verwijderen door over te schakelen naar alleen systeemtoegewezen. Gebruik de volgende opdracht:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Volgende stappen
- [Overzicht van beheerde identiteiten voor Azure-bronnen](overview.md)
- Zie voor de volledige Quickstarts voor het maken van Azure VM: 
  - [Een virtuele Windows-machine maken met CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Maak een Linux virtuele machine met CLI](../../virtual-machines/linux/quick-create-cli.md) 


















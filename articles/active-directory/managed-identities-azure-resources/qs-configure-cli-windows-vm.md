---
title: Beheerde identiteiten configureren op Azure VM met behulp van Azure CLI-Azure AD
description: Stapsgewijze instructies voor het configureren van door het systeem en de gebruiker toegewezen beheerde identiteiten op een Azure-VM met behulp van Azure CLI.
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
ms.openlocfilehash: ca02505ba9b7d93cac4216916909a8c6df7fdd05
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184053"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Beheerde identiteiten configureren voor Azure-resources op een Azure-VM met behulp van Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie, zonder dat u referenties hebt in uw code. 

In dit artikel, met behulp van de Azure CLI, leert u hoe u de volgende beheerde identiteiten voor Azure-bronnen bewerkingen kunt uitvoeren op een virtuele Azure-machine:

- De door het systeem toegewezen beheerde identiteit op een virtuele Azure-machine in-en uitschakelen
- Een door de gebruiker toegewezen beheerde identiteit toevoegen aan en verwijderen van een virtuele machine in azure

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend met beheerde identiteiten voor Azure-resources bent, lees de [overzichtssectie](overview.md). **Lees de [verschil tussen een beheerde identiteit door het systeem is toegewezen en de gebruiker toegewezen](overview.md#how-does-it-work)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u wilt de CLI-scriptvoorbeelden uitvoeren, hebt u drie opties:
    - Gebruik [Azure Cloud Shell](../../cloud-shell/overview.md) vanuit Azure portal (Zie volgende sectie).
    - Gebruik de ingesloten Azure Cloud Shell via het 'Try It' de knop, zich in de rechterbovenhoek van elk codeblok.
    - [Installeer de nieuwste versie van de Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) als u liever een lokale cli-console gebruikt. 
      
      > [!NOTE]
      > De opdrachten zijn bijgewerkt om de nieuwste versie van de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli)weer te geven.     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u de door het systeem toegewezen beheerde identiteit in-en uitschakelt op een Azure-VM met behulp van Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele Azure-machine

Als u een Azure-VM wilt maken met de door het systeem toegewezen beheerde identiteit, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waaronder u de VM wilt implementeren:

   ```azurecli-interactive
   az login
   ```

2. Maak met [az group create](../../azure-resource-manager/resource-group-overview.md#terminology) een [resourcegroep](/cli/azure/group/#az-group-create) voor insluiting en implementatie van uw VM en de bijbehorende bronnen. U kunt deze stap overslaan als u al een resourcegroep hebt die u in plaats daarvan wilt gebruiken:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Maak een VM met [az vm create](/cli/azure/vm/#az-vm-create). In het volgende voor beeld wordt een virtuele machine met de naam *myVM* gemaakt met een door het systeem toegewezen beheerde identiteit, zoals aangevraagd door de para meter `--assign-identity`. Met de parameters `--admin-username` en `--admin-password` worden de naam van de gebruiker met beheerdersrechten en het wachtwoord van het account voor aanmelding bij de virtuele machine opgegeven. Werk deze waarden bij met waarden die geschikt zijn voor uw omgeving: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande virtuele machine van Azure

Als u door het systeem toegewezen beheerde identiteit op een virtuele machine wilt inschakelen, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.

   ```azurecli-interactive
   az login
   ```

2. Gebruik [AZ VM Identity Assign](/cli/azure/vm/identity/) met de `identity assign` opdracht de door het systeem toegewezen identiteit in te scha kelen voor een bestaande virtuele machine:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Door het systeem toegewezen identiteit uitschakelen vanuit een Azure-VM

Als u een door het systeem toegewezen beheerde identiteit op een VM wilt uitschakelen, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

Als u een virtuele machine hebt die de door het systeem toegewezen identiteit niet meer nodig heeft, maar nog steeds door de gebruiker toegewezen identiteiten nodig hebt, gebruikt u de volgende opdracht:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Als u een virtuele machine hebt die geen door het systeem toegewezen identiteit meer nodig heeft en geen door de gebruiker toegewezen identiteiten heeft, gebruikt u de volgende opdracht:

> [!NOTE]
> De waarde `none` is hoofdletter gevoelig. Het moet een kleine letter zijn. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt toevoegen aan en verwijderen uit een Azure-VM met behulp van Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een virtuele Azure-machine

Voor het toewijzen van een door de gebruiker toegewezen identiteit aan een virtuele machine tijdens het maken van uw account moeten de roltoewijzingen voor de rol van de [virtuele machines](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en de rollen voor [beheerde identiteits operators](/azure/role-based-access-control/built-in-roles#managed-identity-operator) worden toegewezen. Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. U kunt deze stap overs Laan als u al een resource groep hebt die u wilt gebruiken. Maak een [resource groep](~/articles/azure-resource-manager/resource-group-overview.md#terminology) voor insluiting en implementatie van uw door de gebruiker toegewezen beheerde identiteit met behulp van [AZ Group Create](/cli/azure/group/#az-group-create). Vervang de parameterwaarden `<RESOURCE GROUP>` en `<LOCATION>` door uw eigen waarden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Maak een door de gebruiker toegewezen beheerde identiteit met [az identity create](/cli/azure/identity#az-identity-create).  De parameter `-g` geeft de resourcegroep aan waarin de door de gebruiker toegewezen beheerde identiteit wordt gemaakt en de parameter `-n` geeft de naam ervan aan.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Het antwoord bevat Details voor de door de gebruiker toegewezen beheerde identiteit, vergelijkbaar met de volgende. De resource-ID-waarde die is toegewezen aan de door de gebruiker toegewezen beheerde identiteit, wordt in de volgende stap gebruikt.

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

3. Maak een VM met [az vm create](/cli/azure/vm/#az-vm-create). In het volgende voor beeld wordt een VM gemaakt die is gekoppeld aan de nieuwe door de gebruiker toegewezen identiteit, zoals opgegeven door de para meter `--assign-identity`. Vervang de parameterwaarden `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande virtuele machine van Azure

Als u een door de gebruiker toegewezen identiteit aan een VM wilt toewijzen, moet uw account de roltoewijzingen van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en de rollen voor de [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) hebben. Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Maak een door de gebruiker toegewezen identiteit met [az identity create](/cli/azure/identity#az-identity-create).  Met de para meter `-g` wordt de resource groep opgegeven waarin de door de gebruiker toegewezen identiteit wordt gemaakt en de para meter `-n` de naam. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden:

    > [!IMPORTANT]
    > Het is momenteel niet mogelijk om door de gebruiker toegewezen beheerde identiteiten te maken met speciale tekens (zoals een onderstrepings teken) in de naam. Gebruik alfanumerieke tekens. Controleer later op updates.  Zie [Veelgestelde vragen en bekende problemen](known-issues.md) voor meer informatie

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Het antwoord bevat Details voor de door de gebruiker toegewezen beheerde identiteit, vergelijkbaar met de volgende. 

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

2. Wijs de door de gebruiker toegewezen identiteit toe aan uw virtuele machine met [AZ VM Identity Assign](/cli/azure/vm). Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VM NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY NAME>` is de eigenschap resource van de door de gebruiker toegewezen beheerde identiteit `name`, zoals deze in de vorige stap is gemaakt:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een Azure-VM

Als u een door de gebruiker toegewezen identiteit wilt verwijderen naar een virtuele machine, moet uw account de roltoewijzing van de [virtuele machines](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben. 

Als dit de enige door de gebruiker toegewezen beheerde identiteit is toegewezen aan de virtuele machine, wordt `UserAssigned` verwijderd uit de waarde van het identiteits type.  Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VM NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY>` wordt de eigenschap `name` van de door de gebruiker toegewezen identiteit, die u kunt vinden in de sectie identiteit van de virtuele machine met behulp van `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Als uw virtuele machine geen door het systeem toegewezen beheerde identiteit heeft en u alle door de gebruiker toegewezen identiteiten wilt verwijderen, gebruikt u de volgende opdracht:

> [!NOTE]
> De waarde `none` is hoofdletter gevoelig. Het moet een kleine letter zijn.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Als uw virtuele machine zowel door het systeem toegewezen als door de gebruiker toegewezen identiteiten heeft, kunt u alle door de gebruiker toegewezen identiteiten verwijderen door te scha kelen naar alleen systeem toegewezen. Gebruik de volgende opdracht:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Volgende stappen
- [Beheerde identiteiten voor een overzicht van Azure-resources](overview.md)
- Zie voor de volledige Snelstartgids voor het maken van virtuele Azure-machines: 
  - [Een virtuele Windows-machine maken met CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Een virtuele Linux-machine maken met CLI](../../virtual-machines/linux/quick-create-cli.md) 


















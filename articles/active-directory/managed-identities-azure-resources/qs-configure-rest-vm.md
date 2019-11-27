---
title: Beheerde identiteiten op een Azure-VM configureren met behulp van REST-Azure AD
description: Stapsgewijze instructies voor het configureren van een door een systeem en de gebruiker toegewezen beheerde identiteiten op een Azure-VM met behulp van krul om REST API aanroepen te maken.
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
ms.date: 06/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d78ddaaae886a33b4d22e8724ade04ab63508f1
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547339"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Beheerde identiteiten configureren voor Azure-resources op een Azure-VM met behulp van REST API-aanroepen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources biedt Azure-Services met een automatisch beheerde systeem identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie, zonder dat u referenties hebt in uw code. 

In dit artikel, met behulp van krul om aanroepen naar het Azure Resource Manager REST-eind punt te maken, leert u hoe u de volgende beheerde identiteiten voor Azure-resources kunt uitvoeren op een virtuele Azure-machine:

- De door het systeem toegewezen beheerde identiteit op een virtuele Azure-machine in-en uitschakelen
- Een door de gebruiker toegewezen beheerde identiteit toevoegen aan en verwijderen van een virtuele machine in azure

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u Windows gebruikt, installeert u het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) of gebruikt u de [Azure Cloud Shell](../../cloud-shell/overview.md) in de Azure Portal.
- [Installeer de lokale Azure cli-console](/cli/azure/install-azure-cli)als u het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) of een [Linux-distributie besturingssysteem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)gebruikt.
- Als u de lokale Azure CLI-console gebruikt, meldt u zich aan bij Azure met `az login` met een account dat is gekoppeld aan het Azure-abonnement waarvoor u systeem-of door de gebruiker toegewezen beheerde identiteiten wilt beheren.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u door het systeem toegewezen beheerde identiteit kunt inschakelen en uitschakelen op een Azure-VM met behulp van krul om aanroepen naar het Azure Resource Manager REST-eind punt te maken.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele Azure-machine

Als u een Azure-VM wilt maken met de door het systeem toegewezen beheerde identiteit, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Maak met [az group create](/cli/azure/group/#az-group-create) een [resourcegroep](../../azure-resource-manager/resource-group-overview.md#terminology) voor insluiting en implementatie van uw VM en de bijbehorende bronnen. U kunt deze stap overslaan als u al een resourcegroep hebt die u in plaats daarvan wilt gebruiken:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Een [netwerk interface](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) maken voor uw VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele machine te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Maak een virtuele machine met behulp van krul om het Azure Resource Manager REST-eind punt aan te roepen. In het volgende voor beeld wordt een virtuele machine met de naam *myVM* gemaakt met een door het systeem toegewezen beheerde identiteit, zoals aangegeven in de hoofd tekst van de aanvraag met de waarde `"identity":{"type":"SystemAssigned"}`. Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen wanneer u een Bearer-toegangs token en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Aanvraag headers**
   
   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        | 
   
   **Aanvraag tekst**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Door het systeem toegewezen identiteit inschakelen op een bestaande virtuele machine van Azure

Als u door het systeem toegewezen beheerde identiteit wilt inschakelen op een virtuele machine die oorspronkelijk is ingericht zonder deze, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele machine te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Gebruik de volgende krul opdracht om het Azure Resource Manager REST-eind punt aan te roepen om door het systeem toegewezen beheerde identiteit op uw VM in te scha kelen, zoals in de hoofd tekst van de aanvraag wordt aangegeven door de waarde `{"identity":{"type":"SystemAssigned"}` voor een virtuele machine met de naam *myVM*.  Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen wanneer u een Bearer-toegangs token en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.
   
   > [!IMPORTANT]
   > Om ervoor te zorgen dat bestaande door de gebruiker toegewezen beheerde identiteiten die aan de VM zijn toegewezen, niet worden verwijderd, moet u de door de gebruiker toegewezen beheerde identiteiten vermelden met behulp van deze krul opdracht: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Als er door de gebruiker toegewezen beheerde identiteiten zijn toegewezen aan de VM, zoals wordt aangegeven in de `identity` waarde in het antwoord, gaat u verder met stap 3 waarin wordt uitgelegd hoe u door de gebruiker toegewezen beheerde identiteiten kunt behouden terwijl de door het systeem toegewezen beheerde identiteit wordt ingeschakeld op de virtuele machine.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        | 
   
   **Aanvraag tekst**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Als u door het systeem toegewezen beheerde identiteit wilt inschakelen op een virtuele machine met bestaande door de gebruiker toegewezen beheerde identiteiten, moet u `SystemAssigned` toevoegen aan de `type` waarde.  
   
   Als uw virtuele machine bijvoorbeeld de door de gebruiker toegewezen beheerde identiteiten `ID1` en `ID2` hieraan heeft toegewezen, en u aan de virtuele machine een door het systeem toegewezen beheerde identiteit wilt toevoegen, gebruikt u de volgende krul aanroep. Vervang `<ACCESS TOKEN>` en `<SUBSCRIPTION ID>` door de waarden die van toepassing zijn op uw omgeving.

   API-versie `2018-06-01` door de gebruiker toegewezen beheerde identiteiten op te slaan in de `userAssignedIdentities` waarde in een woordenlijst indeling, in plaats van de `identityIds` waarde in een matrix indeling die wordt gebruikt in API-versie `2017-12-01`.
   
   **API-VERSIE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        | 

   **Aanvraag tekst**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **API-VERSIE 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        | 

   **Aanvraag tekst**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Door het systeem toegewezen beheerde identiteit uitschakelen vanuit een Azure-VM

Als u een door het systeem toegewezen beheerde identiteit op een VM wilt uitschakelen, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele machine te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Werk de virtuele machine bij met krul om het Azure Resource Manager REST-eind punt aan te roepen om de door het systeem toegewezen beheerde identiteit uit te scha kelen.  In het volgende voor beeld wordt de door het systeem toegewezen beheerde identiteit uitgeschakeld, zoals aangegeven in de hoofd tekst van de aanvraag door de waarde `{"identity":{"type":"None"}}` van een virtuele machine met de naam *myVM*.  Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen wanneer u een Bearer-toegangs token en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.

   > [!IMPORTANT]
   > Om ervoor te zorgen dat bestaande door de gebruiker toegewezen beheerde identiteiten die aan de VM zijn toegewezen, niet worden verwijderd, moet u de door de gebruiker toegewezen beheerde identiteiten vermelden met behulp van deze krul opdracht: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Als er door de gebruiker toegewezen beheerde identiteiten zijn toegewezen aan de VM, zoals wordt aangegeven in de `identity` waarde in het antwoord, gaat u verder met stap 3 waarin wordt uitgelegd hoe u door de gebruiker toegewezen beheerde identiteiten kunt behouden terwijl de door het systeem toegewezen beheerde identiteit op uw virtuele machine wordt uitgeschakeld.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        | 

   **Aanvraag tekst**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Als u een door het systeem toegewezen beheerde identiteit wilt verwijderen van een virtuele machine die door de gebruiker toegewezen beheerde identiteiten heeft, verwijdert u `SystemAssigned` uit de `{"identity":{"type:" "}}` waarde en houdt u de `UserAssigned` waarde en de `userAssignedIdentities` woordenlijst waarden als u **API-versie 2018-06-01**gebruikt. Als u **API versie 2017-12-01** of eerder gebruikt, moet u de `identityIds`-matrix.

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u door de gebruiker toegewezen beheerde identiteit kunt toevoegen aan en verwijderen uit een Azure-VM met behulp van krul om aanroepen naar het Azure Resource Manager REST-eind punt te maken.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een virtuele Azure-machine

Als u een door de gebruiker toegewezen identiteit aan een VM wilt toewijzen, moet uw account de roltoewijzingen van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en de rollen voor de [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) hebben. Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele machine te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Een [netwerk interface](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) maken voor uw VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele machine te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Maak een door de gebruiker toegewezen beheerde identiteit met behulp van de instructies die hier worden gevonden: [Maak een door de gebruiker toegewezen beheerde identiteit](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Maak een virtuele machine met behulp van krul om het Azure Resource Manager REST-eind punt aan te roepen. In het volgende voor beeld wordt een VM gemaakt met de naam *myVM* in de resource groep *myResourceGroup* met een door de gebruiker toegewezen beheerde identiteit `ID1`, zoals aangegeven in de hoofd tekst van de aanvraag met de waarde `"identity":{"type":"UserAssigned"}`. Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen wanneer u een Bearer-toegangs token en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.
 
   **API-VERSIE 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        | 

   **Aanvraag tekst**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **API-VERSIE 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        | 

   **Aanvraag tekst**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande virtuele machine van Azure

Als u een door de gebruiker toegewezen identiteit aan een VM wilt toewijzen, moet uw account de roltoewijzingen van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en de rollen voor de [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) hebben. Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele machine te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Een door de gebruiker toegewezen beheerde identiteit maken met behulp van de instructies die hier worden gevonden, maakt u een door de [gebruiker toegewezen beheerde identiteit](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Als u ervoor wilt zorgen dat bestaande door de gebruiker of het systeem toegewezen beheerde identiteiten die aan de VM zijn toegewezen, niet worden verwijderd, moet u de volgende krul opdracht gebruiken om de identiteits typen weer te geven die aan de virtuele machine zijn toegewezen. Als u beheerde identiteiten hebt toegewezen aan de schaalset voor virtuele machines, worden deze vermeld onder in de waarde `identity`.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.

    Als er door een gebruiker of door het systeem toegewezen beheerde identiteiten zijn toegewezen aan de virtuele machine, zoals wordt aangegeven in de `identity` waarde in het antwoord, gaat u verder met stap 5 waarin wordt uitgelegd hoe u de door het systeem toegewezen beheerde identiteit kunt behouden wanneer u een door de gebruiker toegewezen beheerde identiteit toevoegt aan uw VM.

4. Als er geen door de gebruiker toegewezen beheerde identiteiten aan uw virtuele machine zijn toegewezen, gebruikt u de volgende krul opdracht om het Azure Resource Manager REST-eind punt aan te roepen om de eerste door de gebruiker toegewezen beheerde identiteit aan de virtuele machine toe te wijzen.

   In de volgende voor beelden wordt een door de gebruiker toegewezen beheerde identiteit toegewezen `ID1` aan een virtuele machine met de naam *myVM* in de resource groep *myResourceGroup*.  Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen wanneer u een Bearer-toegangs token en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.

   **API-VERSIE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        |
 
   **Aanvraag tekst**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **API-VERSIE 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        | 

   **Aanvraag tekst**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. Als u een bestaande door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit hebt die aan uw VM is toegewezen:
   
   **API-VERSIE 2018-06-01**

   Voeg de door de gebruiker toegewezen beheerde identiteit toe aan de `userAssignedIdentities` woordenlijst waarde.
    
   Bijvoorbeeld als u een door het systeem toegewezen beheerde identiteit en de door de gebruiker toegewezen beheerde identiteit `ID1` die momenteel aan uw virtuele machine zijn toegewezen, wilt toevoegen aan de door de gebruiker toegewezen beheerde identiteit `ID2`:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        | 

   **Aanvraag tekst**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **API-VERSIE 2017-12-01**

   Behoud de door de gebruiker toegewezen beheerde identiteiten die u in de matrix waarde `identityIds` wilt behouden wanneer u de nieuwe door de gebruiker toegewezen beheerde identiteit toevoegt.

   Bijvoorbeeld als u een door het systeem toegewezen beheerde identiteit en de door de gebruiker toegewezen beheerde identiteit `ID1` die momenteel aan uw virtuele machine zijn toegewezen, wilt toevoegen aan de door de gebruiker toegewezen beheerde identiteit `ID2`: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        | 

   **Aanvraag tekst**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een Azure-VM

Als u een door de gebruiker toegewezen identiteit wilt verwijderen naar een virtuele machine, moet uw account de roltoewijzing van de [virtuele machines](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.

1. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele machine te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Om ervoor te zorgen dat u geen bestaande door de gebruiker toegewezen beheerde identiteiten verwijdert die u aan de virtuele machine wilt laten toewijzen of de door het systeem toegewezen beheerde identiteit wilt verwijderen, moet u de beheerde identiteiten weer geven met behulp van de volgende krul opdracht: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.
 
   Als u beheerde identiteiten aan de virtuele machine hebt toegewezen, worden deze weer gegeven in de reactie in de `identity` waarde.

   Als u bijvoorbeeld beheerde identiteiten hebt `ID1` en `ID2` toegewezen aan uw virtuele machine, en u wilt de door het systeem toegewezen identiteit alleen `ID1` toegewezen houden en vasthouden:
   
   **API-VERSIE 2018-06-01**

   Voeg `null` toe aan de door de gebruiker toegewezen beheerde identiteit die u wilt verwijderen:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        | 

   **Aanvraag tekst**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **API-VERSIE 2017-12-01**

   Bewaar alleen de door de gebruiker toegewezen beheerde identiteiten die u in de `identityIds` matrix wilt behouden:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.        | 

   **Aanvraag tekst**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Als uw virtuele machine zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten heeft, kunt u alle door de gebruiker toegewezen beheerde identiteiten verwijderen door te scha kelen naar het gebruik van alleen door het systeem toegewezen beheerde identiteit met de volgende opdracht:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Aanvraag headers**

|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
|*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token. | 

**Aanvraag tekst**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Als uw virtuele machine alleen door de gebruiker toegewezen beheerde identiteiten heeft en u deze allemaal wilt verwijderen, gebruikt u de volgende opdracht:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Aanvraag headers**

|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
|*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token.| 

**Aanvraag tekst**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie voor informatie over het maken, weer geven of verwijderen van door de gebruiker toegewezen beheerde identiteiten met behulp van:

- [Door de gebruiker toegewezen beheerde identiteiten maken, weer geven of verwijderen met behulp van REST API-aanroepen](how-to-manage-ua-identity-rest.md)

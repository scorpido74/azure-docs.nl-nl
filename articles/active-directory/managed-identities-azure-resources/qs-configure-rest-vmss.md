---
title: Beheerde identiteiten op Azure VMSS configureren met behulp van REST-Azure AD
description: Stapsgewijze instructies voor het configureren van een door een systeem en de gebruiker toegewezen beheerde identiteiten op een Azure-VMSS met behulp van krul om REST API-aanroepen te maken.
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
ms.openlocfilehash: f757819e16db9908e80c5e7350c082dbf1573677
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547236"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Beheerde identiteiten configureren voor Azure-resources op een schaalset voor virtuele machines met behulp van REST API-aanroepen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources biedt Azure-Services met een automatisch beheerde systeem identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie, zonder dat u referenties hebt in uw code. 

In dit artikel, met behulp van krul om aanroepen naar het Azure Resource Manager REST-eind punt te maken, leert u hoe u de volgende beheerde identiteiten voor Azure-bronnen bewerkingen kunt uitvoeren op een schaalset voor virtuele machines:

- De door het systeem toegewezen beheerde identiteit inschakelen en uitschakelen op een virtuele-machine schaalset van Azure
- Een door de gebruiker toegewezen beheerde identiteit toevoegen aan en verwijderen uit een Azure virtual machine-schaalset

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de beheer bewerkingen in dit artikel wilt uitvoeren, moet uw account de volgende toegangs beheer toewijzingen op basis van Azure-rollen hebben:

    > [!NOTE]
    > Er zijn geen aanvullende Azure AD Directory-roltoewijzingen vereist.

    - [Inzender voor virtuele machines](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) voor het maken van een schaalset voor virtuele machines en het inschakelen en verwijderen van door het systeem en/of gebruiker toegewezen beheerde identiteit van een schaalset voor virtuele machines.
    - Rol van [beheerde identiteit Inzender](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) om een door de gebruiker toegewezen beheerde identiteit te maken.
    - De rol [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) voor het toewijzen en verwijderen van een door de gebruiker toegewezen identiteit van en naar een virtuele-machine schaalset.
- Als u Windows gebruikt, installeert u het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) of gebruikt u de [Azure Cloud Shell](../../cloud-shell/overview.md) in de Azure Portal.
- [Installeer de lokale Azure cli-console](/cli/azure/install-azure-cli)als u het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) of een [Linux-distributie besturingssysteem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)gebruikt.
- Als u de lokale Azure CLI-console gebruikt, meldt u zich aan bij Azure met `az login` met een account dat is gekoppeld aan het Azure-abonnement waarvoor u systeem-of door de gebruiker toegewezen beheerde identiteiten wilt beheren.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u door het systeem toegewezen beheerde identiteit kunt inschakelen en uitschakelen op een schaalset voor virtuele machines met behulp van krul om aanroepen naar het Azure Resource Manager REST-eind punt te maken.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een schaalset voor virtuele machines

Als u een schaalset voor virtuele machines wilt maken waarvoor een door het systeem toegewezen beheerde identiteit is ingeschakeld, moet u een schaalset voor virtuele machines maken en een toegangs Token ophalen om krul te gebruiken om het Resource Manager-eind punt aan te roepen van de waarde van het door het systeem toegewezen beheerde identiteits type.

1. Maak een [resource groep](../../azure-resource-manager/resource-group-overview.md#terminology) voor insluiting en implementatie van uw schaalset voor virtuele machines en de bijbehorende gerelateerde resources met behulp van [AZ Group Create](/cli/azure/group/#az-group-create). U kunt deze stap overslaan als u al een resourcegroep hebt die u in plaats daarvan wilt gebruiken:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Een [netwerk interface](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) maken voor de schaalset voor virtuele machines:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw schaalset voor virtuele machines te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Maak een schaalset voor virtuele machines met behulp van krul om het Azure Resource Manager REST-eind punt aan te roepen. In het volgende voor beeld wordt een schaalset voor virtuele machines gemaakt met de naam *myVMSS* in de *myResourceGroup* met een door het systeem toegewezen beheerde identiteit, zoals aangegeven in de hoofd tekst van de aanvraag door de waarde `"identity":{"type":"SystemAssigned"}`. Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen wanneer u een Bearer-toegangs token en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token. | 

   **Aanvraag tekst**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande schaalset voor virtuele machines

Als u door het systeem toegewezen beheerde identiteit wilt inschakelen voor een bestaande schaalset voor virtuele machines, moet u een toegangs token verkrijgen en vervolgens krul gebruiken om het type van de Resource Manager REST-eind punt bij te werken.

1. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw schaalset voor virtuele machines te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Gebruik de volgende krul opdracht om het Azure Resource Manager REST-eind punt aan te roepen om de door het systeem toegewezen beheerde identiteit in te scha kelen voor de schaalset van de virtuele machine, zoals aangegeven in de hoofd tekst van de aanvraag door de waarde `{"identity":{"type":"SystemAssigned"}` voor een virtuele-machine schaalset met de naam *myVMSS*.  Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen wanneer u een Bearer-toegangs token en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.
   
   > [!IMPORTANT]
   > Om ervoor te zorgen dat u geen bestaande door de gebruiker toegewezen beheerde identiteiten verwijdert die aan de schaalset voor virtuele machines zijn toegewezen, moet u de door de gebruiker toegewezen beheerde identiteiten vermelden met behulp van deze krul opdracht: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Als u een door de gebruiker toegewezen beheerde identiteit hebt die is toegewezen aan de virtuele-machine schaalset, zoals aangegeven in de `identity` waarde in het antwoord, gaat u verder met stap 3 waarin wordt uitgelegd hoe u door de gebruiker toegewezen beheerde identiteiten kunt behouden terwijl de door het systeem toegewezen beheerde identiteit wordt ingeschakeld voor de schaalset van de virtuele machine.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

3. Als u door het systeem toegewezen beheerde identiteit wilt inschakelen voor een schaalset voor virtuele machines met bestaande door de gebruiker toegewezen beheerde identiteiten, moet u `SystemAssigned` toevoegen aan de `type` waarde.  
   
   Als uw schaalset voor virtuele machines bijvoorbeeld de door de gebruiker toegewezen beheerde identiteiten `ID1` en `ID2` heeft toegewezen, en u aan de door het systeem toegewezen beheerde identiteit wilt toevoegen aan de schaalset van de virtuele machine, gebruikt u de volgende krul oproep. Vervang `<ACCESS TOKEN>` en `<SUBSCRIPTION ID>` door de waarden die van toepassing zijn op uw omgeving.

   API-versie `2018-06-01` door de gebruiker toegewezen beheerde identiteiten op te slaan in de `userAssignedIdentities` waarde in een woordenlijst indeling, in plaats van de `identityIds` waarde in een matrix indeling die wordt gebruikt in API-versie `2017-12-01`.
   
   **API-VERSIE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
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

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit uitschakelen vanuit een schaalset voor virtuele machines

Als u een door het systeem toegewezen identiteit wilt uitschakelen voor een bestaande schaalset voor virtuele machines, moet u een toegangs token verkrijgen en vervolgens krul gebruiken om het type van de Resource Manager REST-eind punt bij te werken naar `None`.

1. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw schaalset voor virtuele machines te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Werk de schaalset voor virtuele machines met krul bij om het Azure Resource Manager REST-eind punt aan te roepen om door het systeem toegewezen beheerde identiteit uit te scha kelen.  In het volgende voor beeld wordt de door het systeem toegewezen beheerde identiteit uitgeschakeld, zoals aangegeven in de hoofd tekst van de aanvraag door de waarde `{"identity":{"type":"None"}}` uit een schaalset voor virtuele machines met de naam *myVMSS*.  Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen wanneer u een Bearer-toegangs token en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.

   > [!IMPORTANT]
   > Om ervoor te zorgen dat u geen bestaande door de gebruiker toegewezen beheerde identiteiten verwijdert die aan de schaalset voor virtuele machines zijn toegewezen, moet u de door de gebruiker toegewezen beheerde identiteiten vermelden met behulp van deze krul opdracht: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Als u een door de gebruiker toegewezen beheerde identiteit hebt die is toegewezen aan de schaalset voor virtuele machines, gaat u naar stap 3 waarin wordt getoond hoe de door de gebruiker toegewezen beheerde identiteiten behouden blijven terwijl de door het systeem toegewezen beheerde identiteit wordt verwijderd uit de schaalset van de virtuele machine.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"None"
       }
    }
   ```

   Als u door het systeem toegewezen beheerde identiteit wilt verwijderen uit een schaalset voor virtuele machines die door de gebruiker toegewezen beheerde identiteiten heeft, verwijdert u `SystemAssigned` uit de `{"identity":{"type:" "}}` waarde en houdt u de `UserAssigned` waarde en de `userAssignedIdentities` woordenlijst waarden als u **API-versie 2018-06-01**gebruikt. Als u **API versie 2017-12-01** of eerder gebruikt, moet u de `identityIds`-matrix.

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u door de gebruiker toegewezen beheerde identiteit kunt toevoegen aan en verwijderen uit een schaalset voor virtuele machines met behulp van krul om aanroepen naar het Azure Resource Manager REST-eind punt te maken.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een schaalset voor virtuele machines

1. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw schaalset voor virtuele machines te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Een [netwerk interface](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) maken voor de schaalset voor virtuele machines:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw schaalset voor virtuele machines te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Maak een door de gebruiker toegewezen beheerde identiteit met behulp van de instructies die hier worden gevonden: [Maak een door de gebruiker toegewezen beheerde identiteit](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Maak een schaalset voor virtuele machines met behulp van krul om het Azure Resource Manager REST-eind punt aan te roepen. In het volgende voor beeld wordt een schaalset voor virtuele machines gemaakt met de naam *myVMSS* in de resource groep *myResourceGroup* met een door de gebruiker toegewezen beheerde identiteit `ID1`, zoals aangegeven in de hoofd tekst van de aanvraag met de waarde `"identity":{"type":"UserAssigned"}`. Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen wanneer u een Bearer-toegangs token en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.
 
   **API-VERSIE 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token. | 

   **Aanvraag tekst**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **API-VERSIE 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Inhouds type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token. |
 
   **Aanvraag tekst**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande schaalset voor virtuele Azure-machines

1. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw schaalset voor virtuele machines te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Een door de gebruiker toegewezen beheerde identiteit maken met behulp van de instructies die hier worden gevonden, maakt u een door de [gebruiker toegewezen beheerde identiteit](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Om ervoor te zorgen dat bestaande door de gebruiker of het systeem toegewezen beheerde identiteiten die aan de virtuele-machine schaalset zijn toegewezen, niet worden verwijderd, moet u de identiteits typen weer geven die zijn toegewezen aan de schaalset voor virtuele machines met behulp van de volgende krul opdracht. Als u beheerde identiteiten hebt toegewezen aan de schaalset voor virtuele machines, worden deze weer gegeven in de `identity` waarde.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token. |   
 

4. Als u geen door de gebruiker of het systeem toegewezen beheerde identiteiten hebt die zijn toegewezen aan de schaalset van uw virtuele machines, gebruikt u de volgende krul opdracht om het Azure Resource Manager REST-eind punt aan te roepen om de eerste door de gebruiker toegewezen beheerde identiteit toe te wijzen aan de virtuele machine schaalset.  Als u een door een gebruiker of aan het systeem toegewezen beheerde identiteit (en) hebt toegewezen aan de schaalset van de virtuele machine, gaat u naar stap 5 waarin wordt uitgelegd hoe u meerdere door de gebruiker toegewezen beheerde identiteiten aan een schaalset voor virtuele machines toevoegt, terwijl u ook de door het systeem toegewezen beheer beheert persoon.

   In het volgende voor beeld wordt een door de gebruiker toegewezen beheerde identiteit toegewezen `ID1` aan een virtuele-machine schaalset met de naam *myVMSS* in de resource groep *myResourceGroup*.  Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen wanneer u een Bearer-toegangs token en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.

   **API-VERSIE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **API-VERSIE 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Als u een bestaande door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit hebt die is toegewezen aan uw schaalset voor virtuele machines:
   
   **API-VERSIE 2018-06-01**

   Voeg de door de gebruiker toegewezen beheerde identiteit toe aan de `userAssignedIdentities` woordenlijst waarde.

   Als u bijvoorbeeld een door het systeem toegewezen beheerde identiteit en de door de gebruiker toegewezen beheerde identiteit hebt `ID1` die momenteel is toegewezen aan de schaal van de virtuele machine en u de door de gebruiker toegewezen beheerde identiteit wilt toevoegen `ID2`:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

   Als u bijvoorbeeld een door het systeem toegewezen identiteit hebt en de door de gebruiker toegewezen beheerde identiteit `ID1` die momenteel is toegewezen aan de schaalset van de virtuele machine en u de door de gebruiker toegewezen beheerde identiteit wilt toevoegen `ID2`:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een schaalset voor virtuele machines

1. Haal een Bearer-toegangs token op, dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw schaalset voor virtuele machines te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Om ervoor te zorgen dat u geen bestaande door de gebruiker toegewezen beheerde identiteiten verwijdert die u aan de schaalset van de virtuele machine wilt laten toewijzen of de door het systeem toegewezen beheerde identiteit wilt verwijderen, moet u de beheerde identiteiten weer geven met behulp van de volgende krul opdracht :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraag headers**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Autorisatie*     | Vereist. Stel in op een geldig `Bearer` toegangs token. |
   
   Als u beheerde identiteiten aan de virtuele machine hebt toegewezen, worden deze weer gegeven in de reactie in de `identity` waarde. 
    
   Als u bijvoorbeeld beheerde identiteiten hebt `ID1` en `ID2` toegewezen aan uw schaalset voor virtuele machines, en u `ID1` wilt blijven toewijzen en de door het systeem toegewezen beheerde identiteit wilt behouden, gaat u als volgt te werk:

   **API-VERSIE 2018-06-01**

   Voeg `null` toe aan de door de gebruiker toegewezen beheerde identiteit die u wilt verwijderen:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Als uw schaalset voor virtuele machines zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten heeft, kunt u alle door de gebruiker toegewezen beheerde identiteiten verwijderen door te scha kelen naar het gebruik van alleen het systeem toegewezen met de volgende opdracht:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
    
Als uw schaalset voor virtuele machines alleen beheerde identiteiten heeft en u deze wilt verwijderen, gebruikt u de volgende opdracht:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie voor informatie over het maken, weer geven of verwijderen van door de gebruiker toegewezen beheerde identiteiten met behulp van:

- [Een door de gebruiker toegewezen beheerde identiteit maken, weer geven of verwijderen met REST API-aanroepen](how-to-manage-ua-identity-rest.md)

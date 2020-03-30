---
title: Beheerde identiteiten configureren op Azure VM met REST - Azure AD
description: Stapsgewijze instructies voor het configureren van een systeem en door de gebruiker toegewezen beheerde identiteiten op een Azure VM met BEHULP VAN CURL om REST API-aanroepen uit te voeren.
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
ms.openlocfilehash: 9f975595e935a5c0254450168aa295e6e7366a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244158"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Beheerde identiteiten configureren voor Azure-resources op een Azure VM met REST API-aanroepen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde systeemidentiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben. 

In dit artikel leert u in dit artikel met CURL om aan te bellen naar het EINDPUNT VAN Azure Resource Manager REST, hoe u de volgende beheerde identiteiten uitvoert voor Azure-resourcesbewerkingen op een Azure VM:

- De door het systeem toegewezen beheerde identiteit op een Azure VM in- en uitschakelen
- Een door de gebruiker toegewezen beheerde identiteit toevoegen en verwijderen op een Azure VM

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u Windows gebruikt, installeert u het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) of gebruikt u de [Azure Cloud Shell](../../cloud-shell/overview.md) in de Azure-portal.
- [Installeer de lokale azure CLI-console](/cli/azure/install-azure-cli)als u het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) of een [Linux-distributiebesturingssysteem gebruikt.](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
- Als u de lokale console Azure CLI `az login` gebruikt, meldt u zich aan bij Azure met een account dat is gekoppeld aan het Azure-abonnement dat u wilt beheren van systeem- of door de gebruiker toegewezen beheerde identiteiten.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u beheerde identiteit met behulp van CURL in- en uitschakelen met behulp van CURL om aan te bellen naar het REST-eindpunt van Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Beheerde identiteit met systeemtoegewezen status inschakelen tijdens het maken van een Azure VM

Als u een Azure VM wilt maken met de door het systeem toegewezen beheerde identiteit ingeschakeld, heeft uw account de roltoewijzing [voor virtuele machineinzenderbijdrage](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) nodig.  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Maak met [az group create](/cli/azure/group/#az-group-create) een [resourcegroep](../../azure-resource-manager/management/overview.md#terminology) voor insluiting en implementatie van uw VM en de bijbehorende bronnen. U kunt deze stap overslaan als u al een resourcegroep hebt die u in plaats daarvan wilt gebruiken:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Maak een [netwerkinterface](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) voor uw vm:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw VM te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Maak een VM met CURL om het REST-eindpunt van Azure Resource Manager aan te roepen. In het volgende voorbeeld wordt een VM met de naam *myVM* met een `"identity":{"type":"SystemAssigned"}`door het systeem toegewezen beheerde identiteit, zoals aangegeven in de aanvraaginstantie door de waarde . Vervang `<ACCESS TOKEN>` de waarde die u in de vorige stap hebt `<SUBSCRIPTION ID>` ontvangen toen u een access token voor toonder aanvroeg en de waarde die geschikt is voor uw omgeving.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Kopteksten aanvragen**
   
   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        | 
   
   **Aanvraaginstantie**

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

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Systeemtoegewezen identiteit inschakelen op een bestaande Azure VM

Als u beheerde identiteit met systeemtoegewezen wilt inschakelen op een vm die oorspronkelijk zonder deze is ingericht, heeft uw account de roltoewijzing [voor virtuele machineinzender](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) nodig.  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw VM te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Gebruik de volgende opdracht CURL om het AZURE Resource Manager REST-eindpunt aan te roepen om beheerde `{"identity":{"type":"SystemAssigned"}` identiteit met systeemtoegewezen op uw VM in te schakelen, zoals aangegeven in de aanvraaginstantie door de waarde voor een VM met de naam *myVM*.  Vervang `<ACCESS TOKEN>` de waarde die u in de vorige stap hebt `<SUBSCRIPTION ID>` ontvangen toen u een access token voor toonder aanvroeg en de waarde die geschikt is voor uw omgeving.
   
   > [!IMPORTANT]
   > Als u ervoor wilt zorgen dat u geen bestaande door de gebruiker toegewezen beheerde identiteiten verwijdert die aan de `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`VM zijn toegewezen, moet u de door de gebruiker toegewezen beheerde identiteiten aanbieden met behulp van de opdracht CURL: . Als u beheerde identiteiten die door de gebruiker zijn `identity` toegewezen, hebt toegewezen aan de VM, zoals aangegeven in de waarde in het antwoord, gaat u naar stap 3 waarin u laat zien hoe u door de gebruiker toegewezen beheerde identiteiten behouden terwijl de door het systeem toegewezen beheerde identiteit op uw VM wordt ingeschakeld.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        | 
   
   **Aanvraaginstantie**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Als u beheerde identiteit met een vm met bestaande door de `SystemAssigned` gebruiker `type` toegewezen beheerde identiteiten wilt inschakelen, moet u aan de waarde toevoegen.  
   
   Als uw VM bijvoorbeeld de door de `ID1` gebruiker `ID2` toegewezen beheerde identiteiten heeft toegewezen en eraan is toegewezen en u de door het systeem toegewezen beheerde identiteit aan de VM wilt toevoegen, gebruikt u de volgende CURL-aanroep. Vervang `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` en met waarden die passen bij uw omgeving.

   `2018-06-01` API-versie slaat door de gebruiker `userAssignedIdentities` toegewezen beheerde identiteiten `identityIds` op in de waarde in `2017-12-01`een woordenboekindeling in tegenstelling tot de waarde in een matrixindeling die wordt gebruikt in API-versie.
   
   **API-VERSIE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        | 

   **Aanvraaginstantie**

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
    
   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        | 

   **Aanvraaginstantie**

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

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Beheerde identiteit met systeemtoegewezen identiteit uitschakelen vanuit een Azure-vm

Als u de door het systeem toegewezen beheerde identiteit op een virtuele apparaat wilt uitschakelen, heeft uw account de roltoewijzing [voor virtuele machineinzender nodig.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw VM te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Werk de VM bij met CURL om het REST-eindpunt azure resource manager aan te roepen om beheerde identiteit met het systeem uit te schakelen.  In het volgende voorbeeld wordt een door het systeem toegewezen `{"identity":{"type":"None"}}` beheerde identiteit uitgeschakeld die in de aanvraaginstantie is geïdentificeerd door de waarde van een VM met de naam *myVM*.  Vervang `<ACCESS TOKEN>` de waarde die u in de vorige stap hebt `<SUBSCRIPTION ID>` ontvangen toen u een access token voor toonder aanvroeg en de waarde die geschikt is voor uw omgeving.

   > [!IMPORTANT]
   > Als u ervoor wilt zorgen dat u geen bestaande door de gebruiker toegewezen beheerde identiteiten verwijdert die aan de `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`VM zijn toegewezen, moet u de door de gebruiker toegewezen beheerde identiteiten aanbieden met behulp van de opdracht CURL: . Als u beheerde identiteiten die door de gebruiker zijn `identity` toegewezen, hebt toegewezen aan de VM, zoals aangegeven in de waarde in het antwoord, gaat u naar stap 3 waarin wordt aangegeven hoe u door de gebruiker toegewezen beheerde identiteiten behouden terwijl de door het systeem toegewezen beheerde identiteit op uw VM wordt uitgeschakeld.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        | 

   **Aanvraaginstantie**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Als u beheerde identiteit met systeemtoegewezen wilt verwijderen uit een `SystemAssigned` virtuele `{"identity":{"type:" "}}` machine met `UserAssigned` door `userAssignedIdentities` de gebruiker toegewezen beheerde identiteiten, verwijdert u de waarde en de woordenboekwaarden terwijl u **API-versie 2018-06-01**gebruikt. Als u **API-versie 2017-12-01** of eerder `identityIds` gebruikt, houdt u de array.

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u beheerde identiteit met behulp van een Azure-vm met CURL toevoegt en verwijdert om aan te bellen naar het REST-eindpunt van Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een Azure VM

Als u een door de gebruiker toegewezen identiteit aan een vm wilt toewijzen, heeft uw account de roltoewijzingen [Virtual Machine Contributor](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en Managed Identity [Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nodig. Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw VM te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Maak een [netwerkinterface](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) voor uw vm:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw VM te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Maak een door de gebruiker toegewezen beheerde identiteit met behulp van de instructies die hier worden gevonden: [Een door de gebruiker toegewezen beheerde identiteit maken.](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)

5. Maak een VM met CURL om het REST-eindpunt van Azure Resource Manager aan te roepen. In het volgende voorbeeld wordt een VM met de naam *myVM* `ID1`in de resourcegroep *myResourceGroup* met een door de gebruiker toegewezen beheerde identiteit, zoals aangegeven in de aanvraaginstantie door de waarde `"identity":{"type":"UserAssigned"}`. Vervang `<ACCESS TOKEN>` de waarde die u in de vorige stap hebt `<SUBSCRIPTION ID>` ontvangen toen u een access token voor toonder aanvroeg en de waarde die geschikt is voor uw omgeving.
 
   **API-VERSIE 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        | 

   **Aanvraaginstantie**

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

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        | 

   **Aanvraaginstantie**

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande Azure-vm

Als u een door de gebruiker toegewezen identiteit aan een vm wilt toewijzen, heeft uw account de roltoewijzingen [Virtual Machine Contributor](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en Managed Identity [Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nodig. Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw VM te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Maak een door de gebruiker toegewezen beheerde identiteit met behulp van de instructies die hier worden gevonden, [Een door de gebruiker toegewezen beheerde identiteit maken.](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)

3. Als u ervoor wilt zorgen dat bestaande door gebruikers of door het systeem toegewezen beheerde identiteiten die aan de VM zijn toegewezen, niet worden verwijderd, moet u de identiteitstypen die aan de VM zijn toegewezen, weergeven met behulp van de volgende opdracht CURL. Als u beheerde identiteiten hebt toegewezen aan de virtuele machineschaalset, worden deze weergegeven onder in de `identity` waarde.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.

    Als u beheerde identiteiten met een gebruiker of systeem toegewezen `identity` aan de VM hebt toegewezen, zoals aangegeven in de waarde in het antwoord, gaat u naar stap 5 waarin wordt aangegeven hoe u de door het systeem toegewezen beheerde identiteit behouden terwijl u een door de gebruiker toegewezen beheerde identiteit toevoegt aan uw VM.

4. Als u geen door de gebruiker toegewezen beheerde identiteiten aan uw VM hebt toegewezen, gebruikt u de volgende opdracht CURL om het REST-eindpunt azure resource manager aan te roepen om de eerste door de gebruiker toegewezen beheerde identiteit aan de VM toe te wijzen.

   In de volgende voorbeelden wordt een door `ID1` de gebruiker toegewezen beheerde identiteit toegewezen aan een VM met de naam *myVM* in de brongroep *myResourceGroup*.  Vervang `<ACCESS TOKEN>` de waarde die u in de vorige stap hebt `<SUBSCRIPTION ID>` ontvangen toen u een access token voor toonder aanvroeg en de waarde die geschikt is voor uw omgeving.

   **API-VERSIE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        |
 
   **Aanvraaginstantie**

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
   
   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        | 

   **Aanvraaginstantie**

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

5. Als u een bestaande door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit hebt toegewezen aan uw VM:
   
   **API-VERSIE 2018-06-01**

   Voeg de door de gebruiker `userAssignedIdentities` toegewezen beheerde identiteit toe aan de woordwaarde.
    
   Als u bijvoorbeeld een door het systeem toegewezen beheerde `ID1` identiteit en de door de gebruiker toegewezen beheerde identiteit hebt die momenteel aan uw vm is toegewezen en de door de gebruiker toegewezen beheerde identiteit `ID2` eraan wilt toevoegen:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        | 

   **Aanvraaginstantie**

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

   Bewaar de door de gebruiker toegewezen beheerde `identityIds` identiteiten die u in de arraywaarde wilt behouden en voeg tegelijkertijd de nieuwe door de gebruiker toegewezen beheerde identiteit toe.

   Als u bijvoorbeeld een door het systeem toegewezen beheerde `ID1` identiteit en de door de gebruiker toegewezen beheerde identiteit hebt die momenteel aan uw vm is toegewezen en de door de gebruiker toegewezen beheerde identiteit `ID2` eraan wilt toevoegen: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        | 

   **Aanvraaginstantie**

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een Azure-vm

Als u een door de gebruiker toegewezen identiteit aan een vm wilt verwijderen, heeft uw account de roltoewijzing [voor virtuele machineinzender](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) nodig.

1. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw VM te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Om ervoor te zorgen dat u geen bestaande door de gebruiker toegewezen beheerde identiteiten verwijdert die u aan de VM wilt toegewezen houden of de door het systeem toegewezen beheerde identiteit wilt verwijderen, moet u de beheerde identiteiten weergeven met de volgende opdracht CURL: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.
 
   Als u beheerde identiteiten hebt toegewezen aan de VM, `identity` worden deze weergegeven in het antwoord in de waarde.

   Als u bijvoorbeeld door de gebruiker `ID1` toegewezen `ID2` beheerde identiteiten hebt toegewezen `ID1` en aan uw VM bent toegewezen, wilt u alleen toegewezen blijven en de door het systeem toegewezen identiteit behouden:
   
   **API-VERSIE 2018-06-01**

   Voeg `null` toe aan de door de gebruiker toegewezen beheerde identiteit die u wilt verwijderen:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        | 

   **Aanvraaginstantie**

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

   Bewaar alleen de door de gebruiker toegewezen beheerde `identityIds` identiteit(en) die u in de array wilt behouden:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        | 

   **Aanvraaginstantie**

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

Als uw VM zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten heeft, u alle door de gebruiker toegewezen beheerde identiteiten verwijderen door over te schakelen naar alleen door het systeem toegewezen beheerde identiteit met behulp van de volgende opdracht:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Kopteksten aanvragen**

|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
|*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken. | 

**Aanvraaginstantie**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Als uw VM alleen door de gebruiker toegewezen beheerde identiteiten heeft en u ze allemaal wilt verwijderen, gebruikt u de volgende opdracht:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Kopteksten aanvragen**

|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
|*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.| 

**Aanvraaginstantie**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie voor informatie over het maken, aanbieden of verwijderen van door gebruikers toegewezen beheerde identiteiten met REST:

- [Een door de gebruiker toegewezen beheerde identiteiten maken, aanbieden of verwijderen met REST API-aanroepen](how-to-manage-ua-identity-rest.md)

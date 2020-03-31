---
title: Beheerde identiteiten configureren op Azure VMSS met REST - Azure AD
description: Stapsgewijze instructies voor het configureren van een systeem en door de gebruiker toegewezen beheerde identiteiten op een Azure VMSS met BEHULP VAN CURL om REST API-aanroepen uit te voeren.
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
ms.openlocfilehash: dce9894b26d03c351a2209792cc076de91feba54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253336"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Beheerde identiteiten configureren voor Azure-resources op een schaalset van virtuele machines met REST API-aanroepen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde systeemidentiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben. 

In dit artikel leert u in dit artikel met CURL om aan te bellen naar het EINDPUNT AZURE Resource Manager REST, hoe u de volgende beheerde identiteiten uitvoert voor Azure-resourcesbewerkingen op een virtuele machineschaalset:

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
    - [Functie Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) om een door de gebruiker toegewezen identiteit toe te wijzen en te verwijderen van en naar een virtuele machineschaalset.
- Als u Windows gebruikt, installeert u het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) of gebruikt u de [Azure Cloud Shell](../../cloud-shell/overview.md) in de Azure-portal.
- [Installeer de lokale azure CLI-console](/cli/azure/install-azure-cli)als u het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) of een [Linux-distributiebesturingssysteem gebruikt.](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
- Als u de lokale console Azure CLI `az login` gebruikt, meldt u zich aan bij Azure met een account dat is gekoppeld aan het Azure-abonnement dat u wilt beheren van systeem- of door de gebruiker toegewezen beheerde identiteiten.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u beheerde identiteit met een virtuele machine in- en uitschakelt op een virtuele machineschaalset met BEHULP van CURL om aan te bellen naar het REST-eindpunt van Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Beheerde identiteit met systeemtoegewezen status inschakelen tijdens het maken van een virtuele machineschaalset

Als u een virtuele machineschaalset wilt maken met systeemtoegewezen beheerde identiteit ingeschakeld, moet u een virtuele machineschaalset maken en een toegangstoken ophalen om CURL te gebruiken om het Resource Manager-eindpunt aan te roepen met de door het systeem toegewezen beheerde identiteitstypewaarde.

1. Maak een [resourcegroep](../../azure-resource-manager/management/overview.md#terminology) voor het indammen en implementeren van uw virtuele machineschaalset en de bijbehorende resources met behulp van [de AZ-groep maken.](/cli/azure/group/#az-group-create) U kunt deze stap overslaan als u al een resourcegroep hebt die u in plaats daarvan wilt gebruiken:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Maak een [netwerkinterface](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) voor uw virtuele machineschaalset:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw virtuele machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Maak een virtuele machineschaalset met CURL om het REST-eindpunt azure resource manager aan te roepen. In het volgende voorbeeld wordt een virtuele machineschaalset met de naam *myVMSS* in de *myResourceGroup* `"identity":{"type":"SystemAssigned"}`gemaakt met een door het systeem toegewezen beheerde identiteit, zoals aangegeven in de aanvraaginstantie door de waarde . Vervang `<ACCESS TOKEN>` de waarde die u in de vorige stap hebt `<SUBSCRIPTION ID>` ontvangen toen u een access token voor toonder aanvroeg en de waarde die geschikt is voor uw omgeving.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken. | 

   **Aanvraaginstantie**

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

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Beheerde identiteit met systeem toegewezen op een bestaande virtuele machineschaalset inschakelen

Als u beheerde identiteit met systeemtoegewezen op een bestaande virtuele machineschaalset wilt inschakelen, moet u een toegangstoken aanschaffen en vervolgens CURL gebruiken om het REST-eindpunt van Resource Manager aan te roepen om het identiteitstype bij te werken.

1. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw virtuele machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Gebruik de volgende opdracht CURL om het AZURE Resource Manager REST-eindpunt aan te roepen om beheerde identiteit `{"identity":{"type":"SystemAssigned"}` met systeemtoegewezen op uw virtuele machineschaalset in te schakelen die is geïdentificeerd in de aanvraaginstantie door de waarde voor een virtuele machineschaalset met de naam *myVMSS*.  Vervang `<ACCESS TOKEN>` de waarde die u in de vorige stap hebt `<SUBSCRIPTION ID>` ontvangen toen u een access token voor toonder aanvroeg en de waarde die geschikt is voor uw omgeving.
   
   > [!IMPORTANT]
   > Als u ervoor wilt zorgen dat u geen bestaande door de gebruiker toegewezen beheerde identiteiten verwijdert die zijn toegewezen aan `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`de virtuele machineschaalset, moet u de door de gebruiker toegewezen beheerde identiteiten weergeven met behulp van de opdracht CURL: . Als u beheerde identiteiten die door de gebruiker zijn toegewezen, `identity` hebt toegewezen aan de virtuele machineschaaldie is ingesteld in de waarde in het antwoord, gaat u naar stap 3 waarin u laat zien hoe u door de gebruiker toegewezen beheerde identiteiten behouden terwijl de door het systeem toegewezen beheerde identiteit wordt ingeschakeld op uw virtuele machineschaalset.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

3. Als u beheerde identiteit met een virtuele machine wilt inschakelen op een door `SystemAssigned` de `type` gebruiker toegewezen beheerde identiteit, moet u de waarde toevoegen.  
   
   Als uw virtuele machineschaalset bijvoorbeeld de door de `ID1` `ID2` gebruiker toegewezen beheerde identiteiten heeft toegewezen en eraan is toegewezen en u de door het systeem toegewezen beheerde identiteit wilt toevoegen aan de virtuele machineschaalset, gebruikt u de volgende CURL-aanroep. Vervang `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` en met waarden die passen bij uw omgeving.

   `2018-06-01` API-versie slaat door de gebruiker `userAssignedIdentities` toegewezen beheerde identiteiten `identityIds` op in de waarde in `2017-12-01`een woordenboekindeling in tegenstelling tot de waarde in een matrixindeling die wordt gebruikt in API-versie.
   
   **API-VERSIE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken. | 

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

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Beheerde identiteit met systeemtoegewezen uitschakelen vanuit een schaalset van virtuele machines

Als u een door het systeem toegewezen identiteit wilt uitschakelen op een bestaande virtuele machineschaalset, moet u een toegangstoken aanschaffen en vervolgens CURL gebruiken om het REST-eindpunt van Resource Manager aan te roepen om het identiteitstype bij te werken naar `None`.

1. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw virtuele machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Werk de set voor virtuele machineschaal bij met CURL om het REST-eindpunt azure resource manager aan te roepen om beheerde identiteit met het systeem uit te schakelen.  In het volgende voorbeeld wordt een door het systeem toegewezen `{"identity":{"type":"None"}}` beheerde identiteit uitgeschakeld die in de aanvraaginstantie is geïdentificeerd door de waarde van een virtuele machineschaalset met de naam *myVMSS*.  Vervang `<ACCESS TOKEN>` de waarde die u in de vorige stap hebt `<SUBSCRIPTION ID>` ontvangen toen u een access token voor toonder aanvroeg en de waarde die geschikt is voor uw omgeving.

   > [!IMPORTANT]
   > Als u ervoor wilt zorgen dat u geen bestaande door de gebruiker toegewezen beheerde identiteiten verwijdert die zijn toegewezen aan `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`de virtuele machineschaalset, moet u de door de gebruiker toegewezen beheerde identiteiten weergeven met behulp van de opdracht CURL: . Als u een door de gebruiker toegewezen beheerde identiteit hebt toegewezen aan de virtuele machineschaalset, gaat u naar stap 3 die u laat zien hoe u de door de gebruiker toegewezen beheerde identiteit behoudt terwijl u de door het systeem toegewezen beheerde identiteit verwijdert uit uw virtuele machineschaalset.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"None"
       }
    }
   ```

   Als u beheerde identiteit met systeemtoegewezen wilt verwijderen uit een door `SystemAssigned` de `{"identity":{"type:" "}}` virtuele machine `UserAssigned` toegewezen `userAssignedIdentities` beheerde identiteiten, verwijdert u de waarde terwijl u de waarde en de woordenboekwaarden wilt behouden als u **API-versie 2018-06-01**gebruikt. Als u **API-versie 2017-12-01** of eerder `identityIds` gebruikt, houdt u de array.

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u beheerde identiteit met gebruikers toegewezen op een virtuele machineschaalset toevoegen en verwijderen met BEHULP van CURL om aan te bellen naar het REST-eindpunt van Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een virtuele machineschaalset

1. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw virtuele machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Maak een [netwerkinterface](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) voor uw virtuele machineschaalset:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw virtuele machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Maak een door de gebruiker toegewezen beheerde identiteit met behulp van de instructies die hier worden gevonden: [Een door de gebruiker toegewezen beheerde identiteit maken.](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)

5. Maak een virtuele machineschaalset met CURL om het REST-eindpunt azure resource manager aan te roepen. In het volgende voorbeeld wordt een virtuele machineschaalset met de naam *myVMSS* set gemaakt in de resourcegroep `"identity":{"type":"UserAssigned"}` *myResourceGroup* met een door de gebruiker toegewezen beheerde identiteit `ID1`, zoals aangegeven in de aanvraaginstantie door de waarde . Vervang `<ACCESS TOKEN>` de waarde die u in de vorige stap hebt `<SUBSCRIPTION ID>` ontvangen toen u een access token voor toonder aanvroeg en de waarde die geschikt is voor uw omgeving.
 
   **API-VERSIE 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken. | 

   **Aanvraaginstantie**

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

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken. |
 
   **Aanvraaginstantie**

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande Azure-schaalset voor virtuele machines

1. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw virtuele machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Maak een door de gebruiker toegewezen beheerde identiteit met behulp van de instructies die hier worden gevonden, [Een door de gebruiker toegewezen beheerde identiteit maken.](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)

3. Als u ervoor wilt zorgen dat bestaande gebruikers- of systeemtoegewezen beheerde identiteiten die zijn toegewezen aan de virtuele machineschaalset, niet worden verwijderd, moet u de identiteitstypen weergeven die zijn toegewezen aan de virtuele machineschaal die is ingesteld met behulp van de volgende opdracht CURL. Als u beheerde identiteiten hebt toegewezen aan de virtuele `identity` machineschaalset, worden deze weergegeven in de waarde.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken. |   
 

4. Als u geen door de gebruiker of systeem toegewezen beheerde identiteiten hebt toegewezen aan uw virtuele machineschaalset, gebruikt u de volgende opdracht CURL om het REST-eindpunt azure resource manager aan te roepen om de eerste door de gebruiker toegewezen beheerde identiteit aan de virtuele machine toe te wijzen schaalset.  Als u een door de gebruiker of systeem toegewezen beheerde identiteit(en) hebt toegewezen aan de virtuele machineschaalset, gaat u naar stap 5 die u laat zien hoe u meerdere door de gebruiker toegewezen beheerde identiteiten toevoegt aan een virtuele machineschaalset terwijl u ook het beheerde systeem Identiteit.

   In het volgende voorbeeld wordt een `ID1` door de gebruiker toegewezen beheerde identiteit toegewezen aan een virtuele machineschaalset met de naam *myVMSS* in de brongroep *myResourceGroup.*  Vervang `<ACCESS TOKEN>` de waarde die u in de vorige stap hebt `<SUBSCRIPTION ID>` ontvangen toen u een access token voor toonder aanvroeg en de waarde die geschikt is voor uw omgeving.

   **API-VERSIE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
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

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken. | 

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

5. Als u een bestaande door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit hebt toegewezen aan uw virtuele machineschaalset:
   
   **API-VERSIE 2018-06-01**

   Voeg de door de gebruiker `userAssignedIdentities` toegewezen beheerde identiteit toe aan de woordwaarde.

   Als u bijvoorbeeld een door het systeem toegewezen beheerde `ID1` identiteit en de door de gebruiker toegewezen beheerde `ID2` identiteit hebt die momenteel is toegewezen aan uw virtuele machineschaal en u de door de gebruiker toegewezen beheerde identiteit eraan wilt toevoegen:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

   Als u bijvoorbeeld een door het systeem toegewezen identiteit `ID1` en de door de gebruiker toegewezen beheerde identiteit hebt `ID2` die momenteel is toegewezen aan uw virtuele machineschaalset en u de door de gebruiker toegewezen beheerde identiteit eraan wilt toevoegen:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een schaalset van een virtuele machine

1. Een access token voor dragers ophalen, dat u in de volgende stap in de kopautorisatie gebruikt om uw virtuele machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Om ervoor te zorgen dat u geen bestaande door de gebruiker toegewezen beheerde identiteiten verwijdert die u wilt blijven toegewezen aan de virtuele machineschaalset of de door het systeem toegewezen beheerde identiteit wilt verwijderen, moet u de beheerde identiteiten vermelden met behulp van de volgende opdracht CURL :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Kopteksten aanvragen**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken. |
   
   Als u beheerde identiteiten hebt toegewezen aan de VM, `identity` worden deze weergegeven in het antwoord in de waarde. 
    
   Als u bijvoorbeeld door de gebruiker `ID1` toegewezen `ID2` beheerde identiteiten hebt toegewezen en bent `ID1` toegewezen aan uw virtuele machineschaalset, wilt u alleen toegewezen blijven en de door het systeem toegewezen beheerde identiteit behouden:

   **API-VERSIE 2018-06-01**

   Voeg `null` toe aan de door de gebruiker toegewezen beheerde identiteit die u wilt verwijderen:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Als uw virtuele machineschaalset zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten heeft, u alle door de gebruiker toegewezen beheerde identiteiten verwijderen door over te schakelen naar alleen systeemtoegewezen gebruiken met behulp van de volgende opdracht:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
    
Als uw virtuele machineschaalset alleen door de gebruiker toegewezen beheerde identiteiten heeft en u ze allemaal wilt verwijderen, gebruikt u de volgende opdracht:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie voor informatie over het maken, aanbieden of verwijderen van door gebruikers toegewezen beheerde identiteiten met REST:

- [Een door de gebruiker toegewezen beheerde identiteit maken, aanbieden of verwijderen met REST API-aanroepen](how-to-manage-ua-identity-rest.md)

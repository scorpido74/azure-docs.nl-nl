---
title: Een Linux-vm maken met de REST-API
description: Meer informatie over het maken van een Virtuele Linux-machine in Azure die beheerde schijven en SSH-verificatie gebruikt met Azure REST API.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 1594c030839cccdd48c4b032c6ad92f746f78e26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970275"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Maak een Virtuele Linux-machine die SSH-verificatie gebruikt met de REST API

Een Virtuele Linux-machine (VM) in Azure bestaat uit verschillende bronnen, zoals schijven en netwerkinterfaces, en definieert parameters zoals locatie-, grootte- en besturingssysteembeeld- en verificatie-instellingen.

U een Linux-VM maken via de Azure-portal, Azure CLI 2.0, veel Azure SDK's, Azure Resource Manager-sjablonen en veel hulpprogramma's van derden, zoals Ansible of Terraform. Al deze tools uiteindelijk gebruik maken van de REST API om de Linux VM te creëren.

In dit artikel ziet u hoe u de REST API gebruiken om een Linux-VM met Ubuntu 18.04-LTS met beheerde schijven en SSH-verificatie te maken.

## <a name="before-you-start"></a>Voordat u begint

Voordat u de aanvraag maakt en indient, moet u het:

* De `{subscription-id}` voor uw abonnement
  * Als u meerdere abonnementen hebt, [raadpleegt u Werken met meerdere abonnementen](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)
* Een `{resourceGroupName}` die u van tevoren hebt gemaakt
* Een [virtuele netwerkinterface](../../virtual-network/virtual-network-network-interface.md) in dezelfde brongroep
* Een SSH-sleutelpaar (u [een nieuw paar genereren](mac-create-ssh-keys.md) als u er geen hebt)

## <a name="request-basics"></a>Basisbeginselen aanvragen

Als u een virtuele machine wilt maken of bijwerken, gebruikt u de volgende *PUT-bewerking:*

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Naast `{subscription-id}` de parameters `{resourceGroupName}` en parameters moet u `{vmName}` de`api-version` ( is optioneel, maar `api-version=2017-12-01`dit artikel is getest met )

De volgende headers zijn vereist:

| Aanvraagheader   | Beschrijving |
|------------------|-----------------|
| *Inhoudstype:*  | Vereist. Ingesteld op `application/json`. |
| *Authorization:* | Vereist. Ingesteld op een geldig `Bearer` [toegangstoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Zie [Componenten van een REST API-aanvraag/-antwoord voor](/rest/api/azure/#components-of-a-rest-api-requestresponse)algemene informatie over het werken met REST API-aanvragen.

## <a name="create-the-request-body"></a>De aanvraaginstantie maken

De volgende algemene definities worden gebruikt om een aanvraaginstantie op te bouwen:

| Name                       | Vereist | Type                                                                                | Beschrijving  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | tekenreeks                                                                              | Locatie van de resource. |
| name                       |          | tekenreeks                                                                              | Naam voor de virtuele machine. |
| properties.hardwareProfiel |          | [HardwareProfiel](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Hiermee geeft u de hardware-instellingen voor de virtuele machine op. |
| eigenschappen.storageProfiel  |          | [StorageProfiel](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Hiermee geeft u de opslaginstellingen voor de virtuele machineschijven op. |
| properties.osProfiel       |          | [OSProfiel](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Hiermee geeft u de instellingen van het besturingssysteem voor de virtuele machine op. |
| properties.networkProfiel  |          | [NetwerkProfiel](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Hiermee geeft u de netwerkinterfaces van de virtuele machine op. |

Een voorbeeld aanvraag lichaam is hieronder. Zorg ervoor dat u de `{computerName}` `{name}` VM-naam opgeeft in de parameters `networkInterfaces`en parameters, `adminUsername` `path`de naam van de netwerkinterface die u hebt gemaakt onder `keyData`, uw gebruikersnaam in en , en het *openbare* gedeelte van uw SSH-sleutelpaar (bijvoorbeeld in `~/.ssh/id_rsa.pub`) in . Andere parameters die u `location` wilt `vmSize`wijzigen, omvatten en .  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Zie Virtuele machines maken of bijwerken van definities van [de aanvraaginstantie voor](/rest/api/compute/virtualmachines/createorupdate#definitions)een volledige lijst met de beschikbare definities in de aanvraaginstantie.

## <a name="sending-the-request"></a>Het verzoek verzenden

U de klant van uw voorkeur gebruiken voor het verzenden van dit HTTP-verzoek. U ook een [in-browser tool](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) gebruiken door op **de knop Proberen te** klikken.

### <a name="responses"></a>Antwoorden

Er zijn twee succesvolle antwoorden voor de bewerking om een virtuele machine te maken of bij te werken:

| Name        | Type                                                                              | Beschrijving |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Gemaakt | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Gemaakt     |

Een gecondenseerd *antwoord van 201 Gemaakt* van de vorige voorbeeldaanvraaginstantie die een VM maakt, toont een *vmId* is toegewezen en de *provisioningState* is *Creating:*

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Zie [Het antwoordbericht verwerken](/rest/api/azure/#process-the-response-message)voor meer informatie over DE API-antwoorden van REST.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende voor meer informatie over de Azure REST API's of andere beheerhulpprogramma's zoals Azure CLI of Azure PowerShell:

- [Rest-API van Azure Compute-provider](/rest/api/compute/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)
- [Azure-CLI](/cli/azure/)
- [Azure PowerShell-module](/powershell/azure/overview)

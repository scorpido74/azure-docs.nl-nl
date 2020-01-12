---
title: Implementeren op toegewezen hosts
description: Gebruik toegewezen hosts om de isolatie op hostniveau te krijgen voor uw workloads
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 619a39f4d08a4308cb0f566bc50860e9562bf9e4
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903754"
---
# <a name="deploy-on-dedicated-hosts"></a>Implementeren op toegewezen hosts

' Dedicated ' is een Azure Container Instances-SKU (ACI) die een geïsoleerde en toegewezen reken omgeving biedt voor het veilig uitvoeren van containers. Het gebruik van de toegewezen SKU resulteert in elke container groep die een speciale fysieke server heeft in een Azure-Data Center, zodat de isolatie van de volledige werk belasting wordt gewaarborgd om te voldoen aan de beveiligings-en nalevings vereisten van uw organisatie. 

De toegewezen SKU is geschikt voor werkbelastingen van containers waarvoor isolatie van de werk belasting vereist is vanuit een oogpunt van een fysieke server.

## <a name="using-the-dedicated-sku"></a>De toegewezen SKU gebruiken

> [!IMPORTANT]
> Het gebruik van de toegewezen SKU is alleen beschikbaar in de nieuwste API-versie (2019-12-01) die momenteel wordt geïmplementeerd. Geef deze API-versie op in uw implementatie sjabloon. Daarnaast is de standaard limiet voor een abonnement voor het gebruik van de toegewezen SKU 0. Als u deze SKU wilt gebruiken voor de implementaties van uw productie container, moet u een [Azure-ondersteuningsaanvraag][azure-support] maken

Vanaf API-versie 2019-12-01 is er een ' SKU-eigenschap ' in de sectie eigenschappen van container groep van een implementatie sjabloon, die vereist is voor een ACI-implementatie. Op dit moment kunt u deze eigenschap gebruiken als onderdeel van een Azure Resource Manager implementatie sjabloon voor ACI. Meer informatie over het implementeren van ACI-resources met een sjabloon vindt u in de [zelf studie: een groep met meerdere containers implementeren met behulp van een resource manager-sjabloon](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

De SKU-eigenschap kan een van de volgende waarden hebben:
* Standaard: de standaard implementatie keuze van ACI, die nog steeds beveiliging op Hyper Visor niveau garandeert 
* Toegewezen: wordt gebruikt voor isolatie op werkbelasting niveau met toegewezen fysieke hosts voor de container groep

## <a name="modify-your-json-deployment-template"></a>De JSON-implementatie sjabloon wijzigen

Zorg er in uw implementatie sjabloon voor dat de container groeps bron is opgegeven, dat de `"apiVersion": "2019-12-01",`. Stel in het gedeelte Eigenschappen van de resource container groep `"sku": "Dedicated",`in.

Hier volgt een voorbeeld fragment voor het gedeelte resources van een sjabloon voor container groep implementatie die gebruikmaakt van de toegewezen SKU:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux",
            },
            "location": "eastus2euap",
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Uw container groep implementeren

Als u het implementatie sjabloon bestand op uw bureau blad hebt gemaakt en bewerkt, kunt u het uploaden naar uw Cloud Shell Directory door het bestand naar de map te slepen. 

Een resourcegroep maken met de opdracht [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementeer de sjabloon met de opdracht [AZ Group Deployment Create][az-group-deployment-create] .

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

U ontvangt binnen enkele seconden een eerste reactie van Azure. Zodra de implementatie is voltooid, worden alle gegevens die betrekking hebben op deze door de ACI-service bewaard, versleuteld met de sleutel die u hebt ingevoerd.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

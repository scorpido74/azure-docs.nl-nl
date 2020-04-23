---
title: Implementeren op dedicated host
description: Gebruik een speciale host om echte isolatie op hostniveau te bereiken voor uw Azure Container Instances-workloads
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: a614d6b5d0cf5c6c1df5ffcb90e56960d6b8a2a9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025030"
---
# <a name="deploy-on-dedicated-hosts"></a>Implementeren op toegewezen hosts

"Dedicated" is een Azure Container Instances (ACI) sku die een geïsoleerde en speciale compute-omgeving biedt voor het veilig uitvoeren van containers. Het gebruik van de speciale sku resulteert in elke containergroep met een speciale fysieke server in een Azure-datacenter, zodat volledige workloadisolatie wordt gegarandeerd om te voldoen aan de beveiligings- en nalevingsvereisten van uw organisatie. 

De speciale sku is geschikt voor containerworkloads waarvoor workloads moeten worden geïsoleerd vanuit een fysiek serverperspectief.

## <a name="prerequisites"></a>Vereisten

* De standaardlimiet voor elk abonnement om de speciale sku te gebruiken is 0. Als u deze sku wilt gebruiken voor uw productiecontainerimplementaties, maakt u een [Azure Support-aanvraag][azure-support] om de limiet te verhogen.

## <a name="use-the-dedicated-sku"></a>Gebruik de speciale sku

> [!IMPORTANT]
> Het gebruik van de speciale sku is alleen beschikbaar in de nieuwste API-versie (2019-12-01) die momenteel wordt uitgerold. Geef deze API-versie op in uw implementatiesjabloon.
>

Vanaf API-versie 2019-12-01 is `sku` er een eigenschap onder de sectie eigenschappen van containergroep van een implementatiesjabloon, die vereist is voor een ACI-implementatie. Momenteel u deze eigenschap gebruiken als onderdeel van een Azure Resource Manager-implementatiesjabloon voor ACI. Meer informatie over het implementeren van ACI-resources met een sjabloon in de [zelfstudie: een groep met meerdere containers implementeren met behulp van een resourcemanagersjabloon](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

De `sku` eigenschap kan een van de volgende waarden hebben:
* `Standard`- de standaard ACI-implementatiekeuze, die nog steeds beveiliging op hypervisorniveau garandeert 
* `Dedicated`- gebruikt voor isolatie op werkbelastingsniveau met speciale fysieke hosts voor de containergroep

## <a name="modify-your-json-deployment-template"></a>Uw JSON-implementatiesjabloon wijzigen

Wijzig of voeg in uw implementatiesjabloon de volgende eigenschappen toe:
* Onder `resources`, `apiVersion` `2019-12-01`ingesteld op .
* Voeg onder de eigenschappen `sku` van de `Dedicated`containergroep een eigenschap met waarde toe .

Hier vindt u een voorbeeldfragment voor het gedeelte resources van een implementatiesjabloon voor containergroepen waarmee de speciale sku wordt gebruikt:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Hieronder volgt een volledige sjabloon waarmee een voorbeeldcontainergroep met één containerinstantie wordt geïmplementeerd:

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
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Uw containergroep implementeren

Als u het implementatiesjabloonbestand op uw bureaublad hebt gemaakt en bewerkt, u het uploaden naar uw Cloud Shell-map door het bestand erin te slepen. 

Een resourcegroep maken met de opdracht [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementeer de sjabloon met de [opdracht Implementatie van az-groep.][az-group-deployment-create]

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

U ontvangt binnen enkele seconden een eerste reactie van Azure. Een succesvolle implementatie vindt plaats op een speciale host.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

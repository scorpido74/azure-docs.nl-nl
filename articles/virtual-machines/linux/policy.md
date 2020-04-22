---
title: Beveiliging afdwingen met beleid voor Linux VM's in Azure
description: Een beleid toepassen op een Virtual Machine van Azure Resource Manager Linux
author: mimckitt
ms.service: virtual-machines-linux
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 6eb571ccc8c996a06d3bdf2dda25860df4dc930f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759379"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Beleidsregels toepassen op Linux VM's met Azure Resource Manager
Door beleid te gebruiken, kan een organisatie verschillende conventies en regels in de hele onderneming afdwingen. Handhaving van het gewenste gedrag kan helpen risico's te beperken en tegelijkertijd bij te dragen aan het succes van de organisatie. In dit artikel beschrijven we hoe u azure resource manager-beleid gebruiken om het gewenste gedrag voor de virtuele machines van uw organisatie te definiëren.

Zie [Wat is Azure-beleid voor](../../governance/policy/overview.md)een inleiding tot beleidsregels?

## <a name="permitted-virtual-machines"></a>Toegestane virtuele machines
Om ervoor te zorgen dat virtuele machines voor uw organisatie compatibel zijn met een toepassing, u de toegestane besturingssystemen beperken. In het volgende beleidsvoorbeeld staat u toe dat alleen Ubuntu 14.04.2-LTS Virtual Machines worden gemaakt.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Gebruik een wild card om het vorige beleid te wijzigen om een Ubuntu LTS-afbeelding toe te staan: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Zie [Beleidsaliassen voor](../../governance/policy/concepts/definition-structure.md#aliases)informatie over beleidsvelden .

## <a name="managed-disks"></a>Managed Disks

Als u het gebruik van beheerde schijven wilt vereisen, gebruikt u het volgende beleid:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Afbeeldingen voor virtuele machines

Om veiligheidsredenen u vereisen dat alleen goedgekeurde aangepaste afbeeldingen in uw omgeving worden geïmplementeerd. U de resourcegroep opgeven die de goedgekeurde afbeeldingen bevat, of de specifieke goedgekeurde afbeeldingen.

In het volgende voorbeeld worden afbeeldingen van een goedgekeurde resourcegroep vereist:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

In het volgende voorbeeld worden de goedgekeurde afbeeldings-geïdentificeerde afbeeldingen opgegeven:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Virtuele machine-extensies

U het gebruik van bepaalde typen extensies verbieden. Een extensie is bijvoorbeeld mogelijk niet compatibel met bepaalde aangepaste afbeeldingen van virtuele machines. In het volgende voorbeeld ziet u hoe u een specifieke extensie blokkeren. Het maakt gebruik van uitgever en type om te bepalen welke extensie te blokkeren.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>Volgende stappen
* Nadat u een beleidsregel hebt gedefinieerd (zoals in de voorgaande voorbeelden), moet u de beleidsdefinitie maken en aan een bereik toewijzen. Het bereik kan een abonnement, resourcegroep of resource zijn. Zie [Azure portal gebruiken om resourcebeleid toe te wijzen en te beheren,](../../governance/policy/assign-policy-portal.md) [PowerShell gebruiken om beleid toe te wijzen](../../governance/policy/assign-policy-powershell.md)of Azure CLI gebruiken om beleid toe te [wijzen.](../../governance/policy/assign-policy-azurecli.md)
* Zie [Wat is Azure-beleid voor](../../governance/policy/overview.md)een inleiding tot resourcebeleid?
* Voor begeleiding bij de manier waarop ondernemingen Resource Manager effectief kunnen gebruiken voor het beheer van abonnementen, gaat u naar [Azure enterprise-platform - Prescriptieve abonnementsgovernance](/azure/architecture/cloud-adoption-guide/subscription-governance).

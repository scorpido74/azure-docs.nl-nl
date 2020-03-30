---
title: Beveiliging afdwingen met beleid voor Windows VM's in Azure
description: Een beleid toepassen op een Virtuele Machine van Azure Resource Manager Windows
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: a2ee5f36b7dba03bea3cc219651804c5039115d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74032980"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Beleidsregels toepassen op Windows VM's met Azure Resource Manager
Door beleid te gebruiken, kan een organisatie verschillende conventies en regels in de hele onderneming afdwingen. Handhaving van het gewenste gedrag kan helpen risico's te beperken en tegelijkertijd bij te dragen aan het succes van de organisatie. In dit artikel beschrijven we hoe u azure resource manager-beleid gebruiken om het gewenste gedrag voor de virtuele machines van uw organisatie te definiëren.

Zie [Wat is Azure-beleid voor](../../governance/policy/overview.md)een inleiding tot beleidsregels?

## <a name="permitted-virtual-machines"></a>Toegestane virtuele machines
Om ervoor te zorgen dat virtuele machines voor uw organisatie compatibel zijn met een toepassing, u de toegestane besturingssystemen beperken. In het volgende beleidsvoorbeeld staat u toe dat alleen virtuele windows server 2012 R2-datacentermachines worden gemaakt:

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
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
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

Gebruik een wildcard om het vorige beleid te wijzigen om een Windows Server Datacenter-afbeelding toe te staan:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Gebruik anyOf om het voorgaande beleid te wijzigen om een Windows Server 2012 R2-datacenter of hogere afbeelding toe te staan:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
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


## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

Wanneer u een on-premises licentie hebt, u de licentiekosten opslaan op uw virtuele machines. Als je de licentie niet hebt, moet je de optie verbieden. Het volgende beleid verbiedt het gebruik van Azure Hybrid Use Benefit (AHUB):

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
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
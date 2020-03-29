---
title: Beheerde bronnen bijwerken
description: Beschrijft hoe u werken aan resources in de beheerde brongroep voor een door Azure beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651213"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Werken met resources in de beheerde brongroep voor azure-beheerde toepassing

In dit artikel wordt beschreven hoe u resources bijwerkt die zijn geïmplementeerd als onderdeel van een beheerde toepassing. Als uitgever van een beheerde toepassing hebt u toegang tot de bronnen in de beheerde brongroep. Als u deze bronnen wilt bijwerken, moet u de beheerde resourcegroep vinden die is gekoppeld aan een beheerde toepassing en toegang krijgen tot de bron in die resourcegroep.

In dit artikel wordt ervan uitgegaan dat u de beheerde toepassing hebt geïmplementeerd in de [Voorbeeldfunctie Managed Web Application (IaaS) met voorbeeldproject Azure-beheerservices.](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) Die beheerde toepassing omvat een **Standard_D1_v2** virtuele machine. Als u die beheerde toepassing niet hebt geïmplementeerd, u dit artikel nog steeds gebruiken om vertrouwd te raken met de stappen voor het bijwerken van een beheerde resourcegroep.

In de volgende afbeelding ziet u de geïmplementeerde beheerde toepassing.

![Geïmplementeerde beheerde toepassing](./media/update-managed-resources/deployed.png)

In dit artikel gebruikt u Azure CLI om:

* De beheerde toepassing identificeren
* De beheerde resourcegroep identificeren
* De bron(en) van virtuele machines in de beheerde resourcegroep identificeren
* De VM-grootte wijzigen (naar een kleiner formaat als deze niet wordt gebruikt, of een groter om meer belasting te ondersteunen)
* Een beleid toewijzen aan de beheerde resourcegroep die de toegestane locaties opgeeft

## <a name="get-managed-application-and-managed-resource-group"></a>Beheerde toepassing en beheerde resourcegroep beheren

Gebruik het als u de beheerde toepassingen in een resourcegroep wilt gebruiken:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Gebruik het als u de id van de beheerde resourcegroep wilt opdoen:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Formaat VM's wijzigen in beheerde resourcegroep

Geef de naam van de beheerde resourcegroep op als u de virtuele machines in de beheerde resourcegroep wilt weergeven.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Gebruik het als u de grootte van de VM's wilt bijwerken:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Nadat de bewerking is voltooid, controleert u of de toepassing wordt uitgevoerd op Standaard D2 v2.

![Beheerde toepassing met standaard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Beleid toepassen op beheerde resourcegroep

Download de beheerde resourcegroep en wijs een beleid op dat bereik. Het beleid **e56962a6-4747-49cd-b67b-bf8b01975c4c** is een ingebouwd beleid voor het specificeren van toegestane locaties.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Gebruik het als u de toegestane locaties wilt bekijken:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

De beleidstoewijzing wordt weergegeven in de portal.

![Beleidstoewijzing weergeven](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van beheerde toepassingen](overview.md) voor een inleiding tot beheerde toepassingen.
* Zie [Voorbeeldprojecten voor door Azure beheerde toepassingen voor](sample-projects.md)voorbeeldprojecten .

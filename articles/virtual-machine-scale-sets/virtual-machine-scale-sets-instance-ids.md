---
title: Informatie over instantie-iD's voor VM-vm-vm's van Azure-
description: Begrijp instantie-id's voor Azure VM-schaal stelt virtuele machines in en de verschillende manieren waarop ze worden uitgevoerd.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: 99ad4249a4134bcc1b1cf5aba92b8a95a034db33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534419"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Informatie over instantie-iD's voor VM-vm-vm's van Azure-
In dit artikel worden instantie-id's beschreven voor schaalsets en de verschillende manieren waarop ze worden aangedoken.

## <a name="scale-set-instance-ids"></a>Set-instel-geïdentificeerde iD's schalen

Elke VM in een schaalset krijgt een instantie-id die deze op unieke wijze identificeert. Deze instantie-id wordt gebruikt in de schaalset API's om bewerkingen uit te voeren op een specifieke VM in de schaalset. U bijvoorbeeld een specifieke instantie-id opgeven om opnieuw te worden weergegeven wanneer u de reimage-API gebruikt:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (zie voor meer informatie de [REST API-documentatie)](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)

Powershell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (voor meer informatie, zie de [Powershell documentatie](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (zie voor meer informatie de [CLI-documentatie).](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)

U de lijst met instantie-geïdentificeerde gegevens opmaken door alle instanties in een schaalset op te geven:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (zie voor meer informatie de [REST API-documentatie)](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list)

Powershell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (voor meer informatie, zie de [Powershell documentatie](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (zie voor meer informatie de [CLI-documentatie).](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)

U [ook resources.azure.com](https://resources.azure.com) of de [Azure-SDK's](https://azure.microsoft.com/downloads/) gebruiken om de VM's in een schaalset weer te geven.

De exacte presentatie van de uitvoer is afhankelijk van de opties die u aan de opdracht geeft, maar hier is een voorbeeld uitvoer van de CLI:

```azurecli
az vmss show -g {resourceGroupName} -n {vmScaleSetName}
```

```output
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Zoals u zien, is de eigenschap instanceId slechts een decimaal getal. De instantie-id's kunnen opnieuw worden gebruikt voor nieuwe exemplaren zodra oude exemplaren zijn verwijderd.

>[!NOTE]
> Er is **geen garantie** op de manier waarop instantie-id's zijn toegewezen aan de VM's in de schaalset. Ze lijken soms opeenvolgend toe te nemen, maar dit is niet altijd het geval. Neem geen afhankelijkheid van de specifieke manier waarop instantie-id's aan de VM's zijn toegewezen.

## <a name="scale-set-vm-names"></a>Vm-namen schalen

In de bovenstaande voorbeelduitvoer is er ook een "naam" voor de VM. Deze naam is de vorm "{scale-set-name}_{instance-id}". Deze naam ziet u in de Azure-portal wanneer u instanties in een schaalset opneemt:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

Het {instance-id}-gedeelte van de naam is hetzelfde decimaal getal als de eigenschap 'instanceId' die eerder is besproken.

## <a name="instance-metadata-vm-name"></a>VM-naam instantiemetagegevens

Als u de [metagegevens](../virtual-machines/windows/instance-metadata-service.md) van de instantie opvraagt vanuit een vm met schaalset, ziet u een 'naam' in de uitvoer:

```output
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Deze naam is hetzelfde als de eerder besproken naam.

## <a name="scale-set-vm-computer-name"></a>Naam vm-computer van schalen

Elke VM in een schaalset krijgt ook een computernaam toegewezen. Deze computernaam is de hostnaam van de VM in de [door Azure geleverde DNS-naamomzetting binnen het virtuele netwerk.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) Deze computernaam is van het formulier "{computer-name-prefix}{base-36-instance-id}".

De {base-36-instance-id} bevindt zich in [basis 36](https://en.wikipedia.org/wiki/Base36) en is altijd zes cijfers lang. Als de basis 36-weergave van het getal minder dan zes cijfers vergt, wordt de {base-36-instance-id} opgevuld met nullen om er zes cijfers lang van te maken. Een instantie met {computer-name-prefix} "nsgvmss" en instantie ID 85 heeft bijvoorbeeld de computernaam "nsgvmss00002D".

>[!NOTE]
> Het voorvoegsel van de computernaam is een eigenschap van het schaalsetmodel dat u instellen, zodat het kan verschillen van de naam van de schaalset zelf.

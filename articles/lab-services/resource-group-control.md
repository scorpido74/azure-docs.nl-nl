---
title: Resourcegroep opgeven voor VM's in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het opgeven van een resourcegroep voor VM's in een lab in Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 29816d158cf1428727b7ff17bcc2c347f402dedf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77134532"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Een resourcegroep opgeven voor virtuele laboratoriummachines in Azure DevTest Labs

Als eigenaar van een lab u uw virtuele laboratoriummachines configureren die in een specifieke brongroep worden gemaakt. Deze functie helpt u bij de volgende scenario's:

- Minder resourcegroepen maken door labs in uw abonnement.
- Laat uw labs werken binnen een vaste set resourcegroepen die u configureert.
- Werk rond beperkingen en goedkeuringen die nodig zijn voor het maken van resourcegroepen binnen uw Azure-abonnement.
- Consolideer al uw labbronnen binnen één resourcegroep om het bijhouden van deze bronnen te vereenvoudigen en [beleid](../governance/policy/overview.md) toe te passen om resources op het niveau van de resourcegroep te beheren.

Met deze functie u een script gebruiken om een nieuwe of bestaande brongroep op te geven binnen uw Azure-abonnement voor al uw lab-VM's. Momenteel ondersteunt Azure DevTest Labs deze functie via een API.

> [!NOTE]
> Alle abonnementslimieten zijn van toepassing wanneer u labs maakt in DevTest Labs. Denk aan een lab als elke andere bron in uw abonnement. In het geval van resourcegroepen is de limiet [980 resourcegroepen per abonnement.](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 

## <a name="use-azure-portal"></a>Azure Portal gebruiken
Volg deze stappen om een resourcegroep op te geven voor alle VM's die in het lab zijn gemaakt. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services** in het linkernavigatiemenu. 
3. Selecteer **DevTest Labs** uit de lijst.
4. Selecteer in de lijst met labs uw **lab.**  
5. Selecteer **Configuratie en beleid** in de sectie **Instellingen** in het linkermenu. 
6. Selecteer **Lab-instellingen** in het linkermenu. 
7. Selecteer **Alle virtuele machines in één resourcegroep**. 
8. Selecteer een bestaande resourcegroep in de vervolgkeuzelijst (of selecteer **Nieuw maken,** voer een **naam** in voor de resourcegroep en selecteer **OK**. 

    ![De resourcegroep selecteren voor alle vm's van het lab](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>PowerShell gebruiken 
In het volgende voorbeeld ziet u hoe u een PowerShell-script gebruikt om alle virtuele laboratoriummachines in een nieuwe brongroep te maken.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Roep het script aan met de volgende opdracht. ResourceGroup.ps1 is het bestand dat het voorgaande script bevat:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon gebruiken
Als u een Azure Resource Manager-sjabloon gebruikt om een lab te maken, gebruikt u de eigenschap **vmCreationResourceGroupId** in de sectie labeigenschappen van uw sjabloon, zoals in het volgende voorbeeld wordt weergegeven:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API om een resourcegroep voor labVM's te configureren
U hebt de volgende opties als eigenaar van een lab wanneer u deze API gebruikt:

- Kies de **resourcegroep van het lab** voor alle virtuele machines.
- Kies een **andere bestaande resourcegroep** dan de resourcegroep van het lab voor alle virtuele machines.
- Voer een **nieuwe naam van de resourcegroep** in voor alle virtuele machines.
- Doorgaan met het bestaande gedrag, waarbij voor elke VM in het lab een resourcegroep wordt gemaakt.
 
Deze instelling is van toepassing op nieuwe virtuele machines die in het lab zijn gemaakt. De oudere VM's in uw lab die zijn gemaakt in hun eigen resourcegroepen blijven ongewijzigd. Omgevingen die in uw lab zijn gemaakt, blijven in hun eigen resourcegroepen behouden.

Deze API gebruiken:
- API-versie **2018_10_15_preview gebruiken.**
- Als u een nieuwe resourcegroep opgeeft, moet u ervoor zorgen dat u schrijfmachtigingen hebt **voor resourcegroepen** in uw abonnement. Als u schrijfmachtigingen niet hebt, mislukt het maken van nieuwe virtuele machines in de opgegeven resourcegroep.
- Als u de API gebruikt, geeft u de **volledige brongroep-id door.** Bijvoorbeeld: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Controleer of de resourcegroep zich in hetzelfde abonnement bevindt als het lab. 


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Beleid instellen voor een lab](devtest-lab-get-started-with-lab-policies.md)
- [Veelgestelde vragen](devtest-lab-faq.md)

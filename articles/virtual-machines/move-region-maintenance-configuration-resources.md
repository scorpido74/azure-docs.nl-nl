---
title: Resources die zijn gekoppeld aan een onderhoudsconfiguratie verplaatsen naar een ander gebied
description: Meer informatie over het verplaatsen van resources die zijn gekoppeld aan een VM-onderhoudsconfiguratie naar een ander Azure-gebied
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304444"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Resources in een configuratie van onderhoudsbeheer verplaatsen naar een andere regio

Volg dit artikel om resources die zijn gekoppeld aan een configuratie met onderhoudsbeheer naar een ander Azure-gebied te verplaatsen. U een configuratie verplaatsen om een aantal redenen. Bijvoorbeeld om te profiteren van een nieuwe regio, om functies of services te implementeren die beschikbaar zijn in een specifieke regio, om te voldoen aan interne beleids- en governancevereisten of als reactie op capaciteitsplanning.

Met onderhoudsbeheer, met aangepaste onderhoudsconfiguraties, u bepalen hoe platformupdates worden toegepast op [Windows-](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) en Linux-VM's en op Azure Dedicated Hosts. [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) Er zijn een paar scenario's voor het verplaatsen van onderhoudscontrole in verschillende regio's:

- Volg dit artikel om de resources te verplaatsen die zijn gekoppeld aan een onderhoudsconfiguratie, maar niet de configuratie zelf.
- Volg [deze instructies](move-region-maintenance-configuration.md)om de configuratie van uw onderhoudsbeheer te verplaatsen, maar niet de resources die aan de configuratie zijn gekoppeld.
- Volg deze [instructies](move-region-maintenance-configuration.md)eerst om zowel de onderhoudsconfiguratie als de bijbehorende resources te verplaatsen. Volg vervolgens de instructies in dit artikel.

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het verplaatsen van de resources die zijn gekoppeld aan een configuratie van onderhoudsbeheer:

- Zorg ervoor dat de resources die u verplaatst, in de nieuwe regio bestaan voordat u begint.
- Controleer de onderhoudsbeheerconfiguraties die zijn gekoppeld aan de Azure VM's en Azure Dedicated Hosts die u wilt verplaatsen. Controleer elke resource afzonderlijk. Er is momenteel geen manier om configuraties voor meerdere resources op te halen.
- Bij het ophalen van configuraties voor een resource:
    - Zorg ervoor dat u de abonnements-ID voor het account gebruikt, niet voor een Azure Dedicated Host ID.
    - CLI: De parameter --outputtabel wordt alleen gebruikt voor leesbaarheid en kan worden verwijderd of gewijzigd.
    - PowerShell: De parameter Opmaaktabelnaam wordt alleen gebruikt voor leesbaarheid en kan worden verwijderd of gewijzigd.
    - Als u PowerShell gebruikt, wordt er een fout weergegeven als u configuraties probeert op te sommen voor een resource die geen bijbehorende configuraties heeft. De fout zal vergelijkbaar zijn met: "Bewerking is mislukt met status: 'Niet gevonden'. Details: 404 Client Error: Not Found for url".

    
## <a name="prepare-to-move"></a>Voorbereiden om te bewegen

1. Definieer deze variabelen voordat u begint. We hebben een voorbeeld voor elk.

    **Variabele** | **Details** | **Voorbeeld**
    --- | ---
    $subId | ID voor abonnement met de onderhoudsconfiguraties | "onze-abonnement-ID"
    $rsrcGroupName | Naam van resourcegroep (Azure VM) | "VMResourceGroup"
    $vmName | VM-resourcenaam |  "myVM"
    $adhRsrcGroupName |  Resourcegroep (toegewezen hosts) | "HostResourceGroup"
    $adh | Speciale hostnaam | "myHost"
    $adhParentName | Naam van bovenliggende bron | "HostGroup"
    
2. Ga als het gaat om de onderhoudsconfiguraties op te halen met de opdracht PowerShell [Get-AZConfigurationAssignment:](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0)

    - Voer voor Azure Dedicated Hosts het als:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Voer voor Azure VM's het als:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Ga als lid van het onderhoud van de onderhoudsopdracht CLI [az](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest) als lid van het onderhoud:

    - Voor Azure Dedicated Hosts:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Voor Azure VM's:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Verplaatsen 

1. [Volg deze instructies](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) om de Azure VM's naar het nieuwe gebied te verplaatsen.
2. Nadat de resources zijn verplaatst, past u de onderhoudsconfiguraties opnieuw toe op de resources in de nieuwe regio, afhankelijk van of u de onderhoudsconfiguraties hebt verplaatst. U een onderhoudsconfiguratie toepassen op een resource met [PowerShell](../virtual-machines/maintenance-control-powershell.md) of [CLI.](../virtual-machines/maintenance-control-cli.md)


## <a name="verify-the-move"></a>De verplaatsing verifiëren

Controleer resources in het nieuwe gebied en controleer de bijbehorende configuraties voor de resources in het nieuwe gebied. 

## <a name="clean-up-source-resources"></a>Bronbronnen opschonen

Overweeg na de verhuizing de verplaatste resources in het brongebied te verwijderen.


## <a name="next-steps"></a>Volgende stappen

Volg [deze instructies](move-region-maintenance-configuration.md) als u onderhoudsconfiguraties moet verplaatsen. 

---
title: Een onderhoudsconfiguratie verplaatsen naar een andere Azure-regio
description: Meer informatie over het verplaatsen van een VM-onderhoudsconfiguratie naar een andere Azure-regio
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304457"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Een configuratie van onderhoudsbeheer naar een andere regio verplaatsen

Volg dit artikel om een configuratie met onderhoudsbeheer naar een andere Azure-regio te verplaatsen. U een configuratie verplaatsen om een aantal redenen. Bijvoorbeeld om te profiteren van een nieuwe regio, om functies of services te implementeren die beschikbaar zijn in een specifieke regio, om te voldoen aan interne beleids- en governancevereisten of als reactie op capaciteitsplanning.

Met onderhoudsbeheer, met aangepaste onderhoudsconfiguraties, u bepalen hoe platformupdates worden toegepast op [Windows-](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) en Linux-VM's en op Azure Dedicated Hosts. [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) Er zijn een paar scenario's voor het verplaatsen van onderhoudscontrole in verschillende regio's:

- Volg de instructies in dit artikel om de configuratie van uw onderhoudsbeheer te verplaatsen, maar niet de resources die aan de configuratie zijn gekoppeld.
- Volg [deze instructies](move-region-maintenance-configuration-resources.md)als u de resources wilt verplaatsen die zijn gekoppeld aan een onderhoudsconfiguratie, maar niet de configuratie zelf.
- Volg eerst de instructies in dit artikel om zowel de onderhoudsconfiguratie als de bijbehorende resources te verplaatsen. Volg vervolgens [deze instructies.](move-region-maintenance-configuration-resources.md)

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het verplaatsen van een configuratie van onderhoudscontrole:

- Onderhoudsconfiguraties zijn gekoppeld aan Azure VM's of Azure Dedicated Hosts. Zorg ervoor dat VM/hostbronnen in de nieuwe regio bestaan voordat u begint.
- Identificeren: 
    - Bestaande onderhoudsbeheerconfiguraties.
    - De resourcegroepen waarin bestaande configuraties zich momenteel bevinden. 
    - De resourcegroepen waaraan de configuraties worden toegevoegd nadat ze naar de nieuwe regio zijn verhuisd. 
    - De resources die zijn gekoppeld aan de onderhoudsconfiguratie die u wilt verplaatsen.
    - Controleer of de resources in het nieuwe gebied dezelfde zijn als die welke zijn gekoppeld aan de huidige onderhoudsconfiguraties. De configuraties kunnen dezelfde namen hebben in de nieuwe regio als in het oude, maar dit is niet vereist.

## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen 

1. Haal alle onderhoudsconfiguraties in elk abonnement op. Voer hiervoor de [opdracht voor de lijst met onderhoudsconfiguratie](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) CLI az uit om $subId te vervangen door uw abonnements-ID.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Bekijk de lijst met geretourneerde tabelmetconfiguratierecords binnen het abonnement. Hier volgt een voorbeeld. Uw lijst bevat waarden voor uw specifieke omgeving.

    **Naam** | **Locatie** | **Resourcegroep**
    --- | --- | ---
    Onderhoud overslaan | eastus2 | configuratie-resourcegroep
    IgniteDemoConfig | eastus2 | configuratie-resourcegroep
    standaardMaintenanceConfiguration-eastus | eastus | testconfiguratie
    

3. Sla uw lijst op als referentie. Terwijl u de configuraties verplaatst, u controleren of alles is verplaatst.
4. Wijs elke configuratie/resourcegroep als referentie af op de nieuwe resourcegroep in de nieuwe regio.
5. Maak nieuwe onderhoudsconfiguraties in het nieuwe gebied met [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)of [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Koppel de configuraties aan de resources in het nieuwe gebied met [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)of [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>De verplaatsing verifiëren

Nadat u de configuraties hebt verplaatst, vergelijkt u configuraties en resources in de nieuwe regio met de tabellijst die u hebt opgesteld.


## <a name="clean-up-source-resources"></a>Bronbronnen opschonen

Overweeg na de verhuizing de verplaatste onderhoudsconfiguraties in het [brongebied, PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)of [CLI te](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)verwijderen .


## <a name="next-steps"></a>Volgende stappen

Volg [deze instructies](move-region-maintenance-configuration-resources.md) als u resources wilt verplaatsen die zijn gekoppeld aan onderhoudsconfiguraties. 

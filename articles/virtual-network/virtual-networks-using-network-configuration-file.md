---
title: Een Azure Virtual Network (klassiek) configureren-netwerk configuratie bestand | Microsoft Docs
description: Meer informatie over het maken en wijzigen van virtuele netwerken (klassiek) door het exporteren, wijzigen en importeren van een netwerk configuratie bestand.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: ab4faa0f727469e27eb30af54f24036292ec3118
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058704"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Een virtueel netwerk (klassiek) configureren met behulp van een netwerk configuratie bestand
> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources: [Resource Manager en het klassieke model](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Dit artikel gaat over het gebruik van het klassieke implementatiemodel. Micro soft raadt aan de meeste nieuwe implementaties het Resource Manager-implementatie model te gebruiken.

U kunt een virtueel netwerk (klassiek) maken en configureren met een netwerk configuratie bestand met behulp van de klassieke Azure-opdracht regel interface (CLI) of Azure PowerShell. U kunt een virtueel netwerk niet maken of wijzigen via het Azure Resource Manager-implementatie model met behulp van een netwerk configuratie bestand. U kunt de Azure Portal niet gebruiken voor het maken of wijzigen van een virtueel netwerk (klassiek) met behulp van een netwerk configuratie bestand, maar als u de Azure Portal gebruikt om een virtueel netwerk (klassiek) te maken, hoeft u geen netwerk configuratie bestand te gebruiken.

Voor het maken en configureren van een virtueel netwerk (klassiek) met een netwerk configuratie bestand moet het bestand worden geëxporteerd, gewijzigd en geïmporteerd.

## <a name="export"></a>Een netwerk configuratie bestand exporteren

U kunt Power shell of de klassieke Azure-CLI gebruiken om een netwerk configuratie bestand te exporteren. Power shell exporteert een XML-bestand, terwijl de klassieke Azure-CLI een JSON-bestand exporteert.

### <a name="powershell"></a>PowerShell
 
1. [Installeer Azure PowerShell en meld u aan bij Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Wijzig de directory (en zorg ervoor dat deze bestaat) en typ de bestands naam in de volgende opdracht, en voer vervolgens de opdracht uit om het netwerk configuratie bestand te exporteren:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

1. [Installeer de klassieke Azure-cli](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Voltooi de resterende stappen van een klassieke CLI-opdracht prompt.
2. Meld u aan bij Azure door de `azure login` opdracht in te voeren.
3. Zorg ervoor dat u zich in de ASM- `azure config mode asm` modus bevindt door de opdracht in te voeren.
4. Wijzig de directory (en zorg ervoor dat deze bestaat) en typ de bestands naam in de volgende opdracht, en voer vervolgens de opdracht uit om het netwerk configuratie bestand te exporteren:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Een netwerk configuratie bestand maken of wijzigen

Een netwerk configuratie bestand is een XML-bestand (bij gebruik van Power shell) of een JSON-bestand (bij gebruik van de klassieke CLI). U kunt het bestand bewerken in een wille keurige tekst of XML/JSON-editor. Het artikel schema-instellingen voor het [netwerk configuratie bestand](https://msdn.microsoft.com/library/azure/jj157100.aspx) bevat Details voor alle instellingen. Zie [virtuele netwerken en instellingen weer geven](manage-virtual-network.md#view-virtual-networks-and-settings)voor meer uitleg van de instellingen. De wijzigingen die u aanbrengt in het bestand:

- Moet voldoen aan het schema of het importeren van het netwerk configuratie bestand mislukt.
- Overschrijf alle bestaande netwerk instellingen voor uw abonnement. Wees daarom uiterst voorzichtig wanneer u wijzigingen aanbrengt. U kunt bijvoorbeeld verwijzen naar de voorbeeld netwerk configuratie bestanden die volgen. Zeg het oorspronkelijke bestand bevat twee **VirtualNetworkSite** -instanties en u hebt deze gewijzigd, zoals in de voor beelden wordt weer gegeven. Wanneer u het bestand importeert, verwijdert Azure het virtuele netwerk voor het **VirtualNetworkSite** -exemplaar dat u in het bestand hebt verwijderd. In dit vereenvoudigde scenario wordt ervan uitgegaan dat er geen resources in het virtuele netwerk aanwezig waren, alsof het virtuele netwerk niet kan worden verwijderd en mislukt het importeren.

> [!IMPORTANT]
> Azure beschouwt een subnet waarop iets is geïmplementeerd als **in gebruik**. Wanneer een subnet wordt gebruikt, kan het niet worden gewijzigd. Voordat u de subnetgegevens in een netwerk configuratie bestand wijzigt, moet u alle items die u hebt geïmplementeerd naar het subnet verplaatsen naar een ander subnet dat niet wordt gewijzigd. Zie [een virtuele machine of rolinstantie naar een ander subnet verplaatsen](virtual-networks-move-vm-role-to-subnet.md) voor meer informatie.

### <a name="example-xml-for-use-with-powershell"></a>Voor beeld-XML voor gebruik met Power shell

In het volgende voor beeld wordt een virtueel netwerk met de naam *myVirtualNetwork* gemaakt met een adres ruimte van *10.0.0.0/16* in de regio *VS Oost* Azure. Het virtuele netwerk bevat één subnet met de naam *mySubnet* met het adres voorvoegsel *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

Als het netwerk configuratie bestand dat u hebt geëxporteerd geen inhoud bevat, kunt u de XML in het vorige voor beeld kopiëren en in een nieuw bestand plakken.

### <a name="example-json-for-use-with-the-classic-cli"></a>Voor beeld-JSON voor gebruik met de klassieke CLI

In het volgende voor beeld wordt een virtueel netwerk met de naam *myVirtualNetwork* gemaakt met een adres ruimte van *10.0.0.0/16* in de regio *VS Oost* Azure. Het virtuele netwerk bevat één subnet met de naam *mySubnet* met het adres voorvoegsel *10.0.0.0/24*.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

Als het netwerk configuratie bestand dat u hebt geëxporteerd geen inhoud bevat, kunt u de json in het vorige voor beeld kopiëren en plakken in een nieuw bestand.

## <a name="import"></a>Een netwerk configuratie bestand importeren

U kunt Power shell of de klassieke CLI gebruiken om een netwerk configuratie bestand te importeren. Power shell importeert een XML-bestand, terwijl de klassieke CLI een JSON-bestand importeert. Als het importeren mislukt, controleert u of het bestand voldoet aan het [netwerk configuratie schema](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Installeer Azure PowerShell en meld u aan bij Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Wijzig indien nodig de map en de bestands naam in de volgende opdracht en voer de opdracht uit om het netwerk configuratie bestand te importeren:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

1. [Installeer de klassieke Azure-cli](/cli/azure/install-classic-cli). Voltooi de resterende stappen van een klassieke CLI-opdracht prompt.
2. Meld u aan bij Azure door de `azure login` opdracht in te voeren.
3. Zorg ervoor dat u zich in de ASM- `azure config mode asm` modus bevindt door de opdracht in te voeren.
4. Wijzig indien nodig de map en de bestands naam in de volgende opdracht en voer de opdracht uit om het netwerk configuratie bestand te importeren:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```

---
title: VM maken op basis van een beheerde afbeelding in Azure
description: Maak een virtuele Windows-machine op basis van een algemeen beheerde afbeelding met Azure PowerShell of de Azure-portal in het implementatiemodel Resource Beheer.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: de59edc2e2c702993efd6187a590264d9aac16a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74841928"
---
# <a name="create-a-vm-from-a-managed-image"></a>Een VM maken op basis van een beheerde installatiekopie

U meerdere virtuele machines (VM's) maken op basis van een door Azure beheerde VM-afbeelding met behulp van de Azure-portal of PowerShell. Een beheerde VM-afbeelding bevat de informatie die nodig is om een VM te maken, inclusief het besturingssysteem en gegevensschijven. De virtuele harde schijven (VHD's) die deel uitmaken van de afbeelding, met inbegrip van zowel de OS-schijven en alle gegevensschijven, worden opgeslagen als beheerde schijven. 

Voordat u een nieuwe virtuele machine maakt, moet u [een beheerde VM-afbeelding maken](capture-image-resource.md) om als bronafbeelding te gebruiken en leestoegang op de afbeelding verlenen aan elke gebruiker die toegang tot de afbeelding moet hebben. 


## <a name="use-the-portal"></a>Gebruik de portal

1. Ga naar de [Azure-portal](https://portal.azure.com) om een beheerde afbeelding te vinden. Zoeken naar afbeeldingen en selecteer **deze**.
3. Selecteer de afbeelding die u wilt gebruiken in de lijst. De pagina **Afbeeldingsoverzicht** wordt geopend.
4. Selecteer **VM maken** in het menu.
5. Geef de informatie van de virtuele machine op. De gebruikersnaam en het wachtwoord die hier worden ingevoerd, worden gebruikt om in te loggen op de virtuele machine. Als u klaar bent, selecteert u **OK**. U de nieuwe vm maken in een bestaande resourcegroep of **nieuw maken** kiezen om een nieuwe resourcegroep te maken om de VM op te slaan.
6. Selecteer een grootte voor de VM. Kies om meer groottes weer te geven de optie **Alle weergeven** of wijzig het filter **Ondersteund schijftype**. 
7. Breng **onder Instellingen**indien nodig wijzigingen aan en selecteer **OK**. 
8. Op de overzichtspagina ziet u uw afbeeldingsnaam als **privéafbeelding**. Selecteer **Ok** om de implementatie van de virtuele machine te starten.


## <a name="use-powershell"></a>PowerShell gebruiken

U PowerShell gebruiken om een VM van een afbeelding te maken met behulp van de vereenvoudigde parameterset voor de cmdlet [Nieuw-AzVm.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) De afbeelding moet zich in dezelfde resourcegroep bevindt waarin u de vm maakt.

 

De vereenvoudigde parameterset voor [Nieuw-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) vereist alleen dat u een naam, resourcegroep en afbeeldingsnaam opgeeft om een VM van een afbeelding te maken. Nieuw-AzVm gebruikt automatisch de waarde van de parameter **-Naam** als de naam van alle resources die deze maakt. In dit voorbeeld geven we meer gedetailleerde namen voor elk van de resources, maar laten we de cmdlet ze automatisch maken. U ook vooraf resources maken, zoals het virtuele netwerk, en de naam van de resource doorgeven aan de cmdlet. New-AzVm zal de bestaande bronnen gebruiken als het ze bij hun naam kan vinden.

In het volgende voorbeeld wordt een VM met de naam *myVMFromImage*gemaakt in de resourcegroep *myResourceGroup* op basis van de afbeelding met de naam *myImage*. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>Volgende stappen
[Windows VM's maken en beheren met de Azure PowerShell-module](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


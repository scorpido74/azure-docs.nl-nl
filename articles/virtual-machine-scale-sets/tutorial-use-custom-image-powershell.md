---
title: Zelfstudie - Een aangepaste VM-afbeelding gebruiken in een schaalset met Azure PowerShell
description: Informatie over het gebruik van Azure PowerShell voor het maken van een aangepaste VM-installatiekopie die u kunt gebruiken voor het implementeren van een virtuele-machineschaalset
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: daef03b411a451fc3e5b73e46091672810b0f9bd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76278295"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Zelfstudie: Een aangepaste installatiekopie voor virtuele-machineschaalsets maken en gebruiken met Azure PowerShell

Wanneer u een schaalset maakt, geeft u een installatiekopie op die moet worden gebruikt wanneer de VM-exemplaren zijn geïmplementeerd. Om het aantal taken na de implementatie van VM-exemplaren te verminderen, kunt u een aangepaste VM-installatiekopie gebruiken. Deze aangepaste VM-installatiekopie bevat alle geïnstalleerde toepassingen of configuraties die vereist zijn. Alle VM-exemplaren die in de schaalset zijn gemaakt, gebruiken de aangepaste VM-installatiekopie en zijn gereed voor uw toepassingsverkeer. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een VM maken en aanpassen
> * De inrichting van de VM ongedaan maken en de VM generaliseren
> * Een aangepaste VM-installatiekopie maken van de bron-VM
> * Een schaalset implementeren die gebruikmaakt van de aangepaste VM-installatiekopie

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-and-configure-a-source-vm"></a>Een bron-VM maken en configureren

>[!NOTE]
> Deze zelfstudie leidt u door het proces van het maken en gebruiken van een algemene VM-installatiekopie. Het maken van een schaalset op basis van een gespecialiseerde VHD wordt niet ondersteund.

Maak eerst een resourcegroep met [New-AzResourceGroup](/powershell/module/az.compute/new-azvm) en vervolgens een VM met [New-AzVM](/powershell/module/az.resources/new-azresourcegroup). Deze VM wordt vervolgens gebruikt als bron voor een aangepaste VM-installatiekopie. In het volgende voorbeeld wordt een VM met de naam *myCustomVM* gemaakt in de resourcegroep met de naam *myResourceGroup*. Wanneer u hierom wordt gevraagd, geeft u een gebruikersnaam en wachtwoord op dat moet worden gebruikt als de aanmeldingsreferenties voor de VM:

```azurepowershell-interactive
# Create a resource a group
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Geef voor verbinding met uw VM het openbare IP-adres met [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) als volgt op:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Maak een externe verbinding met de VM. Als u Azure Cloud Shell gebruikt, voert u deze stap uit vanaf een lokale PowerShell-prompt of vanuit de Extern bureaublad-client. Geef uw eigen IP-adres op uit de vorige opdracht. Wanneer u hierom wordt gevraagd, voert u de referenties in die zijn gebruikt bij het maken van de VM:

```powershell
mstsc /v:<IpAddress>
```

We gaan een eenvoudige webserver voor het aanpassen van uw VM installeren. Bij implementatie van het VM-exemplaar in de schaalset heeft dit hiermee alle vereiste pakketten om een webtoepassing uit te voeren. Open een lokale PowerShell-opdrachtprompt op de VM en installeer de IIS-webserver met [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) als volgt:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

De laatste stap voor het voorbereiden van uw VM voor gebruik als een aangepaste installatiekopie is het generaliseren van de VM. Sysprep verwijdert al uw persoonlijke gegevens en configuraties en stelt de virtuele machine opnieuw in op een schone toestand voor toekomstige implementaties. Zie voor meer informatie [Sysprep gebruiken: een inleiding](https://technet.microsoft.com/library/bb457073.aspx).

Voer Sysprep uit om de virtuele machine te generaliseren, en stel de virtuele machine in voor een out-of-the-box-ervaring. Als u klaar bent, instrueer dan Sysprep om de virtuele machine af te sluiten:

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

De externe verbinding naar de virtuele machine wordt automatisch gesloten wanneer het proces door Sysprep is uitgevoerd en de virtuele machine wordt afgesloten.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Een aangepaste VM-installatiekopie maken van de bron-VM
De bron-VM is nu aangepast met de geïnstalleerde IIS-webserver. We maken een aangepaste VM-installatiekopie om deze te gebruiken met een schaalset.

Voor het maken van een installatiekopie moet de toewijzing van de VM ongedaan worden gemaakt. Maak de toewijzing van de virtuele machine ongedaan met [Stop-AzVm](/powershell/module/az.compute/stop-azvm). Vervolgens stelt u de status van de virtuele machine in als gegeneraliseerd met [Set-AzVm](/powershell/module/az.compute/set-azvm), zodat het Azure-platform weet dat de virtuele machine klaar is voor het gebruik van een aangepaste installatiekopie. U kunt alleen een installatiekopie maken op basis van een gegeneraliseerde virtuele machine:

```azurepowershell-interactive
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

Het duurt enkele minuten om de toewijzing ongedaan te maken en de virtuele machine te generaliseren.

Maak nu een installatiekopie van de virtuele machine met [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig) en [New-AzImage](/powershell/module/az.compute/new-azimage). In het volgende voorbeeld wordt er een installatiekopie met de naam *myImage* gemaakt van uw virtuele machine:

```azurepowershell-interactive
# Get VM object
$vm = Get-AzVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```

## <a name="configure-the-network-security-group-rules"></a>De groepsregels voor netwerkbeveiliging configureren
Voordat we de schaalset maken, moeten we de regels van de netwerkbeveiligingsgroep configureren om toegang te krijgen tot HTTP, RDP en Remoting 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$rule2 = New-AzNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389

$rule3 = New-AzNetworkSecurityRuleConfig -Name remoting-rule -Description "Allow PS Remoting" -Access Allow -Protocol Tcp -Direction Inbound -Priority 120 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 5985

New-AzNetworkSecurityGroup -Name "myNSG" -ResourceGroupName "myResourceGroup" -Location "EastUS" -SecurityRules $rule1,$rule2,$rule3
```

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Een schaalset maken op basis van de aangepaste VM-installatiekopie
Maak nu een schaalset met [New-AzVmss](/powershell/module/az.compute/new-azvmss) die gebruikmaakt van de parameter `-ImageName` voor het definiëren van de aangepaste VM-installatiekopie die in de vorige stap is gemaakt. Om het verkeer te distribueren naar de verschillende VM-exemplaren, wordt er ook een load balancer gemaakt. De load balancer bevat regels voor het distribueren van verkeer op TCP-poort 80, en voor het toestaan van verkeer van Extern bureaublad op TCP-poort 3389 en externe toegang via PowerShell op TCP-poort 5985. Geef desgevraagd uw eigen beheerdersreferenties op voor de VM-exemplaren in de schaalset:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNSG"
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.


## <a name="test-your-scale-set"></a>Uw schaalset testen
Als u de schaalset in actie wilt zien, achterhaalt u het openbare IP-adres van de load balancer met [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress). Dit doet u als volgt:


```azurepowershell-interactive
Get-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Voer in uw webbrowser het openbare IP-adres in. De standaard IIS-webpagina wordt weergegeven, zoals in het volgende voorbeeld:

![IIS uitgevoerd vanuit een aangepaste VM-installatiekopie](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Resources opschonen
Als u de schaalset en aanvullende resources wilt verwijderen, verwijdert u de resourcegroep en alle bijbehorende resources met [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). De parameter `-Force` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen. De parameter `-AsJob` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een aangepaste VM-installatiekopie maakt en gebruikt voor uw schaalsets met Azure PowerShell:

> [!div class="checklist"]
> * Een VM maken en aanpassen
> * De inrichting van de VM ongedaan maken en de VM generaliseren
> * Een aangepaste VM-installatiekopie maken
> * Een schaalset implementeren die gebruikmaakt van de aangepaste VM-installatiekopie

Ga door naar de volgende zelfstudie voor informatie over het implementeren van toepassingen naar uw schaalset.

> [!div class="nextstepaction"]
> [Toepassingen in uw schaalsets implementeren](tutorial-install-apps-powershell.md)

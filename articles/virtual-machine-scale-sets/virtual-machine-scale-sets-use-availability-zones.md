---
title: Een Azure-schaalset maken die beschikbaarheidszones gebruikt
description: Meer informatie over het maken van Azure-schaalsets voor virtuele machines die beschikbaarheidszones gebruiken voor meer redundantie tegen uitval
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: cynthn
ms.openlocfilehash: 11695eb889a10dc689b00399a37382a3b9772eae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274410"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Een virtuele machineschaalset maken die beschikbaarheidszones gebruikt

Om uw virtuele machineschaalsets te beschermen tegen fouten op datacenterniveau, u een schaalset maken voor beschikbaarheidszones. Azure-regio's die beschikbaarheidszones ondersteunen, hebben minimaal drie afzonderlijke zones, elk met hun eigen onafhankelijke stroombron, netwerk en koeling. Zie [Overzicht van beschikbaarheidszones voor](../availability-zones/az-overview.md)meer informatie.

## <a name="availability-considerations"></a>Beschikbaarheidsoverwegingen

Wanneer u een schaalset implementeert in een of meer zones vanaf *API-versie 2017-12-01,* hebt u de mogelijkheid om te implementeren met "max spreading" of "static 5 fault domain spreading". Met maximale spreiding verspreidt de schaalset uw VM's over zoveel mogelijk foutdomeinen binnen elke zone. Deze verspreiding kan zich over meer of minder dan vijf foutdomeinen per zone bevinden. Met "static 5 fault domain spreading" verspreidt de schaalset uw VM's over precies vijf foutdomeinen per zone. Als de schaalset geen vijf afzonderlijke foutdomeinen per zone kan vinden om aan de toewijzingsaanvraag te voldoen, mislukt de aanvraag.

We raden u aan om voor **de meeste workloads maximaal te worden geïmplementeerd,** omdat deze aanpak in de meeste gevallen de beste spreiding biedt. Als u replica's nodig hebt die over verschillende hardwareisolatie-eenheden moeten worden verspreid, raden we u aan zich te verspreiden over beschikbaarheidszones en gebruik te maken van maximale verspreiding binnen elke zone.

Met maximale spreiding ziet u slechts één foutdomein in de vm-instantieweergave van de schaalset en in de instantiemetagegevens, ongeacht het aantal foutdomeinen dat de VM's hebben verspreid. De verspreiding binnen elke zone is impliciet.

Als u maximale spreiding wilt gebruiken, stelt u *platformFaultDomainCount* in op *1*. Als u statische verspreiding van vijf foutdomeinen wilt gebruiken, stelt u *platformFaultDomainCount* in op *5*. In API-versie *2017-12-01*is *platformFaultDomainCount* standaard op *1* voor schaalsets met één zone en meerdere zones. Momenteel wordt alleen statische vijf foutdomeinverspreiding ondersteund voor regionale (niet-zonale) schaalsets.

### <a name="placement-groups"></a>Plaatsingsgroepen

Wanneer u een schaalset implementeert, u ook implementeren met één [plaatsingsgroep](./virtual-machine-scale-sets-placement-groups.md) per beschikbaarheidszone of met meerdere per zone. Voor regionale (niet-zonale) schaalsets is de keuze om één plaatsingsgroep in de regio te hebben of meerdere in de regio te hebben. Voor de meeste workloads raden we meerdere plaatsingsgroepen aan, wat een grotere schaal mogelijk maakt. In API-versie *2017-12-01*stelt schaal standaard in op meerdere plaatsingsgroepen voor schaalsets met één zone en meerdere zones, maar deze worden standaard ingesteld op één plaatsingsgroep voor regionale (niet-zonale) schaalsets.

> [!NOTE]
> Als u maximale spreiding gebruikt, moet u meerdere plaatsingsgroepen gebruiken.

### <a name="zone-balancing"></a>Zonebalanceren

Ten slotte hebt u voor schaalsets die in meerdere zones zijn geïmplementeerd, ook de mogelijkheid om "best effort zone balance" of "strict zone balance" te kiezen. Een schaalset wordt als 'gebalanceerd' beschouwd als\\elke zone hetzelfde aantal VM's of + - 1 VM in alle andere zones voor de schaalset. Bijvoorbeeld:

- Een schaalset met 2 VM's in zone 1, 3 VM's in zone 2 en 3 VM's in zone 3 wordt als evenwichtig beschouwd. Er is slechts één zone met een ander VM-aantal en het is slechts 1 minder dan de andere zones. 
- Een schaalset met 1 VM in zone 1, 3 VM's in zone 2 en 3 VM's in zone 3 wordt als onevenwichtig beschouwd. Zone 1 heeft 2 minder VM's dan zones 2 en 3.

Het is mogelijk dat VM's in de schaalset zijn gemaakt, maar extensies op deze VM's kunnen niet worden geïmplementeerd. Deze VM's met extensiefouten worden nog steeds meegeteld bij het bepalen of een schaalset in evenwicht is. Een schaalset met 3 VM's in zone 1, 3 VM's in zone 2 en 3 VM's in zone 3 wordt bijvoorbeeld als evenwichtig beschouwd, zelfs als alle uitbreidingen in zone 1 zijn mislukt en alle extensies zijn geslaagd in de zones 2 en 3.

Met de best-effort zone balans, de schaal set probeert te schalen in en uit met behoud van evenwicht. Als dit om de een of andere reden echter niet mogelijk is (bijvoorbeeld als één zone uitvalt, kan de schaalset geen nieuwe vm in die zone maken), met de schaalset kan tijdelijke onbalans met succes in- of uitschalen. Bij volgende scale-outpogingen voegt de schaalset VM's toe aan zones die meer VM's nodig hebben om de schaalset in evenwicht te brengen. Op dezelfde manier verwijdert de schaalset op volgende schaal in pogingen VM's uit zones waarvoor minder VM's nodig zijn om de weegschaal in evenwicht te brengen. Met "strikte zone balans", de schaal set niet alle pogingen om schaal in of uit als dit zou leiden tot onbalans.

Als u het zonesaldo met de beste inspanning wilt gebruiken, stelt u *zoneBalans* in op *false*. Deze instelling is de standaardinstelling in *API-versie 2017-12-01*. Als u een strikt zonesaldo wilt gebruiken, stelt u *zoneBalance* in *op true.*

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Single-zone en zone-redundante schaalsets

Wanneer u een virtuele machineschaalset implementeert, u ervoor kiezen om één beschikbaarheidszone in een regio of meerdere zones te gebruiken.

Wanneer u een schaalset in één zone maakt, bepaalt u in welke zone al die VM-exemplaren worden uitgevoerd en wordt de schaalset beheerd en wordt de automatische schalen alleen binnen die zone beheerd. Met een zoneredundante schaalset u één schaalset maken die meerdere zones omvat. Als VM-exemplaren worden gemaakt, zijn ze standaard gelijkmatig verdeeld over zones. Als er een onderbreking optreedt in een van de zones, wordt een schaalset niet automatisch uitgeschaald om de capaciteit te vergroten. Een aanbevolen toepassing zou zijn om autoscale regels te configureren op basis van CPU of geheugengebruik. Met de regels voor automatische schaal kan de schaaldie is ingesteld om te reageren op een verlies van de VM-exemplaren in die ene zone door nieuwe exemplaren in de resterende operationele zones uit te schalen.

Als u beschikbaarheidszones wilt gebruiken, moet uw schaalset worden gemaakt in een [ondersteunde Azure-regio.](../availability-zones/az-overview.md#services-support-by-region) U een schaalset maken die beschikbaarheidszones gebruikt met een van de volgende methoden:

- [Azure-portal](#use-the-azure-portal)
- Azure-CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager-sjablonen](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Het proces voor het maken van een schaalset die een beschikbaarheidszone gebruikt, is hetzelfde als beschreven in het [beginartikel.](quick-create-portal.md) Wanneer u een ondersteund Azure-gebied selecteert, u een schaalset maken in een of meer beschikbare zones, zoals in het volgende voorbeeld wordt weergegeven:

![Een schaalset maken in één beschikbaarheidszone](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

De schaalset en ondersteunende resources, zoals de Azure load balancer en het openbare IP-adres, worden gemaakt in de afzonderlijke zone die u opgeeft.

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

Het proces voor het maken van een schaalset die een beschikbaarheidszone gebruikt, is hetzelfde als beschreven in het [beginartikel.](quick-create-cli.md) Als u beschikbaarheidszones wilt gebruiken, moet u uw schaalset maken in een ondersteunde Azure-regio.

Voeg `--zones` de parameter toe aan de [opdracht az vmss create](/cli/azure/vmss) en geef op welke zone je wilt gebruiken (zoals zone *1,* *2*of *3).* In het volgende voorbeeld wordt een schaalset met één zone met de naam *myScaleSet* in zone *1:*

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Zie dit voorbeeld VAN [CLI-script](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh) voor een volledig voorbeeld van één zone-schaalset en netwerkbronnen

### <a name="zone-redundant-scale-set"></a>Zoneredundante schaalset

Als u een zoneredundante schaalset wilt maken, gebruikt u een openbaar IP-adres en een load balancer voor *standaard* SKU. Voor verbeterde redundantie maakt de *Standaard* SKU zoneredundante netwerkbronnen. Zie azure [load balancer standard-overzicht](../load-balancer/load-balancer-standard-overview.md) en [standaardstandaardvoor standaardbelasting en beschikbaarheidszones voor](../load-balancer/load-balancer-standard-availability-zones.md)meer informatie .

Als u een zoneredundante schaalset wilt `--zones` maken, geeft u meerdere zones op met de parameter. In het volgende voorbeeld wordt een zoneredundante schaalset met de naam *myScaleSet* gemaakt in zones *1,2,3:*

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

Het duurt een paar minuten om alle schaalsetresources en VM's in de door u opgegeven zone(s) te maken en te configureren. Zie [dit voorbeeld VAN CLI-script](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh) voor een volledig voorbeeld van een zoneredundante schaalset en netwerkbronnen

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

Als u beschikbaarheidszones wilt gebruiken, moet u uw schaalset maken in een ondersteunde Azure-regio. Voeg `-Zone` de parameter toe aan de opdracht [Nieuw-AzVmsConfig](/powershell/module/az.compute/new-azvmssconfig) en geef op welke zone u wilt gebruiken (zoals zone *1,* *2*of *3*).

In het volgende voorbeeld wordt een schaalset met één zone met de naam *myScaleSet* in *zone 1 oost-VS 2* *.* De Azure-netwerkresources voor het virtuele netwerk, het openbare IP-adres en de load balancer worden automatisch gemaakt. Geef desgevraagd uw eigen beheerdersreferenties op voor de VM-exemplaren in de schaalset:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Zoneredundante schaalset

Als u een zoneredundante schaalset wilt `-Zone` maken, geeft u meerdere zones op met de parameter. In het volgende voorbeeld wordt een zoneredundante schaalset met de naam *myScaleSet* gemaakt in *de Oost-VS 2-zones* *1, 2, 3*. De zoneredundante Azure-netwerkbronnen voor virtueel netwerk, openbaar IP-adres en load balancer worden automatisch gemaakt. Geef desgevraagd uw eigen beheerdersreferenties op voor de VM-exemplaren in de schaalset:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken

Het proces om een schaalset te maken die een beschikbaarheidszone gebruikt, is hetzelfde als in het beginartikel voor [Linux](quick-create-template-linux.md) of [Windows.](quick-create-template-windows.md) Als u beschikbaarheidszones wilt gebruiken, moet u uw schaalset maken in een ondersteunde Azure-regio. Voeg `zones` de eigenschap toe aan het resourcetype *Microsoft.Compute/virtualMachineScaleSets* in uw sjabloon en geef op welke zone u wilt gebruiken (zoals zone *1,* *2*of *3*).

In het volgende voorbeeld wordt een Linux-schaalset met de naam *myScaleSet* in *zone 1 van Oost-VS* *2:*

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Zie [deze sjabloon voor voorbeeld](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json) van een schaal met één zone en netwerkbronnen voor een volledig voorbeeld van een schaalset met één zone en netwerkbronnen

### <a name="zone-redundant-scale-set"></a>Zoneredundante schaalset

Als u een zoneredundante schaalset wilt `zones` maken, geeft u meerdere waarden op in de eigenschap voor het resourcetype *Microsoft.Compute/virtualMachineScaleSets.* In het volgende voorbeeld wordt een zoneredundante schaalset met de naam *myScaleSet* gemaakt in *oost-AMERIKAANSE 2-zones* *1,2,3:*

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Als u een openbaar IP-adres of een load balancer maakt, geeft u de eigenschap *'sku': {'name': 'Standaard' }'* op om zoneredundante netwerkbronnen te maken. U moet ook een netwerkbeveiligingsgroep en regels maken om verkeer mogelijk te maken. Zie azure [load balancer standard-overzicht](../load-balancer/load-balancer-standard-overview.md) en [standaardstandaardvoor standaardbelasting en beschikbaarheidszones voor](../load-balancer/load-balancer-standard-availability-zones.md)meer informatie .

Zie [deze sjabloon voor voorbeeld](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json) van een zoneredundante schaal en netwerkbronnen voor een volledig voorbeeld van een zoneredundante schaalset en netwerkbronnen

## <a name="next-steps"></a>Volgende stappen

Nu u een schaalset hebt gemaakt in een beschikbaarheidszone, u leren hoe u [toepassingen implementeert op virtuele machineschaalsets](tutorial-install-apps-cli.md) of [Automatisch schalen gebruiken met virtuele machineschaalsets.](tutorial-autoscale-cli.md)

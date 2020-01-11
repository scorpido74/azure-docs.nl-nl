---
title: Een schaalset maken die gebruikmaakt van Azure spot Vm's (preview-versie)
description: Meer informatie over het maken van virtuele-machine schaal sets van Azure die gebruikmaken van behulp van de functie voor het besparen van kosten.
services: virtual-machine-scale-sets
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/23/2019
ms.author: cynthn
ms.openlocfilehash: b57c13d4a5c671595a3e82ac7858c027456107f2
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894080"
---
# <a name="preview-azure-spot-vms-for-virtual-machine-scale-sets"></a>Voor beeld: Azure spot-Vm's voor schaal sets voor virtuele machines 

Met Azure spot op schaal sets kunt u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kosten besparing. Op elk moment dat Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur spot instanties. Daarom zijn spot instanties geweldig voor werk belastingen die onderbrekingen kunnen afhandelen, zoals batch verwerkings taken, ontwikkel-en test omgevingen, grote Compute-workloads, en meer.

De hoeveelheid beschik bare capaciteit kan variëren op basis van grootte, regio, tijd van de dag en meer. Bij het implementeren van spot instanties op schaal sets wordt het exemplaar alleen door Azure toegewezen als er capaciteit beschikbaar is, maar er is geen SLA voor deze instanties. Er wordt een set met hoge Beschik baarheid in één fout domein geïmplementeerd.

> [!IMPORTANT]
> Spot instanties zijn momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
>
> Voor het vroege deel van de open bare preview hebben spot instanties een vaste prijs, zodat er geen op prijzen gebaseerde verwijderingen zijn.

## <a name="pricing"></a>Prijzen

Prijzen voor spot instanties zijn variabel, op basis van de regio en de SKU. Zie prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)voor meer informatie. 


Met variabele prijzen kunt u een maximum prijs instellen, in Amerikaanse dollars (USD), met Maxi maal vijf decimalen. De waarde `0.98765`bijvoorbeeld een maximum prijs van $0,98765 USD per uur. Als u de maximum prijs instelt op `-1`, wordt het exemplaar niet verwijderd op basis van de prijs. De prijs voor de instantie is de huidige prijs voor de steun waarde of de prijs voor een standaard exemplaar, die ooit kleiner is, zolang er capaciteit en quota beschikbaar zijn.

## <a name="eviction-policy"></a>Verwijderings beleid

Bij het maken van een spot schaal sets kunt u het verwijderings beleid zo instellen dat de *toewijzing wordt opheffen* (standaard) of *verwijderen*. 

Het beleid voor het *opheffen* van de toewijzing verplaatst de verwijderde exemplaren naar de status stopped-disallocated, zodat u verwijderde exemplaren opnieuw kunt implementeren. Er is echter geen garantie dat de toewijzing slaagt. De toegewezen Vm's worden geteld voor het quotum van uw schaalset-exemplaar en er worden kosten in rekening gebracht voor de onderliggende schijven. 

Als u wilt dat uw instanties in uw schaalset worden verwijderd wanneer ze worden gewist, kunt u het verwijderings beleid instellen op *verwijderen*. Als het verwijderings beleid is ingesteld op verwijderen, kunt u nieuwe virtuele machines maken door de eigenschap aantal exemplaren van de schaalset te verhogen. De verwijderde Vm's worden samen met de onderliggende schijven verwijderd en daarom worden er geen kosten in rekening gebracht voor de opslag. U kunt ook de functie voor automatisch schalen van schaal sets gebruiken om automatisch te proberen en te compenseren voor verwijderde Vm's, maar er is echter geen garantie dat de toewijzing slaagt. Het wordt aanbevolen dat u de functie voor automatisch schalen op steun schaal sets gebruikt wanneer u het verwijderings beleid instelt op verwijderen om de kosten van uw schijven te voor komen en quotum limieten te bepalen. 

Gebruikers kunnen zich aanmelden om in-VM-meldingen te ontvangen via [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md). Hiermee wordt u op de hoogte gesteld als uw Vm's worden verwijderd en u 30 seconden hebt om taken te volt ooien en afsluit taken uit te voeren vóór de verwijdering. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Implementatie-Vm's in schaal sets implementeren

Als u virtuele-machine bewerkingen wilt implementeren op schaal sets, kunt u de vlag nieuwe *prioriteit* instellen op *Spot*. Alle virtuele machines in uw schaalset worden ingesteld op spot. Gebruik een van de volgende methoden voor het maken van een schaalset met behulp van spot-Vm's:
- [Azure Portal](#portal)
- [Azure-CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager sjablonen](#resource-manager-templates)

## <a name="portal"></a>Portal

Het proces voor het maken van een schaalset die gebruikmaakt van stapsgewijze Vm's, is gelijk aan die in het [artikel aan](quick-create-portal.md)de slag. Wanneer u een schaalset implementeert, kunt u kiezen voor het instellen van de vlag spot en het verwijderings beleid: ![een schaalset maken met behulp van een vaste virtuele machine](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure-CLI

Het proces voor het maken van een schaalset met stapsgewijze Vm's is hetzelfde als die in het [artikel aan](quick-create-cli.md)de slag. Voeg alleen de '---Priority spot ' toe en voeg `--max-price`toe. In dit voor beeld gebruiken we `-1` voor `--max-price` zodat het exemplaar niet wordt verwijderd op basis van de prijs.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Het proces voor het maken van een schaalset met stapsgewijze Vm's is hetzelfde als die in het [artikel aan](quick-create-powershell.md)de slag.
Voeg alleen '-Priority spot ' toe en geef een `-max-price` op de [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Het proces voor het maken van een schaalset die gebruikmaakt van stapsgewijze Vm's, is gelijk aan die in het artikel aan de slag voor [Linux](quick-create-template-linux.md) of [Windows](quick-create-template-windows.md). Voeg de eigenschap Priority toe aan het resource type *micro soft. Compute/virtualMachineScaleSets/virtualMachineProfile* in uw sjabloon en geef als waarde een *Spot* op. Zorg ervoor dat u *2019-03-01* API-versie of hoger gebruikt. 

Als u het verwijderings beleid wilt instellen op verwijderen, voegt u de para meter ' evictionPolicy ' toe en stelt u deze in op *verwijderen*.

In het volgende voor beeld wordt een schaalset voor Linux gemaakt met de naam *myScaleSet* in *West-Centraal VS*, waardoor de vm's in de schaalset bij verwijdering worden *verwijderd* :

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2019-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Spot",
       "evictionPolicy": "delete",
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
## <a name="faq"></a>Veelgestelde vragen

**V:** Nadat het is gemaakt, is dezelfde instantie hetzelfde als standaard exemplaar?

**A:** Ja, behalve als er geen SLA is voor de spot-Vm's, en ze kunnen op elk gewenst moment worden verwijderd.


**V:** Wat te doen wanneer u weggaat, maar nog steeds capaciteit nodig heeft?

**A:** U wordt aangeraden standaard Vm's te gebruiken in plaats van op de plek waar u de capaciteit direct nodig hebt.


**V:** Hoe wordt het quotum voor steun beheerd?

**A:** Spot instanties en standaard instanties hebben afzonderlijke quota groepen. Het steun quotum wordt gedeeld tussen Vm's en scale-set-exemplaren. Zie [Azure-abonnement en servicelimieten, quota en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) voor meer informatie.


**V:** Kan ik een extra quotum voor plaats vragen?

**A:** Ja, u kunt de aanvraag indienen om uw quotum voor de begeleide Vm's te verhogen via het [standaard quotum aanvraag proces](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**V:** Kan ik bestaande schaal sets converteren naar een steun schaalset?

**A:** Nee, het instellen van de `Spot` vlag wordt alleen ondersteund tijdens de aanmaak tijd.


**V:** Als ik `low` heb gebruikt voor schaal sets met lage prioriteit, moet ik dan `Spot` in plaats daarvan beginnen met het gebruik ervan?

**A:** Momenteel werken zowel `low` als `Spot`, maar u moet overstappen op het gebruik van `Spot`.


**V:** Kan ik een schaalset maken met zowel normale virtuele machines als virtuele machines met spot mogelijkheden?

**A:** Nee, een schaalset kan niet meer dan één prioriteits type ondersteunen.


**V:**  Kan ik automatisch schalen met schaal sets gebruiken?

**A:** Ja, u kunt regels voor automatisch schalen instellen op de schaalset met steun schalen. Als uw Vm's worden verwijderd, kunt u met automatisch schalen nieuwe spot-Vm's maken. Vergeet niet dat u deze capaciteit echter niet gegarandeerd. 


**V:**  Werkt automatisch schalen met beide verwijderings beleidsregels (toewijzing en verwijdering ongedaan maken)?

**A:** Het is raadzaam dat u het verwijderings beleid instelt dat u wilt verwijderen wanneer u automatisch schalen gebruikt. Dit komt doordat niet-toegewezen instanties worden geteld voor het aantal capaciteit van de schaalset. Wanneer u automatisch schalen gebruikt, bereikt u waarschijnlijk snel het aantal doel instanties als gevolg van de opgeheven, verwijderde exemplaren. 


**V:** Welke kanalen ondersteunen de ondersteuning van virtuele machines?

**A:** Zie de onderstaande tabel voor meer informatie over de beschik baarheid van spot VM'S.

<a name="channel"></a>

| Azure-kanalen               | Beschik baarheid van Azure spot Vm's       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | Ja                               |
| Betalen per gebruik                | Ja                               |
| Cloud serviceprovider (CSP) | [Neem contact op met uw partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Voordelen                     | Niet beschikbaar                     |
| Gesponsord                    | Niet beschikbaar                     |
| Gratis proefversie                   | Niet beschikbaar                     |


**V:** Waar kan ik vragen plaatsen?

**A:** U kunt uw vraag met `azure-spot` op [Q & A](https://docs.microsoft.com/answers/topics/azure-spot.html)plaatsen en labelen. 

## <a name="next-steps"></a>Volgende stappen
Nu u een schaalset met spot-Vm's hebt gemaakt, kunt u de [sjabloon voor automatisch schalen implementeren met behulp van spot](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Bekijk de [prijs pagina voor de schaalset voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) voor meer informatie.

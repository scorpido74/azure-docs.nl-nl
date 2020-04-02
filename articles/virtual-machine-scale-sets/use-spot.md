---
title: Een schaalset maken die Azure Spot VM's gebruikt
description: Meer informatie over het maken van Azure-schaalsets voor virtuele machines die spotvm's gebruiken om kosten te besparen.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: a7bd22032a554c83a2ea2323ffdb3ae52dfe4faf
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545943"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>Azure Spot VM's voor virtuele machineschaalsets 

Met Azure Spot on scale-sets u profiteren van onze ongebruikte capaciteit tegen aanzienlijke kostenbesparingen. Op elk moment waarop Azure de capaciteit terug nodig heeft, wordt de Azure-infrastructuur uit Spot-exemplaren gezet. Daarom zijn Spot-exemplaren ideaal voor workloads die onderbrekingen kunnen verwerken, zoals batchverwerkingstaken, dev/testomgevingen, grote compute-workloads en meer.

De hoeveelheid beschikbare capaciteit kan variëren op basis van grootte, regio, tijdstip en meer. Bij het implementeren van Spot-exemplaren op schaalsets wijst Azure de instantie alleen toe als er capaciteit beschikbaar is, maar er is geen SLA voor deze exemplaren. Een spotschaalset wordt geïmplementeerd in één foutdomein en biedt geen hoge beschikbaarheidsgaranties.


## <a name="pricing"></a>Prijzen

Prijzen voor spotexemplaren zijn variabel, gebaseerd op regio en SKU. Zie prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)voor meer informatie. 


Met variabele prijzen hebt u de optie om een maximale prijs in te stellen, in Amerikaanse dollars (USD), met maximaal 5 decimalen. De waarde `0.98765`is bijvoorbeeld een maximale prijs van $ 0,98765 USD per uur. Als u de maximale `-1`prijs instelt, wordt de instantie niet uitgezet op basis van de prijs. De prijs voor de instantie is de huidige prijs voor Spot of de prijs voor een standaardexemplaar, die ooit minder is, zolang er capaciteit en quotum beschikbaar is.

## <a name="eviction-policy"></a>Uitzettingsbeleid

Wanneer u steunschaalsets maakt, u het uitzettingsbeleid instellen *op Toewijzen* (standaard) of *Verwijderen*. 

Met het beleid *Detoewijzen* worden uw verwijderde exemplaren verplaatst naar de status met stopbereik waarmee u verwijderde exemplaren opnieuw implementeren. Er is echter geen garantie dat de toewijzing zal slagen. De vm's met dealtoegewezen tellen mee voor het quotum van uw schaalsetinstantie en er worden kosten in rekening gebracht voor uw onderliggende schijven. 

Als u wilt dat uw exemplaren in uw spotschaal worden verwijderd wanneer ze worden verwijderd, u het uitzettingsbeleid instellen om te *verwijderen.* Als u het verwijderingsbeleid wilt verwijderen, u nieuwe VM's maken door de eigenschap Voor het aantal gevallen van schaalinstel te verhogen. De uitgezette VM's worden samen met de onderliggende schijven verwijderd en daarom worden er geen kosten in rekening gebracht voor de opslag. U ook de functie automatisch schalen van schaalsets gebruiken om automatisch te proberen en te compenseren voor uitgezette VM's, maar er is geen garantie dat de toewijzing zal slagen. Het wordt aanbevolen dat u de functie automatisch schalen alleen gebruikt op spotschaalsets wanneer u het uitzettingsbeleid instelt om te verwijderen om de kosten van uw schijven te voorkomen en quotalimieten te raken. 

Gebruikers kunnen zich aanmelden om in-VM-meldingen te ontvangen via [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md). Dit zal u op de hoogte stellen als uw VM's worden uitgezet en u 30 seconden hebt om taken te voltooien en afsluittaken uit te voeren voorafgaand aan de uitzetting. 


## <a name="deploying-spot-vms-in-scale-sets"></a>SpotVM's implementeren in schaalsets

Als u Spot VM's op schaalsets wilt implementeren, u de nieuwe *prioriteitsvlag* instellen op *Spot.* Alle VM's in uw schaalset worden ingesteld op Spot. Als u een schaalset met Spot VM's wilt maken, gebruikt u een van de volgende methoden:
- [Azure Portal](#portal)
- [Azure-CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager-sjablonen](#resource-manager-templates)

## <a name="portal"></a>Portal

Het proces om een schaalset te maken die Spot VM's gebruikt, is hetzelfde als beschreven in het [beginartikel.](quick-create-portal.md) Wanneer u een schaalset implementeert, u ervoor kiezen de ![spotvlag en het uitzettingsbeleid in te stellen: Een schaalset maken met spotvm's](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Het proces om een schaalset met Spot VM's te maken is hetzelfde als beschreven in het [beginartikel.](quick-create-cli.md) Voeg gewoon de '--Priority Spot' toe en voeg . `--max-price` In dit voorbeeld `-1` gebruiken `--max-price` we voor, zodat de instantie niet wordt uitgezet op basis van de prijs.

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

Het proces om een schaalset met Spot VM's te maken is hetzelfde als beschreven in het [beginartikel.](quick-create-powershell.md)
Voeg gewoon '-Priority Spot' `-max-price` toe en lever een aan de [Nieuw-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig)

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

Het proces om een schaalset te maken die Spot VM's gebruikt, is hetzelfde als beschreven in het beginartikel voor [Linux](quick-create-template-linux.md) of [Windows.](quick-create-template-windows.md) 

Voor implementaties van`"apiVersion": "2019-03-01"` spotsjablonen u of hoger worden gebruikt. Voeg `priority`de `evictionPolicy` `billingProfile` eigenschappen en `"virtualMachineProfile":` eigenschappen toe aan de sectie in uw sjabloon: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

Als u de instantie wilt verwijderen nadat `evictionPolicy` deze `Delete`is verwijderd, wijzigt u de parameter in .

## <a name="faq"></a>Veelgestelde vragen

**V:** Is een voorbeeld spot na het maken hetzelfde als standaardinstantie?

**A:** Ja, behalve dat er geen SLA is voor Spot VM's en ze kunnen op elk gewenst moment worden uitgezet.


**V:** Wat te doen als je wordt uitgezet, maar nog steeds capaciteit nodig?

**A:** We raden u aan standaard VM's te gebruiken in plaats van Spot VM's als u meteen capaciteit nodig hebt.


**V:** Hoe wordt quota beheerd voor Spot?

**A:** Erworden spotexemplaren en standaardexemplaren afzonderlijke quotumgroepen. Spotquota worden gedeeld tussen VM's en schaalset-instanties. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) voor meer informatie.


**V:** Kan ik een extra quotum voor Spot aanvragen?

**A:** Ja, u de aanvraag indienen om uw quotum voor spotVM's te verhogen via de [standaardquotaaanvraagprocedure.](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)


**V:** Kan ik bestaande schaalsets converteren naar spotschaalsets?

**A:** Nee, het `Spot` instellen van de vlag wordt alleen ondersteund bij het maken van de tijd.


**V:** Als ik `low` voor set's met lage prioriteit gebruikte, moet ik dan in plaats daarvan beginnen met gebruiken? `Spot`

**A:** Voor nu, `low` `Spot` beide en zal werken, maar `Spot`je moet beginnen met de overgang naar het gebruik .


**V:** Kan ik een schaalset maken met zowel gewone VM's als Spot VM's?

**A:** Nee, een schaalset kan niet meer dan één prioriteitstype ondersteunen.


**V:**  Kan ik automatisch schalen gebruiken met spotschaalsets?

**A:** Ja, u regels voor automatisch schalen instellen op uw spotschaalset. Als uw VM's worden uitgezet, kan automatisch schalen proberen nieuwe Spot VM's te maken. Vergeet niet, bent u niet gegarandeerd deze capaciteit wel. 


**V:**  Werkt autoscale met zowel uitzettingsbeleid (toewijzen en verwijderen)?

**A:** Het wordt aanbevolen dat u uw uitzettingsbeleid instelt om te verwijderen wanneer u autoscale gebruikt. Dit komt omdat deal-toegewezen exemplaren worden meegeteld bij het tellen van uw capaciteit op de schaalset. Wanneer u autoscale gebruikt, zult u waarschijnlijk snel het aantal doelexemplaren raken als gevolg van de toegewezen, uitgezette exemplaren. 


**V:** Welke kanalen ondersteunen Spot VM's?

**A:** Zie de onderstaande tabel voor de beschikbaarheid van Spot VM.

<a name="channel"></a>

| Azure-kanalen               | Beschikbaarheid azure spot VM's       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | Ja                               |
| Betalen naar gebruik                | Ja                               |
| Cloud Service Provider (CSP) | [Neem contact op met uw partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Voordelen                     | Niet beschikbaar                     |
| Gesponsorde                    | Niet beschikbaar                     |
| Gratis proefversie                   | Niet beschikbaar                     |


**V:** Waar kan ik vragen plaatsen?

**A:** U uw vraag `azure-spot` plaatsen en taggen bij [Q&A.](https://docs.microsoft.com/answers/topics/azure-spot.html) 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [prijspagina voor de prijsbepaling van de virtuele machineschaal](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) voor prijsdetails.

---
title: Automatische upgrades van os-afbeeldingen met Azure-schaalsets voor virtuele machines
description: Meer informatie over het automatisch upgraden van de OS-afbeelding op VM-exemplaren in een schaalset
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: manayar
ms.openlocfilehash: 6d550e8e960cb8e212702796467c91d1cd1ebb23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235184"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure virtual machine scale set automatic OS image upgrades Azure virtual machine scale set automatic OS image upgrades Azure virtual machine scale set automatic OS image upgrades Azure virtual

Als u automatische upgrades van de osafbeelding inschakelt op uw schaalset, u het updatebeheer vergemakkelijken door de osschijf veilig en automatisch te upgraden voor alle exemplaren in de schaalset.

Automatische os-upgrade heeft de volgende kenmerken:

- Eenmaal geconfigureerd, wordt de nieuwste OS-afbeelding die door afbeeldingsuitgevers is gepubliceerd, automatisch toegepast op de schaalset zonder tussenkomst van de gebruiker.
- Hiermee worden batches van instanties op een rollende manier bijgewerkt telkens wanneer een nieuwe afbeelding door de uitgever wordt gepubliceerd.
- Integreert met toepassingsstatussondes en [application health extensie.](virtual-machine-scale-sets-health-extension.md)
- Werkt voor alle VM-formaten en voor zowel Windows- als Linux-afbeeldingen.
- U zich op elk gewenst moment afmelden voor automatische upgrades (OS-upgrades kunnen ook handmatig worden gestart).
- De OS-schijf van een VM wordt vervangen door de nieuwe OS Disk die is gemaakt met de nieuwste afbeeldingsversie. Geconfigureerde extensies en aangepaste gegevensscripts worden uitgevoerd, terwijl blijvende gegevensschijven worden bewaard.
- [Extensiesequencing](virtual-machine-scale-sets-extension-sequencing.md) wordt ondersteund.
- Automatische os-afbeeldingsupgrade kan worden ingeschakeld op een schaalset van elke grootte.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hoe werkt automatische os-image-upgrade?

Een upgrade werkt door de OS-schijf van een VM te vervangen door een nieuwe schijf die is gemaakt met de nieuwste afbeeldingsversie. Alle geconfigureerde extensies en aangepaste gegevensscripts worden uitgevoerd op de OS-schijf, terwijl blijvende gegevensschijven worden bewaard. Om de downtime van de toepassing te minimaliseren, vinden upgrades plaats in batches, waarbij niet meer dan 20% van de schaalset-upgrades op elk gewenst moment worden uitgevoerd. U ook een Azure Load Balancer-toepassingsstatussssonde of [application health-extensie](virtual-machine-scale-sets-health-extension.md)integreren. We raden aan om een toepassingsheartbeat op te nemen en het succes van de upgrade voor elke batch in het upgradeproces te valideren.

Het upgradeproces werkt als volgt:
1. Voordat het upgradeproces begint, zorgt de orchestrator ervoor dat niet meer dan 20% van de exemplaren in de gehele schaalset niet in orde zijn (om welke reden dan ook).
2. De upgrade orchestrator identificeert de batch van VM-exemplaren om te upgraden, waarbij een batch een maximum van 20% van het totale aantal exemplaren heeft, afhankelijk van een minimale batchgrootte van één virtuele machine.
3. De osschijf van de geselecteerde batch VM-exemplaren wordt vervangen door een nieuwe osschijf die is gemaakt op basis van de nieuwste afbeelding. Alle opgegeven extensies en configuraties in het schaalsetmodel worden toegepast op de geüpgradede instantie.
4. Voor schaalsets met geconfigureerde toepassingsstatussondes of application health-extensie wacht de upgrade tot 5 minuten voordat de instantie gezond wordt, voordat de volgende batch wordt geüupgradet. Als een instantie de status niet binnen 5 minuten na een upgrade herstelt, wordt standaard de vorige OS-schijf voor de instantie hersteld.
5. De upgrade orchestrator houdt ook het percentage exemplaren bij dat ongezond wordt na een upgrade. De upgrade stopt als meer dan 20% van de geüpgradede exemplaren ongezond wordt tijdens het upgradeproces.
6. Het bovenstaande proces wordt voortgezet totdat alle exemplaren in de schaalset zijn bijgewerkt.

De scale set OS upgrade orchestrator controleert op de algehele schaalsetstatus voordat elke batch wordt geüpgraded. Tijdens het upgraden van een batch kunnen er andere gelijktijdige geplande of ongeplande onderhoudsactiviteiten zijn die van invloed kunnen zijn op de status van uw schaalsetinstanties. In dergelijke gevallen als meer dan 20% van de exemplaren van de schaalset ongezond wordt, stopt de upgrade met de schaalset aan het einde van de huidige batch.

## <a name="supported-os-images"></a>Ondersteunde besturingssysteemafbeeldingen
Alleen bepaalde OS-platformafbeeldingen worden momenteel ondersteund. Aangepaste afbeeldingsondersteuning is beschikbaar [in preview](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images-preview) voor aangepaste afbeeldingen via Shared [Image Gallery](shared-image-galleries.md).

De volgende platform SKU's worden momenteel ondersteund (en meer worden periodiek toegevoegd):

| Uitgever               | OS-aanbieding      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7,5                |
| CoreOS                  | CoreOS        | Stabiel             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | Datacenter 2016    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-met-containers |
| Microsoft Corporation   | WindowsServer | Datacenter 2019 |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-met-Containers |
| Microsoft Corporation   | WindowsServer | Datacenter-Core-1903-met-Containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Vereisten voor het configureren van automatische os-image-upgrade

- De *eigenschap versie* van de afbeelding moet worden ingesteld op de *laatste*.
- Gebruik toepassingsstatussondes of [toepassingsstatusextensie](virtual-machine-scale-sets-health-extension.md) voor niet-servicestructuurschaalsets.
- Compute API-versie 2018-10-01 of hoger gebruiken.
- Controleer of externe resources die in het model van de schaalset zijn opgegeven, beschikbaar en bijgewerkt zijn. Voorbeelden hiervan zijn SAS URI voor bootstrapping payload in VM extensie eigenschappen, payload in opslag account, verwijzing naar geheimen in het model, en nog veel meer.
- Voor schaalsets met windows-virtuele machines, te beginnen met Compute API-versie 2019-03-01, moet de eigenschap *virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates* instellen op *false* in de definitie van het schaalsetmodel. De bovenstaande eigenschap maakt in-VM-upgrades mogelijk waarbij 'Windows Update' patches van het besturingssysteem toepast zonder de OS-schijf te vervangen. Als automatische upgrades van de os-afbeelding zijn ingeschakeld op uw schaalset, is een extra update via 'Windows Update' niet vereist.

### <a name="service-fabric-requirements"></a>Service Fabric-vereisten

Als u Service Fabric gebruikt, moet u ervoor zorgen dat aan de volgende voorwaarden is voldaan:
-   Service Fabric [duurzaamheid niveau](../service-fabric/service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) is zilver of goud, en niet Brons.
-   De extensie Servicefabric op de modeldefinitie van de schaalset moet TypeHandlerVersion 1.1 of hoger hebben.
-   Het duurzaamheidsniveau moet hetzelfde zijn bij het servicefabriccluster en de extensie Service Fabric op de modeldefinitie van de schaalset.

Zorg ervoor dat de duurzaamheidsinstellingen niet overeenkomen met het cluster en de servicestructuurextensie servicestructuur, omdat een mismatch zal leiden tot upgradefouten. Duurzaamheidsniveaus kunnen worden gewijzigd volgens de richtlijnen die op [deze pagina](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)worden beschreven.


## <a name="automatic-os-image-upgrade-for-custom-images-preview"></a>Automatische upgrade van de afbeelding van het besturingssysteem voor aangepaste afbeeldingen (voorbeeld)

> [!IMPORTANT]
> Automatische upgrade van de afbeelding van het besturingssysteem voor aangepaste afbeeldingen bevindt zich momenteel in Openbare preview. Een opt-in procedure is nodig om de public preview-functionaliteit te gebruiken die hieronder wordt beschreven.
> Deze preview-versie wordt geleverd zonder serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Automatische upgrade van de afbeelding van het besturingssysteem is beschikbaar in preview voor aangepaste afbeeldingen die zijn geïmplementeerd via [Shared Image Gallery.](shared-image-galleries.md) Andere aangepaste afbeeldingen worden niet ondersteund voor automatische upgrades van os-afbeeldingen.

Voor het inschakelen van de preview-functionaliteit is een eenmalige opt-in nodig voor de functie *AutomaticOSUpgradeWithGalleryImage* per abonnement, zoals hieronder beschreven.

### <a name="rest-api"></a>REST API
In het volgende voorbeeld wordt beschreven hoe u de preview voor uw abonnement inschakelt:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage/register?api-version=2015-12-01`
```

Functieregistratie kan tot 15 minuten duren. Ga als het gaat om de registratiestatus:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage?api-version=2015-12-01`
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-inproces door de wijziging door te geven aan de Compute-resourceprovider.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) om de preview voor uw abonnement in te schakelen.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

Functieregistratie kan tot 15 minuten duren. Ga als het gaat om de registratiestatus:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-inproces door de wijziging door te geven aan de Compute-resourceprovider.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [het AZ-functieregister](/cli/azure/feature#az-feature-register) om de preview voor uw abonnement in te schakelen.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

Functieregistratie kan tot 15 minuten duren. Ga als het gaat om de registratiestatus:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-inproces door de wijziging door te geven aan de Compute-resourceprovider.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

### <a name="additional-requirements-for-custom-images"></a>Aanvullende vereisten voor aangepaste afbeeldingen
- Het hierboven beschreven opt-inproces hoeft slechts één keer per abonnement te worden voltooid. Na opt-in voltooiing kunnen automatische OS-upgrades worden ingeschakeld voor elke schaal die in dat abonnement is ingesteld.
- De gedeelde afbeeldingsgalerie kan in elk abonnement zitten en hoeft niet afzonderlijk te worden aangemeld. Alleen het abonnement met de schaalset vereist de opt-in van de functie.
- Het configuratieproces voor automatische upgrade van de osafbeelding is hetzelfde voor alle schaalsets zoals beschreven in het [configuratiegedeelte](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) van deze pagina.
- Schaalsets die zijn geconfigureerd voor automatische upgrades van osafbeeldingen, worden geüpgraded naar de nieuwste versie van de afbeelding van de gedeelde afbeelding wanneer een nieuwe versie van de afbeelding wordt gepubliceerd en [gerepliceerd](shared-image-galleries.md#replication) naar de regio van die schaalset. Als de nieuwe afbeelding niet wordt gerepliceerd naar het gebied waar de schaal wordt geïmplementeerd, worden de schaalsetinstanties niet geüpgraded naar de nieuwste versie. Met regionale beeldreplicatie u de implementatie van de nieuwe afbeelding voor uw schaalsets beheren.
- De nieuwe afbeeldingsversie mag niet worden uitgesloten van de nieuwste versie voor die galerijafbeelding. Afbeeldingsversies die zijn uitgesloten van de nieuwste versie van de galerijafbeelding, worden niet uitgerold naar de schaaldie is ingesteld via een automatische upgrade van de osafbeelding.

> [!NOTE]
>Het kan tot 2 uur duren voordat een schaalset wordt ingesteld om de eerste afbeeldingsimplementatie te krijgen nadat de schaalset is geconfigureerd voor automatische os-upgrades. Dit is een eenmalige vertraging per schaalset. Volgende afbeeldingsimplementaties worden zonder deze vertraging toegepast op de schaaldie is ingesteld.


## <a name="configure-automatic-os-image-upgrade"></a>Automatische afbeeldingsupgrade voor besturingssysteem configureren
Als u de automatische upgrade van de afbeelding van het besturingssysteem wilt configureren, moet u ervoor zorgen dat de eigenschap *automaticOSUpgrade.enableAutomaticOSUpgrade* is ingesteld op *true* in de modeldefinitie van schaalsetset.

### <a name="rest-api"></a>REST API
In het volgende voorbeeld wordt beschreven hoe u automatische os-upgrades instelt op een schaalsetmodel:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) om automatische upgrades van osafbeeldingen voor uw schaalset te configureren. In het volgende voorbeeld worden automatische upgrades geconfigureerd voor de schaalset met de naam *myScaleSet* in de resourcegroep *myResourceGroup:*

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [de AZ VMSS-update](/cli/azure/vmss#az-vmss-update) om automatische os-afbeeldingsupgrades voor uw schaalset te configureren. Gebruik Azure CLI 2.0.47 of hoger. In het volgende voorbeeld worden automatische upgrades geconfigureerd voor de schaalset met de naam *myScaleSet* in de resourcegroep *myResourceGroup:*

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Toepassingsstatussondes gebruiken

Tijdens een OS-upgrade worden VM-exemplaren in een schaalset één batch tegelijk geüpgraded. De upgrade mag alleen worden voortgezet als de toepassing van de klant in orde is op de bijgewerkte VM-exemplaren. We raden aan dat de toepassing gezondheidssignalen geeft aan de scale set OS Upgrade-engine. Standaard beschouwt het platform tijdens OS-upgrades de status VM-stroomstatus en extensieom te bepalen of een VM-exemplaar na een upgrade in orde is. Tijdens de OS-upgrade van een VM-exemplaar wordt de OS-schijf op een VM-exemplaar vervangen door een nieuwe schijf op basis van de nieuwste afbeeldingsversie. Nadat de OS-upgrade is voltooid, worden de geconfigureerde extensies uitgevoerd op deze VM's. De toepassing wordt alleen als gezond beschouwd wanneer alle extensies op de instantie met succes zijn ingericht.

Een schaalset kan optioneel worden geconfigureerd met Application Health Probes om het platform nauwkeurige informatie te geven over de doorlopende status van de toepassing. Application Health Probes zijn Custom Load Balancer Probes die worden gebruikt als een gezondheidssignaal. De toepassing die wordt uitgevoerd op een vm-exemplaar met schaalset, kan reageren op externe HTTP- of TCP-aanvragen die aangeven of deze in orde is. Zie voor meer informatie over hoe Custom Load Balancer Probes werken, zie [load balancer probes begrijpen.](../load-balancer/load-balancer-custom-probe-overview.md) Application Health Probes worden niet ondersteund voor servicefabric-schaalsets. Schaalsets voor niet-servicefabricen vereisen health probes voor load balancer-toepassingen of [extensie Toepassingsstatus.](virtual-machine-scale-sets-health-extension.md)

Als de schaalset is geconfigureerd om meerdere plaatsingsgroepen te gebruiken, moeten sondes met behulp van een [standaard load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) worden gebruikt.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Een aangepaste load balancer-sonde configureren als toepassingsstatussprobe op een schaalset
Als aanbevolen methode maakt u een lastenbalansssonde expliciet voor de status van de schaalset. Hetzelfde eindpunt voor een bestaande HTTP-sonde of TCP-sonde kan worden gebruikt, maar een statussonde kan ander gedrag vereisen dan een traditionele last-balancer-sonde. Een traditionele sonde van de load balancer kan bijvoorbeeld ongezond retourneren als de belasting van de instantie te hoog is, maar dat is niet geschikt voor het bepalen van de instantiestatus tijdens een automatische os-upgrade. Configureer de sonde met een hoge indringende snelheid van minder dan twee minuten.

De load-balancer sonde kan worden verwezen in het *netwerkProfiel* van de schaal set en kan worden gekoppeld aan een interne of openbare geconfronteerd load-balancer als volgt:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Wanneer u automatische os-upgrades met servicestructuur gebruikt, wordt de nieuwe OS-afbeelding uitgerold Update-domein per updatedomein om de hoge beschikbaarheid van de services die in Service Fabric worden uitgevoerd, te behouden. Als u automatische os-upgrades in servicestructuur wilt gebruiken, moet uw cluster zijn geconfigureerd om de Silver Sustainability Tier of hoger te gebruiken. Voor meer informatie over de duurzaamheidskenmerken van Service Fabric clusters, zie [deze documentatie.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)

### <a name="keep-credentials-up-to-date"></a>Referenties up-to-date houden
Als uw schaalset referenties gebruikt om toegang te krijgen tot externe bronnen, zoals een VM-extensie die is geconfigureerd om een SAS-token voor opslagaccount te gebruiken, moet u ervoor zorgen dat de referenties worden bijgewerkt. Als referenties, waaronder certificaten en tokens, zijn verlopen, mislukt de upgrade en wordt de eerste batch VM's in een mislukte status achtergelaten.

De aanbevolen stappen om VM's te herstellen en automatische os-upgrade opnieuw in te schakelen als er een fout is in de verificatie van resources zijn:

* Regenereren van het token (of andere referenties) doorgegeven in uw extensie(s).
* Controleer of alle referenties die vanuit de VM worden gebruikt om met externe entiteiten te praten, up-to-date zijn.
* Update extensie(s) in het schaalsetmodel met nieuwe tokens.
* Implementeer de bijgewerkte schaalset, waarmee alle VM-exemplaren, inclusief de mislukte, worden bijgewerkt.

## <a name="using-application-health-extension"></a>Extensie Toepassingsstatus gebruiken
De extensie Toepassingsstatus wordt geïmplementeerd in een instantie met virtuele machineschaalset en rapporteert over de VM-status vanuit de schaalsetinstantie. U de extensie configureren om te sondeopten op een toepassingseindpunt en de status van de toepassing op die instantie bijwerken. Deze instantiestatus wordt gecontroleerd door Azure om te bepalen of een instantie in aanmerking komt voor upgradebewerkingen.

Aangezien de extensie de status rapporteert vanuit een VM, kan de extensie worden gebruikt in situaties waarin externe sondes zoals Application Health Probes (die gebruikmaken van aangepaste Azure Load [Balancer-sondes)](../load-balancer/load-balancer-custom-probe-overview.md)niet kunnen worden gebruikt.

Er zijn meerdere manieren om de extensie Toepassingsstatus in te zetten voor uw schaalsets zoals beschreven in de voorbeelden in [dit artikel.](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Krijg de geschiedenis van automatische upgrades van os-afbeeldingen
U de geschiedenis van de meest recente os-upgrade controleren die op uw schaalset is uitgevoerd met Azure PowerShell, Azure CLI 2.0 of de REST-API's. U geschiedenis voor de laatste vijf OS upgrade pogingen in de afgelopen twee maanden.

### <a name="rest-api"></a>REST API
In het volgende voorbeeld wordt [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) gebruikt om de status te controleren voor de schaalset met de naam *myScaleSet* in de resourcegroep *myResourceGroup:*

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

De GET-oproep retourneert eigenschappen die vergelijkbaar zijn met de volgende voorbeelduitvoer:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de [cmdlet Get-AzVmss](/powershell/module/az.compute/get-azvmss) om de upgradegeschiedenis van het besturingssysteem voor uw schaalset te controleren. In het volgende voorbeeld wordt beschreven hoe u de upgradestatus van het besturingssysteem controleert voor een schaalset met de naam *myScaleSet* in de resourcegroep met de naam *myResourceGroup:*

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [de get-os-upgrade-geschiedenis van AZ vmss](/cli/azure/vmss#az-vmss-get-os-upgrade-history) om de upgradegeschiedenis van het besturingssysteem voor je schaalset te controleren. Gebruik Azure CLI 2.0.47 of hoger. In het volgende voorbeeld wordt beschreven hoe u de upgradestatus van het besturingssysteem controleert voor een schaalset met de naam *myScaleSet* in de resourcegroep met de naam *myResourceGroup:*

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Hoe krijg je de nieuwste versie van een platform OS-afbeelding?

U de beschikbare afbeeldingsversies voor automatische OS-upgrade ondersteund SKU's met behulp van de onderstaande voorbeelden:

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Upgrades van OS-afbeeldingen handmatig activeren
Als de automatische upgrade van de osafbeelding is ingeschakeld op uw schaalset, hoeft u geen afbeeldingsupdates handmatig te activeren op uw schaalset. De os-upgradeorchestrator past automatisch de laatst beschikbare afbeeldingsversie toe op uw schaalset-instanties zonder handmatige tussenkomst.

Voor specifieke gevallen waarin u niet wilt wachten tot de orchestrator de nieuwste afbeelding toepast, u handmatig een upgrade van een os-afbeelding activeren met behulp van de onderstaande voorbeelden.

> [!NOTE]
> Handmatige trigger van os-afbeeldingupgrades biedt geen automatische terugdraaimogelijkheden. Als een instantie de status na een upgradebewerking niet herstelt, kan de vorige osschijf niet worden hersteld.

### <a name="rest-api"></a>REST API
Gebruik de [API-aanroep Start OS Upgrade](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) om een rolling upgrade te starten om alle exemplaren van de virtuele machineschaalset te verplaatsen naar de nieuwste beschikbare versie van het afbeeldingsbesturingssysteem. Instanties die al de nieuwste beschikbare versie van het besturingssysteem uitvoeren, worden niet beïnvloed. In het volgende voorbeeld wordt beschreven hoe u een upgrade van een rollend besturingssysteem starten op een schaalset met de naam *myScaleSet* in de resourcegroep met de naam *myResourceGroup:*

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) om de upgradegeschiedenis van het besturingssysteem voor uw schaalset te controleren. In het volgende voorbeeld wordt beschreven hoe u een upgrade van een rollend besturingssysteem starten op een schaalset met de naam *myScaleSet* in de resourcegroep met de naam *myResourceGroup:*

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik de start van de [upgradevan AZ VMSS Rolling-Upgrade](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) om de upgradegeschiedenis van het besturingssysteem voor uw schaalset te controleren. Gebruik Azure CLI 2.0.47 of hoger. In het volgende voorbeeld wordt beschreven hoe u een upgrade van een rollend besturingssysteem starten op een schaalset met de naam *myScaleSet* in de resourcegroep met de naam *myResourceGroup:*

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Implementeren met een sjabloon

U sjablonen gebruiken om een schaalset te implementeren met automatische OS-upgrades voor ondersteunde afbeeldingen zoals [Ubuntu 16.04-LTS.](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json)

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Volgende stappen
Voor meer voorbeelden over het gebruik van automatische OS-upgrades met schaalsets, raadpleegt u de [GitHub-repo](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).

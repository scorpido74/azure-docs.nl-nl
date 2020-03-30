---
title: Onderhoudsbeheer
description: Meer informatie over hoe u bepalen wanneer onderhoud wordt toegepast op uw Azure VM's met behulp van onderhoudsbeheer.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 58c0964d170f49066802b955f09dab01eaf998a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250177"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Preview: Updates beheren met onderhoudsbeheer en azure cli

Beheer platformupdates, waarvoor geen herstart nodig is, met behulp van onderhoudscontrole. Azure werkt zijn infrastructuur regelmatig bij om de betrouwbaarheid, prestaties, beveiliging of lancering van nieuwe functies te verbeteren. De meeste updates zijn transparant voor gebruikers. Sommige gevoelige workloads, zoals gaming, mediastreaming en financiële transacties, kunnen zelfs enkele seconden van een VM-bevriezing of loskoppeling voor onderhoud niet verdragen. Onderhoudscontrole geeft u de mogelijkheid om te wachten op platformupdates en deze toe te passen binnen een rollend venster van 35 dagen. 

Met onderhoudsbeheer u bepalen wanneer u updates wilt toepassen op uw geïsoleerde VM's en Azure Dedicated Hosts.

Met onderhoudscontrole u:
- Batch-updates in één updatepakket.
- Wacht tot 35 dagen om updates toe te passen. 
- Automatiseer platformupdates voor uw onderhoudsvenster met Azure Functions.
- Onderhoudsconfiguraties werken voor abonnementen en resourcegroepen. 

> [!IMPORTANT]
> Maintenance Control is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
>

## <a name="limitations"></a>Beperkingen

- VM's moeten zich op een [speciale host](./linux/dedicated-hosts.md)bevindt of worden gemaakt met behulp van een [geïsoleerde VM-grootte](./linux/isolation.md).
- Na 35 dagen wordt automatisch een update toegepast.
- De gebruiker moet toegang tot **resourceinzender** hebben.


## <a name="install-the-maintenance-extension"></a>De onderhoudsextensie installeren

Als u ervoor kiest de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) lokaal te installeren, hebt u versie 2.0.76 of hoger nodig.

Installeer `maintenance` de preview-CLI-extensie lokaal of in Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Een onderhoudsconfiguratie maken

Gebruiken `az maintenance configuration create` om een onderhoudsconfiguratie te maken. In dit voorbeeld wordt een onderhoudsconfiguratie met de naam *myConfig* gemaakt die is uitgevoerd op de host. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

Kopieer de configuratie-id van de uitvoer naar later te gebruiken.

Met `--maintenanceScope host` behulp van zorgt ervoor dat het onderhoud config wordt gebruikt voor het regelen van updates voor de host.

Als u een configuratie met dezelfde naam probeert te maken, maar op een andere locatie, krijgt u een foutmelding. Configuratienamen moeten uniek zijn voor uw abonnement.

U vragen stellen voor `az maintenance configuration list`beschikbare onderhoudsconfiguraties met behulp van.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>De configuratie toewijzen

Gebruik `az maintenance assignment create` om de configuratie toe te wijzen aan uw geïsoleerde VM of Azure Dedicated Host.

### <a name="isolated-vm"></a>Geïsoleerde VM

Pas de configuratie toe op een vm met de id van de configuratie. Geef `--resource-type virtualMachines` de naam van de `--resource-name`VM voor en de resourcegroep voor op aan de VM in `--resource-group`en de locatie van de VM voor `--location`. 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Toegewezen host

Als u een configuratie wilt toepassen op `--resource-type hosts` `--resource-parent-name` een speciale host, moet `--resource-parent-type hostGroups`u de naam van de hostgroep opnemen en . 

De `--resource-id` parameter is de ID van de host. U [de az vm-host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) gebruiken om de ID van uw toegewijde host te krijgen.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Configuratie controleren

U controleren of de configuratie correct is toegepast of controleren `az maintenance assignment list`welke configuratie momenteel wordt toegepast met behulp van.

### <a name="isolated-vm"></a>Geïsoleerde VM

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Toegewezen host 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Controleren op updates in behandeling

Gebruiken `az maintenance update list` om te zien of er updates in behandeling zijn. Update --abonnement als de ID voor het abonnement dat de VM bevat.

Als er geen updates zijn, retourneert de opdracht een `Resource not found...StatusCode: 404`foutbericht, dat de tekst bevat: .

Als er updates zijn, wordt er slechts één geretourneerd, zelfs als er meerdere updates in behandeling zijn. De gegevens voor deze update worden geretourneerd in een object:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Geïsoleerde VM

Controleer op updates in behandeling voor een geïsoleerde virtuele machine. In dit voorbeeld wordt de uitvoer opgemaakt als een tabel voor leesbaarheid.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Toegewezen host

Controleren op updates in behandeling voor een speciale host. In dit voorbeeld wordt de uitvoer opgemaakt als een tabel voor leesbaarheid. Vervang de waarden voor de resources door die van u.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Updates toepassen

Hiermee `az maintenance apply update` u updates in behandeling toepassen. Na succes, zal deze opdracht JSON met de details van de update retourneren.

### <a name="isolated-vm"></a>Geïsoleerde VM

Maak een verzoek om updates toe te passen op een geïsoleerde virtuele machine.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Toegewezen host

Pas updates toe op een speciale host.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>De status van het toepassen van updates controleren 

U de voortgang van `az maintenance applyupdate get`de updates controleren met behulp van. 

U kunt `default` als updatenaam de resultaten voor de `myUpdateName` laatste update bekijken of vervangen door `az maintenance applyupdate create`de naam van de update die is geretourneerd toen u deze uitvoert.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime is het moment waarop de update is voltooid, ofwel geïnitieerd door u of door het platform in het geval het zelfonderhoudsvenster niet werd gebruikt. Als er nog nooit een update is toegepast via onderhoudsbeheer, wordt de standaardwaarde weergegeven.

### <a name="isolated-vm"></a>Geïsoleerde VM

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Toegewezen host

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Een onderhoudsconfiguratie verwijderen

Een `az maintenance configuration delete` onderhoudsconfiguratie verwijderen. Als u de configuratie verwijdert, wordt het onderhoudsbeheer verwijderd uit de bijbehorende resources.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Volgende stappen
Zie [Onderhoud en updates](maintenance-and-updates.md)voor meer informatie.

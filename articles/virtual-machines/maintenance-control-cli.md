---
title: Onderhoudsbeheer
description: Meer informatie over hoe u kunt bepalen wanneer onderhoud wordt toegepast op uw Azure-Vm's met behulp van onderhouds beheer.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: cf4c8c57121a06a607770470c5aef69c5edc0c5f
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115633"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Voor beeld: updates beheren met onderhouds beheer en de Azure CLI

Platform updates beheren, die niet opnieuw moeten worden opgestart, met behulp van onderhouds beheer. Azure werkt de infra structuur regel matig bij om de betrouw baarheid, prestaties en beveiliging te verbeteren of nieuwe functies te starten. De meeste updates zijn transparant voor gebruikers. Sommige gevoelige werk belastingen, zoals games, mediastreaming en financiële trans acties, kunnen niet worden toegestaan tot zelfs enkele seconden van een VM-bevriezing of de verbinding met het beheer voor onderhoud. Onderhouds beheer biedt u de mogelijkheid te wachten op platform updates en deze toe te passen binnen een 35-daagse venster. 

Met de onderhouds controle kunt u bepalen wanneer u updates wilt Toep assen op uw geïsoleerde Vm's en voor Azure toegewezen hosts.

Met onderhouds controle kunt u het volgende doen:
- Batch updates in één update pakket.
- Wacht tot 35 dagen om updates toe te passen. 
- Platform updates voor uw onderhouds venster automatiseren met Azure Functions.
- Onderhouds configuraties werken in abonnementen en resource groepen. 

> [!IMPORTANT]
> Onderhouds beheer is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
>

## <a name="limitations"></a>Beperkingen

- Vm's moeten op een [specifieke host](./linux/dedicated-hosts.md)staan of worden gemaakt met behulp van een [geïsoleerde VM-grootte](./linux/isolation.md).
- Na 35 dagen wordt een update automatisch toegepast.
- De gebruiker moet toegang hebben tot de **resource bijdrager** .


## <a name="install-the-maintenance-extension"></a>De onderhouds uitbreiding installeren

Als u ervoor kiest om de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) lokaal te installeren, hebt u versie 2.0.76 of hoger nodig.

Installeer de `maintenance` preview CLI-extensie lokaal of in Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Een onderhouds configuratie maken

Gebruik `az maintenance configuration create` om een onderhouds configuratie te maken. In dit voor beeld wordt een onderhouds configuratie gemaakt met de naam *myConfig* scoped to the host. 

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

Kopieer de configuratie-ID uit de uitvoer, zodat u deze later kunt gebruiken.

Met `--maintenanceScope host` zorgt u ervoor dat de onderhouds configuratie wordt gebruikt om updates voor de host te beheren.

Als u probeert een configuratie met dezelfde naam te maken, maar op een andere locatie, krijgt u een fout melding. Configuratie namen moeten uniek zijn voor uw abonnement.

U kunt een query uitvoeren voor beschik bare onderhouds configuraties met behulp van `az maintenance configuration list`.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>De configuratie toewijzen

Gebruik `az maintenance assignment create` om de configuratie toe te wijzen aan uw geïsoleerde virtuele machine of de toegewezen Azure-host.

### <a name="isolated-vm"></a>Geïsoleerde VM

Pas de configuratie toe op een virtuele machine met behulp van de ID van de configuratie. Geef `--resource-type virtualMachines` op en voer de naam van de virtuele machine voor `--resource-name`en de resource groep voor de virtuele machine in `--resource-group`in en de locatie van de virtuele machine voor `--location`. 

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

Als u een configuratie wilt Toep assen op een specifieke host, moet u `--resource-type hosts`, `--resource-parent-name` toevoegen met de naam van de hostgroep en `--resource-parent-type hostGroups`. 

De para meter `--resource-id` is de ID van de host. U kunt [AZ VM host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) gebruiken om de id van uw specifieke host op te halen.

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

U kunt controleren of de configuratie correct is toegepast, of controleren welke configuratie momenteel wordt toegepast met behulp van `az maintenance assignment list`.

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


## <a name="check-for-pending-updates"></a>Controleren op updates die nog niet zijn uitgevoerd

Gebruik `az maintenance update list` om te zien of er updates in behandeling zijn. Update--abonnement als ID voor het abonnement dat de virtuele machine bevat.

Als er geen updates zijn, wordt er een fout bericht weer gegeven met de volgende tekst: `Resource not found...StatusCode: 404`.

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

Controleren op updates die in behandeling zijn voor een geïsoleerde virtuele machine. In dit voor beeld wordt de uitvoer opgemaakt als een tabel voor de Lees baarheid.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Toegewezen host

Controleren op updates die in behandeling zijn voor een specifieke host. In dit voor beeld wordt de uitvoer opgemaakt als een tabel voor de Lees baarheid. Vervang de waarden voor de resources door uw eigen gegevens.

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

Gebruik `az maintenance apply update` om in behandeling zijnde updates toe te passen. Als de opdracht is voltooid, wordt JSON geretourneerd met de details van de update.

### <a name="isolated-vm"></a>Geïsoleerde VM

Een aanvraag maken om updates op een geïsoleerde virtuele machine toe te passen.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Toegewezen host

Updates Toep assen op een specifieke host.

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

## <a name="check-the-status-of-applying-updates"></a>De status van het Toep assen van updates controleren 

U kunt de voortgang van de updates controleren met behulp van `az maintenance applyupdate get`. 

U kunt `default` gebruiken als update naam om de resultaten voor de laatste update te bekijken of om `myUpdateName` te vervangen door de naam van de update die werd geretourneerd tijdens het uitvoeren van `az maintenance applyupdate create`.

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


## <a name="delete-a-maintenance-configuration"></a>Een onderhouds configuratie verwijderen

Gebruik `az maintenance configuration delete` om een onderhouds configuratie te verwijderen. Als u de configuratie verwijdert, wordt de onderhouds controle van de bijbehorende resources verwijderd.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Volgende stappen
Zie [onderhoud en updates](maintenance-and-updates.md)voor meer informatie.

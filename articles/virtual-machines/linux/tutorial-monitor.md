---
title: 'Zelf studie: virtuele Linux-machines bewaken in azure | Microsoft Docs'
description: In deze zelf studie leert u hoe u de prestatie-en gedetecteerde toepassings onderdelen kunt bewaken die op uw virtuele Linux-machines worden uitgevoerd.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 9c6458eea2b1352e7d13ea6691eac4498182ecd3
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71680076"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Zelfstudie: Een virtuele Linux-machine in azure bewaken

Azure monitoring gebruikt agenten voor het verzamelen van opstart-en prestatie gegevens van Azure-Vm's, het opslaan van deze gegevens in azure Storage en het toegankelijk maken via de portal, de Azure PowerShell module en Azure CLI. Geavanceerde bewaking wordt geleverd met Azure Monitor voor VM's door metrische gegevens over prestaties te verzamelen, toepassings onderdelen te detecteren die op de virtuele machine zijn geïnstalleerd en die prestatie grafieken en afhankelijkheids kaarten bevatten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Diagnostische gegevens over opstarten op een VM inschakelen
> * Diagnostische gegevens over opstarten weergeven
> * Metrische gegevens over de VM-host weergeven
> * Azure Monitor voor VM's inschakelen
> * Metrische gegevens over de prestaties van de virtuele machine weer geven
> * Een waarschuwing maken

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-vm"></a>VM maken

Om diagnostische gegevens en metrische gegevens in actie te zien, hebt u een virtuele machine nodig. Maak eerst een resourcegroep met [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupMonitor* gemaakt op de locatie *VS Oost*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Maak een virtuele machine met [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). In het volgende voorbeeld wordt een VM gemaakt met de naam *myVM* en worden er in *~/.ssh/* SSH-sleutels gemaakt als deze nog niet bestaan:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Diagnostische gegevens over opstarten inschakelen

Als virtuele Linux-machines worden opgestart, wordt uitvoer vastgelegd in de extensie voor diagnostische gegevens over opstarten en opgeslagen in Azure Storage. Deze gegevens kunnen worden gebruikt om opstartproblemen met virtuele machines op te lossen. Diagnostische gegevens over opstarten zijn niet automatisch ingeschakeld wanneer u een Linux-VM met de Azure CLI maakt.

Voordat u diagnostische gegevens over opstarten inschakelt, moet een opslagaccount worden gemaakt voor het opslaan van de opstartlogboeken. Opslagaccounts moeten een globaal unieke naam hebben, tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. Maak een opslagaccount met de opdracht [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). In dit voorbeeld wordt een willekeurige tekenreeks gebruikt voor het maken van een unieke naam voor het opslagaccount.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Als u diagnostische gegevens over opstarten inschakelt, is de URI naar de opslagcontainer voor de blob nodig. Met de volgende opdracht wordt een query uitgevoerd op de opslagaccount om deze URI op te halen. De URI-waarde wordt opgeslagen in een variabele met de naam *bloburi*, die in de volgende stap wordt gebruikt.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Schakel diagnostische gegevens nu in met [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). De waarde `--storage` is de blob-URI die in de vorige stap is opgehaald.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Diagnostische gegevens over opstarten weergeven

Wanneer diagnostische gegevens over opstarten zijn ingeschakeld, wordt telkens wanneer u de virtuele machine stopt en start informatie over het opstartproces naar een logboekbestand geschreven. In dit voorbeeld moet u eerst de toewijzing van de virtuele machine als volgt ongedaan maken met de opdracht [az vm deallocate](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate):

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Start de virtuele machine nu als volgt met de opdracht [az vm start](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-start):

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

U kunt de diagnostische gegevens over opstarten voor *myVM* als volgt ophalen met de opdracht [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log):

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Metrische gegevens over de host weergeven

Een Linux-VM heeft een toegewezen host in Azure die met deze VM samenwerkt. Metrische gegevens worden automatisch verzameld voor de host, en kunnen als volgt worden weergegeven in Azure Portal:

1. Selecteer in Azure Portal de optie **Resourcegroepen**, kies **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.
1. Als u wilt zien hoe de host-VM presteert, selecteert u **Metrische gegevens** in het VM-venster en kiest u vervolgens een van de metrische gegevens voor *[Host]* onder **Beschikbare metrische gegevens**.

    ![Metrische gegevens over de host weergeven](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Geavanceerde bewaking inschakelen

Als u de bewaking van uw Azure-VM met Azure Monitor voor VM's wilt inschakelen:

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.

2. Op de pagina virtuele machine in de **bewaking** sectie, selecteer **inzichten (preview)** .

3. Op de **inzichten (preview)** weergeeft, schakelt **Probeer nu**.

    ![Azure Monitor voor virtuele machines voor een virtuele machine inschakelen](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

4. Op de **Azure Monitor Insights voorbereiden** pagina, hebt u een bestaande Log Analytics-werkruimte in hetzelfde abonnement, selecteert u deze in de vervolgkeuzelijst.  

    De lijst preselecteert de standaard werkruimte en de locatie waar de virtuele machine in het abonnement is geïmplementeerd. 

    >[!NOTE]
    >Zie [een log Analytics-werk ruimte maken](../../azure-monitor/learn/quick-create-workspace.md)om een nieuwe log Analytics-werk ruimte te maken voor het opslaan van de bewakings gegevens van de virtuele machine. Uw Log Analytics-werk ruimte moet deel uitmaken van een van de [ondersteunde regio's](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

Nadat u bewaking hebt ingeschakeld, moet u mogelijk enkele minuten wachten voordat u de metrische gegevens voor prestaties voor de virtuele machine kunt weer geven.

![Azure Monitor inschakelen voor virtuele machines verwerking van de implementatie controleren](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Metrische gegevens over de prestaties van de virtuele machine weer geven

Azure Monitor voor VM's bevat een reeks prestatie diagrammen die gericht zijn op verschillende Key Performance Indica tors (Kpi's) waarmee u kunt bepalen hoe goed een virtuele machine wordt uitgevoerd. Voer de volgende stappen uit om toegang te krijgen vanaf uw virtuele machine.

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.

2. Op de pagina virtuele machine in de **bewaking** sectie, selecteer **inzichten (preview)** .

3. Selecteer het tabblad **prestaties** .

Deze pagina bevat niet alleen diagrammen voor prestatie gebruik, maar ook een tabel met voor elke gedetecteerde logische schijf, de capaciteit, het gebruik en het totale gemiddelde van elke meting.

## <a name="create-alerts"></a>Waarschuwingen maken

U kunt waarschuwingen maken op basis van specifieke maatstaven voor prestaties. Waarschuwingen kunnen worden gebruikt om u een melding te sturen wanneer het gemiddelde CPU-gebruik een bepaalde drempelwaarde overschrijdt of de beschikbare vrije schijfruimte onder een bepaalde hoeveelheid komt. Waarschuwingen worden weergegeven in Azure Portal of kunnen worden verzonden via e-mail. U kunt ook Azure Automation-runbooks of Azure Logic Apps activeren als reactie op het genereren van waarschuwingen.

In het volgende voorbeeld wordt een waarschuwing gemaakt voor het gemiddelde CPU-gebruik.

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.

2. Klik op de VM-blade op **Waarschuwingsregels** en klik vervolgens boven aan de waarschuwingenblade op **Waarschuwing voor metrische gegevens toevoegen**.

3. Geef een **Naam** op voor de waarschuwing, zoals *myAlertRule*

4. Als u een waarschuwing wilt activeren wanneer het CPU-percentage gedurende vijf minuten 1.0 overschrijdt, laat u alle overige standaardwaarden geselecteerd.

5. Schakel desgewenst het selectievakje voor *E-mailadressen van eigenaren, bijdragers en lezers* in om een e-mailmelding te verzenden. Standaard wordt een melding in de portal weergegeven.

6. Klik op de knop **OK**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de prestaties van uw VM geconfigureerd en bekeken. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een resourcegroep en VM maken
> * Diagnostische gegevens over opstarten op de virtuele machine inschakelen
> * Diagnostische gegevens over opstarten weergeven
> * Metrische gegevens over de host weergeven
> * Azure Monitor voor VM's inschakelen
> * Metrische gegevens over de VM weergeven
> * Een waarschuwing maken

Ga naar de volgende zelfstudie voor meer informatie over Azure Security Center.

> [!div class="nextstepaction"]
> [VM-beveiliging beheren](../../security/fundamentals/overview.md)

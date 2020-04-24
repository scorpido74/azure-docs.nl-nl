---
title: 'Zelf studie: virtuele Windows-machines bewaken in azure'
description: In deze zelf studie leert u hoe u de prestatie-en gedetecteerde toepassings onderdelen kunt bewaken die op uw virtuele Windows-machines worden uitgevoerd.
author: mgoedtel
manager: carmonm
ms.service: virtual-machines-windows
ms.subservice: monitoring
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: a021675632a093d41e2565f63f8bb4e844213628
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101617"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Zelf studie: een virtuele Windows-machine bewaken in azure

Azure monitoring gebruikt agenten voor het verzamelen van opstart-en prestatie gegevens van Azure-Vm's, het opslaan van deze gegevens in azure Storage en het toegankelijk maken via de portal, de Azure PowerShell module en Azure CLI. Geavanceerde bewaking wordt geleverd met Azure Monitor voor VM's door metrische gegevens over prestaties te verzamelen, toepassings onderdelen te detecteren die op de virtuele machine zijn geïnstalleerd en die prestatie grafieken en afhankelijkheids kaarten bevatten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Diagnostische gegevens over opstarten op een VM inschakelen
> * Diagnostische gegevens over opstarten bekijken
> * Metrische gegevens over de VM-host weergeven
> * Azure Monitor voor VM's inschakelen
> * Metrische gegevens over de prestaties van de virtuele machine weer geven
> * Een waarschuwing maken

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook starten op een afzonderlijk browser tabblad door naar te [https://shell.azure.com/powershell](https://shell.azure.com/powershell)gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="create-virtual-machine"></a>Virtuele machine maken

Voor het configureren van Azure-bewaking en updatebeheer in deze zelfstudie hebt u een Windows-VM in Azure nodig. Stel eerst een beheerdersnaam en -wachtwoord in voor de virtuele machine met [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Maak nu de VM met [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt op de locatie *VS Oost*. Als deze niet al bestaan, worden de resourcegroep *myResourceGroupMonitorMonitor* en ondersteunende netwerkresources gemaakt:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Het duurt enkele minuten voordat de bronnen en virtuele machine zijn gemaakt.

## <a name="view-boot-diagnostics"></a>Diagnostische gegevens over opstarten bekijken

Als virtuele Windows-machines opstarten, legt de agent voor diagnostische opstartgegevens schermuitvoer vast voor het oplossen van problemen. Deze mogelijkheid is standaard ingeschakeld. De vastgelegde scherm afbeeldingen worden opgeslagen in een Azure-opslag account, dat ook standaard wordt gemaakt.

U krijgt de diagnostische opstartgegevens met de opdracht [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata). In het volgende voorbeeld wordt de diagnostische opstartgegevens gedownload naar de hoofdmap van station * c:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Metrische gegevens over de host weergeven

Een Windows-VM heeft een toegewezen host-VM in Windows die met deze VM samenwerkt. Metrische gegevens worden automatisch verzameld voor de host en kunnen worden weergegeven in Azure Portal.

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.
2. Als u wilt zien hoe de host-VM wordt uitgevoerd, klikt u op **Metrische gegevens** op de blade van de virtuele machine en selecteert u vervolgens een van de metrische gegevens over Host onder **Beschikbare metrische gegevens**.

    ![Metrische gegevens over de host weergeven](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Geavanceerde bewaking inschakelen

Als u de bewaking van uw Azure-VM met Azure Monitor voor VM's wilt inschakelen:

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.

2. Selecteer op de pagina VM, in de sectie **bewaking** , de optie **inzichten (preview)**.

3. Selecteer **nu proberen**op de pagina **inzichten (preview)** .

    ![Azure Monitor voor VM's inschakelen voor een VM](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Als u een bestaande Log Analytics-werk ruimte in hetzelfde abonnement hebt, selecteert u op de pagina **Azure monitor Insights voorbereiden** .  

    De lijst preselecteert de standaard werkruimte en de locatie waar de virtuele machine in het abonnement is geïmplementeerd. 

    >[!NOTE]
    >Zie [een log Analytics-werk ruimte maken](../../azure-monitor/learn/quick-create-workspace.md)om een nieuwe log Analytics-werk ruimte te maken voor het opslaan van de bewakings gegevens van de virtuele machine. Uw Log Analytics-werk ruimte moet deel uitmaken van een van de [ondersteunde regio's](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

Nadat u bewaking hebt ingeschakeld, moet u mogelijk enkele minuten wachten voordat u de metrische gegevens voor prestaties voor de virtuele machine kunt weer geven.

![Verwerking van de implementatie van Azure Monitor voor VM's-bewaking inschakelen](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Metrische gegevens over de prestaties van de virtuele machine weer geven

Azure Monitor voor VM's bevat een reeks prestatie diagrammen die gericht zijn op verschillende Key Performance Indica tors (Kpi's) waarmee u kunt bepalen hoe goed een virtuele machine wordt uitgevoerd. Voer de volgende stappen uit om toegang te krijgen vanaf uw virtuele machine.

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.

2. Selecteer op de pagina VM, in de sectie **bewaking** , de optie **inzichten (preview)**.

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
> * Diagnostische gegevens over opstarten bekijken
> * Metrische gegevens over de host weergeven
> * Azure Monitor voor VM's inschakelen
> * Metrische gegevens over de VM weergeven
> * Een waarschuwing maken

Ga naar de volgende zelfstudie voor meer informatie over Azure Security Center.

> [!div class="nextstepaction"]
> [VM-beveiliging beheren](../../security/fundamentals/overview.md)

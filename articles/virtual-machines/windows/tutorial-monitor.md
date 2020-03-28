---
title: Zelfstudie - Windows-virtuele machines in Azure controleren
description: In deze zelfstudie leert u hoe u de prestaties controleren en welke toepassingsonderdelen op uw virtuele Windows-machines worden uitgevoerd.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 9e807927a10399b02c2c89359c2ffffaf87ba57b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74112485"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Zelfstudie: Een virtuele Windows-machine in Azure controleren

Azure-bewaking maakt gebruik van agents om opstart- en prestatiegegevens te verzamelen van Azure VM's, deze gegevens op te slaan in Azure-opslag en deze toegankelijk te maken via portal, de Azure PowerShell-module en Azure CLI. Geavanceerde bewaking wordt geleverd met Azure Monitor voor VM's door prestatiestatistieken te verzamelen, toepassingsonderdelen te ontdekken die op de VM zijn geïnstalleerd en prestatiegrafieken en afhankelijkheidskaart.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Diagnostische gegevens over opstarten op een VM inschakelen
> * Diagnostische gegevens over opstarten bekijken
> * Metrische gegevens over de VM-host weergeven
> * Azure-monitor voor VM's inschakelen
> * Vm-prestatiestatistieken weergeven
> * Een waarschuwing maken

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U Cloud Shell ook starten op [https://shell.azure.com/powershell](https://shell.azure.com/powershell)een apart browsertabblad door naar. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

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

Als virtuele Windows-machines opstarten, legt de agent voor diagnostische opstartgegevens schermuitvoer vast voor het oplossen van problemen. Deze mogelijkheid is standaard ingeschakeld. De vastgelegde schermafbeeldingen worden opgeslagen in een Azure-opslagaccount, dat ook standaard is gemaakt.

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

Ga als volgt te werk om bewaking van uw Azure VM in te schakelen met Azure Monitor voor VM's:

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.

2. Selecteer op de VM-pagina in de sectie **Monitoring** de optie **Insights (voorbeeld)**.

3. Selecteer **nu proberen**op de pagina **Insights (preview).**

    ![Azure-monitor voor VM's inschakelen voor een vm](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Als u op de **onboarding-pagina Azure Monitor Insights** een bestaande Log Analytics-werkruimte in hetzelfde abonnement hebt, selecteert u deze in de vervolgkeuzelijst.  

    De lijst selecteert vooraf de standaardwerkruimte en locatie waar de VM in het abonnement wordt geïmplementeerd. 

    >[!NOTE]
    >Zie [Een werkruimte logboekanalyse maken](../../azure-monitor/learn/quick-create-workspace.md)als u een nieuwe werkruimte Log Analytics wilt maken om de bewakingsgegevens van de vm op te slaan. Uw loganalytics-werkruimte moet deel uitmaken van een van de [ondersteunde regio's](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

Nadat u de bewaking hebt ingeschakeld, moet u mogelijk enkele minuten wachten voordat u de prestatiestatistieken voor de vm bekijken.

![Azure Monitor voor VM's-controle-implementatieverwerking inschakelen](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Vm-prestatiestatistieken weergeven

Azure Monitor voor VM's bevat een set prestatiegrafieken die verschillende key performance indicators (KPI's) targeten om u te helpen bepalen hoe goed een virtuele machine presteert. Voer de volgende stappen uit om toegang te krijgen vanaf uw VM.

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.

2. Selecteer op de VM-pagina in de sectie **Monitoring** de optie **Insights (voorbeeld)**.

3. Selecteer het tabblad **Prestaties.**

Deze pagina bevat niet alleen prestatieschema's, maar ook een tabel met voor elke logische schijf die wordt ontdekt, de capaciteit, het gebruik en het totale gemiddelde per maat.

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

In deze zelfstudie hebt u de prestaties van uw vm geconfigureerd en bekeken. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een resourcegroep en VM maken
> * Diagnostische gegevens over opstarten op de virtuele machine inschakelen
> * Diagnostische gegevens over opstarten bekijken
> * Metrische gegevens over de host weergeven
> * Azure-monitor voor VM's inschakelen
> * Metrische gegevens over de VM weergeven
> * Een waarschuwing maken

Ga naar de volgende zelfstudie voor meer informatie over Azure Security Center.

> [!div class="nextstepaction"]
> [VM-beveiliging beheren](../../security/fundamentals/overview.md)

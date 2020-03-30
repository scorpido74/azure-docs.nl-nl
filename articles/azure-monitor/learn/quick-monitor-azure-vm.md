---
title: Een Azure-bron bewaken met Azure Monitor
description: Meer informatie over het verzamelen en analyseren van gegevens voor een Azure-bron in Azure Monitor.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 12616fbc80468d35ebb8b0bc5cd12bfd5871e788
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79503655"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Snelstart: een virtuele Azure-machine controleren met Azure Monitor
[Azure Monitor](../overview.md) begint met het verzamelen van gegevens van virtuele Azure-machines op het moment dat ze worden gemaakt. In deze quickstart maakt u een korte wandeling door de gegevens die automatisch worden verzameld voor een Azure-vm en hoe u deze bekijken in de Azure-portal. Vervolgens schakelt u [Azure Monitor voor VM's in](../insights/vminsights-overview.md) voor uw VM, waarmee agents op de VM gegevens van het gastbesturingssysteem kunnen verzamelen en analyseren, inclusief processen en hun afhankelijkheden.

In deze Quick Start wordt ervan uitgegaan dat u een bestaande Azure-VM hebt. Zo niet, dan u een [Windows VM](../../virtual-machines/windows/quick-create-portal.md) maken of een [Linux-vm](../../virtual-machines/linux/quick-create-cli.md) maken na onze VM-quickstarts.

Zie [Azure-virtuele machines met Azure Monitor voor](../insights/monitor-vm-azure.md)meer gedetailleerde beschrijvingen van bewakingsgegevens die zijn verzameld uit Azure-bronnen.


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Voltooi de monitor een Azure-bron snelstart.
Beheer [Een Azure-bron met Azure Monitor](quick-monitor-azure-resource.md) om de overzichtspagina, activiteitenlogboek en metrische gegevens voor een VM in uw abonnement weer te geven. Azure VM's verzamelen dezelfde bewakingsgegevens als elke andere Azure-bron, maar dit is alleen voor de host-VM. De rest van deze quickstart zal zich richten op het monitoren van het gastbesturingssysteem en de workloads.


## <a name="enable-azure-monitor-for-vms"></a>Azure-monitor voor VM's inschakelen
Hoewel statistieken en activiteitslogboeken worden verzameld voor de host-VM, hebt u een agent en een bepaalde configuratie nodig om bewakingsgegevens van het gastbesturingssysteem en de workloads ervan te verzamelen en te analyseren. Azure Monitor voor VM's installeert deze agents en biedt extra krachtige functies voor het bewaken van uw virtuele machines.

1. Ga naar het menu voor uw virtuele machine.
2. Klik op **Naar inzicht gaan** vanuit de tegel op de pagina **Overzicht** of klik op **Inzichten** in het menu **Controle.**

    ![Overzichtspagina](media/quick-monitor-azure-vm/overview-insights.png)

3. Als Azure Monitor voor VM's nog niet is ingeschakeld voor de virtuele machine, klikt u op **Inschakelen**. 

    ![Inzichten inschakelen](media/quick-monitor-azure-vm/enable-insights.png)

4. Als de virtuele machine nog niet is gekoppeld aan een Log Analytics-werkruimte, wordt u gevraagd een bestaande werkruimte te selecteren of een nieuwe werkruimte te maken. Selecteer de standaardinstelling die een werkruimte is met een unieke naam in hetzelfde gebied als uw virtuele machine.

    ![Werkruimte selecteren](media/quick-monitor-azure-vm/select-workspace.png)

5. Onboarding duurt een paar minuten, omdat extensies zijn ingeschakeld en agents op uw virtuele machine zijn geïnstalleerd. Wanneer het is voltooid, krijgt u een bericht dat inzichten zijn geïmplementeerd. Klik **op Azure Monitor** om Azure Monitor voor VM's te openen.

    ![Azure-monitor openen](media/quick-monitor-azure-vm/azure-monitor.png)

6. U ziet uw vm met andere VM's in uw abonnement die aan boord zijn. Selecteer het tabblad **Niet gecontroleerd** als u virtuele machines in uw abonnement wilt bekijken die niet aan boord zijn.

    ![Aan de slag](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Werkruimte configureren
Wanneer u een nieuwe Log Analytics-werkruimte maakt, moet deze worden geconfigureerd om logboeken te verzamelen. Deze configuratie hoeft slechts één keer te worden uitgevoerd omdat de configuratie wordt verzonden naar virtuele machines die er verbinding mee maken.

1. Selecteer **De configuratie van werkruimte** en selecteer vervolgens uw werkruimte.

2. **Geavanceerde instellingen selecteren**

    ![Geavanceerde instellingen van Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Gegevens verzamelen van virtuele Windows-machines


2. Selecteer **Gegevens** en selecteer vervolgens **Windows-gebeurtenislogboeken**.

3. Voeg een gebeurtenislogboek toe door de naam van het logboek in te typen.  Typ **Systeem** en selecteer **+** het plusteken .

4. Schakel in de tabel de ernstcategorieën **Fout** en **Waarschuwing** in.

5. Selecteer **Opslaan** boven aan de pagina om de configuratie op te slaan.

### <a name="data-collection-from-linux-vm"></a>Gegevens verzamelen van virtuele Linux-machines

1. Selecteer **Syslog**.  

2. Voeg een gebeurtenislogboek toe door de naam van het logboek in te typen.  Typ **Syslog** en selecteer **+** het plusteken .  

3. Schakel in de tabel de selectie uit van de ernst **info**, **Melding en** **foutopsporing**. 

4. Selecteer **Opslaan** boven aan de pagina om de configuratie op te slaan.

## <a name="view-data-collected"></a>Verzamelde gegevens weergeven

7. Klik op uw virtuele machine en selecteer het tabblad **Prestaties.** Dit toont een selecte groep prestatietellers die zijn verzameld uit het gastbesturingssysteem van uw vm. Scroll naar beneden om meer tellers weer te geven en beweeg de muis over een grafiek om gemiddeld en percentielen op verschillende tijdstippen weer te geven.

    ![Prestaties](media/quick-monitor-azure-vm/performance.png)

9. Selecteer **Kaart** om de functie kaarten te openen die de processen toont die op de virtuele machine en hun afhankelijkheden worden uitgevoerd. Selecteer **Eigenschappen** om het eigenschappenvenster te openen als deze nog niet is geopend.

    ![Kaart](media/quick-monitor-azure-vm/map.png)

11. Breid de processen voor uw virtuele machine uit. Selecteer een van de processen om de details te bekijken en de afhankelijkheden ervan te markeren.

    ![Processen](media/quick-monitor-azure-vm/processes.png)

12. Selecteer uw virtuele machine opnieuw en selecteer **Vervolgens Logboekgebeurtenissen**. 

    ![Gebeurtenissen registreren](media/quick-monitor-azure-vm/log-events.png)

13. U ziet een lijst met tabellen die zijn opgeslagen in de werkruimte Log Analytics voor de virtuele machine. Deze lijst zal anders zijn, afhankelijk van of u een Virtuele Windows- of Linux-machine gebruikt. Klik op de **tabel Gebeurtenis.** Dit omvat alle gebeurtenissen uit het Windows-gebeurtenislogboek. Log Analytics wordt geopend met een eenvoudige query om gebeurtenislogboekvermeldingen op te halen.

    ![Logboekanalyses](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u Azure Monitor voor VM's voor een virtuele machine ingeschakeld en de werkruimte Log Analytics geconfigureerd om gebeurtenissen voor het gastbesturingssysteem te verzamelen. Voor informatie over het weergeven en analyseren van de gegevens gaat u verder met de zelfstudie.

> [!div class="nextstepaction"]
> [Gegevens weergeven of analyseren in Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)

---
title: Een Azure-resource bewaken met Azure Monitor
description: Meer informatie over het verzamelen en analyseren van gegevens voor een Azure-resource in Azure Monitor.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 12616fbc80468d35ebb8b0bc5cd12bfd5871e788
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79503655"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Snelstartgids: een virtuele machine van Azure bewaken met Azure Monitor
[Azure monitor](../overview.md) begint met het verzamelen van gegevens van virtuele machines van Azure op het moment dat ze worden gemaakt. In deze Quick Start maakt u een beknopt overzicht van de gegevens die automatisch worden verzameld voor een virtuele Azure-machine en hoe u deze kunt weer geven in de Azure Portal. Vervolgens schakelt u [Azure monitor voor VM's](../insights/vminsights-overview.md) in voor uw VM, waarmee agents op de VM gegevens van het gast besturingssysteem kunnen verzamelen en analyseren, inclusief processen en hun afhankelijkheden.

In deze Quick Start wordt ervan uitgegaan dat u een bestaande Azure-VM hebt. Als dat niet het geval is, kunt u een [Windows-VM](../../virtual-machines/windows/quick-create-portal.md) maken of een virtuele [Linux-machine](../../virtual-machines/linux/quick-create-cli.md) maken op basis van onze VM-Quick starts.

Zie [virtuele machines van Azure bewaken met Azure monitor](../insights/monitor-vm-azure.md)voor meer gedetailleerde beschrijvingen van de bewakings gegevens die worden verzameld van Azure-resources.


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Voltooi de bewaken van een Azure resource Quick Start.
Voltooi de [controle van een Azure-resource met Azure monitor](quick-monitor-azure-resource.md) om de overzichts pagina, het activiteiten logboek en de metrische gegevens voor een virtuele machine in uw abonnement weer te geven. Azure-Vm's verzamelen dezelfde bewakings gegevens als elke andere Azure-resource, maar dit is alleen voor de host-VM. De rest van deze Snelstartgids is gericht op het bewaken van het gast besturingssysteem en de werk belastingen.


## <a name="enable-azure-monitor-for-vms"></a>Azure Monitor voor VM's inschakelen
Terwijl metrische gegevens en activiteiten logboeken voor de host-VM worden verzameld, hebt u een agent en een configuratie nodig voor het verzamelen en analyseren van bewakings gegevens van het gast besturingssysteem en de werk belastingen. Azure Monitor voor VM's installeert deze agents en biedt extra krachtige functies voor het bewaken van uw virtuele machines.

1. Ga naar het menu voor de virtuele machine.
2. Klik op **Ga naar inzichten** in de tegel op de pagina **overzicht** of klik op **inzichten** in het menu **bewaking** .

    ![Overzichts pagina](media/quick-monitor-azure-vm/overview-insights.png)

3. Als Azure Monitor voor VM's nog niet is ingeschakeld voor de virtuele machine, klikt u op **inschakelen**. 

    ![Inzichten inschakelen](media/quick-monitor-azure-vm/enable-insights.png)

4. Als de virtuele machine niet al aan een Log Analytics-werk ruimte is gekoppeld, wordt u gevraagd een bestaande werk ruimte te selecteren of een nieuwe te maken. Selecteer de standaard waarde. Dit is een werk ruimte met een unieke naam in dezelfde regio als de virtuele machine.

    ![Werkruimte selecteren](media/quick-monitor-azure-vm/select-workspace.png)

5. Het duurt enkele minuten voordat uitbrei dingen zijn ingeschakeld en de agents op uw virtuele machine zijn geïnstalleerd. Wanneer het is voltooid, krijgt u een bericht dat inzichten zijn geïmplementeerd. Klik op **Azure monitor** om Azure monitor voor VM's te openen.

    ![Azure Monitor openen](media/quick-monitor-azure-vm/azure-monitor.png)

6. U ziet uw VM met andere virtuele machines in uw abonnement die onboarded zijn. Selecteer het tabblad **niet bewaakt** als u virtuele machines in uw abonnement wilt weer geven waarvoor geen onboarding is uitgevoerd.

    ![Aan de slag](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Werkruimte configureren
Wanneer u een nieuwe Log Analytics-werk ruimte maakt, moet deze worden geconfigureerd voor het verzamelen van Logboeken. Deze configuratie hoeft slechts eenmaal te worden uitgevoerd, omdat de configuratie wordt verzonden naar virtuele machines waarmee verbinding wordt gemaakt.

1. Selecteer **werkruimte configuratie** en selecteer vervolgens uw werk ruimte.

2. **Geavanceerde instellingen** selecteren

    ![Geavanceerde instellingen van Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Gegevens verzamelen van virtuele Windows-machines


2. Selecteer **Gegevens** en selecteer vervolgens **Windows-gebeurtenislogboeken**.

3. Voeg een gebeurtenis logboek toe door de naam van het logboek op te geven.  Type **systeem** en selecteer vervolgens het plus teken **+**.

4. Schakel in de tabel de ernstcategorieën **Fout** en **Waarschuwing** in.

5. Selecteer boven aan de pagina **Opslaan** om de configuratie op te slaan.

### <a name="data-collection-from-linux-vm"></a>Gegevens verzamelen van virtuele Linux-machines

1. Selecteer **Syslog**.  

2. Voeg een gebeurtenis logboek toe door de naam van het logboek op te geven.  Typ **syslog** en selecteer vervolgens het plus teken **+**.  

3. In de tabel deselecteert u de ernst **info**, de **kennisgeving** en de **fout opsporing**. 

4. Selecteer boven aan de pagina **Opslaan** om de configuratie op te slaan.

## <a name="view-data-collected"></a>Verzamelde gegevens weergeven

7. Klik op uw virtuele machine en selecteer vervolgens het tabblad **prestaties** . Hier ziet u een groep prestatie meter items die worden verzameld uit het gast besturingssysteem van de virtuele machine. Schuif omlaag om meer tellers weer te geven en beweeg de muis over een grafiek om het gemiddelde en percentiel op verschillende tijdstippen weer te geven.

    ![Prestaties](media/quick-monitor-azure-vm/performance.png)

9. Selecteer **toewijzing** om de functie Maps te openen, waarin de processen worden weer gegeven die worden uitgevoerd op de virtuele machine en de bijbehorende afhankelijkheden. Selecteer **Eigenschappen** om het eigenschappen venster te openen als dit nog niet is geopend.

    ![Kaart](media/quick-monitor-azure-vm/map.png)

11. Vouw de processen voor uw virtuele machine uit. Selecteer een van de processen om de details ervan weer te geven en om de bijbehorende afhankelijkheden te markeren.

    ![Processen](media/quick-monitor-azure-vm/processes.png)

12. Selecteer de virtuele machine opnieuw en selecteer vervolgens **logboek gebeurtenissen**. 

    ![Logboek gebeurtenissen](media/quick-monitor-azure-vm/log-events.png)

13. U ziet een lijst met tabellen die zijn opgeslagen in de werk ruimte Log Analytics voor de virtuele machine. Deze lijst is anders, afhankelijk van of u een virtuele Windows-of Linux-machine gebruikt. Klik op de **gebeurtenis** tabel. Dit omvat alle gebeurtenissen uit het Windows-gebeurtenis logboek. Log Analytics wordt geopend met een eenvoudige query om logboek vermeldingen op te halen.

    ![Logboekanalyses](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u Azure Monitor voor VM's voor een virtuele machine ingeschakeld en de Log Analytics-werk ruimte geconfigureerd voor het verzamelen van gebeurtenissen voor het gast besturingssysteem. Voor informatie over het weergeven en analyseren van de gegevens gaat u verder met de zelfstudie.

> [!div class="nextstepaction"]
> [Gegevens weergeven of analyseren in Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)

---
title: Prestaties in kaart brengen met Azure Monitor voor VM's
description: Prestaties is een functie van de Azure Monitor voor VM's die automatisch toepassingscomponenten op Windows- en Linux-systemen detecteert en de communicatie tussen services in kaart brengt. In dit artikel vindt u informatie over het gebruik ervan in verschillende scenario's.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: a50ba39777e6a9d3d609e584c0c7d872f2a65f35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283715"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Prestaties in kaart brengen met Azure Monitor voor VM's

Azure Monitor voor VM's bevat een set prestatiegrafieken die verschillende key performance indicators (KPI's) targeten om u te helpen bepalen hoe goed een virtuele machine presteert. De grafieken tonen het gebruik van resources over een bepaalde periode, zodat u knelpunten, afwijkingen of overschakelen naar een perspectief waarin elke machine wordt vermeld om het resourcegebruik weer te geven op basis van de geselecteerde statistiek. Hoewel er tal van elementen zijn waarmee rekening moet worden gehouden bij prestaties, controleert Azure Monitor voor VM's de prestatie-indicatoren van belangrijke besturingssystemen met betrekking tot processor, geheugen, netwerkadapter en schijfgebruik. Prestaties vormen een aanvulling op de functie voor statusbewaking en helpen problemen bloot te leggen die duiden op een mogelijke fout van de systeemcomponent, ondersteuning van tuning en optimalisatie om efficiëntie te bereiken of capaciteitsplanning te ondersteunen.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Multi-VM-perspectief vanuit Azure Monitor

Vanuit Azure Monitor biedt de functie Prestaties een weergave van alle bewaakte VM's die zijn geïmplementeerd in werkgroepen in uw abonnementen of in uw omgeving. Voer de volgende stappen uit om toegang te krijgen vanuit Azure Monitor. 

1. Selecteer In de Azure-portal de optie **Monitor**. 
2. Kies **Virtuele machines** in de sectie **Oplossingen.**
3. Selecteer het tabblad **Prestaties.**

![Vm-inzichten Prestatietop N-lijstweergave](media/vminsights-performance/vminsights-performance-aggview-01.png)

Kies op het tabblad **Topn-grafieken** als u meer dan één werkruimte voor Logboekanalyse hebt, de werkruimte die is ingeschakeld met de oplossing in de **werkruimtekiezer** boven aan de pagina. De **groepkiezer** retourneert abonnementen, resourcegroepen, [computergroepen](../platform/computer-groups.md)en virtuele machineschaalsets met betrekking tot de geselecteerde werkruimte die u gebruiken om de resultaten die in de grafieken op deze pagina en op de andere pagina's worden weergegeven, verder te filteren. Uw selectie is alleen van toepassing op de functie Prestaties en wordt niet overgedragen naar Gezondheid of Kaart.  

Standaard worden de grafieken de afgelopen 24 uur weergegeven. Met de **TimeRange-kiezer** u historische tijdsbereiken van maximaal 30 dagen opvragen om te laten zien hoe de prestaties er in het verleden uitzagen.

De vijf capaciteitsbenuttingsgrafieken op de pagina zijn:

* CPU-gebruik % - toont de top vijf machines met het hoogste gemiddelde processorgebruik 
* Beschikbaar geheugen - toont de top vijf machines met de laagste gemiddelde hoeveelheid beschikbaar geheugen 
* Gebruikte logische schijfruimte % - toont de top vijf machines met de hoogste gemiddelde schijfruimte die wordt gebruikt % voor alle schijfvolumes 
* Verzonden bytes - toont de vijf beste machines met het hoogste gemiddelde van verzonden bytes 
* Aantal bytes ontvangen - toont de top vijf machines met het hoogste gemiddelde van ontvangen bytes 

Als u klikt op het pictogram van de pin in de rechterbovenhoek van een van de vijf grafieken, wordt de geselecteerde grafiek vastgemaakt aan het laatste Azure-dashboard dat u voor het laatst hebt bekeken.  Vanuit het dashboard u het formaat en de positie van de grafiek wijzigen. Als u de grafiek in het dashboard selecteert, wordt u omgeleid naar Azure Monitor voor VM's en wordt het juiste bereik en de juiste weergave geladen.  

Als u op het pictogram links van het pinpictogram op een van de vijf grafieken klikt, wordt de **toplijstweergave** geopend.  Hier ziet u het resourcegebruik voor die prestatiestatistiek per afzonderlijke VM in een lijstweergave en welke machine het hoogst trending is.  

![Topn-lijstweergave voor een geselecteerde prestatiestatistiek](media/vminsights-performance/vminsights-performance-topnlist-01.png)

Wanneer u op de virtuele machine klikt, wordt het deelvenster **Eigenschappen** aan de rechterkant uitgebreid om de eigenschappen van het geselecteerde item weer te geven, zoals systeemgegevens die worden gerapporteerd door het besturingssysteem, eigenschappen van de Azure VM, enz. Als u op een van de opties onder de sectie **Snelle koppelingen** klikt, wordt u rechtstreeks vanuit de geselecteerde virtuele machine naar die functie doorgestuurd.  

![Deelvenster Eigenschappen van virtuele machine](./media/vminsights-performance/vminsights-properties-pane-01.png)

Ga naar het tabblad **Geaggregeerde grafieken** om de prestatiestatistieken weer te geven die zijn gefilterd op gemiddelde of percentialenmetingen.  

![Vm-inzichten Prestatietotaalweergave](./media/vminsights-performance/vminsights-performance-aggview-02.png)

De volgende capaciteitsbenuttekaarten zijn beschikbaar:

* CPU-gebruik % - standaardwaarden met het gemiddelde en de top 95e percentiel 
* Beschikbaar geheugen - standaardinstellingen met het gemiddelde, top 5e en 10e percentiel 
* Gebruikte logische schijfruimte % - standaardwaarden met het gemiddelde en het 95e percentiel 
* Verzonden bytes - standaardwaarden met gemiddelde verzonden bytes 
* Ontvangen van bytes - standaardwaarden met gemiddelde ontvangen bytes

U ook de granulariteit van de grafieken binnen het tijdsbereik wijzigen door **Avg,** **Min**, **Max**, **50e,** **90e**en **95e** in de percentielkiezer te selecteren.

Als u het resourcegebruik van afzonderlijke VM's in een lijstweergave wilt weergeven en wilt zien welke machine trending is met het hoogste gebruik, selecteert u het tabblad **Topn-lijst.**  De **top n lijst** pagina toont de top 20 machines gesorteerd op de meest gebruikte door 95e percentiel voor de metrische *CPU-gebruik %*.  U meer machines zien door **Meer laden**te selecteren en de resultaten worden uitgebreid om de top 500 machines weer te geven. 

>[!NOTE]
>De lijst mag niet meer dan 500 machines tegelijk weergeven.  
>

![Voorbeeld van de pagina Top N-lijst](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Als u de resultaten op een specifieke virtuele machine in de lijst wilt filteren, voert u de computernaam in het tekstvak **Zoeken op naam** in.  

Als u het gebruik liever bekijkt vanuit een andere prestatiestatistiek, selecteert u in de vervolgkeuzelijst **Metrische** vervolgkeuzelijst **Beschikbaar geheugen**, gebruikte gebruikte gebruikte **logische schijfruimte %**, **Netwerkontvangen Byte/s**of **Netwerkverzonden Byte/s** en de lijstupdates om het gebruik van die statistiek weer te geven.  

Als u een virtuele machine selecteert in de lijst, wordt het deelvenster **Eigenschappen** aan de rechterkant van de pagina geopend en vanaf hier u **Prestatiedetails**selecteren.  De pagina **Virtual Machine Detail** wordt geopend en is vergelijkbaar met die VM, vergelijkbaar met de ervaring bij het rechtstreeks openen van VM Insights Performance vanuit de Azure VM.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Prestaties rechtstreeks weergeven vanuit een Azure VM

Voer de volgende stappen uit om rechtstreeks vanaf een virtuele machine toegang te krijgen.

1. Selecteer **virtuele machines**in de Azure-portal . 
2. Kies in de lijst een VM en kies in de sectie **Monitoring** **Inzicht**.  
3. Selecteer het tabblad **Prestaties.** 

Deze pagina bevat niet alleen prestatieschema's, maar ook een tabel met voor elke logische schijf die wordt ontdekt, de capaciteit, het gebruik en het totale gemiddelde per maat.  

De volgende capaciteitsbenuttekaarten zijn beschikbaar:

* CPU-gebruik % - standaardwaarden met het gemiddelde en de top 95e percentiel 
* Beschikbaar geheugen - standaardinstellingen met het gemiddelde, top 5e en 10e percentiel 
* Gebruikte logische schijfruimte % - standaardwaarden met het gemiddelde en het 95e percentiel 
* Logische schijf IOPS - standaardinstellingen met het gemiddelde en 95e percentiel
* Logische schijf MB/s - standaardwaarden met het gemiddelde en 95e percentiel
* Max Logische schijf gebruikt % - standaardinstellingen met het gemiddelde en 95e percentiel
* Verzonden bytes - standaardwaarden met gemiddelde verzonden bytes 
* Ontvangen van bytes - standaardwaarden met gemiddelde ontvangen bytes

Als u klikt op het pictogram van de pin in de rechterbovenhoek van een van de grafieken, wordt de geselecteerde grafiek vastgemaakt aan het laatste Azure-dashboard dat u hebt bekeken. Vanuit het dashboard u het formaat en de positie van de grafiek wijzigen. Als u de grafiek in het dashboard selecteert, wordt u omgeleid naar Azure Monitor voor VM's en wordt de prestatiedetailweergave voor de vm geladen.  

![VM-inzichten Prestaties rechtstreeks vanuit vm-weergave](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Prestaties rechtstreeks weergeven vanuit een Azure-schaalset voor virtuele machines

Voer de volgende stappen uit om rechtstreeks toegang te krijgen vanaf een Azure-schaalset voor virtuele machines.

1. Selecteer **virtuele machineschaalsets**in de Azure-portal .
2. Kies in de lijst een virtuele machine en kies in de sectie **Controle** **Inzicht** om het tabblad **Prestaties** weer te geven.

Op deze pagina wordt de prestatieweergave azure monitor geladen, die wordt ingesteld op de geselecteerde schaalset. Hiermee u de topn-exemplaren zien in de schaalset in de set bewaakte statistieken, de geaggregeerde prestaties in de schaalset bekijken en de trends zien voor geselecteerde statistieken in de afzonderlijke exemplaren, n de schaalset. Als u een instantie selecteert in de lijstweergave, u de kaart laden of naar een gedetailleerde prestatieweergave voor dat exemplaar navigeren.

Als u klikt op het pictogram van de pin in de rechterbovenhoek van een van de grafieken, wordt de geselecteerde grafiek vastgemaakt aan het laatste Azure-dashboard dat u hebt bekeken. Vanuit het dashboard u het formaat en de positie van de grafiek wijzigen. Als u de grafiek in het dashboard selecteert, wordt u omgeleid naar Azure Monitor voor VM's en wordt de prestatiedetailweergave voor de vm geladen.  

![VM-inzichten Prestaties rechtstreeks vanuit de weergave van de virtuele machineschaalset](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>U hebt ook toegang tot een gedetailleerde prestatieweergave voor een specifieke instantie vanuit de weergave Instanties voor uw schaalset. Navigeer naar **Instanties** onder de sectie **Instellingen** en kies **Insights**.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik [van werkmappen](vminsights-workbooks.md) die zijn opgenomen in Azure Monitor voor VM's om de prestaties en netwerkstatistieken verder te analyseren.  

- Zie [Azure Monitor for VM-toewijzing weergeven voor](vminsights-maps.md)meer informatie over gedetecteerde toepassingsafhankelijkheden.

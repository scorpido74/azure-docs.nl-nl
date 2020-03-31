---
title: App-afhankelijkheden weergeven met Azure Monitor voor VM's
description: Kaart is een functie van Azure Monitor voor VM's. Het detecteert automatisch toepassingscomponenten op Windows- en Linux-systemen en brengt de communicatie tussen services in kaart. In dit artikel vindt u informatie over het gebruik van de functie Kaart in verschillende scenario's.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: acb96984a49e4ad8535f87a41da11b3b63ae207b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283851"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>De mapfunctie van Azure Monitor voor VM's gebruiken om inzicht te krijgen in toepassingsonderdelen
In Azure Monitor voor VM's u gedetecteerde toepassingsonderdelen weergeven op virtuele Windows- en Linux-virtuele machines (VM's) die worden uitgevoerd in Azure of uw omgeving. U de VM's op twee manieren observeren. Bekijk een kaart rechtstreeks vanuit een VM of bekijk een kaart vanuit Azure Monitor om de componenten in groepen VM's te bekijken. In dit artikel u inzicht krijgen in deze twee weergavemethoden en hoe u de functie Kaart gebruiken. 

Zie [Azure Monitor voor VM's inschakelen voor](vminsights-enable-overview.md)informatie over het configureren van Azure Monitor voor VM's.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Inleiding tot de kaartervaring
Voordat u in de kaartervaring duikt, moet u begrijpen hoe deze informatie presenteert en visualiseert. Of u nu de functie Kaart rechtstreeks uit een VM of azure-monitor selecteert, de mapfunctie biedt een consistente ervaring. Het enige verschil is dat van Azure Monitor, een kaart toont alle leden van een multiple-tier applicatie of cluster.

De functie Kaart visualiseert de VM-afhankelijkheden door lopende processen te ontdekken die: 

- Actieve netwerkverbindingen tussen servers.
- Inkomende en uitgaande verbindingslatentie.
- Poorten voor elke met TCP verbonden architectuur over een bepaald tijdsbereik.  
 
Vouw een VM uit om procesdetails weer te geven en alleen die processen die met de VM communiceren. De clientgroep toont het aantal front-endclients dat verbinding maakt met de VM. De serverpoortgroepen geven het aantal back-endservers weer waartoe de VM verbinding maakt. Vouw een serverpoortgroep uit om de gedetailleerde lijst met servers te bekijken die verbinding maken via die poort.  

Wanneer u de VM selecteert, worden in het deelvenster **Eigenschappen** aan de rechterkant de eigenschappen van de VM weergegeven. Eigenschappen omvatten systeeminformatie die wordt gerapporteerd door het besturingssysteem, eigenschappen van de Azure VM en een ringdiagram dat de gedetecteerde verbindingen samenvat. 

![Het deelvenster Eigenschappen](./media/vminsights-maps/properties-pane-01.png)

Selecteer aan de rechterkant van het deelvenster **Logboekgebeurtenissen** om een lijst met gegevens weer te geven die de VM naar Azure Monitor heeft verzonden. Deze gegevens zijn beschikbaar voor query's.  Selecteer een recordtype om de pagina **Logboeken** te openen, waarbij u de resultaten voor dat recordtype ziet. U ziet ook een vooraf geconfigureerde query die is gefilterd op de VM.  

![Het deelvenster Logboekgebeurtenissen](./media/vminsights-maps/properties-pane-logs-01.png)

Sluit de pagina **Logboeken** en keer terug naar het deelvenster **Eigenschappen.** Selecteer **hier Waarschuwingen** om waarschuwingen voor VM-statuscriteria weer te geven. De functie Kaart integreert met Azure Alerts om waarschuwingen voor de geselecteerde server in het geselecteerde tijdsbereik weer te geven. De server geeft een pictogram weer voor huidige waarschuwingen en in het deelvenster **Machinewaarschuwingen** worden de waarschuwingen weergegeven. 

![Het deelvenster Waarschuwingen](./media/vminsights-maps/properties-pane-alerts-01.png)

Als u de functie Kaart relevante waarschuwingen wilt weergeven, maakt u een waarschuwingsregel die van toepassing is op een specifieke computer:

- Voeg een clausule toe aan groepswaarschuwingen per computer (bijvoorbeeld **op computerinterval 1 minuut).**
- Baseer de waarschuwing op een statistiek.

Zie [Unified alerts in Azure Monitor](../../azure-monitor/platform/alerts-overview.md)voor meer informatie over Azure Alerts en het maken van waarschuwingsregels.

In de rechterbovenhoek beschrijft de optie **Legenda** de symbolen en rollen op de kaart. Gebruik de zoombesturingselementen in de rechterbenedenhoek om uw kaart nader te bekijken en te verplaatsen. U het zoomniveau instellen en de kaart aanpassen aan de grootte van de pagina.  

## <a name="connection-metrics"></a>Verbindingsstatistieken
In het deelvenster **Verbindingen** worden standaardstatistieken weergegeven voor de geselecteerde verbinding van de VM via de TCP-poort. De statistieken omvatten responstijd, aanvragen per minuut, verkeersdoorvoer en koppelingen.  

![Netwerkconnectiviteitsdiagrammen in het deelvenster Verbindingen](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Mislukte verbindingen
De kaart toont mislukte verbindingen voor processen en computers. Een onderbroken rode lijn geeft aan dat een clientsysteem een proces of poort niet bereikt. Voor systemen die de afhankelijkheidsagent gebruiken, rapporteert de agent over mislukte verbindingspogingen. De functie Kaart bewaakt een proces door TCP-sockets te observeren die geen verbinding tot stand brengen. Deze fout kan het gevolg zijn van een firewall, een verkeerde configuratie in de client of server of een niet-beschikbare externe service.

![Een mislukte verbinding op de kaart](./media/vminsights-maps/map-group-failed-connection-01.png)

Als u mislukte verbindingen begrijpt, u problemen oplossen, migratie valideren, beveiliging analyseren en de algehele architectuur van de service begrijpen. Mislukte verbindingen zijn soms onschadelijk, maar ze wijzen vaak op een probleem. Verbindingen kunnen mislukken, bijvoorbeeld wanneer een failoveromgeving plotseling onbereikbaar wordt of wanneer twee toepassingslagen na een cloudmigratie niet met elkaar kunnen communiceren.

### <a name="client-groups"></a>Clientgroepen
Op de kaart vertegenwoordigen clientgroepen clientmachines die verbinding maken met de toegewezen machine. Eén clientgroep vertegenwoordigt de clients voor een individueel proces of machine.

![Een klantengroep op de kaart](./media/vminsights-maps/map-group-client-groups-01.png)

Als u de bewaakte clients en IP-adressen van de systemen in een clientgroep wilt bekijken, selecteert u de groep. De inhoud van de groep wordt hieronder weergegeven.  

![De lijst met IP-adressen van een clientgroep op de kaart](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Als de groep bewaakte en niet-bewaakte clients bevat, u de juiste sectie van het ringdiagram van de groep selecteren om de clients te filteren.

### <a name="server-port-groups"></a>Serverpoortgroepen
Serverpoortgroepen vertegenwoordigen poorten op servers met binnenkomende verbindingen van de toegewezen machine. De groep bevat de serverpoort en een telling van het aantal servers dat verbindingen met die poort heeft. Selecteer de groep om de afzonderlijke servers en verbindingen te bekijken. 

![Een serverpoortgroep op de kaart](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Als de groep bewaakte en niet-bewaakte servers bevat, u het juiste gedeelte van het ringdiagram van de groep selecteren om de servers te filteren.

## <a name="view-a-map-from-a-vm"></a>Een kaart van een virtuele machine weergeven 

Ga als volgt te werk om azure-monitor voor VM's rechtstreeks vanaf een vm te openen:

1. Selecteer **virtuele machines**in de Azure-portal . 
2. Kies in de lijst een vm. Kies in de sectie **Monitoring** de optie **Insights**.  
3. Selecteer het tabblad **Kaart.**

De kaart visualiseert de afhankelijkheden van de VM door lopende procesgroepen en processen te ontdekken die actieve netwerkverbindingen hebben over een bepaald tijdsbereik.  

Standaard toont de kaart de laatste 30 minuten. Als u wilt zien hoe afhankelijkheden er in het verleden uitzag, u een query uitvoeren voor historische tijdsbereiken van maximaal een uur. Als u de query wilt uitvoeren, gebruikt u de **TimeRange-kiezer** in de linkerbovenhoek. U bijvoorbeeld een query uitvoeren tijdens een incident of om de status vóór een wijziging te bekijken.  

![Overzicht van de directe VM-kaart](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Een kaart bekijken vanaf een virtuele machineschaalset

Ga als volgt te werk om azure-monitor voor VM's rechtstreeks vanaf een schaalset voor virtuele machines te openen:

1. Selecteer **virtuele machineschaalsets**in de Azure-portal .
2. Kies in de lijst een vm. Kies vervolgens in de sectie **Monitoring** de optie **Insights**.  
3. Selecteer het tabblad **Kaart.**

De kaart visualiseert alle instanties in de schaaldie is ingesteld als een groepsknooppunt, samen met de afhankelijkheden van de groep. Het uitgevouwen knooppunt bevat de instanties in de schaalset. U door deze exemplaren 10 tegelijk scrollen. 

Als u een kaart voor een specifieke instantie wilt laden, selecteert u eerst die instantie op de kaart. Selecteer vervolgens de knop **ellips** (...) rechts en kies **Serverkaart laden**. In de kaart die wordt weergegeven, ziet u procesgroepen en processen met actieve netwerkverbindingen over een bepaald tijdsbereik. 

Standaard toont de kaart de laatste 30 minuten. Als u wilt zien hoe afhankelijkheden er in het verleden uitzag, u een query uitvoeren voor historische tijdsbereiken van maximaal een uur. Als u de query wilt uitvoeren, gebruikt u de **timerange-kiezer.** U bijvoorbeeld een query uitvoeren tijdens een incident of om de status vóór een wijziging te bekijken.

![Overzicht van de directe VM-kaart](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>U ook een kaart voor een specifieke instantie openen vanuit de weergave **Instanties** voor uw virtuele machineschaalset. Ga **in** de sectie Instellingen naar **Instanties** > **Insights**.

## <a name="view-a-map-from-azure-monitor"></a>Een kaart bekijken vanuit Azure Monitor

In Azure Monitor biedt de functie Kaart een globaal overzicht van uw VM's en hun afhankelijkheden. Ga als volgt te werk om toegang te krijgen tot de functie Kaart in Azure Monitor:

1. Selecteer In de Azure-portal de optie **Monitor**. 
2. Kies **virtuele machines**in de sectie **Insights** .
3. Selecteer het tabblad **Kaart.**

   ![Overzichtskaart van Azure Monitor met meerdere VM's](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Kies een werkruimte met de **werkruimtekiezer** boven aan de pagina. Als u meer dan één Log Analytics-werkruimte hebt, kiest u de werkruimte die is ingeschakeld met de oplossing en die vm's naar deze werkruimte heeft. 

De **groepkiezer** retourneert abonnementen, resourcegroepen, [computergroepen](../../azure-monitor/platform/computer-groups.md)en virtuele machineschaalsets van computers die zijn gerelateerd aan de geselecteerde werkruimte. Uw selectie is alleen van toepassing op de functie Kaart en wordt niet overgedragen naar Prestaties of Gezondheid.

Standaard toont de kaart de laatste 30 minuten. Als u wilt zien hoe afhankelijkheden er in het verleden uitzag, u een query uitvoeren voor historische tijdsbereiken van maximaal een uur. Als u de query wilt uitvoeren, gebruikt u de **timerange-kiezer.** U bijvoorbeeld een query uitvoeren tijdens een incident of om de status vóór een wijziging te bekijken.  

## <a name="next-steps"></a>Volgende stappen

Zie [Prestatiestatus voor Azure Monitor voor VM's weergeven](vminsights-performance.md)om knelpunten te identificeren, de prestaties te controleren en inzicht te krijgen in het algehele gebruik van uw VM's. 

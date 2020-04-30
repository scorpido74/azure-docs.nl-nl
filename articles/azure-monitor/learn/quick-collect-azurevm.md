---
title: Gegevens verzamelen van een virtuele machine van Azure met Azure Monitor | Microsoft Docs
description: Hier vindt u informatie over hoe u de extensie van de Log Analytics-agent-VM en het verzamelen van gegevens van de Azure-VM's met Log Analytics inschakelt.
ms.subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 08/19/2019
ms.custom: mvc, sec-javascript-october2019
ms.openlocfilehash: 2e63b2ca0ee7246009068e6a2875795a14aeddb3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79240336"
---
# <a name="collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>Gegevens verzamelen van een virtuele machine van Azure met Azure Monitor

[Azure monitor](../overview.md) kunt gegevens rechtstreeks van uw virtuele machines in azure verzamelen in een log Analytics-werk ruimte voor gedetailleerde analyse en correlatie. Door de Log Analytics VM-extensie voor [Windows](../../virtual-machines/extensions/oms-windows.md) en [Linux](../../virtual-machines/extensions/oms-linux.md) te installeren, kan Azure monitor gegevens verzamelen van uw Azure-vm's. In deze Quick start ziet u hoe u met een paar eenvoudige stappen gegevens van uw Azure Linux-of Windows-Vm's kunt configureren en verzamelen met behulp van de VM-extensie.  
 
In deze Quick Start wordt ervan uitgegaan dat u een bestaande Azure-VM hebt. Als u geen [Windows-VM](../../virtual-machines/windows/quick-create-portal.md) of [Linux-VM](../../virtual-machines/linux/quick-create-cli.md) kunt maken, raadpleeg dan onze Quick Starts voor VM’s.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Een werkruimte maken

1. Selecteer in het Azure Portal **alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.

    ![Azure Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selecteer **maken**en selecteer vervolgens opties voor de volgende items:

   * Geef een naam op voor de nieuwe **Log Analytics-werkruimte**, bijvoorbeeld *StandaardLAWerkruimte*.  
   * Selecteer een **abonnement** om te koppelen door een selectie in de vervolgkeuzelijst te maken als de geselecteerde standaardwaarde niet juist is.
   * Selecteer bij **Resourcegroep** een bestaande resourcegroep die een of meer virtuele Azure-machines bevat.  
   * Selecteer de **Locatie** waarop uw virtuele machines zijn geïmplementeerd.  Bekijk [in welke regio's Log Analytics beschikbaar is](https://azure.microsoft.com/regions/services/) voor aanvullende informatie.
   * Als u een werkruimte maakt in een nieuw abonnement dat na 2 april 2018 is gemaakt, gebruikt deze automatisch de prijscategorie *Per GB*. De optie voor het selecteren van een prijscategorie is dan niet beschikbaar.  Als u een werkruimte maakt voor een bestaand abonnement dat is gemaakt vóór 2 april, of voor een abonnement dat is gekoppeld aan een bestaande EA-inschrijving, kunt u hier uw gewenste prijscategorie selecteren.  Zie [Prijzen voor Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) voor aanvullende informatie over de afzonderlijke lagen.
  
        ![Log Analytics resource-Blade maken](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Nadat u de vereiste gegevens hebt opgegeven in het deel venster **log Analytics werkruimte** , selecteert u **OK**.  

Terwijl de gegevens worden geverifieerd en de werkruimte wordt gemaakt, kunt u de voortgang bijhouden onder **Meldingen** in het menu. 

## <a name="enable-the-log-analytics-vm-extension"></a>De Log Analytics VM-extensie inschakelen

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Voor Windows- en Linux-VM’s die al zijn geïmplementeerd in Azure, installeert u de Log Analytics-agent met de VM-extensie van Log Analytics. De extensie vereenvoudigt het installatieproces en configureert automatisch de agent voor het verzenden van gegevens naar de Log Analytics-werkruimte die u opgeeft. De agent wordt ook automatisch bijgewerkt wanneer er een nieuwere versie wordt uitgebracht, zodat u over de nieuwste functies en oplossingen beschikt. Controleer voordat u verdergaat of de VM wordt uitgevoerd, anders mislukt het proces.  

>[!NOTE]
>De Log Analytics-agent voor Linux kan niet worden geconfigureerd om aan meer dan één Log Analytics-werkruimte te rapporteren. 

1. Selecteer in de Azure Portal **alle services** in de linkerbovenhoek. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.

2. Selecteer in de lijst met Log Analytics-werkruimten de *StandaardLAWerkruimte* die u eerder hebt gemaakt.

3. Selecteer in het menu aan de linkerkant onder gegevens bronnen voor werk ruimte de optie **virtuele machines**.  

4. Selecteer in de lijst **Virtuele machines** een virtuele machine waarop u de agent wilt installeren. Zoals u ziet, geeft de **Log Analytics-verbindingsstatus** voor de VM aan dat deze **Niet verbonden** is.

5. Selecteer **Verbinding maken** in de details voor de virtuele machine. De agent wordt automatisch geïnstalleerd en geconfigureerd voor uw Log Analytics-werkruimte. Dit proces duurt enkele minuten, gedurende welke tijd de **status** **verbinding**toont.

6. Nadat u de agent hebt geïnstalleerd en er verbinding mee hebt gemaakt, wordt de **Log Analytics-verbindingsstatus** bijgewerkt met **Deze werkruimte**.

## <a name="collect-event-and-performance-data"></a>Gebeurtenis- en prestatiegegevens verzamelen

Azure Monitor kunt gebeurtenissen verzamelen uit de Windows-gebeurtenis Logboeken of Linux syslog en prestatie meter items die u opgeeft voor langere termijn analyse en rapportage, en actie ondernemen wanneer een bepaalde voor waarde wordt gedetecteerd. Volg deze stappen voor het verzamelen van gebeurtenissen uit het Windows-gebeurtenislogboek en de Linux Syslog en diverse algemene prestatiemeters om mee te beginnen.  

### <a name="data-collection-from-windows-vm"></a>Gegevens verzamelen van virtuele Windows-machines

1. Selecteer **Geavanceerde instellingen**.

    ![Geavanceerde instellingen van Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

2. Selecteer **Gegevens** en selecteer vervolgens **Windows-gebeurtenislogboeken**.

3. U kunt een gebeurtenislogboek toevoegen door de naam van het logboek te typen.  Type **systeem** en selecteer vervolgens het plus teken **+**.

4. Schakel in de tabel de ernstcategorieën **Fout** en **Waarschuwing** in.

5. Selecteer boven aan de pagina **Opslaan** om de configuratie op te slaan.

6. Selecteer **Windows-prestatiegegevens** om het verzamelen van prestatiemeteritems op een Windows-computer in te schakelen.

7. Wanneer u voor het eerst Windows-prestatiemeteritems voor een nieuwe Log Analytics-werkruimte configureert, krijgt u de optie om snel verschillende algemene prestatiemeteritems te maken. Ze worden weergegeven met een selectievakje ernaast.

    ![Standaard Windows-prestatiemeteritems geselecteerd](media/quick-collect-azurevm/windows-perfcounters-default.png)

    Selecteer **de geselecteerde prestatie meter items toevoegen**.  Ze worden toegevoegd en vooraf ingesteld met een sample-interval van tien seconden.
  
8. Selecteer boven aan de pagina **Opslaan** om de configuratie op te slaan.

### <a name="data-collection-from-linux-vm"></a>Gegevens verzamelen van virtuele Linux-machines

1. Selecteer **Syslog**.  

2. U kunt een gebeurtenislogboek toevoegen door de naam van het logboek te typen.  Typ **syslog** en selecteer vervolgens het plus teken **+**.  

3. In de tabel deselecteert u de ernst **info**, de **kennisgeving** en de **fout opsporing**. 

4. Selecteer boven aan de pagina **Opslaan** om de configuratie op te slaan.

5. Selecteer **Linux-prestatiegegevens** om het verzamelen van prestatiemeteritems op een Linux-computer in te schakelen. 

6. Wanneer u Linux-prestatiemeteritems voor een nieuwe Log Analytics-werkruimte voor het eerst configureert, krijgt u de optie om snel verschillende algemene prestatiemeteritems te maken. Ze worden weergegeven met een selectievakje ernaast.

    ![Standaard Windows-prestatiemeteritems geselecteerd](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Selecteer de **onderstaande configuratie Toep assen op mijn machines** en selecteer vervolgens **de geselecteerde prestatie meter items toevoegen**.  Ze worden toegevoegd en vooraf ingesteld met een sample-interval van tien seconden.  

7. Selecteer boven aan de pagina **Opslaan** om de configuratie op te slaan.

## <a name="view-data-collected"></a>Verzamelde gegevens weergeven

Nu u gegevensverzameling hebt ingeschakeld, geven we een voorbeeld van een eenvoudige zoekopdracht in logboeken om enkele gegevens afkomstig van de doel-VM’s weer te geven.  

1. Selecteer **Logboeken**in het linkerdeel venster van de geselecteerde werk ruimte.

2. Typ `Perf` op de pagina logboek registratie de query-editor en selecteer **uitvoeren**.

    ![Voorbeeld van zoeken in logboeken in Log Analytics](./media/quick-collect-windows-computer/log-analytics-portal-queryexample.png) 

    De query in de volgende afbeelding heeft bijvoorbeeld 10.000 prestatie records geretourneerd. Uw resultaat zal aanzienlijk kleiner zijn.

    ![Zoekresultaat uit Log Analytics-logboeken](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer deze niet langer nodig is, verwijdert u de Log Analytics-werkruimte. Als u dit wilt doen, selecteert u de Log Analytics werk ruimte die u eerder hebt gemaakt en selecteert u op de pagina resource de optie **verwijderen**.


![Log Analytics-resource verwijderen](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Volgende stappen

Nu u bezig bent met het verzamelen van operationele gegevens en prestatiegegevens van uw virtuele Windows- of Linux-machines, kunt u beginnen met het verkennen, analyseren en het ondernemen van actie op gegevens die u *gratis* verzamelt.  

Voor informatie over het weergeven en analyseren van de gegevens gaat u verder met de zelfstudie.

> [!div class="nextstepaction"]
> [Gegevens weergeven of analyseren in Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)

---
title: Gegevens verzamelen van een hybride Windows-computer met Azure Monitor
description: In deze Quick Start leert u hoe u de Log Analytics-agent kunt implementeren voor Windows-computers die buiten Azure worden uitgevoerd en het verzamelen van gegevens met Azure Monitor-logboeken inschakelen.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 11b61612a261b32e2d15b5dc70005b18aa112ed4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240343"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Gegevens verzamelen van een Windows-computer in een hybride omgeving met Azure Monitor

Met [Azure monitor](../overview.md) kunt u gegevens rechtstreeks van uw fysieke of virtuele Windows-computers in uw omgeving verzamelen in een log Analytics-werk ruimte voor gedetailleerde analyse en correlatie. Door de [log Analytics-agent](../platform/log-analytics-agent.md) te installeren, kan Azure monitor gegevens verzamelen van een Data Center of een andere cloud omgeving. In deze quickstart wordt beschreven hoe u met een paar eenvoudige stappen gegevens van uw Windows-computer configureert en verzamelt. Zie [gegevens verzamelen over Azure virtual machines](../../azure-monitor/learn/quick-collect-azurevm.md)voor meer informatie over Azure Windows-vm's.  

Zie [ondersteunde Windows-besturings systemen](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) en [netwerk firewall configuratie](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)voor meer informatie over de ondersteunde configuratie.
 
Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Een werkruimte maken

1. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.

    ![Azure-portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selecteer **maken**en selecteer vervolgens opties voor de volgende items:

   * Geef een naam op voor de nieuwe **Log Analytics-werkruimte**, bijvoorbeeld *StandaardLAWerkruimte*.  
   * Selecteer een **abonnement** om te koppelen door een selectie in de vervolgkeuzelijst te maken als de geselecteerde standaardwaarde niet juist is.
   * Selecteer bij **Resourcegroep** een bestaande resourcegroep die een of meer virtuele Azure-machines bevat.  
   * Selecteer de **Locatie** waarop uw virtuele machines zijn geïmplementeerd.  Bekijk [in welke regio's Log Analytics beschikbaar is](https://azure.microsoft.com/regions/services/) voor aanvullende informatie.
   * Als u een werkruimte maakt in een nieuw abonnement dat na 2 april 2018 is gemaakt, gebruikt deze automatisch de prijscategorie *Per GB*. De optie voor het selecteren van een prijscategorie is dan niet beschikbaar.  Als u een werkruimte maakt voor een bestaand abonnement dat is gemaakt vóór 2 april, of voor een abonnement dat is gekoppeld aan een bestaande EA-inschrijving, kunt u hier uw gewenste prijscategorie selecteren.  Zie [Prijzen voor Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) voor aanvullende informatie over de afzonderlijke lagen.
  
        ![Log Analytics resource-Blade maken](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Nadat u de vereiste gegevens hebt opgegeven in het deel venster **log Analytics werkruimte** , selecteert u **OK**.  

Terwijl de gegevens worden geverifieerd en de werkruimte wordt gemaakt, kunt u de voortgang bijhouden onder **Meldingen** in het menu. 


## <a name="get-the-workspace-id-and-key"></a>De werk ruimte-ID en-sleutel ophalen

Voordat u de Log Analytics-agent voor Windows installeert (ook wel micro soft Monitoring Agent (MMA) genoemd), hebt u de werk ruimte-ID en-sleutel voor uw Log Analytics-werk ruimte nodig. De installatie wizard heeft deze informatie nodig om de agent correct te configureren en ervoor te zorgen dat deze kan communiceren met Azure Monitor.  

1. Selecteer in de linkerbovenhoek van de Azure Portal **alle services**. Voer in het zoekvak **log Analytics**in. Terwijl u typt, worden de lijst filters op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.

2. Selecteer de werk ruimte die u eerder hebt gemaakt in de lijst met Log Analytics-werk ruimten. (Mogelijk hebt u de naam **standaardlawerkruimte**.)

3. **Geavanceerde instellingen**selecteren:

    ![Geavanceerde instellingen Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. Selecteer **Verbonden bronnen** en selecteer vervolgens **Windows-servers**.

5. Kopieer de waarden rechts van **werk ruimte-id** en **primaire sleutel**. Plak deze in uw favoriete editor.

## <a name="install-the-agent-for-windows"></a>De agent voor Windows installeren

Met de volgende stappen wordt de agent voor Log Analytics in Azure en Azure Government geïnstalleerd en geconfigureerd. U gebruikt het installatie programma van micro soft monitoring agent om de agent op uw computer te installeren.

1. Nadat u de vorige stappen hebt uitgevoerd, selecteert u op de pagina **Windows-servers** de versie van de **Windows-agent downloaden** die u wilt downloaden. Selecteer de juiste versie voor de processor architectuur van uw Windows-besturings systeem.

2. Voer Setup uit om de agent op de computer te installeren.

3. Op de pagina **Welkom** selecteert u **Volgende**.

4. Lees de licentie op de pagina **Licentievoorwaarden** en selecteer **Akkoord**.

5. Op de pagina **Doelmap** wijzigt u desgewenst de standaardinstallatiemap en selecteert u **Volgende**.

6. Op de pagina **installatie opties voor agent** verbindt u de agent met Azure log Analytics en selecteert u vervolgens **volgende**.

7. Voer de volgende stappen uit op de pagina **Azure log Analytics** :

   1. Plak in de **werk ruimte-id** en **werkruimte sleutel (primaire sleutel)** die u eerder hebt gekopieerd. Als de computer moet rapporteren aan een Log Analytics-werk ruimte in Azure Government, selecteert u **Azure US Government** in de lijst met **Azure-Clouds** .  
   2. Als de computer met de Log Analytics-service moet communiceren via een proxyserver, selecteert u **Geavanceerd** en geeft u de URL en het poortnummer van de proxyserver op. Als voor uw proxy server verificatie is vereist, voert u de gebruikers naam en het wacht woord in voor verificatie bij de proxy server en selecteert u vervolgens **volgende**.  

8. Selecteer **volgende** nadat u de configuratie-instellingen hebt toegevoegd:

    ![Setup van micro soft Monitoring Agent](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. Controleer op de pagina **Gereed om te installeren** uw keuzes en selecteer **Installeren**.

10. Selecteer op de pagina **configuratie is voltooid** de optie **volt ooien**.

Wanneer de installatie en installatie is voltooid, wordt micro soft monitoring agent weer gegeven in het configuratie scherm. U kunt uw configuratie controleren en controleren of de agent is verbonden met de Log Analytics-werk ruimte. Wanneer u verbinding hebt gemaakt, wordt het volgende bericht weer gegeven op het tabblad **Azure log Analytics** : **micro soft monitoring agent heeft verbinding gemaakt met de micro soft log Analytics-service.**<br><br> verbindings status van ![MMA](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Gebeurtenis- en prestatiegegevens verzamelen

Azure Monitor kunt gebeurtenissen verzamelen die u opgeeft in het Windows-gebeurtenis logboek en prestatie meter items voor langere termijn analyse en rapportage. Het kan ook actie ondernemen wanneer een bepaalde voor waarde wordt gedetecteerd. Volg deze stappen om eerst het verzamelen van gebeurtenissen uit de Windows-gebeurtenislogboeken en diverse algemene prestatiemeters te configureren.  

1. Selecteer in de linkerbenedenhoek van de Azure Portal **meer services**. Voer in het zoekvak **log Analytics**in. Terwijl u typt, worden de lijst filters op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.

2. **Geavanceerde instellingen**selecteren:

    ![Geavanceerde instellingen Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. Selecteer **Gegevens** en selecteer vervolgens **Windows-gebeurtenislogboeken**.  

4. U voegt een gebeurtenis logboek toe door de naam van het logboek op te geven. Voer het **systeem**in en selecteer vervolgens het plus teken ( **+** ).  

5. Selecteer in de tabel de **fout** en de ernst van de **waarschuwing** .

6. Selecteer boven aan de pagina **Opslaan** .

7. Selecteer **Windows-prestatiemeteritems** om het verzamelen van prestatiemeteritems op een Windows-computer in te schakelen.

8. Wanneer u voor het eerst Windows-prestatie meter items voor een nieuwe Log Analytics-werk ruimte configureert, krijgt u de optie om snel verschillende algemene prestatie meter items te maken. Elke optie wordt weer gegeven, met een selectie vakje ernaast:

    ![Windows-prestatiemeteritems](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Selecteer **de geselecteerde prestatie meter items toevoegen**. De tellers worden toegevoegd en vooraf ingesteld met een steekproef interval van tien seconden voor de verzameling.

9. Selecteer boven aan de pagina **Opslaan** .

## <a name="view-collected-data"></a>Verzamelde gegevens weer geven

Nu u gegevens verzameling hebt ingeschakeld, gaan we een eenvoudige zoek opdracht naar Logboeken uitvoeren om bepaalde gegevens van de doel computer te bekijken.  

1. Selecteer **Logboeken**in het linkerdeel venster van de geselecteerde werk ruimte.

2. Typ `Perf` in de query-editor op de pagina logboeken query en selecteer **uitvoeren**.
 
    ![Zoeken in Log Analytics logboek](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    De query in deze afbeelding heeft bijvoorbeeld 10.000 prestatie records geretourneerd. Uw resultaat zal aanzienlijk kleiner zijn.

    ![Zoekresultaat uit Log Analytics-logboeken](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de agent van de computer verwijderen en de Log Analytics-werk ruimte verwijderen als u deze niet meer nodig hebt.  

Voer de volgende stappen uit om de agent te verwijderen:

1. Open het configuratie scherm.

2. Open **Programma's en onderdelen**.

3. Selecteer **micro soft Monitoring Agent** in **Program ma's en onderdelen**en selecteer vervolgens **verwijderen**.

Als u de Log Analytics werk ruimte die u eerder hebt gemaakt, wilt verwijderen, selecteert u deze en selecteert u op de pagina resource de optie **verwijderen**:

![Log Analytics werk ruimte verwijderen](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Volgende stappen

Nu u operationele en prestatie gegevens van uw Windows-computer verzamelt, kunt u de gegevens die u verzamelt, *gratis*gaan verkennen, analyseren en verwerken.  

Ga verder met de zelf studie voor meer informatie over het weer geven en analyseren van de gegevens:

> [!div class="nextstepaction"]
> [Gegevens weergeven of analyseren in Log Analytics](tutorial-viewdata.md)

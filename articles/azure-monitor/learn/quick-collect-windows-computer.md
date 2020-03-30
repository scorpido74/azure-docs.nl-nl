---
title: Gegevens verzamelen van hybride Windows-computer met Azure Monitor
description: In deze quickstart leert u hoe u de log-analyse-agent voor Windows-computers die buiten Azure wordt uitgevoerd, implementeert en gegevensverzameling inschakelt met Azure Monitor-logboeken.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240343"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Gegevens van een Windows-computer verzamelen in een hybride omgeving met Azure Monitor

[Azure Monitor](../overview.md) kan gegevens rechtstreeks van uw fysieke of virtuele Windows-computers in uw omgeving verzamelen in een Werkruimte Log Analytics voor gedetailleerde analyse en correlatie. Als azure monitor de [loganalytics-agent](../platform/log-analytics-agent.md) installeert, kan deze gegevens verzamelen uit een datacenter of andere cloudomgeving. In deze quickstart wordt beschreven hoe u met een paar eenvoudige stappen gegevens van uw Windows-computer configureert en verzamelt. Zie [Gegevens verzamelen over virtuele Azure-machines voor](../../azure-monitor/learn/quick-collect-azurevm.md)informatie over Azure Windows VM's.  

Zie [Ondersteunde Windows-besturingssystemen](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) en [netwerkfirewallconfiguratie](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)voor meer informatie over de ondersteunde configuratie.
 
Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op . 

## <a name="create-a-workspace"></a>Een werkruimte maken

1. Selecteer **alle services**in de Azure-portal . Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Logboekanalysewerkruimten**.

    ![Azure Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selecteer **Maken**en selecteer vervolgens keuzes voor de volgende items:

   * Geef een naam op voor de nieuwe **Log Analytics-werkruimte**, bijvoorbeeld *StandaardLAWerkruimte*.  
   * Selecteer een **abonnement** om te koppelen door een selectie in de vervolgkeuzelijst te maken als de geselecteerde standaardwaarde niet juist is.
   * Selecteer bij **Resourcegroep** een bestaande resourcegroep die een of meer virtuele Azure-machines bevat.  
   * Selecteer de **Locatie** waarop uw virtuele machines zijn geïmplementeerd.  Bekijk [in welke regio's Log Analytics beschikbaar is](https://azure.microsoft.com/regions/services/) voor aanvullende informatie.
   * Als u een werkruimte maakt in een nieuw abonnement dat na 2 april 2018 is gemaakt, gebruikt deze automatisch de prijscategorie *Per GB*. De optie voor het selecteren van een prijscategorie is dan niet beschikbaar.  Als u een werkruimte maakt voor een bestaand abonnement dat is gemaakt vóór 2 april, of voor een abonnement dat is gekoppeld aan een bestaande EA-inschrijving, kunt u hier uw gewenste prijscategorie selecteren.  Zie [Prijzen voor Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) voor aanvullende informatie over de afzonderlijke lagen.
  
        ![Logboekanalysebronblad maken](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Nadat u de vereiste informatie hebt verstrekt in het **deelvenster Logboekanalyse** werkruimte, selecteert u **OK**.  

Terwijl de gegevens worden geverifieerd en de werkruimte wordt gemaakt, kunt u de voortgang bijhouden onder **Meldingen** in het menu. 


## <a name="get-the-workspace-id-and-key"></a>De werkruimte-id en -sleutel ophalen

Voordat u de Log Analytics-agent voor Windows installeert (ook wel De Microsoft Monitoring Agent (MMA) genoemd), hebt u de werkruimte-id en sleutel voor uw Log Analytics-werkruimte nodig. De wizard Setup heeft deze informatie nodig om de agent correct te configureren en ervoor te zorgen dat deze kan communiceren met Azure Monitor.  

1. Selecteer **Alle services**in de linkerbovenhoek van de Azure-portal . Voer in het zoekvak **Log Analytics**in . Terwijl u typt, worden de lijstfilters op basis van uw invoer weergegeven. Selecteer **Logboekanalysewerkruimten**.

2. Selecteer in uw lijst met Log Analytics-werkruimten de werkruimte die u eerder hebt gemaakt. (U hebt de naam **DefaultLAWorkspace**mogelijk genoemd .)

3. Selecteer **Geavanceerde instellingen:**

    ![Geavanceerde instellingen voor Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. Selecteer **Verbonden bronnen** en selecteer vervolgens **Windows-servers**.

5. Kopieer de waarden naar rechts van **werkruimte-id** en **primaire sleutel**. Plak ze in je favoriete editor.

## <a name="install-the-agent-for-windows"></a>De agent voor Windows installeren

De volgende stappen installeren en configureren van de agent voor Log Analytics in Azure en Azure Government. U gebruikt het Programma Installatie van Microsoft-bewakingsagent om de agent op uw computer te installeren.

1. Als u verdergaat met de vorige reeks stappen, selecteert u op de pagina **Windows Servers** de **Windows Agent-versie downloaden** die u wilt downloaden. Selecteer de juiste versie voor de processorarchitectuur van uw Windows-besturingssysteem.

2. Voer Setup uit om de agent op de computer te installeren.

3. Op de pagina **Welkom** selecteert u **Volgende**.

4. Lees de licentie op de pagina **Licentievoorwaarden** en selecteer **Akkoord**.

5. Op de pagina **Doelmap** wijzigt u desgewenst de standaardinstallatiemap en selecteert u **Volgende**.

6. Verbind de agent op de pagina **Opties voor agentinstellingen** met Azure Log Analytics en selecteer **Volgende**.

7. Voer op de pagina **Azure Log Analytics** de volgende stappen uit:

   1. Plak de **werkruimte-id** en **werkruimtesleutel (primaire sleutel)** die u eerder hebt gekopieerd. Als de computer moet rapporteren aan een Log Analytics-werkruimte in Azure Government, selecteert u **Azure US Government** in de Azure **Cloud-lijst.**  
   2. Als de computer met de Log Analytics-service moet communiceren via een proxyserver, selecteert u **Geavanceerd** en geeft u de URL en het poortnummer van de proxyserver op. Als uw proxyserver verificatie vereist, voert u de gebruikersnaam en het wachtwoord voor verificatie met de proxyserver in en selecteert u **Volgende**.  

8. Selecteer **Volgende** nadat u de configuratie-instellingen hebt toegevoegd:

    ![Microsoft Monitoring Agent Setup](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. Controleer op de pagina **Gereed om te installeren** uw keuzes en selecteer **Installeren**.

10. Selecteer Op de pagina **Configuratie voltooid de** optie **Voltooien**.

Wanneer de installatie en installatie is voltooid, wordt microsoft monitoring agent weergegeven in het Configuratiescherm. U uw configuratie controleren en controleren of de agent is verbonden met de werkruimte Log Analytics. Wanneer de agent is verbonden op het tabblad **Azure Log Analytics,** geeft de agent dit bericht weer: **de Microsoft-bewakingsagent is met succes verbonden met de Microsoft Log Analytics-service.**<br><br> ![MMA-verbindingsstatus](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Gebeurtenis- en prestatiegegevens verzamelen

Azure Monitor kan gebeurtenissen verzamelen die u opgeeft in het Windows-gebeurtenislogboek en prestatiemeteritems voor analyse en rapportage op langere termijn. Het kan ook actie ondernemen wanneer het een bepaalde aandoening detecteert. Volg deze stappen om eerst het verzamelen van gebeurtenissen uit de Windows-gebeurtenislogboeken en diverse algemene prestatiemeters te configureren.  

1. Selecteer **meer services**in de linkerbenedenhoek van de Azure-portal . Voer in het zoekvak **Log Analytics**in . Terwijl u typt, worden de lijstfilters op basis van uw invoer weergegeven. Selecteer **Logboekanalysewerkruimten**.

2. Selecteer **Geavanceerde instellingen:**

    ![Geavanceerde instellingen voor Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. Selecteer **Gegevens** en selecteer vervolgens **Windows-gebeurtenislogboeken**.  

4. U voegt een gebeurtenislogboek toe door de naam van het logboek in te voeren. Voer **Systeem**in en selecteer**+** vervolgens het plusteken ( ).  

5. Selecteer in de tabel de ernst **van de fout** en **waarschuwing.**

6. Selecteer **Opslaan** boven aan de pagina.

7. Selecteer **Windows-prestatiemeteritems** om het verzamelen van prestatiemeteritems op een Windows-computer in te schakelen.

8. Wanneer u windows-prestatiemeteritems voor het eerst configureert voor een nieuwe Log Analytics-werkruimte, krijgt u de optie om snel een aantal algemene tellers te maken. Elke optie wordt weergegeven, met een selectievakje ernaast:

    ![Windows-prestatiemeteritems](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Selecteer **De geselecteerde prestatiemeteritems toevoegen**. De tellers worden toegevoegd en vooraf ingesteld met een monsterinterval van tien seconden.

9. Selecteer **Opslaan** boven aan de pagina.

## <a name="view-collected-data"></a>Verzamelde gegevens weergeven

Nu u gegevensverzameling hebt ingeschakeld, laten we een eenvoudige logboekzoekopdracht uitvoeren om bepaalde gegevens van de doelcomputer te bekijken.  

1. Selecteer **Logboeken**in de geselecteerde werkruimte in het linkerdeelvenster .

2. Typ `Perf` op de querypagina Logboeken de queryeditor en selecteer **Uitvoeren**.
 
    ![Log Analytics log zoeken](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    De query in deze afbeelding heeft bijvoorbeeld 10.000 prestatierecords geretourneerd. Uw resultaat zal aanzienlijk kleiner zijn.

    ![Zoekresultaat uit Log Analytics-logboeken](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Resources opschonen

U de agent van uw computer verwijderen en de werkruimte Log Analytics verwijderen als u deze niet meer nodig hebt.  

Voer de volgende stappen uit om de agent te verwijderen:

1. Open het Configuratiescherm.

2. Open **Programma's en onderdelen**.

3. Selecteer In **Programma's en onderdelen**de optie **Microsoft Monitoring Agent** en selecteer Vervolgens **Verwijderen**.

Als u de werkruimte Log Analytics wilt verwijderen die u eerder hebt gemaakt, selecteert u deze en selecteert u op de resourcepagina **Verwijderen:**

![Werkruimte Logboekanalyse verwijderen](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Volgende stappen

Nu u operationele en prestatiegegevens van uw Windows-computer verzamelt, u eenvoudig *gratis*beginnen met het verkennen, analyseren en reageren op de gegevens die u verzamelt.  

Ga naar de zelfstudie voor meer informatie over het bekijken en analyseren van de gegevens:

> [!div class="nextstepaction"]
> [Gegevens weergeven of analyseren in Log Analytics](tutorial-viewdata.md)

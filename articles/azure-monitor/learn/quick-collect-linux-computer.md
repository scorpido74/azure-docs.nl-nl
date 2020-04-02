---
title: 'Snelstart: gegevens verzamelen van een hybride Linux-computer met Azure Monitor'
description: In deze quickstart leert u hoe u de log-analyse-agent implementeert voor Linux-computers die buiten Azure worden uitgevoerd en gegevensverzameling inschakelen met Azure Monitor-logboeken.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: b83592aa069b5262961f5e41a909821786a178b6
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528554"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Snelstart: gegevens van een Linux-computer verzamelen in een hybride omgeving met Azure Monitor

[Azure Monitor](../overview.md) kan gegevens rechtstreeks van uw fysieke of virtuele Linux-computers in uw omgeving verzamelen in een Werkruimte Log Analytics voor gedetailleerde analyse en correlatie. Als azure monitor de [loganalytics-agent](../platform/log-analytics-agent.md) installeert, kan deze gegevens verzamelen uit een datacenter of andere cloudomgeving. Deze quickstart laat u zien hoe u gegevens van uw Linux-server configureren en verzamelen met een paar eenvoudige stappen. Zie [Gegevens verzamelen over virtuele Azure-machines voor](../../azure-monitor/learn/quick-collect-azurevm.md)informatie over Azure Linux VM's.  

Zie [Ondersteunde Windows-besturingssystemen](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) en [netwerkfirewallconfiguratie](../../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)voor meer informatie over de ondersteunde configuratie.
 
Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op . 

## <a name="create-a-workspace"></a>Een werkruimte maken

1. Selecteer **alle services**in de Azure-portal . Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Logboekanalysewerkruimten**.

    ![Log Analytics-werkruimte zoeken in de Azure-portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selecteer **Maken**en selecteer vervolgens keuzes voor de volgende items:

   * Geef een naam op voor de nieuwe **Log Analytics-werkruimte**, bijvoorbeeld *StandaardLAWerkruimte*.  
   * Selecteer een **abonnement** om te koppelen door een selectie in de vervolgkeuzelijst te maken als de geselecteerde standaardwaarde niet juist is.
   * Selecteer bij **Resourcegroep** een bestaande resourcegroep die een of meer virtuele Azure-machines bevat.  
   * Selecteer de **Locatie** waarop uw virtuele machines zijn geïmplementeerd.  Bekijk [in welke regio's Log Analytics beschikbaar is](https://azure.microsoft.com/regions/services/) voor aanvullende informatie.
   * Als u een werkruimte maakt in een nieuw abonnement dat na 2 april 2018 is gemaakt, gebruikt deze automatisch de prijscategorie *Per GB*. De optie voor het selecteren van een prijscategorie is dan niet beschikbaar.  Als u een werkruimte maakt voor een bestaand abonnement dat is gemaakt vóór 2 april, of voor een abonnement dat is gekoppeld aan een bestaande EA-inschrijving, kunt u hier uw gewenste prijscategorie selecteren.  Zie [Prijzen voor Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) voor aanvullende informatie over de afzonderlijke lagen.
  
        ![Een Logboekanalysewerkruimte maken in de Azure-portal](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Nadat u de vereiste informatie hebt verstrekt in het **deelvenster Logboekanalyse** werkruimte, selecteert u **OK**.  

Terwijl de gegevens worden geverifieerd en de werkruimte wordt gemaakt, kunt u de voortgang bijhouden onder **Meldingen** in het menu. 

## <a name="obtain-workspace-id-and-key"></a>Werkruimte-ID en -sleutel ophalen

Voordat u de Log Analytics-agent voor Linux installeert, hebt u eerst de werkruimte-id en -sleutel voor de Log Analytics-werkruimte nodig. Deze informatie is vereist door het script van de agentwrapper om de agent correct te configureren en ervoor te zorgen dat deze met Azure Monitor kan communiceren.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Selecteer **Alle services**in de linkerbovenhoek van de Azure-portal . Voer in het zoekvak **Log Analytics**in . Terwijl u typt, worden de lijstfilters op basis van uw invoer weergegeven. Selecteer **Logboekanalysewerkruimten**.

2. Selecteer in uw lijst met Log Analytics-werkruimten de werkruimte die u eerder hebt gemaakt. (U hebt de naam **DefaultLAWorkspace**mogelijk genoemd .)

3. Selecteer **Geavanceerde instellingen:**

    ![Menu Geavanceerde instellingen voor Logboekanalyse in de Azure-portal](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Selecteer **Verbonden bronnen** en selecteer vervolgens **Linux-servers**.

5. De waarde rechts van **Werkruimte-id** en **Primaire sleutel**. Kopieer en plak beide in uw favoriete editor.

## <a name="install-the-agent-for-linux"></a>De agent voor Linux installeren

Met de volgende stappen wordt de installatie van de agent voor Log Analytics in Azure en de Azure Government-cloud geconfigureerd.  

>[!NOTE]
>De Log Analytics-agent voor Linux kan niet worden geconfigureerd om aan meer dan één Log Analytics-werkruimte te rapporteren.  

Als uw Linux-computer via een proxyserver moet communiceren met Log Analytics, kan de proxyconfiguratie op de opdrachtregel worden opgegeven door `-p [protocol://][user:password@]proxyhost[:port]` op te nemen.  De eigenschap *proxyhost* accepteert een volledig gekwalificeerde domeinnaam of het IP-adres van de proxyserver. 

Bijvoorbeeld: `https://user01:password@proxy01.contoso.com:30443`

1. Als u de Linux-computer wilt configureren om verbinding te maken met een Log Analytics-werkruimte, voert u de volgende opdracht uit met de eerder gekopieerde werkruimte-id en primaire sleutel. Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    De volgende opdracht `-p` bevat de proxyparameter en voorbeeldsyntaxis wanneer verificatie vereist is door uw proxyserver:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Als u de Linux-computer wilt configureren om verbinding te maken met de werkruimte Log Analytics in de Azure Government-cloud, voert u de volgende opdracht uit met de eerder gekopieerde werkruimte-id en primaire sleutel. Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    De volgende opdracht `-p` bevat de proxyparameter en voorbeeldsyntaxis wanneer verificatie vereist is door uw proxyserver:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Start de agent opnieuw door de volgende opdracht uit te voeren: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Gebeurtenis- en prestatiegegevens verzamelen

Azure Monitor kan gebeurtenissen verzamelen van de Linux Syslog- en prestatiemeteritems die u opgeeft voor analyse en rapportage op langere termijn. Het kan ook actie ondernemen wanneer het een bepaalde aandoening detecteert. Volg deze stappen om eerst het verzamelen van gebeurtenissen uit de Linux Syslog en diverse algemene prestatiemeters te configureren.  

1. Selecteer **meer services**in de linkerbenedenhoek van de Azure-portal . Voer in het zoekvak **Log Analytics**in . Terwijl u typt, worden de lijstfilters op basis van uw invoer weergegeven. Selecteer **Logboekanalysewerkruimten**.

2. Selecteer **Gegevens**en selecteer **Vervolgens Syslog**.  

3. U voegt syslog toe door de naam van het logboek in te typen. Voer **Syslog** in en **+** selecteer vervolgens het plusteken .  

4. Schakel in de tabel de ernstcategorieën **Info**, **Kennisgeving** en **Fouten opsporen** uit. 

5. Selecteer **Opslaan** boven aan de pagina om de configuratie op te slaan.

6. Selecteer **Linux-prestatiegegevens** om het verzamelen van prestatiemeteritems op een Linux-computer in te schakelen. 

7. Wanneer u Linux-prestatiemeteritems voor een nieuwe Log Analytics-werkruimte voor het eerst configureert, krijgt u de optie om snel verschillende algemene prestatiemeteritems te maken. Ze worden weergegeven met een selectievakje ernaast.

    ![StandaardLinux-prestatiemeteritems geselecteerd in Azure Monitor](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Selecteer **Onderstaande configuratie toepassen op mijn machines** en selecteer Vervolgens De geselecteerde **prestatiemeteritems toevoegen**. Ze worden toegevoegd en vooraf ingesteld met een sample-interval van tien seconden.  

8. Selecteer **Opslaan** boven aan de pagina om de configuratie op te slaan.

## <a name="view-data-collected"></a>Verzamelde gegevens weergeven

Nu u gegevensverzameling hebt ingeschakeld, geven we een voorbeeld van een eenvoudige zoekopdracht in logboeken om enkele gegevens afkomstig van de doelcomputer weer te geven.  

1. Selecteer **Logboeken**in de geselecteerde werkruimte in het linkerdeelvenster .

2. Typ `Perf` op de querypagina Logboeken de queryeditor en selecteer **Uitvoeren**.
 
    ![Log Analytics log zoeken](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    De query in de volgende afbeelding heeft bijvoorbeeld 10.000 prestatierecords geretourneerd. Uw resultaat zal aanzienlijk kleiner zijn.

    ![Zoekresultaat uit Log Analytics-logboeken](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer deze niet langer nodig is, kunt u de agent verwijderen van de Linux-computer en de Log Analytics-werkruimte verwijderen.  

Voer de volgende opdracht op de Linux-computer uit om de agent te verwijderen. Met het argument *--purge* worden de agent en de configuratie ervan volledig verwijderd.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Als u de werkruimte wilt verwijderen, selecteert u de werkruimte Log Analytics die u eerder hebt gemaakt en selecteert u op de resourcepagina **Verwijderen**.

![Log Analytics-resource verwijderen](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Volgende stappen

Nu u bezig bent met het verzamelen van operationele gegevens en prestatiegegevens van uw on-premises Windows-computer, kunt u beginnen met het verkennen, analyseren en het ondernemen van actie op gegevens die u *gratis* verzamelt.  

Voor informatie over het weergeven en analyseren van de gegevens gaat u verder met de zelfstudie.

> [!div class="nextstepaction"]
> [Gegevens weergeven of analyseren in Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)

---
title: 'Quickstart: Gegevens verzamelen van een hybride Linux-computer met Azure Monitor'
description: In deze Quick Start leert u hoe u de Log Analytics-agent voor Linux-computers die buiten Azure worden uitgevoerd, kunt implementeren en hoe u gegevens kunt verzamelen met Azure Monitor-Logboeken.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: magoedte
ms.custom: mvc, seo-javascript-september2019
ms.openlocfilehash: cdaca0fd3e314ecb5c0b68438eaaf87fbfb699b0
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933032"
---
# <a name="collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Gegevens verzamelen van een Linux-computer in een hybride omgeving met Azure Monitor

[Azure monitor](../overview.md) kunt gegevens rechtstreeks vanuit uw fysieke of virtuele Linux-computers in uw omgeving verzamelen in een log Analytics-werk ruimte voor gedetailleerde analyse en correlatie. Door de [log Analytics-agent](../platform/log-analytics-agent.md) te installeren, kan Azure monitor gegevens verzamelen van een Data Center of een andere cloud omgeving. In deze Quick start ziet u hoe u met een paar eenvoudige stappen gegevens van uw Linux-server configureert en verzamelt. Zie [gegevens verzamelen over Azure virtual machines](../../azure-monitor/learn/quick-collect-azurevm.md)voor meer informatie over Azure Linux-vm's.  

Zie [ondersteunde Windows-besturings systemen](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) en [netwerk firewall configuratie](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)voor meer informatie over de ondersteunde configuratie.
 
Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Een werkruimte maken

1. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.

    ![Azure Portal](media/quick-collect-azurevm/azure-portal-01.png)<br>  

2. Selecteer **maken**en selecteer vervolgens opties voor de volgende items:

   * Geef een naam op voor de nieuwe **Log Analytics-werkruimte**, bijvoorbeeld *StandaardLAWerkruimte*.  
   * Selecteer een **abonnement** om te koppelen door een selectie in de vervolgkeuzelijst te maken als de geselecteerde standaardwaarde niet juist is.
   * Selecteer bij **Resourcegroep** een bestaande resourcegroep die een of meer virtuele Azure-machines bevat.  
   * Selecteer de **Locatie** waarop uw virtuele machines zijn geïmplementeerd.  Bekijk [in welke regio's Log Analytics beschikbaar is](https://azure.microsoft.com/regions/services/) voor aanvullende informatie.
   * Als u een werkruimte maakt in een nieuw abonnement dat na 2 april 2018 is gemaakt, gebruikt deze automatisch de prijscategorie *Per GB*. De optie voor het selecteren van een prijscategorie is dan niet beschikbaar.  Als u een werkruimte maakt voor een bestaand abonnement dat is gemaakt vóór 2 april, of voor een abonnement dat is gekoppeld aan een bestaande EA-inschrijving, kunt u hier uw gewenste prijscategorie selecteren.  Zie [Prijzen voor Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) voor aanvullende informatie over de afzonderlijke lagen.
  
        ![Log Analytics resource-Blade maken](media/quick-collect-azurevm/create-loganalytics-workspace-02.png) 

3. Nadat u de vereiste gegevens hebt opgegeven in het deel venster **log Analytics werkruimte** , selecteert u **OK**.  

Terwijl de gegevens worden geverifieerd en de werkruimte wordt gemaakt, kunt u de voortgang bijhouden onder **Meldingen** in het menu. 

## <a name="obtain-workspace-id-and-key"></a>Werkruimte-ID en -sleutel ophalen

Voordat u de Log Analytics-agent voor Linux installeert, hebt u eerst de werkruimte-id en -sleutel voor de Log Analytics-werkruimte nodig.  Deze informatie is vereist voor het agent-wrapper-script om de agent correct te configureren en ervoor te zorgen dat deze kan communiceren met Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Selecteer in de linkerbovenhoek van de Azure Portal **alle services**. Voer in het zoekvak **log Analytics**in. Terwijl u typt, worden de lijst filters op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.

2. Selecteer de werk ruimte die u eerder hebt gemaakt in de lijst met Log Analytics-werk ruimten. (Mogelijk hebt u de naam **standaardlawerkruimte**.)

3. **Geavanceerde instellingen**selecteren:

    ![Geavanceerde instellingen van Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-01.png) 
 
4. Selecteer **Verbonden bronnen** en selecteer vervolgens **Linux-servers**.

5. De waarde rechts van **Werkruimte-ID** en **Primaire sleutel**. Kopieer en plak beide in uw favoriete editor.

## <a name="install-the-agent-for-linux"></a>De agent voor Linux installeren

Met de volgende stappen wordt de installatie van de agent voor Log Analytics in Azure en de Azure Government-cloud geconfigureerd.  

>[!NOTE]
>De Log Analytics-agent voor Linux kan niet worden geconfigureerd om aan meer dan één Log Analytics-werkruimte te rapporteren.  

Als uw Linux-computer via een proxyserver moet communiceren met Log Analytics, kan de proxyconfiguratie op de opdrachtregel worden opgegeven door `-p [protocol://][user:password@]proxyhost[:port]` op te nemen.  De eigenschap *proxyhost* accepteert een volledig gekwalificeerde domeinnaam of het IP-adres van de proxyserver. 

Bijvoorbeeld: `https://user01:password@proxy01.contoso.com:30443`

1. Als u de Linux-computer wilt configureren om verbinding te maken met een Log Analytics-werk ruimte, voert u de volgende opdracht uit, waarbij u de werk ruimte-ID en de primaire sleutel eerder hebt gekopieerd Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    De volgende opdracht bevat de proxyparameter `-p` en een voorbeeld van de syntaxis.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Als u de Linux-computer wilt configureren om verbinding te maken met Log Analytics werk ruimte in Azure Government Cloud, voert u de volgende opdracht uit om de werk ruimte-ID en de primaire sleutel die u eerder hebt gekopieerd, Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    De volgende opdracht bevat de proxyparameter `-p` en een voorbeeld van de syntaxis.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Start de agent opnieuw door de volgende opdracht uit te voeren: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Gebeurtenis- en prestatiegegevens verzamelen

Azure Monitor kunt gebeurtenissen verzamelen uit de Linux syslog-en prestatie meter items die u opgeeft voor langere termijn analyse en rapportage. Het kan ook actie ondernemen wanneer een bepaalde voor waarde wordt gedetecteerd. Volg deze stappen om eerst het verzamelen van gebeurtenissen uit de Linux Syslog en diverse algemene prestatiemeters te configureren.  

1. Selecteer in de linkerbenedenhoek van de Azure Portal **meer services**. Voer in het zoekvak **log Analytics**in. Terwijl u typt, worden de lijst filters op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.

2. Selecteer **gegevens**en selecteer vervolgens **syslog**.  

3. U voegt syslog toe door de naam van het logboek op te geven. Voer **syslog** in en selecteer vervolgens het plus **+** teken.  

4. Schakel in de tabel de ernstcategorieën **Info**, **Kennisgeving** en **Fouten opsporen** uit. 

5. Selecteer boven aan de pagina **Opslaan** om de configuratie op te slaan.

6. Selecteer **Linux-prestatiegegevens** om het verzamelen van prestatiemeteritems op een Linux-computer in te schakelen. 

7. Wanneer u Linux-prestatiemeteritems voor een nieuwe Log Analytics-werkruimte voor het eerst configureert, krijgt u de optie om snel verschillende algemene prestatiemeteritems te maken. Ze worden weergegeven met een selectievakje ernaast.

    ![Standaard Windows-prestatiemeteritems geselecteerd](media/quick-collect-azurevm/linux-perfcounters-default.png)

    Selecteer de **onderstaande configuratie Toep assen op mijn machines** en selecteer vervolgens **de geselecteerde prestatie meter items toevoegen**. Ze worden toegevoegd en vooraf ingesteld met een sample-interval van tien seconden.  

8. Selecteer boven aan de pagina **Opslaan** om de configuratie op te slaan.

## <a name="view-data-collected"></a>Verzamelde gegevens weergeven

Nu u gegevensverzameling hebt ingeschakeld, geven we een voorbeeld van een eenvoudige zoekopdracht in logboeken om enkele gegevens afkomstig van de doelcomputer weer te geven.  

1. Selecteer **Logboeken**in het linkerdeel venster van de geselecteerde werk ruimte.

2. Typ `Perf` op de pagina logboek registratie de query-editor en selecteer **uitvoeren**.
 
    ![Zoeken in Log Analytics logboek](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    De query in de volgende afbeelding heeft bijvoorbeeld 10.000 prestatie records geretourneerd. Uw resultaat zal aanzienlijk kleiner zijn.

    ![Zoekresultaat uit Log Analytics-logboeken](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer deze niet langer nodig is, kunt u de agent verwijderen van de Linux-computer en de Log Analytics-werkruimte verwijderen.  

Voer de volgende opdracht op de Linux-computer uit om de agent te verwijderen. Met het argument *--purge* worden de agent en de configuratie ervan volledig verwijderd.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Als u de werk ruimte wilt verwijderen, selecteert u de Log Analytics werk ruimte die u eerder hebt gemaakt en selecteert u op de pagina resource de optie **verwijderen**.

![Log Analytics-resource verwijderen](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Volgende stappen

Nu u bezig bent met het verzamelen van operationele gegevens en prestatiegegevens van uw on-premises Windows-computer, kunt u beginnen met het verkennen, analyseren en het ondernemen van actie op gegevens die u *gratis* verzamelt.  

Voor informatie over het weergeven en analyseren van de gegevens gaat u verder met de zelfstudie.

> [!div class="nextstepaction"]
> [Gegevens weergeven of analyseren in Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)

---
title: De preview-versie van de verbindings monitor maken-Azure Portal
titleSuffix: Azure Network Watcher
description: Meer informatie over het maken van een verbindings monitor (preview) met behulp van de Azure Portal.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: b783a1434ebf6f8e1ad645d69f9f54a4c9c03e7f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362925"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>Maak een verbindings monitor (preview) met behulp van de Azure Portal

Meer informatie over het maken van een verbindings monitor (preview) voor het bewaken van de communicatie tussen uw resources met behulp van de Azure Portal. Het ondersteunt hybride en Azure-Cloud implementaties.

## <a name="before-you-begin"></a>Voordat u begint 

Bij verbindings monitors die u in verbindings monitor (preview) maakt, kunt u zowel on-premises machines als Azure-Vm's toevoegen als bronnen. Deze verbindings monitors kunnen ook de connectiviteit met eind punten bewaken. De eind punten kunnen zich op Azure of een andere URL of een ander IP-adres bevindt.

Verbindings monitor (preview) omvat de volgende entiteiten:


* **Bron van de verbindings monitor** : een regio-specifieke Azure-resource. Alle volgende entiteiten zijn eigenschappen van een verbindingscontrole resource.
* **Eind punt** – een bron of doel die deel uitmaakt van connectiviteits controles. Voor beelden van eind punten zijn: 
    * Azure-VM's
    * Azure-VNETs
    * Azure-subnetten
    * On-premises agents
    * On-premises subnetten
    * On-premises aangepast netwerk dat bestaat uit meerdere subnetten
    * Url's en Ip's 
* **Test configuratie** : een protocol-specifieke configuratie voor een test. Op basis van het protocol dat u hebt gekozen, kunt u de poort, drempel waarden, test frequentie en andere para meters definiëren.
* **Test groep** : de groep die bron-eind punten, bestemmings eindpunten en test configuraties bevat. Een verbindings monitor kan meer dan één test groep bevatten.
* **Testen** : de combi natie van een bron eindpunt, bestemmings eindpunt en test configuratie. Een test is het meest gedetailleerde niveau waarmee bewakings gegevens beschikbaar zijn. De bewakings gegevens omvatten het percentage controles dat is mislukt en de round-trip tijd (RTT).

    ![Diagram van een verbindings monitor, waarbij de relatie tussen test groepen en tests wordt gedefinieerd](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create"></a>Te maken stappen

Volg de onderstaande stappen om een verbindings monitor (preview) te maken met behulp van de Azure Portal:

1. Ga op de start pagina van Azure Portal naar **Network Watcher**.
1. Selecteer aan de linkerkant in het gedeelte **controle** de optie **verbindings monitor (preview-versie)**.
1. U ziet alle verbindings monitors die zijn gemaakt in de verbindings monitor (preview-versie). Ga naar het tabblad **verbindings controle** om de verbindings monitors weer te geven die zijn gemaakt in de klassieke ervaring van de verbindings monitor.

    ![Scherm afbeelding met verbindings monitors die zijn gemaakt in de verbindings monitor (preview)](./media/connection-monitor-2-preview/cm-resource-view.png)   
    
1. Selecteer in de linkerbovenhoek van het dash board **verbindings monitor (preview)** de optie **maken**.

   ![Scherm opname van het tabblad basis beginselen in verbindings monitor](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Voer op het tabblad **basis beginselen** informatie in voor uw verbindings monitor:
   * **Naam van de verbindings monitor** : Voeg de naam van de verbindings monitor toe. Gebruik de standaard naamgevings regels voor Azure-resources.
   * **Abonnement** : Kies een abonnement voor uw verbindings monitor.
   * **Regio** : Kies een regio voor de verbindings monitor. U kunt alleen de bron-Vm's selecteren die in deze regio worden gemaakt.
   * **Werkruimte configuratie** : uw werk ruimte bevat uw bewakings gegevens. U kunt een aangepaste werk ruimte of de standaardwerk ruimte gebruiken. 
       * Als u de standaardwerk ruimte wilt gebruiken, schakelt u het selectie vakje in. 
       * Als u een aangepaste werk ruimte wilt kiezen, schakelt u het selectie vakje uit. Kies vervolgens het abonnement en de regio voor uw aangepaste werk ruimte. 
1. Selecteer onder aan het tabblad **volgende: test groepen**.

   ![Scherm opname van het tabblad test groep maken in de verbindings monitor](./media/connection-monitor-2-preview/create-tg.png)

1. Voeg bronnen, doelen en test configuraties toe aan uw test groepen. Zie [test groepen maken in verbindings monitor](#create-test-groups-in-a-connection-monitor)om uw test groepen in te stellen. 
1. Selecteer onder aan het tabblad **volgende: waarschuwingen maken**.

   ![Scherm opname van het deel venster waarin u waarschuwingen maakt](./media/connection-monitor-2-preview/create-alert.png)

1. Zie [waarschuwingen maken in verbindings monitor](#create-alerts-in-connection-monitor) voor informatie over het maken van waarschuwingen.
1. Selecteer onder aan het tabblad **volgende: controleren + maken**.

  ![Scherm opname van de verbindings monitor, met het tabblad controleren + maken](./media/connection-monitor-2-preview/review-create-cm.png)

1. Controleer op het tabblad **controleren en maken** de basis gegevens en test groepen voordat u de verbindings monitor maakt. Als u de verbindings monitor wilt bewerken:
   * Als u de basis gegevens wilt bewerken, selecteert u het potlood pictogram.
   * Als u een test groep wilt bewerken, selecteert u deze.

   > [!NOTE] 
   > Op het tabblad **controleren en maken** worden de kosten per maand weer gegeven tijdens de preview-fase van de verbindings monitor. Op dit moment worden er geen kosten in rekening gebracht voor de kolom **huidige kosten** . Als verbindings monitor algemeen beschikbaar wordt, wordt in deze kolom een maandelijks bedrag weer gegeven. 
   > 
   > Zelfs in de preview-fase van de verbindings monitor zijn de Log Analytics opname kosten van toepassing.

1. Wanneer u klaar bent om de verbindings monitor te maken, selecteert u onder aan het tabblad **controleren en maken** de optie **maken**.

Met verbindings monitor (preview) wordt de bron van de verbindings monitor gemaakt op de achtergrond.

## <a name="create-test-groups-in-a-connection-monitor"></a>Test groepen maken in een verbindings monitor

Elke test groep in een verbindings monitor bevat bronnen en bestemmingen die worden getest op netwerk parameters. Ze zijn getest op het percentage controles dat mislukt en de RTT over test configuraties.

Als u in de Azure Portal een test groep wilt maken in een verbindings monitor, geeft u waarden op voor de volgende velden:

* **Test groep uitschakelen** : u kunt dit veld selecteren om bewaking uit te scha kelen voor alle bronnen en doelen die door de test groep worden opgegeven. Deze selectie is standaard uitgeschakeld.
* **Naam** : Geef uw test groep een naam.
* **Bronnen** : u kunt zowel virtuele Azure-machines als lokale computers opgeven als bronnen als er agents op zijn geïnstalleerd. Zie [bewakings agenten installeren](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)voor informatie over het installeren van een agent voor uw bron.
   * Als u Azure-agents wilt kiezen, selecteert u het tabblad **Azure-eind punten** . Hier ziet u alleen de virtuele machines die zijn gebonden aan de regio die u hebt opgegeven tijdens het maken van de verbindings monitor. Standaard worden Vm's gegroepeerd in het abonnement waarvan ze deel uitmaken. Deze groepen worden samengevouwen. 
   
       U kunt inzoomen op het abonnements niveau tot andere niveaus in de hiërarchie:

      **Abonnement**  >  **Resource groepen**  >  **VNETs**  >  **Subnetten**  >  **Vm's met agents**

      U kunt ook de waarde van het veld **groeperen op** wijzigen om de structuur te starten vanaf een ander niveau. Als u bijvoorbeeld per virtueel netwerk groepeert, ziet u de virtuele machines die agents hebben in de hiërarchie **VNETs**  >  **subnetten**  >  **vm's met agents**.
       Als u een VNET, subnet of één VM selecteert, wordt de bijbehorende resource-ID ingesteld als het eind punt. Standaard worden alle virtuele machines in het geselecteerde VNET of subnet met Network Watcher extensie deel genomen aan de bewaking. Als u het bereik wilt beperken, selecteert u specifieke subnetten/agents of wijzigt u de waarde van de eigenschap scope. 

      ![Scherm opname van de verbindings monitor, met het paneel bronnen toevoegen en het tabblad Azure agents](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Als u on-premises agents wilt kiezen, selecteert u het tabblad **niet-Azure-agents** . Standaard worden agents gegroepeerd in werk ruimten per regio. Voor al deze werk ruimten is de Netwerkprestatiemeter oplossing geconfigureerd. 
   
       Als u Netwerkprestatiemeter aan uw werk ruimte wilt toevoegen, haalt u deze op via [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Zie [oplossingen controleren in azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)voor informatie over het toevoegen van Netwerkprestatiemeter. 
   
       In de weer gave **verbindings monitor maken** op het tabblad **basis beginselen** is de standaard regio geselecteerd. Als u de regio wijzigt, kunt u agents kiezen uit werk ruimten in de nieuwe regio.  U kunt een of meer agents of subnetten selecteren. In de weer gave subnet kunt u specifieke IP-adressen voor bewaking selecteren. Door meerdere subnetten toe te voegen, maakt u een aangepast on-premises netwerk met de OnPremises_Network_1. U kunt ook de waarde van het veld **groeperen op** wijzigen in gegroepeerd op subnetten.

      ![Scherm opname van de verbindings monitor, met daarin het paneel bronnen toevoegen en het tabblad niet-Azure-agents](./media/connection-monitor-2-preview/add-non-azure-sources.png)

   * U kunt ook recent gebruikte eind punten kiezen met behulp van het tabblad **recente eind punt** 
   
   * Wanneer u klaar bent met het instellen van bronnen, selecteert u **gereed**. U kunt de basis eigenschappen, zoals de naam van het eind punt, nog steeds bewerken door te klikken op het eind punt in de weer gave test groep maken. 

* **Doelen** : u kunt connectiviteit met Azure-vm's, on-premises machines of een wille keurig eind punt (een openbaar IP-adres, URL of FQDN) bewaken door ze op te geven als bestemming. U kunt in één test groep Azure-Vm's, on-premises machines, Office 365-Url's, Dynamics 365-Url's en aangepaste eind punten toevoegen.

    * Als u virtuele Azure-machines als doelen wilt kiezen, selecteert u het tabblad **Azure-eind punten** . Standaard worden de Azure-Vm's gegroepeerd in een abonnements hiërarchie die zich in dezelfde regio bevindt die u hebt geselecteerd in de weer gave **verbindings controle maken** op het tabblad **basis** . U kunt de regio wijzigen en Azure-Vm's kiezen uit de zojuist geselecteerde regio. Vervolgens kunt u inzoomen op abonnements niveau naar andere niveaus in de hiërarchie, net als bij de Azure-bron-eind punten.
     U kunt VNETs, subnetten of enkele Vm's selecteren, vergelijkbaar met de Azure-eind punten van de bron. Als u een VNET, subnet of één VM selecteert, wordt de bijbehorende resource-ID ingesteld als het eind punt. Standaard worden alle virtuele machines in het geselecteerde VNET of subnet met Network Watcher extensie deel genomen aan de bewaking. Als u het bereik wilt beperken, selecteert u specifieke subnetten/agents of wijzigt u de waarde van de eigenschap scope. 

       ![Scherm opname van het deel venster bestemmingen toevoegen met het tabblad Azure-Vm's](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Scherm afbeelding van het deel venster bestemmingen toevoegen met het abonnements niveau](./media/connection-monitor-2-preview/add-azure-dests2.png)
    
    * Als u niet-Azure-agents als doelen wilt kiezen, selecteert u het tabblad niet-Azure-eind punten. Standaard worden agents gegroepeerd in werk ruimten per regio. Voor al deze werk ruimten is de Netwerkprestatiemeter oplossing geconfigureerd. Als u Netwerkprestatiemeter aan uw werk ruimte wilt toevoegen, haalt u deze op via Azure Marketplace. Zie oplossingen controleren in Azure Monitor voor informatie over het toevoegen van Netwerkprestatiemeter. In de weer gave verbindings monitor maken op het tabblad basis beginselen is de standaard regio geselecteerd. Als u de regio wijzigt, kunt u agents kiezen uit werk ruimten in de nieuwe regio. U kunt een of meer agents of subnetten selecteren. In de weer gave subnet kunt u specifieke IP-adressen voor bewaking selecteren. Door meerdere subnetten toe te voegen, maakt u een aangepast on-premises netwerk met de OnPremises_Network_1.  
    
     ![Scherm afbeelding van het deel venster niet-Azure-doelen toevoegen](./media/connection-monitor-2-preview/add-non-azure-dest.png)
    
    * Als u open bare eind punten als bestemming wilt kiezen, selecteert u het tabblad **externe adressen** . De lijst met eind punten omvat Office 365-test-Url's en Dynamics 365-test-Url's, gegroepeerd op naam. Naast deze eind punten kunt u een eind punt kiezen dat is gemaakt in andere test groepen in dezelfde verbindings monitor. 
    
        Als u een nieuw eind punt wilt toevoegen, selecteert u in de rechter bovenhoek **+ eind punten**. Geef vervolgens de naam en URL, het IP-adres of de FQDN van het eind punt op.

       ![Scherm opname waarin wordt getoond waar open bare eind punten als bestemmingen moeten worden toegevoegd in de verbindings monitor](./media/connection-monitor-2-preview/add-endpoints.png)

    * Als u recent gebruikte eind punten wilt kiezen, gaat u naar het tabblad **recente eind punten**   .
    * Wanneer u klaar bent met het kiezen van bestemmingen, selecteert u **gereed**. U kunt de basis eigenschappen, zoals de naam van het eind punt, nog steeds bewerken door te klikken op het eind punt in de weer gave test groep maken. 

* **Test configuraties** : u kunt een of meer test configuraties koppelen in een test groep. Maak een nieuwe test configuratie met behulp van het tabblad ' nieuwe configuratie ' of gebruik een test configuratie die wordt gebruikt in andere test groepen in dezelfde verbindings monitor via het tabblad ' een bestaande ' kiezen.

    * **Naam** : Geef de configuratie van de test op.
    * **Protocol** : Kies TCP, ICMP of http. Als u HTTP-naar-HTTPS wilt wijzigen, selecteert u **http** als protocol en selecteert u **443** als poort.
        * **Test configuratie voor het netwerk maken** : dit selectie vakje wordt alleen weer gegeven als u **http** in het veld **protocol** selecteert. Schakel dit selectie vakje in om een andere test configuratie te maken die gebruikmaakt van dezelfde bronnen en bestemmingen die u elders hebt opgegeven in uw configuratie. De zojuist gemaakte test configuratie heet `<the name of your test configuration>_networkTestConfig` .
        * **Traceroute uitschakelen** : dit veld is van toepassing wanneer het protocol TCP of ICMP is. Schakel dit selectie vakje in om te voor komen dat bronnen worden gedetecteerd voor de detectie van de topologie en de hop-by-Hop-RTT.
    * **Doel poort** – u kunt dit veld aanpassen met een doel poort van uw keuze.
        * Luis teren op poort: dit veld is van toepassing wanneer protocol TCP is. Schakel dit selectie vakje in om de gekozen TCP-poort te openen als deze nog niet is geopend. 
    * **Test frequentie** : gebruik dit veld om te kiezen hoe vaak bronnen worden gepingd op het door u opgegeven protocol en poort. U kunt kiezen uit 30 seconden, 1 minuut, 5 minuten, 15 minuten of 30 minuten. Selecteer aangepast om de gewenste frequentie van 30 seconden tot 30 minuten in te voeren. Bronnen testen de connectiviteit met bestemmingen op basis van de waarde die u kiest.  Als u bijvoorbeeld 30 seconden selecteert, wordt de verbinding met de bestemming ten minste eenmaal in een periode van 30 seconden gecontroleerd.
    * **Drempel waarde voor geslaagd** : u kunt drempels instellen voor de volgende netwerk parameters:
       * **Controles mislukt** : Stel het percentage controles in dat kan mislukken wanneer bronnen de connectiviteit met bestemmingen controleren door gebruik te maken van de criteria die u hebt opgegeven. Voor het TCP-of ICMP-protocol kan het percentage mislukte controles worden vergeleken met het percentage pakket verlies. Voor het HTTP-protocol vertegenwoordigt dit veld het percentage HTTP-aanvragen dat geen antwoord heeft ontvangen.
       * **Retour tijd** : Stel de RTT in milliseconden in om te bepalen hoe lang bronnen kunnen worden uitgevoerd om verbinding te maken met de bestemming via de test configuratie.
    
       ![Scherm opname waarin wordt getoond hoe een test configuratie moet worden ingesteld in de verbindings monitor](./media/connection-monitor-2-preview/add-test-config.png)
       
## <a name="create-alerts-in-connection-monitor"></a>Waarschuwingen maken in de verbindings monitor

U kunt waarschuwingen instellen op tests die zijn mislukt op basis van de drempel waarden die zijn ingesteld in test configuraties.   

Van de Azure Portal om waarschuwingen te maken in een verbindings monitor, geeft u waarden op voor de volgende velden: 

- Waarschuwing maken: u kunt dit veld selecteren om een metrische waarschuwing te maken in Azure Monitor. Als u dit selecteert, worden de andere velden voor bewerken ingeschakeld. Aanvullende kosten voor een waarschuwing zijn van toepassing op basis van de [prijzen voor waarschuwingen](https://azure.microsoft.com/pricing/details/monitor/) 

- Bereik-> resource en bereik-> hiërarchie-dit wordt vooraf ingevuld op basis van de waarden die zijn opgegeven op het tabblad basis beginselen 

- Condition-> de waarschuwing wordt gemaakt op basis van de metrische test resultaten (preview-versie). Wanneer het test resultaat van de verbindings monitor is mislukt, wordt de waarschuwings regel geactiveerd. 

- Actie groep: u kunt ervoor kiezen om uw e-mail adres rechtstreeks in te voeren of u kunt ervoor kiezen om waarschuwingen te maken via actie groepen. Als u ervoor kiest om uw e-mail adres direct in te voeren, wordt een actie groep met de naam NPM e-mail ActionGroup gemaakt en wordt de e-mail-ID toegevoegd aan die actie groep. Als u actie groepen wilt gebruiken, moet u een eerder gemaakte actie groep selecteren. Hier vindt u informatie over het maken van een actie groep. Zodra de waarschuwing is gemaakt, kunt u de koppeling waarschuwingen beheren gebruiken om uw waarschuwingen te beheren. 

- Naam van waarschuwings regel-naam van de verbindings monitor 

- Regel inschakelen bij het maken: Hiermee wordt de waarschuwings regel ingeschakeld op basis van de voor waarde. Schakel deze optie uit als u de regel wilt maken, maar niet wilt inschakelen. 

    ![Scherm opname van het maken van een waarschuwing in het deel venster verbindings monitor](./media/connection-monitor-2-preview/create-alert-filled.png)

## <a name="scale-limits"></a>Schaal limieten

Verbindings monitors hebben de volgende schaal limieten:

* Maximum aantal verbindings monitors per abonnement per regio: 100
* Maximum aantal test groepen per verbindings monitor: 20
* Maximum aantal bronnen en bestemmingen per verbindings monitor: 100
* Maximum aantal test configuraties per verbindings monitor: 2 via de Azure Portal

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het analyseren van bewakings gegevens en het instellen van waarschuwingen](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Meer informatie [over het vaststellen van problemen in uw netwerk](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)

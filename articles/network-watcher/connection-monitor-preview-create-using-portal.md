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
ms.openlocfilehash: 4e7067e537ce8fb6faf82198f7863957f79ffb22
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567929"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>Maak een verbindings monitor (preview) met behulp van de Azure Portal

Meer informatie over het maken van een verbindings monitor (preview) voor het bewaken van de communicatie tussen uw resources met behulp van de Azure Portal. Het ondersteunt hybride en Azure-Cloud implementaties.

## <a name="before-you-begin"></a>Voordat u begint 

Bij verbindings monitors die u in verbindings monitor (preview) maakt, kunt u zowel on-premises machines als Azure-Vm's toevoegen als bronnen. Deze verbindings monitors kunnen ook de connectiviteit met eind punten bewaken. De eind punten kunnen zich op Azure of een andere URL of een ander IP-adres bevindt.

Verbindings monitor (preview) omvat de volgende entiteiten:


* **Bron van de verbindings monitor** : een regio-specifieke Azure-resource. Alle volgende entiteiten zijn eigenschappen van een verbindingscontrole resource.
* **Eind punt** – een bron of doel die deel uitmaakt van connectiviteits controles. Voor beelden van eind punten zijn Azure-Vm's, on-premises agents, Url's en Ip's.
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
1. Voer op het tabblad **basis beginselen** informatie in voor uw verbindings monitor:
   * **Naam van de verbindings monitor** : Voeg de naam van de verbindings monitor toe. Gebruik de standaard naamgevings regels voor Azure-resources.
   * **Abonnement** : Kies een abonnement voor uw verbindings monitor.
   * **Regio** : Kies een regio voor de verbindings monitor. U kunt alleen de bron-Vm's selecteren die in deze regio worden gemaakt.
   * **Werkruimte configuratie** : uw werk ruimte bevat uw bewakings gegevens. U kunt een aangepaste werk ruimte of de standaardwerk ruimte gebruiken. 
       * Als u de standaardwerk ruimte wilt gebruiken, schakelt u het selectie vakje in. 
       * Als u een aangepaste werk ruimte wilt kiezen, schakelt u het selectie vakje uit. Kies vervolgens het abonnement en de regio voor uw aangepaste werk ruimte. 
1. Selecteer onder aan het tabblad **volgende: test groepen**.

   ![Scherm opname van het tabblad basis beginselen in verbindings monitor](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Selecteer op het tabblad **test groepen** de optie **+ test groep**. Zie [test groepen maken in verbindings monitor](#create-test-groups-in-a-connection-monitor)om uw test groepen in te stellen. 
1. Selecteer onder aan het tabblad **volgende: controleren + maken** om uw verbindings monitor te controleren.

   ![Scherm afbeelding van het tabblad test groepen en het deel venster waar u details van test groep toevoegt](./media/connection-monitor-2-preview/create-tg.png)

1. Controleer op het tabblad **controleren en maken** de basis gegevens en test groepen voordat u de verbindings monitor maakt. Als u de verbindings monitor wilt bewerken:
   * Als u de basis gegevens wilt bewerken, selecteert u het potlood pictogram.
   * Als u een test groep wilt bewerken, selecteert u deze.

   > [!NOTE] 
   > Op het tabblad **controleren en maken** worden de kosten per maand weer gegeven tijdens de preview-fase van de verbindings monitor. Op dit moment worden er geen kosten in rekening gebracht voor de kolom **huidige kosten** . Als verbindings monitor algemeen beschikbaar wordt, wordt in deze kolom een maandelijks bedrag weer gegeven. 
   > 
   > Zelfs in de preview-fase van de verbindings monitor zijn de Log Analytics opname kosten van toepassing.

1. Wanneer u klaar bent om de verbindings monitor te maken, selecteert u onder aan het tabblad **controleren en maken** de optie **maken**.

   ![Scherm opname van de verbindings monitor, met het tabblad controleren + maken](./media/connection-monitor-2-preview/review-create-cm.png)

Met verbindings monitor (preview) wordt de bron van de verbindings monitor gemaakt op de achtergrond.

## <a name="create-test-groups-in-a-connection-monitor"></a>Test groepen maken in een verbindings monitor

Elke test groep in een verbindings monitor bevat bronnen en bestemmingen die worden getest op netwerk parameters. Ze zijn getest op het percentage controles dat mislukt en de RTT over test configuraties.

Als u in de Azure Portal een test groep wilt maken in een verbindings monitor, geeft u waarden op voor de volgende velden:

* **Test groep uitschakelen** : u kunt dit veld selecteren om bewaking uit te scha kelen voor alle bronnen en doelen die door de test groep worden opgegeven. Deze selectie is standaard uitgeschakeld.
* **Naam** : Geef uw test groep een naam.
* **Bronnen** : u kunt zowel virtuele Azure-machines als lokale computers opgeven als bronnen als er agents op zijn geïnstalleerd. Zie [bewakings agenten installeren](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)voor informatie over het installeren van een agent voor uw bron.
   * Als u Azure-agents wilt kiezen, selecteert u het tabblad **Azure agents** . Hier ziet u alleen de virtuele machines die zijn gebonden aan de regio die u hebt opgegeven tijdens het maken van de verbindings monitor. Standaard worden Vm's gegroepeerd in het abonnement waarvan ze deel uitmaken. Deze groepen worden samengevouwen. 
   
       U kunt inzoomen op het abonnements niveau tot andere niveaus in de hiërarchie:

      **Abonnement**  >  **Resource groepen**  >  **VNETs**  >  **Subnetten**  >  **Vm's met agents**

      U kunt ook de waarde van het veld **groeperen op** wijzigen om de structuur te starten vanaf een ander niveau. Als u bijvoorbeeld per virtueel netwerk groepeert, ziet u de virtuele machines die agents hebben in de hiërarchie **VNETs**  >  **subnetten**  >  **vm's met agents**.

      ![Scherm opname van de verbindings monitor, met het paneel bronnen toevoegen en het tabblad Azure agents](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Als u on-premises agents wilt kiezen, selecteert u het tabblad **niet-Azure-agents** . Standaard worden agents gegroepeerd in werk ruimten per regio. Voor al deze werk ruimten is de Netwerkprestatiemeter oplossing geconfigureerd. 
   
       Als u Netwerkprestatiemeter aan uw werk ruimte wilt toevoegen, haalt u deze op via [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Zie [oplossingen controleren in azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)voor informatie over het toevoegen van Netwerkprestatiemeter. 
   
       In de weer gave **verbindings monitor maken** op het tabblad **basis beginselen** is de standaard regio geselecteerd. Als u de regio wijzigt, kunt u agents kiezen uit werk ruimten in de nieuwe regio. U kunt ook de waarde van het veld **groeperen op** wijzigen in gegroepeerd op subnetten.

      ![Scherm opname van de verbindings monitor, met daarin het paneel bronnen toevoegen en het tabblad niet-Azure-agents](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Als u de door u geselecteerde Azure-en niet-Azure-agents wilt bekijken, gaat u naar het tabblad **controleren** .

      ![Scherm opname van de verbindings monitor, met het paneel bronnen toevoegen en het tabblad controleren](./media/connection-monitor-2-preview/review-sources.png)

   * Wanneer u klaar bent met het instellen van bronnen, selecteert u aan de onderkant van het paneel **bronnen toevoegen** de optie **gereed**.

* **Doelen** : u kunt de verbinding met virtuele machines van Azure of een eind punt (een openbaar IP-adres, URL of FQDN) bewaken door ze op te geven als bestemming. In één test groep kunt u Azure-Vm's, Office 365 Url's, Dynamics 365-Url's en aangepaste eind punten toevoegen.

    * Als u virtuele Azure-machines als doelen wilt kiezen, selecteert u het tabblad **Azure vm's** . Standaard worden de Azure-Vm's gegroepeerd in een abonnements hiërarchie die zich in dezelfde regio bevindt die u hebt geselecteerd in de weer gave **verbindings controle maken** op het tabblad **basis** . U kunt de regio wijzigen en Azure-Vm's kiezen uit de zojuist geselecteerde regio. Vervolgens kunt u inzoomen op abonnements niveau naar andere niveaus in de hiërarchie, zoals het niveau van Azure-agents.

       ![Scherm opname van het deel venster bestemmingen toevoegen met het tabblad Azure-Vm's](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Scherm afbeelding van het deel venster bestemmingen toevoegen met het abonnements niveau](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Als u eind punten wilt kiezen als bestemmingen, selecteert u het tabblad **eind punten** . De lijst met eind punten omvat Office 365-test-Url's en Dynamics 365-test-Url's, gegroepeerd op naam. Naast deze eind punten kunt u een eind punt kiezen dat is gemaakt in andere test groepen in dezelfde verbindings monitor. 
    
        Als u een nieuw eind punt wilt toevoegen, selecteert u in de rechter bovenhoek **+ eind punten**. Geef vervolgens de naam en URL, het IP-adres of de FQDN van het eind punt op.

       ![Scherm opname van het toevoegen van eind punten als bestemming in de verbindings monitor](./media/connection-monitor-2-preview/add-endpoints.png)

    * Als u de Azure-Vm's en-eind punten wilt bekijken die u hebt gekozen, selecteert u het tabblad **controleren** .
    * Wanneer u klaar bent met het kiezen van bestemmingen, selecteert u **gereed**.

* **Test configuraties** : u kunt test configuraties koppelen aan een test groep. De Azure Portal slechts één test configuratie per test groep toestaan, maar u kunt ARMClient gebruiken om meer toe te voegen.

    * **Naam** : Geef de configuratie van de test op.
    * **Protocol** : Kies TCP, ICMP of http. Als u HTTP-naar-HTTPS wilt wijzigen, selecteert u **http** als protocol en selecteert u **443** als poort.
        * **Test configuratie voor het netwerk maken** : dit selectie vakje wordt alleen weer gegeven als u **http** in het veld **protocol** selecteert. Schakel dit selectie vakje in om een andere test configuratie te maken die gebruikmaakt van dezelfde bronnen en bestemmingen die u elders hebt opgegeven in uw configuratie. De zojuist gemaakte test configuratie heet `<the name of your test configuration>_networkTestConfig` .
        * **Traceroute uitschakelen** : dit veld is van toepassing op test groepen waarvan het protocol TCP of ICMP is. Schakel dit selectie vakje in om te voor komen dat bronnen worden gedetecteerd voor de detectie van de topologie en de hop-by-Hop-RTT.
    * **Doel poort** – u kunt dit veld aanpassen met een doel poort van uw keuze.
    * **Test frequentie** : gebruik dit veld om te kiezen hoe vaak bronnen worden gepingd op het door u opgegeven protocol en poort. U kunt kiezen uit 30 seconden, 1 minuut, 5 minuten, 15 minuten of 30 minuten. Bronnen testen de connectiviteit met bestemmingen op basis van de waarde die u kiest.  Als u bijvoorbeeld 30 seconden selecteert, wordt de verbinding met de bestemming ten minste eenmaal in een periode van 30 seconden gecontroleerd.
    * **Drempel waarde voor geslaagd** : u kunt drempels instellen voor de volgende netwerk parameters:
       * **Controles mislukt** : Stel het percentage controles in dat kan mislukken wanneer bronnen de connectiviteit met bestemmingen controleren door gebruik te maken van de criteria die u hebt opgegeven. Voor het TCP-of ICMP-protocol kan het percentage mislukte controles worden vergeleken met het percentage pakket verlies. Voor het HTTP-protocol vertegenwoordigt dit veld het percentage HTTP-aanvragen dat geen antwoord heeft ontvangen.
       * **Retour tijd** : Stel de RTT in milliseconden in om te bepalen hoe lang bronnen kunnen worden uitgevoerd om verbinding te maken met de bestemming via de test configuratie.
    
       ![Scherm opname waarin wordt getoond hoe een test configuratie moet worden ingesteld in de verbindings monitor](./media/connection-monitor-2-preview/add-test-config.png)


## <a name="scale-limits"></a>Schaal limieten

Verbindings monitors hebben de volgende schaal limieten:

* Maximum aantal verbindings monitors per abonnement per regio: 100
* Maximum aantal test groepen per verbindings monitor: 20
* Maximum aantal bronnen en bestemmingen per verbindings monitor: 100
* Maximum aantal test configuraties per verbindings monitor: 2 via de Azure Portal

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het analyseren van bewakings gegevens en het instellen van waarschuwingen](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Meer informatie [over het vaststellen van problemen in uw netwerk](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)

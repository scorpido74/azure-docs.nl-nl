---
title: 'Azure ExpressRoute: NPM configureren voor circuits'
description: Configureer npm (cloud-based network monitoring) voor Azure ExpressRoute-circuits. Dit omvat monitoring over ExpressRoute private peering en Microsoft peering.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.openlocfilehash: 54fa3dcbfbbcb3153f81407a9bc9b52511405390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076594"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Netwerkprestatiemeter configureren voor ExpressRoute

Met dit artikel u een uitbreiding van de netwerkprestatiemonitor configureren om ExpressRoute te controleren. Netwerkprestatiemeter (NPM) is een cloudoplossing voor netwerkcontrole die de connectiviteit controleert tussen implementaties in de Azure-cloud en on-premises locaties (filialen, enzovoort). NPM is een onderdeel van Azure Monitor-logboeken. NPM biedt een uitbreiding voor ExpressRoute waarmee u netwerkprestaties kunt controleren via ExpressRoute-circuits die zijn geconfigureerd voor het gebruik van persoonlijke peering of Microsoft-peering. Wanneer u NPM configureert voor ExpressRoute, kunt u netwerkproblemen detecteren om te identificeren en op te lossen. Deze service is ook beschikbaar voor Azure Government-Cloud.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

U kunt:

* Verlies en latentie bewaken in verschillende VNets en waarschuwingen instellen

* Alle paden (inclusief redundante paden) op het netwerk bewaken

* Tijdelijke en point-in-time netwerkproblemen oplossen die moeilijk te repliceren zijn

* Helpen bij het bepalen van een specifiek segment op het netwerk dat verantwoordelijk is voor gedegradeerde prestaties

* Doorvoer per virtueel netwerk (Als u agents in elke VNet hebt geïnstalleerd)

* De status van het ExpressRoute-systeem vanaf een eerder tijdstip bekijken

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

Bewakingsagents worden geïnstalleerd op meerdere servers, zowel on-premises als in Azure. De agenten communiceren met elkaar, maar verzenden geen gegevens, ze sturen TCP handshake pakketten. De communicatie tussen de agents stelt Azure in staat om de netwerktopologie en het pad dat het verkeer kan nemen in kaart te brengen.

1. Maak een NPM-werkruimte. Dit is hetzelfde als een Log Analytics-werkruimte.
2. Softwareagents installeren en configureren. (Als u alleen wilt controleren via Microsoft Peering, hoeft u geen softwareagents te installeren en te configureren.): 
    * Installeer bewakingsagents op de on-premises servers en de Azure VM's (voor private peering).
    * Configureer instellingen op de servers van de bewakingsagent zodat de bewakingsagents kunnen communiceren. (Firewallpoorten openen, enz.)
3. Configureer NSG-regels (Network Security Group) zodat de bewakingsagent die is geïnstalleerd op Azure VM's kan communiceren met on-premises bewakingsagents.
4. Controle instellen: automatisch ontdekken en beheren welke netwerken zichtbaar zijn in NPM.

Als u netwerkprestatiemonitor al gebruikt om andere objecten of services te controleren en u al Workspace in een van de ondersteunde regio's hebt, u stap 1 en stap 2 overslaan en uw configuratie starten met stap 3.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>Stap 1: Een werkruimte maken

Maak een werkruimte in het abonnement met de Koppeling VNets naar het ExpressRoute-circuit(en).

1. Selecteer in de [Azure-portal](https://portal.azure.com)het abonnement waarop de VNET's naar uw ExpressRoute-circuit zijn peered. Zoek vervolgens in de lijst met services in de **Marketplace** naar 'Network Performance Monitor'. Klik in de retour om de pagina **Netwerkprestatiemeter** te openen.

   >[!NOTE]
   >U een nieuwe werkruimte maken of een bestaande werkruimte gebruiken. Als u een bestaande werkruimte wilt gebruiken, moet u ervoor zorgen dat de werkruimte is gemigreerd naar de nieuwe querytaal. [Meer informatie...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Klik onder aan de pagina **Netwerkprestatiemonitor** op **Maken** om netwerkprestatiemeter te openen **- Nieuwe oplossingspagina** maken. Klik **op Logboekanalysewerkruimte - selecteer een werkruimte** om de pagina Werkruimten te openen. Klik **op + Nieuwe werkruimte maken** om de pagina Werkruimte te openen.
3. Selecteer op de **werkruimtepagina Log Analytics** de optie **Nieuw maken**en configureer vervolgens de volgende instellingen:

   * Log Analytics Workspace - Typ een naam voor uw werkruimte.
   * Abonnement - Als u meerdere abonnementen hebt, kiest u degene die u wilt koppelen aan de nieuwe werkruimte.
   * Resourcegroep : maak een resourcegroep of gebruik een bestaande groep.
   * Locatie - Deze locatie wordt gebruikt om de locatie op te geven van het opslagaccount dat wordt gebruikt voor de agentverbindingslogboeken.
   * Prijscategorie - Selecteer de prijscategorie.
  
     >[!NOTE]
     >Het ExpressRoute circuit kan overal ter wereld zijn. Het hoeft zich niet in dezelfde regio te bevinden als de werkruimte.
     >
  
     ![werkruimte](./media/how-to-npm/4.png)<br><br>
4. Klik op **OK** om de instellingensjabloon op te slaan en te implementeren. Zodra de sjabloon is gevalideerd, klikt u op **Maken** om de werkruimte te implementeren.
5. Nadat de werkruimte is geïmplementeerd, navigeert u naar de bron **NetworkMonitoring(name)** die u hebt gemaakt. Valideer de instellingen en klik vervolgens op **Oplossing waarvoor extra configuratie nodig is.**

   ![extra configuratie](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>Stap 2: Agents installeren en configureren

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2.1: Het installatiebestand van de agent downloaden

1. Ga naar het tabblad **Algemene instellingen** van de pagina Configuratie van de **netwerkprestatiemonitor** voor uw bron. Klik in de sectie **Systeemanalyseagents** op de agent die overeenkomt met de processor van uw server en download het installatiebestand.
2. Kopieer vervolgens de **werkruimte-id** en **primaire sleutel** naar kladblok.
3. Download in de sectie **Logboekanalyseagents configureren voor controle met tcp-protocol,** download de Powershell Script. Met het PowerShell-script u de relevante firewallpoort voor de TCP-transacties openen.

   ![PowerShell-script](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2.2: Installeer een bewakingsagent op elke bewakingsserver (op elke VNET die u wilt controleren)

We raden u aan ten minste twee agents aan elke kant van de ExpressRoute-verbinding te installeren voor redundantie (bijvoorbeeld on-premises Azure VNETs). De agent moet op een Windows Server (2008 SP1 of hoger) worden geïnstalleerd. Het bewaken van ExpressRoute-circuits met Windows Desktop OS en Linux OS wordt niet ondersteund. Gebruik de volgende stappen om agents te installeren:
   
  >[!NOTE]
  >Agents geduwd door SCOM (inclusief [MMA)](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)kunnen hun locatie mogelijk niet consistent detecteren als ze worden gehost in Azure. We raden u aan deze agents niet in Azure VNET's te gebruiken om ExpressRoute te controleren.
  >

1. Voer **Setup** uit om de agent te installeren op elke server die u wilt gebruiken voor het bewaken van ExpressRoute. De server die u voor controle gebruikt, kan een vm of on-premises zijn en moet toegang tot internet hebben. U moet ten minste één agent on-premises installeren en één agent in elk netwerksegment dat u in Azure wilt controleren.
2. Klik op de pagina **Welkom** op **Volgende**.
3. Lees op de pagina **Licentievoorwaarden** de licentie en klik op **Akkoord**.
4. Wijzig of bewaar op de pagina **Doelmap** de standaardinstallatiemap en klik vervolgens op **Volgende**.
5. Op de pagina **Opties voor agentinstellingen** u ervoor kiezen de agent te verbinden met Azure Monitor-logboeken of Operations Manager. U de opties ook leeg laten als u de agent later wilt configureren. Nadat u uw selectie(s) hebt gemaakt, klikt u op **Volgende**.

   * Als u ervoor hebt gekozen verbinding te maken met **Azure Log Analytics,** plakt u de **werkruimte-id** en **werkruimtesleutel** (primaire sleutel) die u in de vorige sectie naar Kladblok hebt gekopieerd. Klik vervolgens op **Volgende.**

     ![ID en sleutel](./media/how-to-npm/8.png)
   * Als u ervoor kiest verbinding te maken met **Operations Manager,** typt u op de pagina Configuratie van de **beheergroep** de **naam beheergroep**, **beheerserver**en **beheerserverpoort**. Klik vervolgens op **Volgende.**

     ![Operations Manager](./media/how-to-npm/9.png)
   * Kies op de pagina **Agent Action Account** het lokale **systeemaccount** of **domein- of lokaal computeraccount**. Klik vervolgens op **Volgende.**

     ![Account](./media/how-to-npm/10.png)
6. Bekijk op de pagina **Klaar om te installeren** uw keuzes door te nemen en klik op **Installeren**.
7. Klik op de pagina **Configuratie voltooid** op **Voltooien**.
8. Als de Microsoft-bewakingsagent is voltooid, wordt deze weergegeven in het Configuratiescherm. U uw configuratie daar controleren en controleren of de agent is verbonden met Azure Monitor-logboeken. Wanneer de agent is verbonden, wordt een bericht weergegeven met de vermelding: **De Microsoft Monitoring Agent heeft met succes verbinding gemaakt met de Microsoft Operations Management Suite-service.**

9. Herhaal deze procedure voor elke VNET die u moet controleren.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2.3: Proxy-instellingen configureren (optioneel)

Als u een webproxy gebruikt om toegang te krijgen tot internet, gebruikt u de volgende stappen om proxy-instellingen voor de Microsoft Monitoring Agent te configureren. Voer deze stappen uit voor elke server. Als er veel servers zijn die u moet configureren, is het wellicht eenvoudiger om een script te gebruiken om dit proces te automatiseren. Zie [Proxy-instellingen configureren voor de Microsoft-bewakingsagent met behulp van een script.](../log-analytics/log-analytics-windows-agent.md)

Ga als volgt te werk om proxy-instellingen voor de Microsoft-bewakingsagent te configureren met behulp van het Configuratiescherm:

1. Open het **Configuratiescherm**.
2. Open **Microsoft Monitoring Agent**.
3. Klik op het tabblad **Proxyinstellingen**.
4. Selecteer **Een proxyserver gebruiken** en typ het URL- en poortnummer als dat nodig is. Als er voor uw proxyserver verificatie is vereist, voert u de gebruikersnaam en het wachtwoord in om de proxyserver te openen.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2.4: De verbinding met de agent verifiëren

U eenvoudig controleren of uw medewerkers communiceren.

1. Open het **Configuratiescherm**op een server met de bewakingsagent .
2. Open de **Microsoft Monitoring Agent**.
3. Klik op het tabblad **Azure Log Analytics.**
4. In de kolom **Status** moet u zien dat de agent die is verbonden met Azure Monitor-logboeken is gekoppeld.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2.5: Open de firewallpoorten op de servers van de bewakingsagent

Als u het TCP-protocol wilt gebruiken, moet u firewallpoorten openen om ervoor te zorgen dat de bewakingsagents kunnen communiceren.

U een PowerShell-script uitvoeren om de registersleutels te maken die vereist zijn door de netwerkprestatiemonitor. Dit script maakt ook de Windows Firewall-regels waarmee bewakingsagents TCP-verbindingen met elkaar kunnen maken. De registersleutels die door het script zijn gemaakt, geven aan of de foutopsporingslogboeken moeten worden geregistreerd en of het pad voor het logboekbestand moet worden geregistreerd. Het definieert ook de agent TCP-poort die wordt gebruikt voor communicatie. De waarden voor deze toetsen worden automatisch ingesteld door het script. U moet deze toetsen niet handmatig wijzigen.

Poort 8084 wordt standaard geopend. U een aangepaste poort gebruiken door de parameter 'portNumber' aan het script op te geven. Als u dit doet, moet u echter dezelfde poort opgeven voor alle servers waarop u het script uitvoert.

>[!NOTE]
>Het PowerShell-script 'EnableRules' configureert Windows Firewall-regels alleen op de server waar het script wordt uitgevoerd. Als u een netwerkfirewall hebt, moet u ervoor zorgen dat verkeer dat is bestemd is voor de TCP-poort die wordt gebruikt door Network Performance Monitor, wordt gebruikt.
>
>

Open op de agentservers een PowerShell-venster met beheerdersbevoegdheden. Voer het [EnableRules](https://aka.ms/npmpowershellscript) PowerShell-script uit (dat u eerder hebt gedownload). Gebruik geen parameters.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>Stap 3: Regels voor netwerkbeveiliging configureren

Als u agentservers in Azure wilt controleren, moet u NSG-regels (Network Security Group) configureren om TCP-verkeer toe te staan op een poort die door NPM wordt gebruikt voor synthetische transacties. De standaardpoort is 8084. Hierdoor kan een bewakingsagent die is geïnstalleerd op een Azure VM communiceren met een on-premises bewakingsagent.

Zie [Netwerkbeveiligingsgroepen](../virtual-network/virtual-networks-create-nsg-arm-portal.md)voor meer informatie over NSG.

>[!NOTE]
>Zorg ervoor dat u de agents (zowel de on-premises serveragent als de Azure-serveragent) hebt geïnstalleerd en voer het PowerShell-script uit voordat u verdergaat met deze stap.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>Stap 4: Peering-verbindingen ontdekken

1. Navigeer naar de overzichtstegel Netwerkprestatiemeter door naar de pagina **Alle bronnen** te gaan en klik vervolgens op de op de witte lijst staande NPM Workspace.

   ![npm-werkruimte](./media/how-to-npm/npm.png)
2. Klik op de **overzichtstegel Netwerkprestatiemeter** om het dashboard weer te geven. Het dashboard bevat een ExpressRoute-pagina, waaruit blijkt dat ExpressRoute zich in een 'ongeconfigureerde staat' bevindt. Klik **op Functie-instelling** om de configuratiepagina van de netwerkprestatiemonitor te openen.

   ![functie-instelling](./media/how-to-npm/npm2.png)
3. Navigeer op de configuratiepagina naar het tabblad 'ExpressRoute Peerings' op het linkerzijpaneel. Klik vervolgens op **Nu ontdekken.**

   ![Ontdek](./media/how-to-npm/13.png)
4. Wanneer detectie is voltooid, ziet u een lijst met de volgende items:
   * Alle Microsoft-peeringverbindingen in het ExpressRoute-circuit(en) die aan dit abonnement zijn gekoppeld.
   * Alle privé-peeringverbindingen die verbinding maken met de VNets die aan dit abonnement zijn gekoppeld.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>Stap 5: Beeldschermen configureren

In deze sectie configureert u de monitors. Volg de stappen voor het type peering dat u wilt controleren: **private peering**of **Microsoft-peering**.

### <a name="private-peering"></a>Persoonlijke peering

Voor privé-peering ziet u regels voor unieke **circuitnaam** en **VNet-naam**wanneer detectie is voltooid. In eerste instantie zijn deze regels uitgeschakeld.

![regels](./media/how-to-npm/14.png)

1. Schakel het selectievakje **Monitor dit peering-selectievakje** in.
2. Schakel het selectievakje **Statuscontrole inschakelen in voor dit peering**.
3. Kies de controlevoorwaarden. U aangepaste drempelwaarden instellen om statusgebeurtenissen te genereren door drempelwaarden te typen. Wanneer de waarde van de voorwaarde boven de geselecteerde drempelwaarde voor het geselecteerde netwerk/subnetwerkpaar komt, wordt een statusgebeurtenis gegenereerd.
4. Klik op de knop ON-PREM AGENTS **Agents toevoegen** om de on-premises servers toe te voegen van waaruit u de privé-peeringverbinding wilt controleren. Zorg ervoor dat u alleen agents kiest die verbinding hebben met het Eindpunt van de Microsoft-service die u hebt opgegeven in de sectie voor stap 2. De on-premises agenten moeten het eindpunt kunnen bereiken via de ExpressRoute-verbinding.
5. Sla de instellingen op.
6. Nadat u de regels hebt inschakelt en de waarden en agents hebt geselecteerd die u wilt controleren, wacht u ongeveer 30-60 minuten voordat de waarden beginnen te vullen en de **ExpressRoute Monitoring-tegels** beschikbaar te komen.

### <a name="microsoft-peering"></a>Microsoft-peering

Klik voor Microsoft-peering op de Microsoft-peeringverbinding(en) die u wilt controleren en configureer de instellingen.

1. Schakel het selectievakje **Monitor dit peering-selectievakje** in. 
2. (Optioneel) U het eindpunt van de doelmicrosoft-service wijzigen. NPM kiest standaard een eindpunt van de Microsoft-service als doel. NPM bewaakt de connectiviteit van uw on-premises servers tot dit doeleindpunt via ExpressRoute. 
    * Als u dit doeleindpunt wilt wijzigen, klikt u op de koppeling **(bewerken)** onder **Doel:**, en selecteert u een ander eindpunt van microsoft-servicedoel in de lijst met URL's.
      ![doel bewerken](./media/how-to-npm/edit_target.png)<br>

    * U een aangepaste URL of IP-adres gebruiken. Deze optie is met name relevant als u Microsoft-peering gebruikt om een verbinding tot stand te brengen met Azure PaaS-services, zoals Azure Storage, SQL-databases en websites die worden aangeboden op openbare IP-adressen. Klik hiervoor op de koppeling **(Gebruik in plaats daarvan aangepaste URL of IP-adres)** onder aan de URL-lijst en voer vervolgens het openbare eindpunt in van uw Azure PaaS-service die is verbonden via de ExpressRoute Microsoft-peering.
    ![aangepaste URL](./media/how-to-npm/custom_url.png)<br>

    * Als u deze optionele instellingen gebruikt, moet u ervoor zorgen dat hier alleen het eindpunt van de Microsoft-service is geselecteerd. Het eindpunt moet worden aangesloten op ExpressRoute en bereikbaar zijn voor de on-premises agenten.
3. Schakel het selectievakje **Statuscontrole inschakelen in voor dit peering**.
4. Kies de controlevoorwaarden. U aangepaste drempelwaarden instellen om statusgebeurtenissen te genereren door drempelwaarden te typen. Wanneer de waarde van de voorwaarde boven de geselecteerde drempelwaarde voor het geselecteerde netwerk/subnetwerkpaar komt, wordt een statusgebeurtenis gegenereerd.
5. Klik op de knop ON-PREM AGENTS **Agents toevoegen** om de on-premises servers toe te voegen van waaruit u de Microsoft-peering-verbinding wilt controleren. Zorg ervoor dat u alleen agents kiest die verbinding hebben met de eindpunten van de Microsoft-service die u hebt opgegeven in de sectie voor stap 2. De on-premises agenten moeten het eindpunt kunnen bereiken via de ExpressRoute-verbinding.
6. Sla de instellingen op.
7. Nadat u de regels hebt inschakelt en de waarden en agents hebt geselecteerd die u wilt controleren, wacht u ongeveer 30-60 minuten voordat de waarden beginnen te vullen en de **ExpressRoute Monitoring-tegels** beschikbaar te komen.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>Stap 6: Bewakingstegels weergeven

Zodra u de bewakingstegels ziet, worden uw ExpressRoute-circuits en verbindingsbronnen gecontroleerd door NPM. U op de tegel Microsoft Peering klikken om in te zoomen op de status van Microsoft Peering-verbindingen.

![tegels controleren](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Pagina Netwerkprestatiemonitor

De NPM-pagina bevat een pagina voor ExpressRoute met een overzicht van de gezondheid van ExpressRoute-circuits en peerings.

![Dashboard](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Lijst van circuits

Als u een lijst met alle bewaakte ExpressRoute-circuits wilt bekijken, klikt u op de tegel **ExpressRoute-circuits.** U een circuit selecteren en de status ervan weergeven, trenddiagrammen voor pakketverlies, bandbreedtegebruik en latentie. De grafieken zijn interactief. U een aangepast tijdvenster selecteren voor het plotten van de grafieken. U de muis over een gebied in de grafiek slepen om in te zoomen en fijnkorrelige gegevenspunten te zien.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>Trend van verlies, latentie en doorvoer

De bandbreedte-, latentie- en verliesgrafieken zijn interactief. U inzoomen op elk gedeelte van deze grafieken met muisbesturingselementen. U ook de bandbreedte-, latentie- en verliesgegevens voor andere intervallen bekijken door onder de knop Acties linksboven te klikken op **Datum/tijd.**

![Trend](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Peeringslijst

Als u de lijst met alle verbindingen met virtuele netwerken wilt weergeven via privé-peering, klikt u op de tegel **Privé-peerings** op het dashboard. Hier u een virtuele netwerkverbinding selecteren en de status ervan bekijken, trenddiagrammen voor pakketverlies, bandbreedtegebruik en latentie.

![circuitlijst](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Weergave Knooppunten

Als u een lijst wilt weergeven met alle koppelingen tussen de on-premises knooppunten en Azure VM's/Microsoft-serviceeindpunten voor de gekozen ExpressRoute-peeringverbinding, klikt u op **Knooppuntkoppelingen weergeven**. U de status van elke koppeling bekijken, evenals de trend van verlies en latentie die eraan zijn gekoppeld.

![knooppuntenweergave](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Circuittopologie

Als u de topologie van het circuit wilt weergeven, klikt u op de tegel **Topologie.** Dit brengt u naar de topologieweergave van het geselecteerde circuit of peering. Het topologiediagram biedt de latentie voor elk segment in het netwerk. Elke laag 3 hop wordt vertegenwoordigd door een knooppunt van het diagram. Klikken op een hop onthult meer details over de hop.

U het zichtbaarheidsniveau verhogen om on-premises hop op te nemen door de schuifbalk onder **Filters**te verplaatsen. Als u de schuifbalk naar links of rechts verplaatst, wordt het aantal hopin de topologiegrafiek verhoogd/verkleint. De latentie in elk segment is zichtbaar, waardoor segmenten met hoge latentie op uw netwerk sneller kunnen worden geïsoleerd.

![filters](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Gedetailleerde Topologieweergave van een circuit

Deze weergave toont VNet-verbindingen.
![gedetailleerde topologie](./media/how-to-npm/17.png)

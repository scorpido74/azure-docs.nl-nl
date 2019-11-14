---
title: 'Azure ExpressRoute: NPM voor circuits configureren'
description: Configureer cloud-gebaseerde netwerk (NPM) voor Azure ExpressRoute-circuits controleren. Dit bevat informatie over bewaking via ExpressRoute-privépeering en Microsoft-peering.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.openlocfilehash: 54fa3dcbfbbcb3153f81407a9bc9b52511405390
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076594"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Netwerkprestatiemeter configureren voor ExpressRoute

Dit artikel helpt u bij het configureren van een extensie voor Network Performance Monitor voor het controleren van ExpressRoute. Netwerkprestatiemeter (NPM) is een cloudoplossing voor netwerkcontrole die de connectiviteit controleert tussen implementaties in de Azure-cloud en on-premises locaties (filialen, enzovoort). NPM is een onderdeel van Azure Monitor-logboeken. NPM biedt een uitbreiding voor ExpressRoute waarmee u netwerkprestaties kunt controleren via ExpressRoute-circuits die zijn geconfigureerd voor het gebruik van persoonlijke peering of Microsoft-peering. Wanneer u NPM configureert voor ExpressRoute, kunt u netwerkproblemen detecteren om te identificeren en op te lossen. Deze service is ook beschikbaar voor Azure Government-Cloud.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

U kunt:

* Verlies en latentie controleren in verschillende VNets en waarschuwingen kunt instellen

* Alle paden (inclusief redundante paden) op het netwerk controleren

* Problemen met tijdelijke en point-in-time-netwerk die moeilijk te repliceren

* Te helpen bepalen hoe een specifieke segment van het netwerk dat verantwoordelijk is voor de verslechterde prestaties

* Ophalen van doorvoer per virtueel netwerk (als u agents zijn geïnstalleerd in elk VNet hebt)

* Zie de ExpressRoute-systeemstatus van een eerder punt in tijd

## <a name="workflow"></a>Werkstroom

Monitoring agents zijn geïnstalleerd op meerdere servers, zowel on-premises en in Azure. De agents met elkaar communiceren, maar geen gegevens verzenden, ze TCP-handshake pakketten verzenden. De communicatie tussen de agents kan Azure de netwerktopologie en pad van het verkeer kan worden toegewezen.

1. Een NPM-werkruimte maken. Dit is hetzelfde als een Log Analytics-werkruimte.
2. Software-agents installeren en configureren. (Als u alleen wilt bewaken over micro soft-peering, hoeft u geen software-agents te installeren en te configureren.): 
    * Installatie monitoring-agents op de on-premises servers en de Azure VM's (voor persoonlijke peering).
    * Instellingen configureren op de monitoring agentservers om toe te staan de bewakingsagents om te communiceren. (Open firewallpoorten, enz.)
3. Configureer regels voor network security group (NSG) om toe te staan de monitoring agent is geïnstalleerd op Azure Virtual machines om te communiceren met on-premises monitoring-agents.
4. Controle instellen: Voer automatisch ontdekken en te beheren welke netwerken zijn zichtbaar in de NPM.

Als u al van Network Performance Monitor gebruikmaakt voor het bewaken van andere objecten of services, en u al werkruimte in een van de ondersteunde regio's hebt, kunt u stap 1 en stap 2 overslaan en beginnen met de configuratie met stap 3.

## <a name="configure"></a>Stap 1: Een werkruimte maken

Een werkruimte maken in het abonnement waaraan de VNets koppelen aan het ExpressRoute-circuit.

1. In de [Azure-portal](https://portal.azure.com), selecteer het abonnement waaraan de VNETs gekoppeld aan uw ExpressRoute-circuit. Zoek de lijst met services in de **Marketplace** voor 'Network Performance Monitor'. Klik in het retourneren, om te openen de **Network Performance Monitor** pagina.

   >[!NOTE]
   >U kunt een nieuwe werkruimte maken of een bestaande werkruimte gebruiken. Als u een bestaande werkruimte gebruiken wilt, moet u ervoor zorgen dat de werkruimte is gemigreerd naar de nieuwe querytaal. [Meer informatie...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Aan de onderkant van de belangrijkste **Network Performance Monitor** pagina, klikt u op **maken** openen **Network Performance Monitor - de nieuwe oplossing maken** pagina. Klik op **Log Analytics-werkruimte - Selecteer een werkruimte** de werkruimten-pagina te openen. Klik op **+ nieuwe werkruimte maken** om de pagina van de werkruimte te openen.
3. Selecteer op de pagina **log Analytics werk ruimte** de optie **nieuwe maken**en configureer de volgende instellingen:

   * Log Analytics-werkruimte: Typ een naam voor uw werkruimte.
   * Abonnement: als u meerdere abonnementen hebt, kiest u het abonnement die u wilt koppelen aan de nieuwe werkruimte.
   * Resourcegroep - Maak een resourcegroep of gebruik een bestaande resourcegroep.
   * Locatie - deze locatie wordt gebruikt om op te geven van de locatie van het opslagaccount dat wordt gebruikt voor de agent verbinding-Logboeken.
   * Prijscategorie: Selecteer de prijscategorie.
  
     >[!NOTE]
     >Het ExpressRoute-circuit is overal ter wereld. Dit hoeft niet te worden in dezelfde regio als de werkruimte.
     >
  
     ![werkruimte](./media/how-to-npm/4.png)<br><br>
4. Klik op **OK** opslaan en implementeren van de instellingen-sjabloon. Nadat de sjabloon wordt gevalideerd, klikt u op **maken** implementeren van de werkruimte.
5. Nadat de werkruimte is geïmplementeerd, gaat u naar de **NetworkMonitoring(name)** resource die u hebt gemaakt. Het valideren van de instellingen en klik vervolgens op **oplossing is aanvullende configuratie vereist**.

   ![aanvullende configuratie](./media/how-to-npm/5.png)

## <a name="agents"></a>Stap 2: Installeren en configureren van agents

### <a name="download"></a>2.1: de agent setup-bestand downloaden

1. Ga naar de **algemene instellingen die u** tabblad van de **configuratie van netwerkprestaties** -pagina voor uw resource. Klik op de agent die overeenkomt met de processor van de server van de **Log Analytics-Agents installeren** uit en download het setup-bestand.
2. Kopieer vervolgens de **werkruimte-ID** en **primaire sleutel** naar Kladblok.
3. Uit de **Log Analytics-Agents configureren voor bewaking met TCP-protocol** sectie, het Powershell-Script downloaden. Het PowerShell-script kunt u de relevante firewallpoort voor de TCP-transacties openen.

   ![PowerShell-script](./media/how-to-npm/7.png)

### <a name="installagent"></a>2.2: een monitoring agent installeren op elke controleserver (op elke VNET die u wilt bewaken)

Het is raadzaam dat u ten minste twee agents op elke zijde van de ExpressRoute-verbinding voor redundantie (bijvoorbeeld on-premises Azure vnet's installeren). De agent moet worden geïnstalleerd op een Windows Server (2008 SP1 of hoger). Bewaking van ExpressRoute-circuits met behulp van Windows Desktop OS en Linux-besturingssysteem wordt niet ondersteund. Gebruik de volgende stappen uit om agents te installeren:
   
  >[!NOTE]
  >Agents gepusht door SCOM (inclusief [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)) mogelijk niet voor het detecteren van hun locatie consistent als ze worden gehost in Azure. U wordt aangeraden dat u niet deze agents in Azure vnet's gebruikt voor het bewaken van ExpressRoute.
  >

1. Voer **Setup** voor het installeren van de agent op elke server die u gebruiken wilt voor het bewaken van ExpressRoute. De server die u voor het bewaken van gebruikt kan een virtuele machine of on-premises, en moet over toegang tot Internet. U moet ten minste één agent on-premises, en een agent installeren op elke netwerksegment dat u wilt bewaken in Azure.
2. Klik op de pagina **Welkom** op **Volgende**.
3. Op de **licentievoorwaarden** pagina, lees de gebruiksrechtovereenkomst en klik vervolgens op **ik ga akkoord**.
4. Op de **doelmap** pagina, wijzigen of desgewenst de standaardinstallatiemap en klik vervolgens op **volgende**.
5. Op de pagina **installatie opties voor agent** kunt u ervoor kiezen om de agent te verbinden met Azure monitor Logboeken of Operations Manager. Of u kunt de opties leeg laten als u wilt configureren van de agent later opnieuw. Nadat u uw selection(s), klikt u op **volgende**.

   * Als u wilt verbinding maken met **Azure Log Analytics**, plak de **werkruimte-ID** en **Werkruimtesleutel** (primaire sleutel) die u in Kladblok in de vorige sectie hebt gekopieerd. Klik op **Volgende**.

     ![ID en -sleutel](./media/how-to-npm/8.png)
   * Als u wilt verbinding maken met **Operations Manager**op de **Beheergroepconfiguratie** pagina, typt u de **Beheergroepsnaam**, **Management Server** , en de **Beheerserverpoort**. Klik op **Volgende**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Op de **actie-Account van Agent** pagina, kiest u de **lokaal systeem** -account, of **domein of lokaal computeraccount**. Klik op **Volgende**.

     ![Account](./media/how-to-npm/10.png)
6. Op de **gereed voor installatie** pagina, Controleer uw keuzes en klik vervolgens op **installeren**.
7. Klik op de pagina **Configuratie voltooid** op **Voltooien**.
8. Als u klaar bent, is de Microsoft Monitoring Agent wordt weergegeven in het Configuratiescherm. U kunt uw configuratie daar controleren en controleren of de agent is verbonden met Azure Monitor Logboeken. Wanneer verbinding is gemaakt, de agent wordt een bericht weergegeven: **The Microsoft Monitoring Agent verbonden is met de Microsoft Operations Management Suite service**.

9. Herhaal deze procedure voor elke VNET die u nodig hebt om te worden bewaakt.

### <a name="proxy"></a>2.3: Configureer proxy-instellingen (optioneel)

Als u een webproxy gebruikt voor toegang tot het Internet, gebruikt u de volgende stappen uit om te configureren van proxy-instellingen voor de Microsoft Monitoring Agent. Voer deze stappen uit voor elke server. Als er veel servers zijn die u moet configureren, is het wellicht eenvoudiger om een script te gebruiken om dit proces te automatiseren. Als dit het geval is, raadpleegt u [het configureren van proxy-instellingen voor de Microsoft Monitoring Agent met behulp van een script](../log-analytics/log-analytics-windows-agent.md).

Proxy-instellingen voor de Microsoft Monitoring Agent via het Configuratiescherm configureren:

1. Open de **Configuratiescherm**.
2. Open **Microsoft Monitoring Agent**.
3. Klik op het tabblad **Proxyinstellingen**.
4. Selecteer **een proxyserver gebruiken** en typ de URL en het poortnummer, wanneer u een nodig hebt. Als er voor uw proxyserver verificatie is vereist, voert u de gebruikersnaam en het wachtwoord in om de proxyserver te openen.

   ![Proxy](./media/how-to-npm/11.png)

### <a name="verifyagent"></a>2.4: Controleer de verbinding van de agent

U kunt eenvoudig controleren of uw agents communiceren.

1. Open op een server met de monitoring agent, de **Configuratiescherm**.
2. Open de **Microsoft Monitoring Agent**.
3. Klik op de **Azure Log Analytics** tabblad.
4. In de kolom **status** ziet u dat de agent is verbonden met het Azure monitor Logboeken.

   ![status](./media/how-to-npm/12.png)

### <a name="firewall"></a>2.5: open de firewallpoorten op de monitoring agentservers

Als u wilt het TCP-protocol gebruiken, moet u de firewall-poorten om ervoor te zorgen dat de bewakingsagents kunnen communiceren openen.

U kunt een PowerShell-script voor het maken van de registersleutels die voor de Network Performance Monitor vereist zijn uitvoeren. Dit script maakt ook de Windows Firewall-regels om toe te staan monitoring-agents voor het maken van TCP-verbindingen met elkaar. De registersleutels die zijn gemaakt door het script opgeven of de logboeken voor foutopsporing en het pad naar het bestand zich aanmeldt. Het definieert ook de agent TCP-poort die wordt gebruikt voor communicatie. De waarden voor deze sleutels worden automatisch ingesteld door het script. U moet deze sleutels niet handmatig wijzigen.

Poort 8084 wordt standaard geopend. U kunt een aangepaste poort gebruiken door op te geven van de parameter 'portNumber' aan het script. Als u dit doet, moet u echter dezelfde poort voor alle servers waarop u het script uitvoert opgeven.

>[!NOTE]
>Het 'EnableRules' PowerShell-script configureert Windows Firewall-regels alleen op de server waarop het script wordt uitgevoerd. Als u de firewall van een netwerk hebt, moet u ervoor zorgen dat het verkeer dat bestemd is voor de TCP-poort wordt gebruikt door Network Performance Monitor toestaat.
>
>

Open een PowerShell-venster met beheerdersbevoegdheden op de agentservers. Voer de [EnableRules](https://aka.ms/npmpowershellscript) PowerShell-script (die u eerder hebt gedownload). Gebruik geen parameters.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="opennsg"></a>Stap 3: Configureer regels voor netwerkbeveiligingsgroepen

U moet voor het controleren van de agentservers die zich in Azure (NSG) regels voor netwerkbeveiligingsgroepen voor TCP-verkeer op een poort wordt gebruikt door NPM voor synthetische transacties configureren. De standaardpoort is 8084. Hiermee wordt een monitoring agent is geïnstalleerd op een Azure-VM om te communiceren met een on-premises monitoring agent.

Zie voor meer informatie over NSG [Netwerkbeveiligingsgroepen](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Zorg ervoor dat u de agents (zowel de lokale server-agent en de Azure-server-agent) hebt geïnstalleerd en de PowerShell-script voordat u doorgaat met deze stap hebt uitgevoerd.
>

## <a name="setupmonitor"></a>Stap 4: Peeringverbindingen detecteren

1. Navigeer naar de overzichtstegel van Network Performance Monitor door te gaan naar de **alle Resources** pagina en klik vervolgens op de goedgekeurde lijst NPM-werkruimte.

   ![npm-werkruimte](./media/how-to-npm/npm.png)
2. Klik op de **Network Performance Monitor** overzichtstegel om het dashboard. Het dashboard bevat een ExpressRoute-pagina, die laat zien dat ExpressRoute in een niet-geconfigureerde status'. Klik op **functie instellen** om de configuratiepagina van Network Performance Monitor te openen.

   ![functie instellen](./media/how-to-npm/npm2.png)
3. Navigeer naar het tabblad ExpressRoute-Peerings, zich in het deelvenster links op de configuratiepagina. Klik vervolgens op **nu detecteren**.

   ![detecteren](./media/how-to-npm/13.png)
4. Wanneer detectie is voltooid, ziet u een lijst met de volgende items:
   * Alle van de Microsoft-peering-verbindingen in de ExpressRoute-circuit die gekoppeld aan dit abonnement zijn.
   * Alle van de privé-peeringverbindingen die verbinding met de VNets maken die zijn gekoppeld aan dit abonnement.
            
## <a name="configmonitor"></a>Stap 5: Monitors configureren

In deze sectie configureert u de monitors. Volg de stappen voor het type peering dat u wilt bewaken: **privépeering**, of **Microsoft-peering**.

### <a name="private-peering"></a>Persoonlijke peering

Voor persoonlijke peering wanneer detectie is voltooid, ziet u regels voor wordt de unieke **Circuitnaam** en **VNet-naam**. In eerste instantie zijn deze regels uitgeschakeld.

![regels](./media/how-to-npm/14.png)

1. Controleer de **deze peering controleren** selectievakje.
2. Schakel het selectievakje **statuscontrole inschakelen voor deze peering**.
3. Kies de bewaking voorwaarden. U kunt aangepaste drempelwaarden voor het genereren van health-gebeurtenissen door te typen drempelwaarden instellen. Wanneer de waarde van de voorwaarde hoger dan de geselecteerde drempelwaarde voor het geselecteerde netwerk/subnet-paar gaat, wordt een statusgebeurtenis wordt gegenereerd.
4. Klik op de ON-PREMISES AGENTS **Agents toevoegen** om toe te voegen van de on-premises servers van waaruit u wilt bewaken van de privé-peering verbinding. Zorg ervoor dat u alleen agents die zijn verbonden met het Microsoft-service-eindpunt dat u hebt opgegeven in de sectie voor stap 2 kiezen. De on-premises-agents moet kunnen bereiken van het eindpunt met de ExpressRoute-verbinding.
5. Sla de instellingen op.
6. Na het inschakelen van de regels en selecteren van de waarden en de agents die u wilt bewaken, is er een wachttijd van ongeveer 30 tot 60 minuten voor de waarden om te beginnen met het invullen van en de **ExpressRoute-controle** tegels beschikbaar.

### <a name="microsoft-peering"></a>Microsoft-peering

Voor Microsoft-peering, klikt u op de Microsoft-peering verbindingen die u wilt bewaken en configureer de instellingen.

1. Controleer de **deze peering controleren** selectievakje. 
2. (Optioneel) U kunt het doel Microsoft service-eindpunt kunt wijzigen. Standaard kiest NPM een Microsoft-service-eindpunt als het doel. NPM bewaakt connectiviteit vanuit uw on-premises servers naar dit eindpunt doel via ExpressRoute. 
    * Als u wilt dit eindpunt doel wijzigen, klikt u op de **(bewerken)** koppeling onder **doel:** , en selecteer een ander doel eindpunt van de Microsoft-service in de lijst met URL's.
      ![doel bewerken](./media/how-to-npm/edit_target.png)<br>

    * U kunt een aangepaste URL of IP-adres gebruiken. Deze optie is met name relevant als u van Microsoft gebruikmaakt-peering voor het maken van een verbinding met Azure PaaS-services, zoals Azure Storage, SQL-databases en Websites die worden aangeboden op openbare IP-adressen. Klik op de koppeling om dit te doen, **(aangepaste URL of IP-adres gebruiken in plaats daarvan)** aan de onderkant van de lijst met URL's, voert u het openbare eindpunt van uw Azure PaaS-service die is verbonden via de ExpressRoute-Microsoft-peering.
    ![aangepaste URL](./media/how-to-npm/custom_url.png)<br>

    * Als u deze instellingen zijn optioneel, zorg ervoor dat alleen het Microsoft service-eindpunt hier is geselecteerd. Het eindpunt moet zijn verbonden met ExpressRoute en bereikbaar is door de on-premises-agents.
3. Schakel het selectievakje **statuscontrole inschakelen voor deze peering**.
4. Kies de bewaking voorwaarden. U kunt aangepaste drempelwaarden voor het genereren van health-gebeurtenissen door te typen drempelwaarden instellen. Wanneer de waarde van de voorwaarde hoger dan de geselecteerde drempelwaarde voor het geselecteerde netwerk/subnet-paar gaat, wordt een statusgebeurtenis wordt gegenereerd.
5. Klik op de ON-PREMISES AGENTS **Agents toevoegen** om toe te voegen van de on-premises servers van waaruit u wilt bewaken van de Microsoft-peering verbinding. Zorg ervoor dat u alleen agents die zijn verbonden met de Microsoft-service-eindpunten die u hebt opgegeven in de sectie voor stap 2 kiezen. De on-premises-agents moet kunnen bereiken van het eindpunt met de ExpressRoute-verbinding.
6. Sla de instellingen op.
7. Na het inschakelen van de regels en selecteren van de waarden en de agents die u wilt bewaken, is er een wachttijd van ongeveer 30 tot 60 minuten voor de waarden om te beginnen met het invullen van en de **ExpressRoute-controle** tegels beschikbaar.

## <a name="explore"></a>Stap 6: Bewaking tegels weergeven

Als u de bewaking tegels ziet, zijn uw ExpressRoute-circuits en verbindingsresources die worden bewaakt door NPM. U kunt klikken op Microsoft-Peering-tegel om in te zoomen op de status van de Microsoft-Peering-verbindingen.

![bewaking van tegels](./media/how-to-npm/15.png)

### <a name="dashboard"></a>Network Performance Monitor pagina

De NPM-pagina bevat een pagina voor ExpressRoute die een overzicht van de status van ExpressRoute-circuits en -peerings.

![Dashboard](./media/how-to-npm/dashboard.png)

### <a name="circuits"></a>Lijst van circuits

Om weer te geven van een lijst van alle bewaakte ExpressRoute-circuits, klikt u op de **ExpressRoute-circuits** tegel. U kunt een circuit selecteren en de status, trendgrafieken voor pakketverlies en bandbreedtegebruik latentie weergeven. De grafieken zijn interactief. U kunt een aangepaste tijdvenster voor het uitzetten van de grafieken selecteren. Sleep de muis over een gebied in het diagram inzoomen en Zie fijnmazig gegevenspunten.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend"></a>Trend van gegevensverlies, latentie en doorvoer

De grafieken voor bandbreedte, latentie en verlies, zijn interactief. U kunt inzoomen op een gedeelte van deze grafieken met behulp van besturingselementen van de muis. U kunt ook de bandbreedte, latentie en verlies van gegevens voor andere intervallen zien door te klikken op **datum/tijd**, dat zich bevindt onder de knop acties in de linkerbovenhoek.

![trend](./media/how-to-npm/16.png)

### <a name="peerings"></a>Lijst met Peerings

Aan de lijst met alle verbindingen met virtuele netwerken via persoonlijke peering weergeven, klikt u op de **privé-Peerings** tegel op het dashboard. Hier kunt u een virtuele netwerkverbinding en de status, trendgrafieken voor pakketverlies en bandbreedtegebruik latentie weergeven.

![lijst met circuit](./media/how-to-npm/peerings.png)

### <a name="nodes"></a>knooppunten weergeven

Aan de lijst met alle bijbehorende koppelingen tussen de on-premises knooppunten en de Azure VM's / Microsoft-service-eindpunten voor de gekozen ExpressRoute-peering verbinding weergeven, klikt u op **knooppuntkoppelingen weergeven**. U kunt de status van elke koppeling, evenals de trend van verlies en latentie die is gekoppeld aan deze weergeven.

![knooppunten weergeven](./media/how-to-npm/nodes.png)

### <a name="topology"></a>Circuit-topologie

Als u wilt weergeven van circuit topologie, klikt u op de **topologie** tegel. Hiermee gaat u naar de Topologieweergave van de geselecteerde circuit of peering. Diagram van de topologie biedt de latentie voor elk segment in het netwerk. Elke laag-3-hop wordt vertegenwoordigd door een knooppunt in het diagram. Meer informatie over de hop te klikken op een hop worden weergegeven.

U kunt het niveau van de zichtbaarheid van on-premises hops opnemen door over te stappen van de schuifregelaar in onderstaande verhogen **Filters**. De schuifregelaar naar links of rechts verplaatsen, verhoogt/vermindert het aantal hops in de grafiek topologie. De latentie voor elk segment is zichtbaar, waardoor voor snellere isolatie van segmenten van hoge latentie in uw netwerk.

![filters](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Gedetailleerde weergave van de topologie van een circuit

Deze weergave toont de VNet-verbindingen.
![gedetailleerde topologie](./media/how-to-npm/17.png)

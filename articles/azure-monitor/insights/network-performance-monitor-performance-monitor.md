---
title: Prestatiemonitor in netwerkprestatiemonitor
description: Met de prestatiemetermogelijkheid in netwerkprestatiemeter u de netwerkconnectiviteit op verschillende punten in uw netwerk bewaken. U cloudimplementaties en on-premises locaties, meerdere datacenters en filialen en bedrijfskritische multitier-toepassingen of microservices bewaken.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 126cca9d3606b378e59e4f4e1c5b52d985d19d94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055692"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Oplossing voor netwerkprestatiemonitor: prestatiebewaking

Met de prestatiemetermogelijkheid in [netwerkprestatiemeter](network-performance-monitor.md) u de netwerkconnectiviteit op verschillende punten in uw netwerk bewaken. U cloudimplementaties en on-premises locaties, meerdere datacenters en filialen en bedrijfskritische multitier-toepassingen of microservices bewaken. Met Performance Monitor u netwerkproblemen detecteren voordat uw gebruikers klagen. De belangrijkste voordelen zijn dat u: 

- Monitor verlies en latentie in verschillende subnetten en stel waarschuwingen in.
- Controleer alle paden (inclusief redundante paden) op het netwerk.
- Tijdelijke en point-in-time netwerkproblemen oplossen, die moeilijk te repliceren zijn.
- Bepaal het specifieke segment op het netwerk, dat verantwoordelijk is voor verminderde prestaties.
- Controleer de status van het netwerk, zonder dat er SNMP nodig is.


![Netwerkprestatiemeter](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Configuratie
Als u de configuratie voor netwerkprestatiemonitor wilt openen, opent u de [oplossing netwerkprestatiemeter](network-performance-monitor.md)en selecteert **u Configureren**.

![Netwerkprestatiemeter configureren](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Nieuwe netwerken maken

Een netwerk in Network Performance Monitor is een logische container voor subnetten. Het helpt u bij het organiseren van de monitoring van uw netwerkinfrastructuur op basis van uw behoeften. U een netwerk maken met een vriendelijke naam en er subnetten aan toevoegen volgens uw bedrijfslogica. U bijvoorbeeld een netwerk met de naam Londen maken en alle subnetten in uw datacenter in Londen toevoegen. Of u een netwerk met de naam *ContosoFrontEnd* maken en aan dit netwerk alle subnetten met de naam Contoso toevoegen die de voorkant van uw app bedienen. De oplossing maakt automatisch een standaardnetwerk, dat alle subnetten bevat die in uw omgeving zijn ontdekt. 

Wanneer u een netwerk maakt, voegt u er een subnet aan toe. Vervolgens wordt dat subnet uit het standaardnetwerk verwijderd. Als u een netwerk verwijdert, worden alle subnetten automatisch teruggestuurd naar het standaardnetwerk. Het standaardnetwerk fungeert als een container voor alle subnetten die niet in een door de gebruiker gedefinieerd netwerk zijn opgenomen. U het standaardnetwerk niet bewerken of verwijderen. Het blijft altijd in het systeem. U zoveel aangepaste netwerken maken als u nodig hebt. In de meeste gevallen zijn de subnetten in uw organisatie gerangschikt in meer dan één netwerk. Maak een of meer netwerken om uw subnetten te groeperen voor uw bedrijfslogica.

Een nieuw netwerk maken:


1. Selecteer het tabblad **Netwerken.**
1. Selecteer **Netwerk toevoegen**en voer de netwerknaam en -beschrijving in. 
2. Selecteer een of meer subnetten en selecteer **Toevoegen**. 
3. Selecteer **Opslaan** om de configuratie op te slaan. 


### <a name="create-monitoring-rules"></a>Controleregels maken 

Prestatiemonitor genereert statusgebeurtenissen wanneer de drempelwaarde voor de prestaties van netwerkverbindingen tussen twee subnetwerken of tussen twee netwerken wordt geschonden. Het systeem kan deze drempels automatisch leren. U ook aangepaste drempels verstrekken. Het systeem maakt automatisch een standaardregel, die een statusgebeurtenis genereert wanneer verlies of latentie tussen een paar netwerk- of subnetwerkkoppelingen de door het systeem geleerde drempel overschrijdt. Dit proces helpt de oplossing uw netwerkinfrastructuur te bewaken totdat u geen controleregels expliciet hebt gemaakt. Als de standaardregel is ingeschakeld, verzenden alle knooppunten synthetische transacties naar alle andere knooppunten die u hebt ingeschakeld voor bewaking. De standaardregel is handig bij kleine netwerken. Een voorbeeld is een scenario waarin u een klein aantal servers hebt waarop een microservice wordt uitgevoerd en u ervoor wilt zorgen dat alle servers verbinding met elkaar hebben.

>[!NOTE]
> We raden u aan de standaardregel uit te schakelen en aangepaste controleregels te maken, vooral bij grote netwerken waar u een groot aantal knooppunten gebruikt voor bewaking. Aangepaste controleregels kunnen het verkeer dat door de oplossing wordt gegenereerd, verminderen en u helpen bij het organiseren van de bewaking van uw netwerk.

Maak controleregels volgens uw bedrijfslogica. Een voorbeeld is als u de prestaties van de netwerkconnectiviteit van twee kantoorsites naar het hoofdkantoor wilt controleren. Groepeer alle subnetten in kantoorsite1 in netwerk O1. Vervolgens groepeer alle subnetten in office site2 in netwerk O2. Tot slot, groepeer alle subnetten in het hoofdkantoor in netwerk H. Maak twee controleregels - een tussen O1 en H en de andere tussen O2 en H. 

Aangepaste controleregels maken:

1. Selecteer **Regel toevoegen** op het tabblad **Monitor** en voer de regelnaam en -beschrijving in.
2. Selecteer het paar netwerk- of subnetwerkkoppelingen dat u in de lijsten wilt controleren. 
3. Selecteer het netwerk dat de gewenste subnetwerken bevat in de vervolgkeuzelijst voor het netwerk. Selecteer vervolgens de subnetwerken in de vervolgkeuzelijst voor het bijbehorende subnetwerk. Als u alle subnetwerken in een netwerkkoppeling wilt controleren, selecteert u **Alle subnetwerken**. Selecteer op dezelfde manier de andere subnetwerken die u wilt. Als u bewaking voor bepaalde subnetwerkkoppelingen wilt uitsluiten van de selecties die u hebt gemaakt, selecteert u **Uitzondering toevoegen**. 
4. Kies tussen ICMP- en TCP-protocollen om synthetische transacties uit te voeren. 
5. Als u geen statusgebeurtenissen wilt maken voor de items die u hebt geselecteerd, schakelt u **Statuscontrole inschakelen uit op de koppelingen die onder deze regel vallen.** 
6. Kies controlevoorwaarden. Als u aangepaste drempelwaarden wilt instellen voor het genereren van statusgebeurtenissen, voert u drempelwaarden in. Wanneer de waarde van de voorwaarde de geselecteerde drempelwaarde voor het geselecteerde netwerk- of subnetwerkpaar overschrijdt, wordt een statusgebeurtenis gegenereerd. 
7. Selecteer **Opslaan** om de configuratie op te slaan. 

Nadat u een bewakingsregel hebt opgeslagen, u die regel integreren met Waarschuwingsbeheer door **Waarschuwing maken te**selecteren. Er wordt automatisch een waarschuwingsregel gemaakt met de zoekopdracht. Andere vereiste parameters worden automatisch ingevuld. Met behulp van een waarschuwingsregel u waarschuwingen op basis van e-mail ontvangen, naast de bestaande waarschuwingen in netwerkprestatiemonitor. Waarschuwingen kunnen ook corrigerende acties met runbooks activeren, of ze kunnen worden geïntegreerd met bestaande servicebeheeroplossingen met behulp van webhooks. Selecteer **Waarschuwing beheren** om de waarschuwingsinstellingen te bewerken. 

U nu meer regels voor prestatiemeterregels maken of naar het oplossingsdashboard gaan om de mogelijkheid te gebruiken.

### <a name="choose-the-protocol"></a>Kies het protocol

Network Performance Monitor gebruikt synthetische transacties om netwerkprestatiestatistieken te berekenen, zoals pakketverlies en koppelingslatentie. Als u dit concept beter wilt begrijpen, u een network performance monitor-agent overwegen die is verbonden met één uiteinde van een netwerkkoppeling. Deze netwerkprestatiemonitoragent verzendt sondepakketten naar een tweede netwerkprestatiemonitoragent die is aangesloten op een ander uiteinde van het netwerk. De tweede agent antwoordt met antwoordpakketten. Dit proces wordt een paar keer herhaald. Door het aantal antwoorden te meten en de tijd die nodig is om elk antwoord te ontvangen, beoordeelt de eerste Network Performance Monitor-agent de latentie en pakketdalingen van de verbinding. 

De indeling, grootte en volgorde van deze pakketten wordt bepaald door het protocol dat u kiest wanneer u controleregels maakt. Op basis van het protocol van de pakketten kunnen de tussenliggende netwerkapparaten, zoals routers en switches, deze pakketten anders verwerken. Uw protocolkeuze beïnvloedt dus de nauwkeurigheid van de resultaten. Uw protocolkeuze bepaalt ook of u handmatige stappen moet uitvoeren nadat u de oplossing voor netwerkprestatiemeter hebt geïmplementeerd. 

Network Performance Monitor biedt u de keuze tussen ICMP- en TCP-protocollen voor het uitvoeren van synthetische transacties. Als u ICMP kiest wanneer u een regel voor synthetische transacties maakt, gebruiken de agents netwerkprestatiemeter ICMP ECHO-berichten om de netwerklatentie en pakketverlies te berekenen. ICMP ECHO gebruikt hetzelfde bericht dat wordt verzonden door de conventionele ping utility. Wanneer u TCP als protocol gebruikt, verzenden Network Performance Monitor-agents TCP SYN-pakketten via het netwerk. Deze stap wordt gevolgd door een TCP-handshake voltooiing en de verbinding wordt verwijderd met behulp van RST-pakketten. 

Houd rekening met de volgende gegevens voordat u een protocol kiest: 

* **Detectie van meerdere netwerkroutes.** TCP is nauwkeuriger bij het ontdekken van meerdere routes en heeft minder agents in elk subnet nodig. Een of twee agents die TCP gebruiken, kunnen bijvoorbeeld alle redundante paden tussen subnetten ontdekken. U hebt verschillende agents nodig die ICMP gebruiken om vergelijkbare resultaten te bereiken. Als u ICMP gebruikt, hebt u een aantal routes tussen twee subnetten nodig, meer dan 5N-agents in een bron- of bestemmingssubnet.

* **Nauwkeurigheid van de resultaten.** Routers en switches hebben de neiging om een lagere prioriteit toe te wijzen aan ICMP ECHO-pakketten in vergelijking met TCP-pakketten. In bepaalde situaties, wanneer netwerkapparaten zwaar worden geladen, weerspiegelen de gegevens die door TCP worden verkregen beter het verlies en de latentie die door toepassingen worden ervaren. Dit gebeurt omdat het grootste deel van het toepassingsverkeer over TCP stroomt. In dergelijke gevallen biedt ICMP minder nauwkeurige resultaten in vergelijking met TCP. 

* **Firewallconfiguratie.** TCP-protocol vereist dat TCP-pakketten naar een doelpoort worden verzonden. De standaardpoort die wordt gebruikt door agents Network Performance Monitor is 8084. U de poort wijzigen wanneer u agents configureert. Zorg ervoor dat uw netwerkfirewalls of NSG-regels (network security group) (in Azure) verkeer op de poort toestaan. U moet er ook voor zorgen dat de lokale firewall op de computers waar agents zijn geïnstalleerd, is geconfigureerd om verkeer op deze poort toe te staan. U PowerShell-scripts gebruiken om firewallregels te configureren op uw computers waarop Windows wordt uitgevoerd, maar u moet uw netwerkfirewall handmatig configureren. ICMP werkt daarentegen niet met een poort. In de meeste bedrijfsscenario's is ICMP-verkeer toegestaan via de firewalls, zodat u hulpprogramma's voor netwerkdiagnostiek gebruiken, zoals het ping-hulpprogramma. Als u de ene machine van de andere pingen, u het ICMP-protocol gebruiken zonder dat u firewalls handmatig hoeft te configureren.

>[!NOTE] 
> Sommige firewalls kunnen ICMP blokkeren, wat kan leiden tot doorgifte die resulteert in een groot aantal gebeurtenissen in uw beveiligingsinformatie en eventmanagementsysteem. Zorg ervoor dat het protocol dat u kiest niet wordt geblokkeerd door een netwerkfirewall of NSG. Anders kan network performance monitor het netwerksegment niet controleren. We raden u aan TCP te gebruiken voor monitoring. Gebruik ICMP in scenario's waarin u TCP niet gebruiken, zoals wanneer: 
>
> - U gebruikt windows-clientknooppunten, omdat TCP raw-sockets niet zijn toegestaan in Windows-clients.
> - Uw netwerkfirewall of NSG blokkeert TCP.
> - Je weet niet hoe je het protocol moet veranderen.

Als u ervoor kiest om ICMP tijdens de implementatie te gebruiken, u op elk gewenst moment overschakelen naar TCP door de standaardcontroleregel te bewerken.

1. Ga naar > **Monitor** > **Configure** > **Netwerkprestatiemonitor**configureren . **Monitor** Selecteer vervolgens **Standaardregel**. 
2. Schuif naar de sectie **Protocol** en selecteer het protocol dat u wilt gebruiken. 
3. Selecteer **Opslaan** om de instelling toe te passen. 

Zelfs als de standaardregel een specifiek protocol gebruikt, u nieuwe regels maken met een ander protocol. U zelfs een mix van regels maken waarbij sommige regels ICMP gebruiken en andere TCP. 

## <a name="walkthrough"></a>Kennismaking 

Kijk nu naar een eenvoudig onderzoek naar de oorzaak van een gezondheidsgebeurtenis.

In het oplossingsdashboard laat een statusgebeurtenis zien dat een netwerkkoppeling niet in orde is. Als u het probleem wilt onderzoeken, selecteert u de tegel **Netwerkkoppelingen die worden gecontroleerd.**

De tabel-down pagina laat zien dat de **DMZ2-DMZ1** netwerkkoppeling ongezond is. Selecteer **Subnetkoppelingen weergeven** voor deze netwerkkoppeling. 


De tabel-down pagina toont alle subnetwerkkoppelingen in de **DMZ2-DMZ1** netwerkkoppeling. Voor beide subnetwerkkoppelingen is de latentie de drempel overschreden, waardoor de netwerkkoppeling niet in orde is. U ook de latentietrends van beide subnetwerkkoppelingen zien. Gebruik het tijdselectiebesturingselement in de grafiek om u te concentreren op het vereiste tijdsbereik. U het tijdstip van de dag zien waarop latentie zijn hoogtepunt bereikte. Zoek de logboeken later voor deze periode om het probleem te onderzoeken. Selecteer **Knooppuntkoppelingen weergeven** om verder in te zoomen. 
 
 ![Pagina Subnetwerkkoppelingen](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Net als op de vorige pagina bevat de inzoompagina voor de specifieke subnetwerkkoppeling de koppelingen met het samenstellende knooppunt. U hier vergelijkbare acties uitvoeren als in de vorige stap. Selecteer **Topologie weergeven** om de topologie tussen de twee knooppunten weer te geven. 
 
 ![Pagina Knooppuntkoppelingen](media/network-performance-monitor-performance-monitor/node-links.png) 

Alle paden tussen de twee geselecteerde knooppunten worden uitgezet in de topologiekaart. U de hop-by-hop topologie van routes tussen twee knooppunten op de topologiekaart visualiseren. Het geeft u een duidelijk beeld van hoeveel routes er bestaan tussen de twee knooppunten en welke paden de gegevenspakketten nemen. Knelpunten in netwerkprestaties worden in het rood weergegeven. Als u een defecte netwerkverbinding of een defect netwerkapparaat wilt vinden, bekijkt u de rode elementen op de topologiekaart. 

 ![Topologiedashboard met topologiekaart](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

U het verlies, de latentie en het aantal hop in elk pad in het **deelvenster Actie** bekijken. Gebruik de schuifbalk om de details van de ongezonde paden weer te geven. Gebruik de filters om de paden met de ongezonde hop te selecteren, zodat de topologie voor alleen de geselecteerde paden wordt uitgezet. Als u wilt in- of uitzoomen op de topologiekaart, gebruikt u het muiswiel. 

In de volgende afbeelding wordt de hoofdoorzaak van de probleemgebieden naar het specifieke gedeelte van het netwerk weergegeven in de rode paden en hop. Selecteer een knooppunt in de topologiekaart om de eigenschappen van het knooppunt weer te geven, waaronder het FQDN- en IP-adres. Als u een hop selecteert, wordt het IP-adres van de hop weergegeven. 
 
![Topologiekaart met knooppunteigenschappen geselecteerd](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Volgende stappen
[Zoeklogboeken](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde netwerkprestatiegegevensrecords weer te geven.

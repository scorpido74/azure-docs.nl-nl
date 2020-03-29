---
title: 'Problemen met de prestaties van netwerkkoppelingen oplossen: Azure'
description: Deze pagina biedt een gestandaardiseerde methode voor het testen van de prestaties van Azure-netwerkkoppelingen.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: bb68919fba731caa32dcca3f4c991b8881afc6f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74869643"
---
# <a name="troubleshooting-network-performance"></a>Problemen met de netwerkprestaties oplossen
## <a name="overview"></a>Overzicht
Azure biedt stabiele en snelle manieren om vanuit uw on-premises netwerk verbinding te maken met Azure. Methoden zoals site-naar-site VPN en ExpressRoute worden met succes toegepast door klanten met kleine of grote ondernemingen om hun bedrijf te exploiteren in Azure. Maar wat gebeurt er als de prestaties niet voldoen aan uw verwachting of eerdere ervaring? Dit document kan helpen bij het standaardiseren van de manier waarop u uw specifieke omgeving test en basislijn.

In dit document ziet u hoe u de netwerklatentie en bandbreedte tussen twee hosts eenvoudig en consistent testen. Dit document geeft ook advies over manieren om naar het Azure-netwerk te kijken en probleempunten te isoleren. De PowerShell script en tools besproken vereisen twee hosts op het netwerk (aan weerszijden van de link wordt getest). De ene host moet een Windows Server of Desktop zijn, de andere kan Windows of Linux zijn. 

>[!NOTE]
>De benadering van probleemoplossing, de gebruikte tools en methoden zijn persoonlijke voorkeuren. Dit document beschrijft de aanpak en tools die ik vaak neem. Uw aanpak zal waarschijnlijk verschillen, er is niets mis met verschillende benaderingen van het oplossen van problemen. Als u echter geen vaste aanpak hebt, u met dit document aan de slag op het pad naar het bouwen van uw eigen methoden, hulpprogramma's en voorkeuren voor het oplossen van netwerkproblemen.
>
>

## <a name="network-components"></a>Netwerkonderdelen
Voordat we ons verdiepen in het oplossen van problemen, bespreken we enkele algemene termen en componenten. Deze discussie zorgt ervoor dat we nadenken over elk onderdeel in de end-to-end-keten die connectiviteit in Azure mogelijk maakt.
![1][1]

Op het hoogste niveau beschrijf ik drie belangrijke netwerkroutingdomeinen;

- het Azure-netwerk (blauwe wolk aan de rechterkant)
- internet of WAN (groene wolk in het midden)
- het Bedrijfsnetwerk (perzikwolk aan de linkerkant)

Als we het diagram van rechts naar links bekijken, bespreken we elk onderdeel kort:
 - **Virtuele machine** - De server kan meerdere NIC's hebben, ervoor zorgen dat statische routes, standaardroutes en instellingen van het besturingssysteem verkeer verzenden en ontvangen zoals u denkt dat het is. Ook heeft elke VM SKU een bandbreedtebeperking. Als u een kleinere VM SKU gebruikt, wordt uw verkeer beperkt door de bandbreedte die beschikbaar is voor de NIC. Ik gebruik meestal een DS5v2 voor het testen (en verwijder dan eenmaal gedaan met testen om geld te besparen) om voldoende bandbreedte te garanderen op de VM.
 - **NIC** - Zorg ervoor dat u het privé-IP kent dat is toegewezen aan de betreffende NIC.
 - **NIC NSG** - Er kunnen specifieke NSG's worden toegepast op NIC-niveau, zorg ervoor dat de NSG-regelset geschikt is voor het verkeer dat u probeert te passeren. Zorg er bijvoorbeeld voor dat poorten 5201 voor iPerf, 3389 voor RDP of 22 voor SSH open staan om testverkeer te laten passeren.
 - **VNet Subnet** - De NIC wordt toegewezen aan een specifiek subnet, zorg ervoor dat u weet welke en de regels die zijn gekoppeld aan dat subnet.
 - **Subnet NSG** - Net als de NIC kunnen ook NSG's op het subnet worden toegepast. Zorg ervoor dat de NSG-regelset geschikt is voor het verkeer dat u probeert te passeren. (voor verkeer dat aan NIC binnengaat is het subnet NSG eerst van toepassing, dan NIC NSG, omgekeerd voor verkeer uitgaand van VM is NIC NSG eerst van toepassing dan subnet NSG komt in spel).
 - **Subnet UDR** - User Defined Routes kunnen verkeer naar een tussenliggende hop leiden (zoals een firewall of load-balancer). Zorg ervoor dat u weet of er een UDR in de plaats voor uw verkeer en zo ja waar het gaat en wat die volgende hop zal doen om uw verkeer. (een firewall kan bijvoorbeeld wat verkeer passeren en ander verkeer tussen dezelfde twee hosts weigeren).
 - **Gateway subnet / NSG / UDR** - Net als het VM subnet, kan de gateway subnet hebben NSGs en UDRRs. Zorg ervoor dat u weet of ze er zijn en welke effecten ze hebben op uw verkeer.
 - **VNet Gateway (ExpressRoute)** - Zodra peering (ExpressRoute) of VPN is ingeschakeld, zijn er niet veel instellingen die van invloed kunnen zijn op hoe of als verkeer routes. Als u meerdere ExpressRoute-circuits of VPN-tunnels hebt die zijn verbonden met dezelfde VNet-gateway, moet u zich bewust zijn van de instellingen voor het verbindingsgewicht, aangezien deze instelling van invloed is op de voorkeur van de verbinding en van invloed is op het pad dat uw verkeer neemt.
 - **Routefilter** (niet weergegeven) - Een routefilter is alleen van toepassing op Microsoft Peering op ExpressRoute, maar is van cruciaal belang om te controleren of u de routes die u verwacht op Microsoft Peering niet ziet. 

Op dit moment bevindt u zich op het WAN-gedeelte van de koppeling. Dit routeringsdomein kan uw serviceprovider, uw zakelijke WAN of internet zijn. Veel hop, technologieën en bedrijven die betrokken zijn bij deze links kan het enigszins moeilijk maken om problemen op te lossen. Vaak werkt u eerst uit te sluiten voor zowel Azure als uw bedrijfsnetwerken voordat u in deze verzameling bedrijven en hop springt.

In het voorgaande diagram, helemaal links is uw bedrijfsnetwerk. Afhankelijk van de grootte van uw bedrijf kan dit routeringsdomein een paar netwerkapparaten zijn tussen u en het WAN of meerdere lagen apparaten in een campus/bedrijfsnetwerk.

Gezien de complexiteit van deze drie verschillende netwerkomgevingen op hoog niveau, is het vaak optimaal om aan de randen te beginnen en te proberen te laten zien waar de prestaties goed zijn en waar het degradeert. Deze aanpak kan helpen bij het identificeren van het probleemrouterdomein van de drie en vervolgens uw probleemoplossing richten op die specifieke omgeving.

## <a name="tools"></a>Hulpprogramma's
De meeste netwerkproblemen kunnen worden geanalyseerd en geïsoleerd met behulp van basistools zoals ping en traceroute. Het is zeldzaam dat je nodig hebt om zo diep te gaan als een pakket analyse als Wireshark. Om te helpen bij het oplossen van problemen is de Azure Connectivity Toolkit (AzureCT) ontwikkeld om een aantal van deze hulpprogramma's in een eenvoudig pakket te plaatsen. Voor het testen van de prestaties, ik wil iPerf en PSPing gebruiken. iPerf is een veelgebruikt hulpmiddel en draait op de meeste besturingssystemen. iPerf is goed voor basisprestaties tests en is vrij eenvoudig te gebruiken. PSPing is een ping tool ontwikkeld door SysInternals. PSPing is een eenvoudige manier om ICMP- en TCP-pings uit te voeren in één ook eenvoudig te gebruiken commando. Beide tools zijn lichtgewicht en zijn "geïnstalleerd" gewoon door het omgaan met de bestanden om een directory op de host.

Ik heb al deze tools en methoden verpakt in een PowerShell-module (AzureCT) die u installeren en gebruiken.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT - de Azure Connectivity Toolkit
De AzureCT PowerShell-module heeft twee componenten [Beschikbaarheidtesten][Availability Doc] en [Prestatietesten.][Performance Doc] Dit document heeft alleen betrekking op prestatietests, dus laten we ons richten op de twee Opdrachten voor Koppelingprestaties in deze PowerShell-module.

Er zijn drie basisstappen om deze toolkit te gebruiken voor prestatietesten. 1) Installeer de PowerShell-module, 2) Installeer de ondersteunende toepassingen iPerf en PSPing 3) Voer de prestatietest uit.

1. De PowerShell-module installeren

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Deze opdracht downloadt de PowerShell-module en installeert deze lokaal.

2. De ondersteunende toepassingen installeren
    ```powershell
    Install-LinkPerformance
    ```
    Deze AzureCT-opdracht installeert iPerf en PSPing in een nieuwe map "C:\ACTTools", het opent ook de Windows Firewall-poorten om ICMP- en poort5201 -verkeer (iPerf) toe te staan.

3. De prestatietest uitvoeren

    Ten eerste, op de externe host moet u iPerf installeren en uitvoeren in de servermodus. Zorg er ook voor dat de externe host luistert op 3389 (RDP voor Windows) of 22 (SSH voor Linux) en verkeer toestaat op poort 5201 voor iPerf. Als de externe host windows is, installeert u de AzureCT en voert u de opdracht Install-LinkPerformance uit om iPerf in te stellen en de firewallregels die nodig zijn om iPerf in de servermodus te starten. 
    
    Zodra de externe machine klaar is, opent u PowerShell op de lokale machine en start u de test:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Met deze opdracht wordt een reeks gelijktijdige belasting- en latentietests uitgevoerd om de bandbreedtecapaciteit en latentie van uw netwerkkoppeling te schatten.

4. Bekijk de output van de tests

    De PowerShell-uitvoerindeling lijkt op:

    ![4][4]

    De gedetailleerde resultaten van alle iPerf- en PSPing-tests bevinden zich in afzonderlijke tekstbestanden in de map met AzureCT-hulpprogramma's op 'C:\ACTTools'.

## <a name="troubleshooting"></a>Problemen oplossen
Als de prestatietest u geen verwachte resultaten geeft, moet u uitzoeken waarom een progressief stapsgewijs proces moet zijn. Gezien het aantal componenten in het pad, een systematische aanpak biedt over het algemeen een snellere weg naar resolutie dan springen rond en potentieel onnodig doen dezelfde testen meerdere keren.

>[!NOTE]
>Het scenario hier is een prestatieprobleem, geen verbindingsprobleem. De trappen zouden anders zijn als het verkeer helemaal niet voorbij kwam.
>
>

Ten eerste, daag je veronderstellingen uit. Is uw verwachting redelijk? Als u bijvoorbeeld een 1-Gbps ExpressRoute-circuit en 100 ms latentie hebt, is het onredelijk om de volledige 1 Gbps verkeer te verwachten, gezien de prestatiekenmerken van TCP ten opzichte van hoge latentiekoppelingen. Zie de [sectie Referenties](#references) voor meer informatie over prestatieveronderstellingen.

Vervolgens raad ik aan om aan de randen tussen routeringsdomeinen te beginnen en te proberen het probleem te isoleren naar één belangrijk routeringsdomein; het Bedrijfsnetwerk, het WAN of het Azure-netwerk. Mensen vaak de schuld van de "zwarte doos" in het pad, terwijl de schuld van de zwarte doos is gemakkelijk te doen, kan het aanzienlijk vertragen resolutie vooral als het probleem is eigenlijk in een gebied dat je de mogelijkheid om veranderingen aan te brengen. Zorg ervoor dat u uw due diligence te doen voordat het overhandigen aan uw dienstverlener of ISP.

Zodra u het belangrijkste routeringsdomein hebt geïdentificeerd dat het probleem lijkt te bevatten, moet u een diagram van het betrokken gebied maken. Ofwel op een whiteboard, kladblok, of Visio als een diagram biedt een concrete "battle map" om een methodische aanpak om het probleem verder te isoleren. U testpunten plannen en de kaart bijwerken terwijl u gebieden wist of dieper graaft naarmate het testen vordert.

Nu u een diagram hebt, begint u het netwerk in segmenten te verdelen en het probleem te beperken. Zoek uit waar het werkt en waar het niet werkt. Blijf uw testpunten verplaatsen om te isoleren tot aan de gewraakte component.

Vergeet ook niet om te kijken naar andere lagen van het OSI-model. Het is eenvoudig om je te concentreren op het netwerk en lagen 1 - 3 (Fysieke, Gegevens- en Netwerklagen), maar de problemen kunnen ook oplopen bij Laag 7 in de toepassingslaag. Houd een open geest en controleer veronderstellingen.

## <a name="advanced-expressroute-troubleshooting"></a>Geavanceerde ExpressRoute-probleemoplossing
Als u niet zeker weet waar de rand van de cloud zich eigenlijk bevindt, kan het isoleren van de Azure-componenten een uitdaging zijn. Wanneer ExpressRoute wordt gebruikt, is de rand een netwerkcomponent genaamd de Microsoft Enterprise Edge (MSEE). **Bij het gebruik van ExpressRoute**is de MSEE het eerste aanspreekpunt in het netwerk van Microsoft en de laatste hop die het Microsoft-netwerk verlaat. Wanneer u een verbindingsobject maakt tussen uw VNet-gateway en het ExpressRoute-circuit, maakt u daadwerkelijk een verbinding met de MSEE. Het herkennen van de MSEE als de eerste of laatste hop (afhankelijk van welke richting u gaat) is van cruciaal belang voor het isoleren van Azure Network-problemen om te bewijzen dat het probleem zich in Azure bevindt of verder stroomafwaarts in het WAN- of bedrijfsnetwerk. 

![2][2]

>[!NOTE]
> Merk op dat de MSEE zich niet in de Azure-cloud bevindt. ExpressRoute is eigenlijk aan de rand van het Microsoft-netwerk niet echt in Azure. Zodra u verbinding hebt gemaakt met ExpressRoute met een MSEE, bent u verbonden met het netwerk van Microsoft, van daaruit u naar een van de cloudservices gaan, zoals Office 365 (met Microsoft Peering) of Azure (met Privé en/of Microsoft Peering).
>
>

Als twee VNets (VNets A en B in het diagram) zijn verbonden met **hetzelfde** ExpressRoute-circuit, u een reeks tests uitvoeren om het probleem in Azure te isoleren (of bewijzen dat het niet in Azure staat)
 
### <a name="test-plan"></a>Testplan
1. Voer de Get-LinkPerformance-test uit tussen VM1 en VM2. Deze test geeft inzicht in of het probleem lokaal is of niet. Als deze test aanvaardbare latentie- en bandbreedteresultaten oplevert, u het lokale VNet-netwerk als goed markeren.
2. Ervan uitgaande dat het lokale VNet-verkeer goed is, voert u de Get-LinkPerformance-test uit tussen VM1 en VM3. Deze test oefent de verbinding via het Microsoft-netwerk uit tot aan de MSEE en terug naar Azure. Als deze test aanvaardbare latentie- en bandbreedteresultaten oplevert, u het Azure-netwerk als goed markeren.
3. Als Azure is uitgesloten, u een vergelijkbare reeks tests uitvoeren op uw bedrijfsnetwerk. Als dat ook goed test, is het tijd om samen te werken met uw serviceprovider of ISP om uw WAN-verbinding te diagnosticeren. Voorbeeld: Voer deze test uit tussen twee filialen of tussen uw bureau en een datacenterserver. Afhankelijk van wat u test, zoek je eindpunten (servers, pc's, enz.) die dat pad kunnen uitoefenen.

>[!IMPORTANT]
> Het is van cruciaal belang dat u voor elke test het tijdstip van de test markeert en de resultaten op een gemeenschappelijke locatie registreert (ik vind OneNote of Excel leuk). Elke testrun moet dezelfde uitvoer hebben, zodat u de resulterende gegevens over testuitvoeringen vergelijken en geen gaten in de gegevens hebt. Consistentie voor meerdere tests is de belangrijkste reden waarom ik de AzureCT gebruik voor het oplossen van problemen. De magie is niet in de exacte belasting scenario's die ik uitte, maar in plaats daarvan de *magie* is het feit dat ik een *consistente test en gegevens output* van elke test. Het opnemen van de tijd en het hebben van consistente gegevens elke keer is vooral handig als je later merkt dat het probleem sporadisch is. Wees ijverig met uw gegevens verzameling op voorhand en je zult voorkomen dat uren van het opnieuw testen van dezelfde scenario's (ik leerde deze harde manier vele jaren geleden).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Het probleem is geïsoleerd, wat nu?
Hoe meer u het probleem isoleren, hoe gemakkelijker het is om op te lossen, hoe vaak u het punt bereikt waarop u niet dieper of verder gaan met uw probleemoplossing. Voorbeeld: u ziet de link tussen uw serviceprovider die hop door Europa neemt, maar uw verwachte pad is allemaal in Azië. Dit punt is wanneer je moet bereiken voor hulp. Wie u vraagt is afhankelijk van het routeringsdomein waaraan u het probleem hebt geïsoleerd, of zelfs beter als u het beperken tot een specifiek onderdeel.

Voor problemen met bedrijfsnetwerken kan uw interne IT-afdeling of serviceprovider die uw netwerk ondersteunt (mogelijk de hardwarefabrikant) helpen bij apparaatconfiguratie of hardwarereparatie.

Voor het WAN kan het delen van uw testresultaten met uw serviceprovider of internetprovider helpen om aan de slag te gaan en te voorkomen dat u een aantal van dezelfde grond bestrijkt die u al hebt getest. Wees echter niet beledigd als ze uw resultaten zelf willen verifiëren. "Vertrouwen maar verifiëren" is een goed motto bij het oplossen van problemen op basis van de gerapporteerde resultaten van andere mensen.

Met Azure is het tijd om de Azure-netwerkdocumentatie te bekijken en, indien het nog nodig is, de [Azure-netwerkdocumentatie][Network Docs] te bekijken en vervolgens, indien nodig, [een ondersteuningsticket te openen.][Ticket Link]

## <a name="references"></a>Verwijzingen
### <a name="latencybandwidth-expectations"></a>Latentie/bandbreedteverwachtingen
>[!TIP]
> Geografische latentie (mijlen of kilometers) tussen de eindpunten die u test is veruit het grootste onderdeel van latentie. Hoewel er apparatuur latentie (fysieke en virtuele componenten, aantal hop, enz.) betrokken is, heeft geografie bewezen de grootste component van de algehele latentie te zijn bij het omgaan met WAN-verbindingen. Het is ook belangrijk op te merken dat de afstand is de afstand van de vezel lopen niet de rechte lijn of routekaart afstand. Deze afstand is ongelooflijk moeilijk te krijgen met enige nauwkeurigheid. Als gevolg daarvan gebruik ik over het algemeen een stad afstand rekenmachine op het internet en weet dat deze methode is een grove onjuiste maatregel, maar is genoeg om een algemene verwachting.
>
>

Ik heb een ExpressRoute setup in Seattle, Washington in de VS. De volgende tabel toont de latentie en bandbreedte die ik zag testen naar verschillende Azure-locaties. Ik heb de geografische afstand tussen elk einde van de test geschat.

Testopstelling:
 - Een fysieke server met Windows Server 2016 met een NIC van 10 Gbps, verbonden met een ExpressRoute-circuit.
 - Een 10Gbps Premium ExpressRoute circuit op de locatie geïdentificeerd met Private Peering ingeschakeld.
 - Een Azure VNet met een UltraPerformance-gateway in het opgegeven gebied.
 - Een DS5v2 VM met Windows Server 2016 op het VNet. De VM is niet-domein samengevoegd, opgebouwd uit de standaard Azure-afbeelding (geen optimalisatie of aanpassing) met AzureCT geïnstalleerd.
 - Alle tests werden uitgevoerd met de opdracht AzureCT Get-LinkPerformance met een belastingstest van 5 minuten voor elk van de zes testuitvoeringen. Bijvoorbeeld:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - De gegevensstroom voor elke test had de belasting die van de on-premises fysieke server (iPerf-client in Seattle) naar de Azure VM (iPerf-server in de vermelde Azure-regio) stroomde.
 - De kolomgegevens 'Latentie' zijn afkomstig van de No Load-test (een TCP-latentietest zonder dat iPerf wordt uitgevoerd).
 - De kolomgegevens 'Maximale bandbreedte' zijn afkomstig van de 16 TCP-stroombelastingstest met een venstergrootte van 1 mb.

![3][3]

### <a name="latencybandwidth-results"></a>Latentie/bandbreedteresultaten
>[!IMPORTANT]
> Deze nummers zijn alleen voor algemene referentie. Veel factoren beïnvloeden latentie en hoewel deze waarden over het algemeen consistent zijn in de loop van de tijd, kunnen de omstandigheden binnen Azure of het Service Providers-netwerk op elk gewenst moment verkeer via verschillende paden verzenden, waardoor latentie en bandbreedte kunnen worden beïnvloed. Over het algemeen leiden de effecten van deze veranderingen niet tot significante verschillen.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Locatie|Azure<br/>Regio|Geschatte<br/>Afstand (km)|Latentie|1 Sessie<br/>Bandbreedte|Maximum<br/>Bandbreedte|
| Seattle | VS - west 2        |    191 km |   5 ms | 262,0 Mbits/sec |  3,74 Gbits/sec |
| Seattle | VS - west          |  1.094 km |  18 ms |  82,3 Mbits/sec |  3,70 Gbits/sec |
| Seattle | VS - centraal       |  2.357 km |  40 ms |  38,8 Mbits/sec |  2,55 Gbits/sec |
| Seattle | VS - zuid-centraal |  2.877 km |  51 ms |  30,6 Mbits/sec |  2,49 Gbits/sec |
| Seattle | VS - noord-centraal |  2.792 km |  55 ms |  27,7 Mbits/sec |  2,19 Gbits/sec |
| Seattle | VS - oost 2        |  3.769 km |  73 ms |  21,3 Mbits/sec |  1,79 Gbits/sec |
| Seattle | VS - oost          |  3.699 km |  74 ms |  21,1 mbbes/seconde |  1,78 Gbits/sec |
| Seattle | Japan - oost       |  7.705 km | 106 ms |  14,6 Mbits/sec |  1,22 Gbits/sec |
| Seattle | Verenigd Koninkrijk Zuid         |  7.708 km | 146 ms |  10,6 Mbits/sec |   896 Mbits/sec |
| Seattle | Europa -west      |  7.834 km | 153 ms |  10,2 Mbits/sec |   761 Mbits/sec |
| Seattle | Australië - oost   | 12.484 km | 165 ms |   9,4 Mbits/sec |   794 Mbits/sec |
| Seattle | Azië - zuidoost   | 12.989 km | 170 ms |   9,2 Mbits/sec |   756 Mbits/sec |
| Seattle | Brazilië Zuid *   | 10.930 km | 189 ms |   8,2 Mbits/sec |   699 Mbits/sec |
| Seattle | India - zuid      | 12.918 km | 202 ms |   7,7 Mbits/sec |   634 Mbits/sec |

\*De latentie naar Brazilië is een goed voorbeeld waar de rechte lijn afstand aanzienlijk verschilt van de fiber run afstand. Ik zou verwachten dat de latentie zou worden in de buurt van 160 ms, maar is eigenlijk 189 ms. Dit verschil met mijn verwachting zou kunnen wijzen op een netwerk probleem ergens, maar waarschijnlijk dat de vezel run niet naar Brazilië in een rechte lijn en heeft een extra 1.000 km of zo van de reis naar Brazilië van Seattle.

## <a name="next-steps"></a>Volgende stappen
1. Download de Azure Connectivity Toolkit van GitHub op[https://aka.ms/AzCT][ACT]
2. Volg de instructies voor [het testen van de koppelingsprestaties][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Azure-netwerkcomponenten"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "Probleemoplossing expressroute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Perf Test omgeving"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell-uitvoer"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT

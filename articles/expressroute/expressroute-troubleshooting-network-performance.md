---
title: 'Problemen met de netwerk verbinding oplossen: Azure'
description: Deze pagina biedt een gestandaardiseerde methode voor het testen van de prestaties van Azure-netwerk koppelingen.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: bb68919fba731caa32dcca3f4c991b8881afc6f9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869643"
---
# <a name="troubleshooting-network-performance"></a>Problemen met netwerk prestaties oplossen
## <a name="overview"></a>Overzicht
Azure biedt stabiele en snelle manieren om vanuit uw on-premises netwerk verbinding te maken met Azure. Methoden zoals site-naar-site VPN en ExpressRoute worden met succes toegepast door klanten met kleine of grote ondernemingen om hun bedrijf te exploiteren in Azure. Maar wat gebeurt er wanneer de prestaties niet voldoen aan uw verwachting of een eerdere ervaring? Dit document helpt u bij het standaardiseren van de manier waarop u uw specifieke omgeving test en basis lijn.

Dit document laat zien hoe u de netwerk latentie en band breedte tussen twee hosts eenvoudig en consistent kunt testen. Dit document bevat ook een aantal adviezen over manieren om het Azure-netwerk te bekijken en probleem punten te isoleren. Het script en de hulpprogram ma's van Power shell vereisen twee hosts op het netwerk (aan beide uiteinden van de koppeling die wordt getest). Eén host moet een Windows-Server of-bureau blad zijn, de andere kan Windows of Linux zijn. 

>[!NOTE]
>De aanpak van het oplossen van problemen, de hulpprogram ma's en de gebruikte methoden zijn persoonlijke voor keuren. In dit document worden de aanpak en de hulpprogram ma's beschreven die vaak worden gebruikt. Uw benadering wijkt waarschijnlijk af van de verschillende benaderingen van het oplossen van problemen. Als u echter geen benadering hebt, kunt u met dit document aan de slag gaan met het pad om uw eigen methoden, hulpprogram ma's en voor keuren te bouwen om netwerk problemen op te lossen.
>
>

## <a name="network-components"></a>Netwerkonderdelen
Voordat u Blijf spitten in het oplossen van problemen, bespreken we enkele algemene voor waarden en onderdelen. Deze bespreking zorgt ervoor dat er wordt geadviseerd over elk onderdeel in de end-to-end keten dat connectiviteit in azure mogelijk maakt.
![1][1]

Op het hoogste niveau worden er drie grote netwerk routerings domeinen beschreven.

- het Azure-netwerk (blauwe cloud aan de rechter kant)
- Internet of WAN (groene Cloud in het midden)
- het bedrijfs netwerk (perzik-cloud aan de linkerkant)

Bekijk het diagram van rechts naar links om elk onderdeel kort te bespreken:
 - **Virtuele machine** : de server kan meerdere nic's hebben, Controleer of alle statische routes, standaard routes en instellingen van het besturings systeem verkeer verzenden en ontvangen zoals u dat wilt. Daarnaast heeft elke VM-SKU een bandbreedte beperking. Als u een kleinere VM-SKU gebruikt, wordt uw verkeer beperkt door de band breedte die beschikbaar is voor de NIC. Normaal gesp roken gebruikt u een DS5v2 voor het testen (en verwijdert u vervolgens eenmaal met testen om geld te besparen) om te zorgen voor voldoende band breedte op de VM.
 - **NIC** : Zorg ervoor dat u weet wat het privé-IP-adres is dat is toegewezen aan de NIC in kwestie.
 - **NIC-NSG** : er is mogelijk specifieke nsg's toegepast op het niveau van de NIC, zodat de NSG-regel is ingesteld op het verkeer dat u probeert door te geven. Zorg er bijvoorbeeld voor dat poort 5201 voor iPerf, 3389 voor RDP of 22 voor SSH is geopend, zodat test verkeer kan worden door gegeven.
 - **VNet-subnet** : de NIC wordt toegewezen aan een specifiek subnet. Zorg ervoor dat u weet welke en welke regels aan dat subnet zijn gekoppeld.
 - **SUBNET NSG** -net als de NIC kan nsg's ook worden toegepast op het subnet. Zorg ervoor dat de regel set NSG geschikt is voor het verkeer dat u probeert door te geven. (voor verkeer dat binnenkomt voor de NIC waarop de subnet-NSG van toepassing is, wordt de NIC NSG daarentegen voor uitgaand verkeer van de virtuele machine waarop de NIC NSG van toepassing is, waarna het subnet NSG wordt afgespeeld).
 - **SUBNET UDR** : door de gebruiker gedefinieerde routes kunnen verkeer omleiden naar een tussenliggende hop (zoals een firewall of een load balancer). Zorg ervoor dat u weet of er een UDR is voor uw verkeer en zo ja, wanneer het gaat en wat de volgende hop naar uw verkeer doet. (een firewall kan bijvoorbeeld verkeer door lopen en ander verkeer tussen dezelfde twee hosts weigeren).
 - **Gateway subnet/NSG/UDR** -net als het VM-subnet kan het gateway-subnet Nsg's en udr's hebben. Zorg ervoor dat u weet of ze daar zijn en wat het effect hiervan op uw verkeer is.
 - **VNet-gateway (ExpressRoute)** : zodra peering (ExpressRoute) of VPN is ingeschakeld, zijn er geen instellingen die van invloed kunnen zijn op de manier waarop of wanneer er verkeer wordt gerouteerd. Als u meerdere ExpressRoute-circuits of VPN-tunnels hebt die zijn verbonden met dezelfde VNet-gateway, moet u rekening houden met de instellingen voor verbindings gewicht als deze instelling van invloed is op de verbindings voorkeur en die van invloed is op het pad dat het verkeer in beslag neemt.
 - **Route filter** (niet weer gegeven): een route filter is alleen van toepassing op micro soft-peering op ExpressRoute, maar is essentieel om te controleren of u de routes die u verwacht op micro soft-peering niet ziet. 

U bevindt zich op dit punt in het WAN-gedeelte van de koppeling. Dit routerings domein kan uw service provider, het WAN van uw bedrijf of Internet zijn. Veel hops, technologieën en bedrijven die bij deze koppelingen betrokken zijn, kunnen het moeilijk maken om problemen op te lossen. Vaak kunt u eerst zowel Azure als uw bedrijfs netwerken regel matig afwerken voordat u overgaat naar deze verzameling bedrijven en hops.

In het voor gaande diagram is helemaal links uw bedrijfs netwerk. Afhankelijk van de grootte van uw bedrijf, kunnen dit routerings domein een aantal netwerk apparaten tussen u en de WAN-of meerdere lagen van apparaten in een campus/Enter prise-netwerk zijn.

Gezien de complexiteit van deze drie verschillende netwerk omgevingen op hoog niveau, is het vaak optimaal om aan de randen te beginnen en om te zien waar de prestaties goed zijn en waar deze worden verminderd. Deze aanpak kan helpen bij het identificeren van het routerings domein van het probleem van de drie en richt zich vervolgens op het oplossen van problemen met die specifieke omgeving.

## <a name="tools"></a>Tools
De meeste netwerk problemen kunnen worden geanalyseerd en geïsoleerd met behulp van basis hulpprogramma's als ping en traceroute. Het lijkt erop dat u zo diep als een pakket analyse moet gaan, zoals wireshark. Voor hulp bij het oplossen van problemen is de Azure Connectivity Toolkit (AzureCT) ontwikkeld om enkele van deze hulpprogram ma's in een eenvoudig pakket te zetten. Voor het testen van de prestaties, ik wil iPerf en PSPing gebruiken. iPerf is een veelgebruikt hulp programma dat wordt uitgevoerd op de meeste besturings systemen. iPerf is geschikt voor elementaire uitvoeringen testen en is redelijk eenvoudig te gebruiken. PSPing is een ping-hulp programma dat is ontwikkeld door SysInternals. PSPing is een eenvoudige manier om ICMP-en TCP-pings uit te voeren in een eenvoudig te gebruiken opdracht. Beide hulpprogram ma's zijn licht gewicht en worden ' geïnstalleerd ' door de bestanden te omgaan naar een map op de host.

Ik heb al deze hulpprogram ma's en methoden genest in een Power shell-module (AzureCT) die u kunt installeren en gebruiken.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT-de Azure Connectivity Toolkit
De AzureCT Power shell-module heeft twee [Beschik baarheid][Availability Doc] van onderdelen en [prestatie testen][Performance Doc]. Dit document is alleen gericht op prestatie tests, zodat u zich kunt richten op de twee opdrachten voor koppelings prestaties in deze Power shell-module.

Er zijn drie basis stappen voor het gebruik van deze Toolkit voor het testen van de prestaties. 1) de Power shell-module installeren, 2) Installeer de ondersteunende toepassingen iPerf en PSPing 3) de prestatie test uitvoeren.

1. De Power shell-module installeren

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Met deze opdracht wordt de Power shell-module gedownload en lokaal geïnstalleerd.

2. De ondersteunende toepassingen installeren
    ```powershell
    Install-LinkPerformance
    ```
    Met deze AzureCT-opdracht worden iPerf en PSPing in een nieuwe directory "C:\ACTTools" geïnstalleerd, wordt ook de Windows Firewall poorten geopend om verkeer van ICMP-en poort 5201 (iPerf) toe te staan.

3. De prestatie test uitvoeren

    Eerst moet u op de externe host iPerf installeren en uitvoeren in de server modus. Zorg er ook voor dat de externe host op 3389 (RDP voor Windows) of 22 (SSH voor Linux) luistert en verkeer op poort 5201 voor iPerf toestaat. Als de externe host Windows is, installeert u de AzureCT en voert u de opdracht install-LinkPerformance uit om iPerf in te stellen en de firewall regels die nodig zijn om iPerf in de server modus te kunnen starten. 
    
    Zodra de externe computer klaar is, opent u Power shell op de lokale computer en start u de test:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Met deze opdracht voert u een reeks gelijktijdige laad-en latentie tests uit om de bandbreedte capaciteit en latentie van uw netwerk koppeling te schatten.

4. Bekijk de uitvoer van de tests

    De Power shell-uitvoer indeling ziet er ongeveer als volgt uit:

    ![4][4]

    De gedetailleerde resultaten van alle iPerf-en PSPing-tests vindt u in afzonderlijke tekst bestanden in de map hulpprogram ma's van AzureCT op ' C:\ACTTools. '

## <a name="troubleshooting"></a>Problemen oplossen
Als de prestatie test niet de verwachte resultaten oplevert, kunt u het beste stapsgewijs een progressief proces maken. Op basis van het aantal onderdelen in het pad biedt een systematische benadering doorgaans een sneller pad naar de oplossing dan het probleem en mogelijk onnodig dezelfde tests meerdere keren uitvoeren.

>[!NOTE]
>Het scenario hier is een prestatie probleem, geen connectiviteits probleem. De stappen zijn anders als verkeer helemaal niet is door gegeven.
>
>

Vraag eerst uw veronderstellingen af. Is uw verwachting redelijk? Als u bijvoorbeeld een ExpressRoute-circuit van 1 Gbps en 100 MS van de latentie hebt, is het onredelijk dat u de volledige 1 Gbps aan verkeer krijgt, gezien de prestatie kenmerken van TCP via koppelingen met een hoge latentie. Zie het [gedeelte met verwijzingen](#references) voor meer informatie over prestatie aannames.

Vervolgens raden we u aan om te beginnen bij de randen tussen routerings domeinen en het probleem te isoleren met één groot routerings domein. het bedrijfs netwerk, het WAN of het Azure-netwerk. Mensen liggen vaak het ' Black Box ' in het pad, terwijl blaming het zwarte vak gemakkelijk te doen is, kan de resolutie aanzienlijk vertragen als het probleem zich in een gebied bevindt dat u de mogelijkheid hebt om wijzigingen aan te brengen. Zorg ervoor dat u uw terdege toeneemt voordat u uw service provider of Internet provider uitlevert.

Zodra u het grote routerings domein hebt geïdentificeerd dat het probleem bevat, moet u een diagram van het betreffende gebied maken. Op een White Board, Klad blok of Visio als diagram kunt u een beton ' vechten-kaart ' maken, zodat een methode wordt gebruikt om het probleem verder te isoleren. U kunt test punten plannen en de kaart bijwerken wanneer u gebieden wist of dieper gaat naarmate de tests worden uitgevoerd.

Nu u een diagram hebt, begint u met het verdelen van het netwerk in segmenten en beperkt u het probleem. Ontdek waar het werkt en waar dat niet het geval is. Blijf uw test punten verplaatsen om ze te isoleren tot het conflicterende onderdeel.

Vergeet ook niet om andere lagen van het OSI-model te bekijken. Het is eenvoudig om te richten op het netwerk en de lagen 1-3 (fysieke, gegevens en netwerk lagen), maar de problemen kunnen ook op laag 7 in de toepassingslaag worden weer gegeven. Houd een open gedachte en controleer hypo theses.

## <a name="advanced-expressroute-troubleshooting"></a>Geavanceerde ExpressRoute problemen oplossen
Als u niet zeker weet waar de rand van de Cloud zich daad werkelijk bevindt, kan het isoleren van de onderdelen van Azure een uitdaging zijn. Wanneer ExpressRoute wordt gebruikt, is de rand een netwerk onderdeel dat de micro soft Enter prise Edge (MSEE) wordt genoemd. **Wanneer u ExpressRoute gebruikt**, is de MSEE het eerste contact punt in het netwerk van micro soft en de laatste hop die het micro soft-netwerk verlaat. Wanneer u een verbindings object tussen uw VNet-gateway en het ExpressRoute-circuit maakt, maakt u een verbinding met de MSEE. Het herkennen van de MSEE als de eerste of laatste hop (afhankelijk van de richting die u gaat) is van cruciaal belang voor het isoleren van Azure-netwerk problemen om te bewijzen dat het probleem zich in azure of verder downstream in het WAN of het bedrijfs netwerk bevindt. 

![2][2]

>[!NOTE]
> U ziet dat de MSEE zich niet in de Azure-Cloud bevindt. ExpressRoute is daad werkelijk aan de rand van het micro soft-netwerk niet echt in Azure. Zodra u verbinding hebt gemaakt met ExpressRoute voor een MSEE, hebt u verbinding met het netwerk van micro soft. daar kunt u vervolgens naar een van de Cloud Services gaan, zoals Office 365 (met micro soft-peering) of Azure (met persoonlijke en/of micro soft-peering).
>
>

Als twee VNets (VNets A en B in het diagram) zijn verbonden met **hetzelfde** ExpressRoute-circuit, kunt u een reeks tests uitvoeren om het probleem in azure te isoleren (of bewijzen dat het zich niet in azure bevindt)
 
### <a name="test-plan"></a>Test plan
1. Voer de Get-LinkPerformance-test uit tussen VM1 en VM2. Deze test geeft inzicht in het geval dat het probleem lokaal is of niet. Als deze test acceptabele latentie en bandbreedte resultaten produceert, kunt u het lokale VNet-netwerk markeren als goed.
2. Ervan uitgaande dat het lokale VNet-verkeer goed is, voert u de Get-LinkPerformance-test tussen VM1 en VM3 uit. Met deze test wordt de verbinding via het micro soft-netwerk tot stand gebracht met de MSEE en weer in Azure. Als deze test acceptabele latentie en bandbreedte resultaten produceert, kunt u het Azure-netwerk markeren als goed.
3. Als Azure wordt uitgesloten, kunt u een vergelijk bare reeks testen op uw bedrijfs netwerk uitvoeren. Als dat ook goed wordt getest, is het tijd om samen te werken met uw service provider of provider om uw WAN-verbinding te diagnosticeren. Voor beeld: Voer deze test uit tussen twee filialen of tussen uw bureau en een Data Center-Server. Afhankelijk van wat u wilt testen, zoekt u eind punten (servers, Pc's, enzovoort) die dit pad kunnen uitoefenen.

>[!IMPORTANT]
> Het is essentieel dat voor elke test het tijdstip waarop u de test uitvoert, de resultaten op een algemene locatie (zoals OneNote of Excel) vastlegt. Elke test uitvoering moet een identieke uitvoer hebben, zodat u de resulterende gegevens in de test uitvoeringen kunt vergelijken en geen ' gaten ' in de gegevens hebt. Consistentie tussen meerdere tests is de belangrijkste reden waarom ik de AzureCT gebruik voor het oplossen van problemen. Het Magic is niet in de exacte werk scenario's die ik uitvoer, maar in plaats *daarvan is het* een *consistente test en gegevens uitvoer* van elke test. Het vastleggen van de tijd en het hebben van consistente gegevens elke enige tijd is met name handig als u later merkt dat het probleem sporadisch is. Wees zo snel mogelijk met uw gegevens verzameling, zodat u kunt voor komen dat dezelfde scenario's opnieuw worden getest (ik heb deze vaste weg al jaren geleden geleerd).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Is het probleem geïsoleerd, nu wat?
Hoe meer u het probleem kunt isoleren, hoe eenvoudiger het is om te herstellen, maar vaak hebt u het punt waar u niet meer kunt doen met uw probleem oplossing. Voor beeld: u ziet de koppeling tussen uw service provider en hops, maar uw verwachte pad is allemaal in Azië. Dit punt is wanneer u hulp nodig hebt. Wie u vraagt, is afhankelijk van het routerings domein waar u het probleem hebt geïsoleerd, of nog beter als u het kunt beperken tot een specifiek onderdeel.

Bij problemen met het bedrijfs netwerk is uw interne IT-afdeling of service provider die uw netwerk ondersteunt (wat mogelijk de hardwarefabrikant kan zijn) mogelijk bij het configureren van apparaatconfiguratie of het herstellen van hardware.

Voor het WAN kan het delen van de test resultaten met uw service provider of ISP ertoe bijdragen dat ze worden gestart en wordt voor komen dat een deel van hetzelfde terrein dat u al hebt getest, wordt bedekt. Als u uw resultaten zelf wilt verifiëren, is dit echter niet het geval. ' Vertrouwen, maar controleren ' is een goede motto bij het oplossen van problemen op basis van de gerapporteerde resultaten van andere gebruikers.

Wanneer u het probleem met Azure hebt geïsoleerd, is het tijd om de documentatie van het [Azure-netwerk][Network Docs] te bekijken en vervolgens als u nog steeds [een ondersteunings ticket hebt geopend][Ticket Link].

## <a name="references"></a>Naslaginformatie
### <a name="latencybandwidth-expectations"></a>Verwachtingen voor latentie/band breedte
>[!TIP]
> Een geografische latentie (mijl of kilo meter) tussen de eind punten die u wilt testen, is het grootste deel van de latentie. Hoewel er sprake is van een latentie van apparatuur (fysieke en virtuele onderdelen, het aantal hops enz.), is de geografie bewezen als het grootste onderdeel van de totale latentie bij het omgaan met WAN-verbindingen. Het is ook belang rijk om te weten dat de afstand de afstand van de fiber-uitvoering is en niet de lijn afstand van de lineaire of de weg. Deze afstand is hard moeilijk om een nauw keurigheid te bereiken. Als gevolg hiervan gebruiken we doorgaans een lokale reken machine op internet en weet u dat deze methode een nagenoeg onnauwkeurige meting is, maar voldoende is om een algemene verwachting in te stellen.
>
>

Ik heb een ExpressRoute-installatie in Seattle, Washington in de Verenigde Staten. De volgende tabel toont de latentie en band breedte die ik heb getest op verschillende Azure-locaties. Ik heb een schatting van de geografische afstand tussen elk einde van de test.

Setup testen:
 - Een fysieke server met Windows Server 2016 met een NIC van 10 Gbps, verbonden met een ExpressRoute-circuit.
 - Een 10 Gbps Premium ExpressRoute-circuit in de locatie die is geïdentificeerd met persoonlijke peering ingeschakeld.
 - Een Azure VNet met een Ultra Performance-gateway in de opgegeven regio.
 - Een DS5v2-VM met Windows Server 2016 op het VNet. De virtuele machine is toegevoegd aan een ander domein, gebouwd op basis van de standaard installatie kopie van Azure (geen optimalisatie of aanpassing) met AzureCT geïnstalleerd.
 - Alle tests hebben de opdracht AzureCT Get-LinkPerformance gebruikt met een belasting test van 5 minuten voor elk van de zes test uitvoeringen. Bijvoorbeeld:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - De gegevens stroom voor elke test had de belasting van de on-premises fysieke server (iPerf-client in Seattle) tot de Azure VM (iPerf-server in de vermelde Azure-regio).
 - De gegevens van de latentie kolom zijn afkomstig uit de test geen belasting (een TCP-latentie test zonder iPerf).
 - De kolom maximale band breedte is van de 16 TCP-stroom belasting test met een window-grootte van 1 MB.

![3][3]

### <a name="latencybandwidth-results"></a>Resultaten van latentie/band breedte
>[!IMPORTANT]
> Deze nummers gelden alleen voor algemene naslag informatie. Veel factoren beïnvloeden latentie, en hoewel deze waarden in de loop van de tijd doorgaans consistent zijn, kunnen de voor waarden in azure of het netwerk van de service providers verkeer via verschillende paden op elk gewenst moment verzenden, zodat latentie en band breedte kan worden beïnvloed. Over het algemeen zijn de gevolgen van deze wijzigingen niet van belang rijke verschillen.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Locatie|Azure<br/>Regio|Bepaald<br/>Afstand (km)|Latentie|1 sessie<br/>Bandbreedte|Maximum<br/>Bandbreedte|
| Seattle | US - west 2        |    191 km |   5 ms | 262,0 Mbit per seconde |  3,74 Gbits per seconde |
| Seattle | US - west          |  1\.094 km |  18 MS |  82,3 Mbit per seconde |  3,70 Gbits per seconde |
| Seattle | US - centraal       |  2\.357 km |  40 MS |  38,8 Mbit per seconde |  2,55 Gbits per seconde |
| Seattle | US - zuid-centraal |  2\.877 km |  51 ms |  30,6 Mbit per seconde |  2,49 Gbits per seconde |
| Seattle | US - noord-centraal |  2\.792 km |  55 MS |  27,7 Mbit per seconde |  2,19 Gbits per seconde |
| Seattle | VS - oost 2        |  3\.769 km |  73 MS |  21,3 Mbit per seconde |  1,79 Gbits per seconde |
| Seattle | VS - oost          |  3\.699 km |  74 MS |  21,1 Mbit per seconde |  1,78 Gbits per seconde |
| Seattle | Japan - Oost       |  7\.705 km | 106 MS |  14,6 Mbit per seconde |  1,22 Gbits per seconde |
| Seattle | UK - zuid         |  7\.708 km | 146 ms |  10,6 Mbit per seconde |   896 Mbit per seconde |
| Seattle | Europa - west      |  7\.834 km | 153 MS |  10,2 Mbit per seconde |   761 Mbit per seconde |
| Seattle | Australië - oost   | 12.484 km | 165 MS |   9,4 Mbit per seconde |   794 Mbit per seconde |
| Seattle | Azië - zuidoost   | 12.989 km | 170 MS |   9,2 Mbit per seconde |   756 Mbit per seconde |
| Seattle | Brazilië-zuid *   | 10.930 km | 189 ms |   8,2 Mbit per seconde |   699 Mbit per seconde |
| Seattle | India - zuid      | 12.918 km | 202 MS |   7,7 Mbit per seconde |   634 Mbit per seconde |

\* de latentie van Brazilië is een goed voor beeld waarbij de lineaire afstand sterk afwijkt van de afstand van de fiber-uitvoering. Ik zou verwachten dat de latentie zich in de groep van 160 MS bevindt, maar wel in werkelijkheid 189 MS. Dit verschil ten opzichte van mijn verwachting kan ergens duiden op een netwerk probleem, maar het is waarschijnlijk dat de fiber-uitvoering niet naar Brazilië gaat in een rechte lijn en een extra 1.000 km of meer van de weg heeft om aan Brazilië van Seattle te gaan.

## <a name="next-steps"></a>Volgende stappen
1. Down load de Azure Connectivity Toolkit van GitHub op [https://aka.ms/AzCT][ACT]
2. Volg de instructies voor het testen van de [koppelings prestaties][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Azure-netwerk onderdelen"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute problemen oplossen"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Prestatie test omgeving"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "Power shell-uitvoer"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT

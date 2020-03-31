---
title: Azure Traffic Manager - methoden voor het routeren van verkeer
description: Met deze artikelen u inzicht krijgen in de verschillende verkeersrouteringsmethoden die door Traffic Manager worden gebruikt
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: rohink
ms.openlocfilehash: 4a035506943eeffa2c3fc4fec27c47da4136683b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250905"
---
# <a name="traffic-manager-routing-methods"></a>Methoden voor het doorsturen van Traffic Manager

Azure Traffic Manager ondersteunt zes verkeersrouteringsmethoden om te bepalen hoe netwerkverkeer naar de verschillende serviceeindpunten kan worden geleid. Voor elk profiel past Traffic Manager de bijbehorende verkeersrouteringsmethode toe op elke DNS-query die het ontvangt. De routeringsmethode voor verkeer bepaalt welk eindpunt wordt geretourneerd in de DNS-respons.

De volgende routeringsmethoden voor verkeer zijn beschikbaar in Traffic Manager:

* **[Prioriteit:](#priority-traffic-routing-method)** Selecteer **Prioriteit** wanneer u een eindpunt voor de primaire service wilt gebruiken voor al het verkeer en back-ups wilt maken voor het geval de primaire of de back-upeindpunten niet beschikbaar zijn.
* **[Gewogen:](#weighted)** Selecteer **Gewogen** wanneer u verkeer wilt verdelen over een set eindpunten, gelijkmatig of op basis van gewichten, die u definieert.
* **[Prestaties:](#performance)** selecteer **Prestaties** wanneer u eindpunten op verschillende geografische locaties hebt en u wilt dat eindgebruikers het "dichtstbijzijnde" eindpunt gebruiken in termen van de laagste netwerklatentie.
* **[Geografisch:](#geographic)** Selecteer **Geografisch** zodat gebruikers worden doorverwezen naar specifieke eindpunten (Azure, External of Nested) op basis van welke geografische locatie hun DNS-query afkomstig is. Dit stelt Traffic Manager-klanten in staat om scenario's in te schakelen waarbij het kennen van de geografische regio van een gebruiker en het routeren ervan op basis daarvan belangrijk is. Voorbeelden hiervan zijn het naleven van datasoevereiniteitsmandaten, het lokaliseren van inhoud & gebruikerservaring en het meten van verkeer uit verschillende regio's.
* **[Multivalue:](#multivalue)** Selecteer **MultiValue** for Traffic Manager-profielen die alleen IPv4/IPv6-adressen als eindpunten kunnen hebben. Wanneer een query voor dit profiel wordt ontvangen, worden alle gezonde eindpunten geretourneerd.
* **[Subnet](#subnet):** Selecteer subnet-verkeersrouteringsmethode om sets van IP-adresbereiken van eindgebruikers toe te wijzen op een specifiek eindpunt binnen een Traffic Manager-profiel. **Subnet** Wanneer een aanvraag wordt ontvangen, wordt het geretourneerde eindpunt toegewezen aan het bron-IP-adres van die aanvraag. 


Alle Traffic Manager-profielen omvatten het bewaken van de status van eindpunt en automatische failover van eindpunten. Zie [Eindpuntbewaking van verkeersbeheer voor](traffic-manager-monitoring.md)meer informatie . Eén Traffic Manager-profiel kan slechts één routeringsmethode gebruiken. U op elk gewenst moment een andere routeringsmethode voor uw profiel selecteren. Wijzigingen worden binnen een minuut toegepast en er wordt geen downtime gemaakt. Verkeersrouteringsmethoden kunnen worden gecombineerd met behulp van geneste Traffic Manager-profielen. Nesten maakt geavanceerde en flexibele verkeersrouteringsconfiguraties mogelijk die voldoen aan de behoeften van grotere, complexe toepassingen. Zie [geneste traffic manager-profielen](traffic-manager-nested-profiles.md)voor meer informatie .

## <a name="priority-traffic-routing-method"></a>Methode voor prioriteitverkeer

Vaak wil een organisatie betrouwbaarheid bieden voor haar services door een of meer back-upservices te implementeren voor het geval hun primaire service uitvalt. Met de 'Priority'-verkeersrouteringsmethode kunnen Azure-klanten dit failoverpatroon eenvoudig implementeren.

![Methode voor het routeren van het azure Traffic Manager 'Priority'](media/traffic-manager-routing-methods/priority.png)

Het Traffic Manager-profiel bevat een lijst met service-eindpunten met prioriteit. Standaard verzendt Traffic Manager al het verkeer naar het primaire eindpunt (met de hoogste prioriteit). Als het primaire eindpunt niet beschikbaar is, routeert Traffic Manager het verkeer naar het tweede eindpunt. Als zowel het primaire als het secundaire eindpunt niet beschikbaar is, gaat het verkeer naar het derde, enzovoort. De beschikbaarheid van het eindpunt is gebaseerd op de geconfigureerde status (ingeschakeld of uitgeschakeld) en de voortdurende eindpuntcontrole.

### <a name="configuring-endpoints"></a>Eindpunten configureren

Met Azure Resource Manager configureert u de eindpuntprioriteit expliciet met behulp van de eigenschap 'prioriteit' voor elk eindpunt. Deze eigenschap is een waarde tussen 1 en 1000. Lagere waarden vertegenwoordigen een hogere prioriteit. Eindpunten kunnen geen prioriteitswaarden delen. Het instellen van de eigenschap is optioneel. Wanneer deze wordt weggelaten, wordt een standaardprioriteit op basis van de eindpuntvolgorde gebruikt.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Gewogen verkeersrouteringsmethode
Met de 'Weighted' traffic-routing-methode u het verkeer gelijkmatig verdelen of een vooraf gedefinieerde weging gebruiken.

![Azure Traffic Manager 'Gewogen' verkeersrouteringsmethode](media/traffic-manager-routing-methods/weighted.png)

In de methode Gewogen verkeersroutering wijst u een gewicht toe aan elk eindpunt in de profielconfiguratie Traffic Manager. Het gewicht is een geheel getal tussen 1 en 1000. Deze parameter is optioneel. Als u dit niet doet, gebruikt Traffic Managers een standaardgewicht van '1'. Hoe hoger het gewicht, hoe hoger de prioriteit.

Voor elke ontvangen DNS-query kiest Traffic Manager willekeurig een beschikbaar eindpunt. De kans dat een eindpunt wordt gekozen, is gebaseerd op het gewicht dat is toegewezen aan alle beschikbare eindpunten. Het gebruik van hetzelfde gewicht over alle eindpunten resulteert in een gelijkmatige verkeersverdeling. Door hogere of lagere gewichten te gebruiken op specifieke eindpunten worden deze eindpunten min of meer vaak geretourneerd in de DNS-antwoorden.

De gewogen methode maakt een aantal nuttige scenario's mogelijk:

* Geleidelijke upgrade van toepassingen: Wijs een percentage verkeer toe om naar een nieuw eindpunt te routeren en verhoog het verkeer geleidelijk tot 100%.
* Toepassingsmigratie naar Azure: maak een profiel met zowel Azure- als externe eindpunten. Pas het gewicht van de eindpunten aan om de nieuwe eindpunten te verkiezen.
* Cloud-bursting voor extra capaciteit: Breid snel een on-premises implementatie uit naar de cloud door deze achter een Traffic Manager-profiel te plaatsen. Wanneer u extra capaciteit in de cloud nodig hebt, u meer eindpunten toevoegen of inschakelen en opgeven welk deel van het verkeer naar elk eindpunt gaat.

Naast het gebruik van de Azure-portal u gewichten configureren met Azure PowerShell, CLI en de REST-API's.

Het is belangrijk om te begrijpen dat DNS-antwoorden worden opgeslagen door clients en door de recursieve DNS-servers die de clients gebruiken om DNS-namen op te lossen. Deze caching kan een impact hebben op gewogen verkeersverdelingen. Wanneer het aantal clients en recursieve DNS-servers groot is, werkt de verkeersdistributie zoals verwacht. Wanneer het aantal clients of recursieve DNS-servers echter klein is, kan caching de verkeersverdeling aanzienlijk scheeftrekken.

Veelvoorkomende use cases zijn:

* Ontwikkel- en testomgevingen
* Communicatie tussen toepassing en toepassing
* Toepassingen gericht op een smalle user-base die een gemeenschappelijke recursieve DNS-infrastructuur delen (bijvoorbeeld werknemers van het bedrijf dat verbinding maakt via een proxy)

Deze DNS-caching-effecten zijn gemeenschappelijk voor alle DNS-gebaseerde verkeersrouteringssystemen, niet alleen Azure Traffic Manager. In sommige gevallen kan het expliciet wissen van de DNS-cache een tijdelijke oplossing bieden. In andere gevallen kan een alternatieve routeringsmethode geschikter zijn.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Methode voor het routeren van het verkeer

Het implementeren van eindpunten op twee of meer locaties over de hele wereld kan de responsiviteit van veel toepassingen verbeteren door verkeer naar de locatie te routeren die 'het dichtst' bij u ligt. De 'Performance' verkeersrouteringsmethode biedt deze mogelijkheid.

![Methode voor het routeren van het verkeer van Azure Traffic Manager 'Prestaties'.](media/traffic-manager-routing-methods/performance.png)

Het 'dichtstbijzijnde' eindpunt is niet noodzakelijkerwijs het dichtst, zoals gemeten door geografische afstand. In plaats daarvan bepaalt de verkeersrouteringsmethode 'Prestaties' het dichtstbijzijnde eindpunt door de netwerklatentie te meten. Traffic Manager onderhoudt een Internet Latency Table om de retourtijd tussen IP-adresbereiken en elk Azure-datacenter bij te houden.

Traffic Manager zoekt het bron-IP-adres op van het binnenkomende DNS-verzoek in de InternetLatency Table. Traffic Manager kiest vervolgens een beschikbaar eindpunt in het Azure-datacenter met de laagste latentie voor dat IP-adresbereik en retourneert dat eindpunt in de DNS-respons.

Zoals uitgelegd in [How Traffic Manager Works,](traffic-manager-how-it-works.md)ontvangt Traffic Manager geen DNS-query's rechtstreeks van clients. Dns-query's zijn veeleer afkomstig van de recursieve DNS-service die de clients hebben geconfigureerd om te gebruiken. Daarom is het IP-adres dat wordt gebruikt om het 'dichtstbijzijnde' eindpunt te bepalen niet het IP-adres van de client, maar het IP-adres van de recursieve DNS-service. In de praktijk is dit IP-adres een goede proxy voor de klant.


Traffic Manager werkt de Internet Latency Table regelmatig bij om rekening te houden met wijzigingen in het wereldwijde internet en nieuwe Azure-regio's. De prestaties van toepassingen variëren echter afhankelijk van real-time variaties in belasting op het internet. Prestatieverkeerroutering controleert de belasting op een bepaald serviceeindpunt niet. Als een eindpunt echter niet beschikbaar wordt, neemt Traffic Manager het niet op in DNS-queryreacties.


Aandachtspunten:

* Als uw profiel meerdere eindpunten in dezelfde Azure-regio bevat, verdeelt Traffic Manager het verkeer gelijkmatig over de beschikbare eindpunten in die regio. Als u de voorkeur geeft aan een andere verkeersverdeling binnen een regio, u [geneste Traffic Manager-profielen](traffic-manager-nested-profiles.md)gebruiken.
* Als alle ingeschakelde eindpunten in de dichtstbijzijnde Azure-regio zijn afgebroken, verplaatst Traffic Manager het verkeer naar de eindpunten in de dichtstbijzijnde Azure-regio. Als u een voorkeursfailoverreeks wilt definiëren, gebruikt u [geneste Traffic Manager-profielen](traffic-manager-nested-profiles.md).
* Wanneer u de routeringsmethode Voor het uitvoeren van verkeer gebruikt met externe eindpunten of geneste eindpunten, moet u de locatie van die eindpunten opgeven. Kies het Azure-gebied dat het dichtst bij uw implementatie ligt. Deze locaties zijn de waarden die worden ondersteund door de Internet Latency Table.
* Het algoritme dat het eindpunt kiest, is deterministisch. Herhaalde DNS-query's van dezelfde client worden naar hetzelfde eindpunt geleid. Clients gebruiken doorgaans verschillende recursieve DNS-servers tijdens het reizen. De klant kan worden doorgestuurd naar een ander eindpunt. Routering kan ook worden beïnvloed door updates van de Internet Latency Table. Daarom garandeert de methode voor het routeren van het verkeer uitvoeren niet dat een client altijd naar hetzelfde eindpunt wordt doorgestuurd.
* Wanneer de Internet Latency Table wordt gewijzigd, u merken dat sommige clients naar een ander eindpunt worden geleid. Deze routeringswijziging is nauwkeuriger op basis van de huidige latentiegegevens. Deze updates zijn essentieel om de nauwkeurigheid van performance traffic-routing te behouden terwijl het internet voortdurend evolueert.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Methode voor het routeren van geografisch verkeer

Traffic Manager-profielen kunnen worden geconfigureerd om de geografische routeringsmethode te gebruiken, zodat gebruikers worden doorverwezen naar specifieke eindpunten (Azure, Extern of Genesed) op basis van welke geografische locatie hun DNS-query afkomstig is. Dit stelt Traffic Manager-klanten in staat om scenario's in te schakelen waarbij het kennen van de geografische regio van een gebruiker en het routeren ervan op basis daarvan belangrijk is. Voorbeelden hiervan zijn het naleven van datasoevereiniteitsmandaten, het lokaliseren van inhoud & gebruikerservaring en het meten van verkeer uit verschillende regio's.
Wanneer een profiel is geconfigureerd voor geografische routering, moet elk eindpunt dat aan dat profiel is gekoppeld, een set geografische regio's hebben toegewezen. Een geografische regio kan op de volgende granulaire niveaus liggen 
- Wereld: elke regio
- Regionale groepering – bijvoorbeeld Afrika, Midden-Oosten, Australië/Stille Oceaan enz. 
- Land/regio – bijvoorbeeld Ierland, Peru, Hong Kong SAR etc. 
- Staat / provincie - bijvoorbeeld, VS-Californië, Australië-Queensland, Canada-Alberta etc. (let op: dit granulariteitsniveau wordt alleen ondersteund voor staten / provincies in Australië, Canada en de VS).

Wanneer een regio of een reeks regio's aan een eindpunt is toegewezen, worden aanvragen uit die regio's alleen naar dat eindpunt doorgestuurd. Traffic Manager gebruikt het bron-IP-adres van de DNS-query om te bepalen van welk gebied een gebruiker zich bevraagt - meestal is dit het IP-adres van de lokale DNS-resolver die de query namens de gebruiker doet.  

![Azure Traffic Manager 'Geografische' verkeersrouteringsmethode](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager leest het bron-IP-adres van de DNS-query en bepaalt uit welke geografische regio deze afkomstig is. Vervolgens wordt gekeken of er een eindpunt is dat deze geografische regio in kaart heeft gebracht. Deze lookup begint op het laagste granulariteitsniveau (Staat/provincie waar het wordt ondersteund, anders op land/regioniveau) en gaat helemaal naar het hoogste niveau, dat **wereld**is. De eerste overeenkomst die met deze traversal wordt gevonden, wordt aangeduid als het eindpunt om terug te keren in het queryantwoord. Wanneer u overeenkomt met een genest typeeindpunt, wordt een eindpunt binnen dat onderliggende profiel geretourneerd op basis van de routeringsmethode. De volgende punten zijn van toepassing op dit gedrag:

- Een geografische regio kan slechts worden toegewezen aan één eindpunt in een Traffic Manager-profiel wanneer het routeringstype Geografische routering is. Dit zorgt ervoor dat routering van gebruikers deterministisch is en dat klanten scenario's kunnen inschakelen die eenduidige geografische grenzen vereisen.
- Als de regio van een gebruiker onder de geografische toewijzing van twee verschillende eindpunten valt, selecteert Traffic Manager het eindpunt met de laagste granulariteit en overweegt het geen routeringsaanvragen van die regio naar het andere eindpunt. Denk bijvoorbeeld aan een profiel van het type Geografische routering met twee eindpunten : Eindpunt1 en Endpoint2. Endpoint1 is geconfigureerd om verkeer uit Ierland te ontvangen en Endpoint2 is geconfigureerd om verkeer uit Europa te ontvangen. Als een aanvraag afkomstig is uit Ierland, wordt het altijd doorgestuurd naar Endpoint1.
- Aangezien een regio slechts aan één eindpunt kan worden toegewezen, retourneert Traffic Manager het, ongeacht of het eindpunt al dan niet in orde is.

    >[!IMPORTANT]
    >Het wordt ten zeerste aanbevolen dat klanten die de methode voor geografische routering gebruiken, deze koppelen aan de geneste typeeindpunten met onderliggende profielen die ten minste twee eindpunten bevatten.
- Als er een eindpuntovereenkomst wordt gevonden en dat eindpunt in de status **Gestopt** staat, retourneert Traffic Manager een NODATA-antwoord. In dit geval worden er geen verdere lookups hoger in de hiërarchie van de geografische regio. Dit gedrag is ook van toepassing op geneste eindpunttypen wanneer het onderliggende profiel zich in de status **Gestopt** of **Uitgeschakeld bevindt.**
- Als een eindpunt een **uitgeschakelde** status weergeeft, wordt het niet opgenomen in het proces voor regiomatching. Dit gedrag is ook van toepassing op geneste eindpunttypen wanneer het eindpunt zich in de **status Uitgeschakeld** bevindt.
- Als een query afkomstig is uit een geografische regio die geen toewijzing in dat profiel heeft, retourneert Traffic Manager een NODATA-antwoord. Daarom wordt het ten zeerste aanbevolen dat klanten geografische routering gebruiken met één eindpunt, idealiter van het type Genest met ten minste twee eindpunten binnen het onderliggende profiel, waarbij de regio **Wereld** eraan is toegewezen. Dit zorgt er ook voor dat ip-adressen die niet aan een regio worden toegewezen, worden verwerkt.

Zoals uitgelegd in [How Traffic Manager Works,](traffic-manager-how-it-works.md)ontvangt Traffic Manager geen DNS-query's rechtstreeks van clients. Dns-query's zijn veeleer afkomstig van de recursieve DNS-service die de clients hebben geconfigureerd om te gebruiken. Daarom is het IP-adres dat wordt gebruikt om de regio te bepalen niet het IP-adres van de client, maar het is het IP-adres van de recursieve DNS-service. In de praktijk is dit IP-adres een goede proxy voor de klant.

### <a name="faqs"></a>Veelgestelde vragen

* [Wat zijn enkele use cases waarbij geografische routering nuttig is?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Hoe bepaal ik of ik de routeringsmethode voor prestaties of een geografische routeringsmethode moet gebruiken?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Wat zijn de regio's die worden ondersteund door Traffic Manager voor geografische routering?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Hoe bepaalt de verkeersbeheerder waar een gebruiker vandaan komt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Is het gegarandeerd dat Traffic Manager in elk geval de exacte geografische locatie van de gebruiker correct kan bepalen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Moet een eindpunt zich fysiek in dezelfde regio bevinden als het eindpunt waarmee het is geconfigureerd voor geografische routering?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Kan ik geografische regio's toewijzen aan eindpunten in een profiel dat niet is geconfigureerd om geografische routering uit te brengen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Waarom krijg ik een foutmelding wanneer ik de routeringsmethode van een bestaand profiel probeer te wijzigen in Geografisch?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Waarom wordt het ten zeerste aanbevolen dat klanten geneste profielen maken in plaats van eindpunten onder een profiel waarop geografische routering is ingeschakeld?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Zijn er beperkingen op de API-versie die dit routeringtype ondersteunt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Methode voor het routeren van verkeer met meerdere waarden
Met de methode voor het routeren van verkeer **met meerdere** waarden u meerdere gezonde eindpunten krijgen in één DNS-queryrespons. Hierdoor kan de beller opnieuw naar de client-side-items met andere eindpunten gaan in het geval dat een geretourneerd eindpunt niet reageert. Dit patroon kan de beschikbaarheid van een service vergroten en de latentie verminderen die is gekoppeld aan een nieuwe DNS-query om een gezond eindpunt op te halen. MultiValue-routeringsmethode werkt alleen als alle eindpunten van het type 'Extern' zijn opgegeven als IPv4- of IPv6-adressen. Wanneer een query voor dit profiel wordt ontvangen, worden alle gezonde eindpunten geretourneerd en worden ze voorzien van een configureerbaar maximaal aantal retouren.

### <a name="faqs"></a>Veelgestelde vragen

* [Wat zijn enkele use cases waarbij MultiValue routing nuttig is?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Hoeveel eindpunten worden geretourneerd wanneer MultiValue-routering wordt gebruikt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Krijg ik dezelfde set eindpunten wanneer MultiValue-routering wordt gebruikt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Subnet-routeringsmethode voor verkeer
Met de methode voor het routeren van **subnetverkeer** u een set IP-adresbereiken van eindgebruikers toewijzen aan specifieke eindpunten in een profiel. Daarna, als Traffic Manager een DNS-query voor dat profiel ontvangt, zal het het bron-IP-adres van dat verzoek inspecteren (in de meeste gevallen is dit het uitgaande IP-adres van de DNS-resolver die door de beller wordt gebruikt), bepaalt aan welk eindpunt het is toegewezen en keert het terug dat eindpunt in het queryantwoord. 

Het IP-adres dat aan een eindpunt moet worden toegewezen, kan worden opgegeven als CIDR-bereiken (bijvoorbeeld 1.2.3.0/24) of als adresbereik (bijvoorbeeld 1.2.3.4-5.6.7.8). De IP-bereiken die aan een eindpunt zijn gekoppeld, moeten uniek zijn binnen dat profiel en mogen geen overlap hebben met de IP-adresset van een ander eindpunt in hetzelfde profiel.
Als u een eindpunt zonder adresbereik definieert, fungeert dat als een terugval en neemt u verkeer van de resterende subnetten. Als er geen terugvaleindpunt is opgenomen, stuurt Traffic Manager een NODATA-antwoord voor ongedefinieerde bereiken. Het is daarom sterk aanbevolen dat u ofwel een fallback-eindpunt definieert, of er anders voor zorgt dat alle mogelijke IP-bereiken worden opgegeven over uw eindpunten.

Subnetroutering kan worden gebruikt om een andere ervaring te bieden voor gebruikers die verbinding maken vanuit een specifieke IP-ruimte. Met behulp van subnetroutering kan een klant bijvoorbeeld alle aanvragen van het hoofdkantoor laten doorsturen naar een ander eindpunt waar ze mogelijk een interne versie van de app testen. Een ander scenario is dat u een andere ervaring wilt bieden aan gebruikers die verbinding maken via een specifieke internetprovider (u wilt bijvoorbeeld gebruikers van een bepaalde provider blokkeren).

### <a name="faqs"></a>Veelgestelde vragen

* [Wat zijn enkele use cases waarbij subnet routing nuttig is?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Hoe weet Traffic Manager het IP-adres van de eindgebruiker?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Hoe kan ik IP-adressen opgeven bij het gebruik van Subnet-routering?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Hoe kan ik een terugvaleindpunt opgeven bij het gebruik van Subnet-routering?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Wat gebeurt er als een eindpunt is uitgeschakeld in een subnetrouteringstypeprofiel?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwikkelen van toepassingen met hoge beschikbaarheid met behulp van [Traffic Manager endpoint monitoring](traffic-manager-monitoring.md)





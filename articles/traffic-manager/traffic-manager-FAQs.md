---
title: Azure Traffic Manager - Veelgestelde vragen
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: rohink
ms.openlocfilehash: acdac6e3eafc5251ebd31a34bcb9a4db34f0ebbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254363"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Veelgestelde vragen over verkeersmanager (faq)

## <a name="traffic-manager-basics"></a>Basisbeginselen van Verkeersmanager

### <a name="what-ip-address-does-traffic-manager-use"></a>Welk IP-adres gebruikt Traffic Manager?

Zoals uitgelegd in [How Traffic Manager Works,](../traffic-manager/traffic-manager-how-it-works.md)werkt Traffic Manager op DNS-niveau. Het stuurt DNS-antwoorden naar directe clients naar het juiste serviceeindpunt. Clients maken vervolgens rechtstreeks verbinding met het serviceeindpunt, niet via Traffic Manager.

Traffic Manager geeft daarom geen eindpunt of IP-adres voor clients om verbinding mee te maken. Als u een statisch IP-adres voor uw service wilt, moet dit worden geconfigureerd bij de service, niet in Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Welke typen verkeer kunnen worden omgeleid met Traffic Manager?
Zoals uitgelegd in [How Traffic Manager Works,](../traffic-manager/traffic-manager-how-it-works.md)kan een Traffic Manager-eindpunt elke internetgerichte service zijn die binnen of buiten Azure wordt gehost. Vandaar dat Traffic Manager verkeer dat afkomstig is van het openbare internet kan routeren naar een reeks eindpunten die ook worden geconfronteerd met internet. Als u eindpunten hebt die zich in een privénetwerk bevinden (bijvoorbeeld een interne versie van [Azure Load Balancer)](../load-balancer/concepts-limitations.md#internalloadbalancer)of als gebruikers DNS-verzoeken van dergelijke interne netwerken kunnen indienen, u Traffic Manager niet gebruiken om dit verkeer te routeren.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Ondersteunt Traffic Manager "sticky" sessies?

Zoals uitgelegd in [How Traffic Manager Works,](../traffic-manager/traffic-manager-how-it-works.md)werkt Traffic Manager op DNS-niveau. Het maakt gebruik van DNS-antwoorden om clients naar het juiste serviceeindpunt te leiden. Clients maken rechtstreeks verbinding met het serviceeindpunt, niet via Traffic Manager. Traffic Manager ziet daarom het HTTP-verkeer tussen de client en de server niet.

Bovendien behoort het bron-IP-adres van de DNS-query die Traffic Manager ontvangt toe aan de recursieve DNS-service, niet tot de client. Traffic Manager heeft daarom geen manier om individuele klanten te volgen en kan geen 'sticky' sessies implementeren. Deze beperking is gemeenschappelijk voor alle DNS-gebaseerde verkeersbeheersystemen en is niet specifiek voor Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Waarom zie ik een HTTP-fout bij het gebruik van Traffic Manager?

Zoals uitgelegd in [How Traffic Manager Works,](../traffic-manager/traffic-manager-how-it-works.md)werkt Traffic Manager op DNS-niveau. Het maakt gebruik van DNS-antwoorden om clients naar het juiste serviceeindpunt te leiden. Clients maken vervolgens rechtstreeks verbinding met het serviceeindpunt, niet via Traffic Manager. Traffic Manager ziet geen HTTP-verkeer tussen client en server. Daarom moet elke HTTP-fout die u ziet afkomstig zijn van uw toepassing. Als de client verbinding kan maken met de toepassing, zijn alle stappen voor DNS-resolutie voltooid. Dat geldt ook voor elke interactie die Traffic Manager heeft op de verkeersstroom van de toepassing.

Verder onderzoek moet daarom gericht zijn op de aanvraag.

De HTTP-hostheader die vanuit de browser van de client wordt verzonden, is de meest voorkomende bron van problemen. Controleer of de toepassing is geconfigureerd om de juiste hostheader te accepteren voor de domeinnaam die u gebruikt. Zie Het configureren van een [aangepaste domeinnaam voor een web-app in Azure App Service met Traffic Manager voor](../app-service/configure-domain-traffic-manager.md)eindpunten met de Azure App Service.

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Wat is de impact van de prestaties van het gebruik van Traffic Manager?

Zoals uitgelegd in [How Traffic Manager Works,](../traffic-manager/traffic-manager-how-it-works.md)werkt Traffic Manager op DNS-niveau. Aangezien clients rechtstreeks verbinding maken met uw serviceeindpunten, is er geen impact op de prestaties bij het gebruik van Traffic Manager zodra de verbinding tot stand is gebracht.

Aangezien Traffic Manager integreert met toepassingen op DNS-niveau, vereist het wel een extra DNS-lookup die in de DNS-resolutieketen moet worden ingevoegd. De impact van Traffic Manager op de DNS-resolutietijd is minimaal. Traffic Manager maakt gebruik van een wereldwijd netwerk van naamservers en maakt gebruik van [anycast-netwerken](https://en.wikipedia.org/wiki/Anycast) om ervoor te zorgen dat DNS-query's altijd worden doorgestuurd naar de dichtstbijzijnde beschikbare naamserver. Bovendien betekent het incachen van DNS-antwoorden dat de extra DNS-latentie die wordt opgelopen door het gebruik van Traffic Manager slechts op een fractie van sessies van toepassing is.

De prestatiemethode leidt het verkeer naar het dichtstbijzijnde beschikbare eindpunt. Het nettoresultaat is dat de totale prestatie-impact in verband met deze methode minimaal moet zijn. Elke toename van de DNS-latentie moet worden gecompenseerd door een lagere netwerklatentie naar het eindpunt.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Welke toepassingsprotocollen kan ik gebruiken met Traffic Manager?

Zoals uitgelegd in [How Traffic Manager Works,](../traffic-manager/traffic-manager-how-it-works.md)werkt Traffic Manager op DNS-niveau. Zodra de DNS-lookup is voltooid, maken clients rechtstreeks verbinding met het eindpunt van de toepassing, niet via Traffic Manager. Daarom kan de verbinding elk toepassingsprotocol gebruiken. Als u TCP als controleprotocol selecteert, kan de statuscontrole van traffic manager worden uitgevoerd zonder toepassingsprotocollen te gebruiken. Als u ervoor kiest de status te laten verifiëren met behulp van een toepassingsprotocol, moet het eindpunt kunnen reageren op HTTP- of HTTPS GET-verzoeken.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Kan ik Traffic Manager gebruiken met een 'naakte' domeinnaam?

Ja. Zie [Een aliasrecord configureren om topdomeinnamen te ondersteunen met Traffic Manager](../dns/tutorial-alias-tm.md)voor meer informatie over het maken van een aliasrecord voor het maken van een aliasrecord voor het maken van een aprofilerecord voor uw domeinnaam.

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Houdt Traffic Manager rekening met het subnetadres van de client bij het verwerken van DNS-query's? 

Ja, naast het bron-IP-adres van de DNS-query die het ontvangt (wat meestal het IP-adres van de DNS-resolver is), houdt traffic manager bij het uitvoeren van lookups voor geografische, prestaties en subnetrouteringsmethoden ook rekening met het subnetadres van de client als het wordt opgenomen in de query door de resolver die het verzoek namens de eindgebruiker indient.  
Specifiek, [RFC 7871 – Client Subnet in DNS Queries](https://tools.ietf.org/html/rfc7871) dat een [extensiemechanisme voor DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) die kan doorgeven aan de client subnet adres van resolvers die het ondersteunen biedt.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Wat is DNS TTL en welke invloed heeft het op mijn gebruikers?

Wanneer een DNS-query op Traffic Manager landt, stelt deze een waarde in in het antwoord dat time-to-live (TTL) wordt genoemd. Deze waarde, waarvan de eenheid binnen enkele seconden is, geeft aan DNS-resolvers downstream aan hoe lang deze reactie moet worden opgeslagen. Hoewel DNS-resolvers niet gegarandeerd zijn om dit resultaat in de cache op te slaan, kunnen ze met caching reageren op eventuele volgende query's uit de cache in plaats van naar DNS-servers van Traffic Manager te gaan. Dit heeft gevolgen voor de antwoorden als volgt:

- een hogere TTL vermindert het aantal query's dat op de DNS-servers van Traffic Manager terechtkomt, wat de kosten voor een klant kan verlagen, omdat het aantal query's dat wordt weergegeven een factureerbaar gebruik is.
- een hogere TTL kan mogelijk de tijd verkorten die nodig is om een DNS-lookup uit te doen.
- een hogere TTL betekent ook dat uw gegevens niet de meest recente gezondheidsinformatie weergeven die Traffic Manager heeft verkregen via zijn indringende middelen.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Hoe hoog of laag kan ik de antwoorden van TTL for Traffic Manager instellen?

U instellen, op een per profiel niveau, de DNS TTL zo laag als 0 seconden en zo hoog als 2.147.483.647 seconden (het maximale bereik compatibel met [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )). Een TTL van 0 betekent dat downstream DNS-resolvers queryreacties niet in de cache plaatsen en dat alle query's naar verwachting de DNS-servers van Traffic Manager bereiken voor oplossing.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Hoe kan ik het aantal query's dat naar mijn profiel komt begrijpen? 

Een van de statistieken die door Traffic Manager wordt verstrekt, is het aantal query's dat door een profiel wordt beantwoord. U deze informatie op profielniveau aggregatie krijgen of u deze verder opsplitsen om het volume van query's te zien waar specifieke eindpunten zijn geretourneerd. Bovendien u waarschuwingen instellen om u op de hoogte te stellen als het antwoordvolume van de query de ingestelde voorwaarden overschrijdt. Voor meer informatie, [Statistieken en waarschuwingen voor Traffic Manager](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Methode voor het routeren van geografische verkeersbeheer

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Wat zijn enkele use cases waarbij geografische routering nuttig is?

Geografisch type routering kan worden gebruikt in elk scenario waarin een Azure-klant zijn gebruikers moet onderscheiden op basis van geografische regio's. Met de methode Voor het routeren van geografisch verkeer u gebruikers uit specifieke regio's bijvoorbeeld een andere gebruikerservaring geven dan gebruikers uit andere regio's. Een ander voorbeeld is het naleven van lokale gegevenssoevereiniteitsmandaten die vereisen dat gebruikers uit een bepaalde regio alleen worden bediend door eindpunten in die regio.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Hoe bepaal ik of ik de routeringsmethode voor prestaties of een geografische routeringsmethode moet gebruiken?

Het belangrijkste verschil tussen deze twee populaire routeringsmethoden is dat in de routeringsmethode prestaties uw primaire doel is om verkeer naar het eindpunt te verzenden dat de laagste latentie naar de beller kan bieden, terwijl in geografische routering het primaire doel is om een geo af te dwingen hek voor uw bellers, zodat u ze bewust doorsturen naar een specifiek eindpunt. De overlapping gebeurt omdat er een correlatie is tussen geografische nabijheid en lagere latentie, hoewel dit niet altijd waar is. Er kan een eindpunt in een andere geografie die een betere latentie-ervaring voor de beller kan bieden en in dat geval stuurt de routeringsfunctie de gebruiker naar dat eindpunt, maar geografische routering stuurt ze altijd naar het eindpunt dat u hebt toegewezen voor hun geografische regio. Om het verder duidelijk te maken, overweeg dan het volgende voorbeeld - met Geografische routering u ongebruikelijke toewijzingen maken, zoals al het verkeer vanuit Azië naar eindpunten in de VS en al het Amerikaanse verkeer naar eindpunten in Azië verzenden. In dat geval zal Geografische routering bewust precies doen waarvoor u het hebt geconfigureerd en is prestatieoptimalisatie geen overweging. 
>[!NOTE]
>Er kunnen scenario's zijn waarin u zowel prestaties als geografische routeringsmogelijkheden nodig hebt, want deze scenario's kunnen geneste profielen een goede keuze zijn. U bijvoorbeeld een bovenliggend profiel instellen met geografische routering waarbij u al het verkeer vanuit Noord-Amerika naar een genest profiel met eindpunten in de VS verzendt en prestatieroutering gebruikt om dat verkeer naar het beste eindpunt binnen die set te verzenden. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Wat zijn de regio's die worden ondersteund door Traffic Manager voor geografische routering?

De land-/regiohiërarchie die wordt gebruikt door Traffic Manager is [hier](traffic-manager-geographic-regions.md)te vinden. Hoewel deze pagina up-to-date wordt gehouden met eventuele wijzigingen, u dezelfde informatie ook programmatisch ophalen met behulp van de [Azure Traffic Manager REST API.](https://docs.microsoft.com/rest/api/trafficmanager/) 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hoe bepaalt de verkeersbeheerder waar een gebruiker vandaan komt?

Traffic Manager kijkt naar het bron-IP van de query (dit is waarschijnlijk een lokale DNS-resolver die de query doet namens de gebruiker) en gebruikt een interne IP-naar-regiokaart om de locatie te bepalen. Deze kaart wordt voortdurend bijgewerkt om rekening te houden met wijzigingen in het internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Is het gegarandeerd dat Traffic Manager in elk geval de exacte geografische locatie van de gebruiker correct kan bepalen?

Nee, Traffic Manager kan niet garanderen dat het geografische gebied dat we afleiden van het bron-IP-adres van een DNS-query altijd overeenkomt met de locatie van de gebruiker om de volgende redenen:

- Ten eerste, zoals beschreven in de vorige FAQ, de bron IP-adres zien we is dat van een DNS resolver doet de lookup namens de gebruiker. Hoewel de geografische locatie van de DNS-resolver een goede proxy is voor de geografische locatie van de gebruiker, kan deze ook verschillen, afhankelijk van de voetafdruk van de DNS-resolver-service en de specifieke DNS-resolver-service die een klant heeft gekozen om te gebruiken. Een klant in Maleisië kan bijvoorbeeld in de instellingen van zijn apparaat een DNS-resolver-service opgeven waarvan de DNS-server in Singapore kan worden gekozen om de queryresoluties voor die gebruiker/apparaat te verwerken. In dat geval kan Traffic Manager alleen het IP-adres van de resolver zien dat overeenkomt met de locatie in Singapore. Zie ook de eerdere veelgestelde vragen over ondersteuning voor subnetadres van klanten op deze pagina.

- Ten tweede gebruikt Traffic Manager een interne kaart om het IP-adres uit te brengen naar geografische regiovertaling. Hoewel deze kaart voortdurend wordt gevalideerd en bijgewerkt om de nauwkeurigheid ervan te vergroten en rekening te houden met de evoluerende aard van het internet, is er nog steeds de mogelijkheid dat onze informatie geen exacte weergave is van de geografische locatie van alle IP-gebieden Adressen.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Moet een eindpunt zich fysiek in dezelfde regio bevinden als het eindpunt waarmee het is geconfigureerd voor geografische routering?

Nee, de locatie van het eindpunt legt geen beperkingen op aan welke regio's eraan in kaart kunnen worden gebracht. Een eindpunt in de Azure-regio VS-Centraal kan bijvoorbeeld alle gebruikers uit India erop hebben gericht.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Kan ik geografische regio's toewijzen aan eindpunten in een profiel dat niet is geconfigureerd om geografische routering uit te brengen?

Ja, als de routeringsmethode van een profiel niet geografisch is, u de [AZURE Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) gebruiken om geografische regio's toe te wijzen aan eindpunten in dat profiel. In het geval van niet-geografische routeringstypeprofielen wordt deze configuratie genegeerd. Als u een dergelijk profiel op een later tijdstip wijzigt in een geografisch routeringstype, kan Traffic Manager deze toewijzingen gebruiken.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Waarom krijg ik een foutmelding wanneer ik de routeringsmethode van een bestaand profiel probeer te wijzigen in Geografisch?

Alle eindpunten onder een profiel met geografische routering moeten ten minste één regio toegewezen hebben. Als u een bestaand profiel wilt converteren naar een geografisch routeringtype, moet u eerst geografische regio's koppelen aan al de eindpunten met behulp van de [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) voordat u het routeringstype wijzigt in geografisch. Als u portal gebruikt, verwijdert u eerst de eindpunten, wijzigt u de routeringsmethode van het profiel in geografisch en voegt u de eindpunten toe samen met de toewijzing van hun geografische regio.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Waarom wordt het ten zeerste aanbevolen dat klanten geneste profielen maken in plaats van eindpunten onder een profiel waarop geografische routering is ingeschakeld?

Een regio kan slechts aan één eindpunt in een profiel worden toegewezen als het de geografische routeringsmethode gebruikt. Als dat eindpunt geen genest type is met een onderliggend profiel eraan gekoppeld, als dat eindpunt ongezond wordt, blijft Traffic Manager verkeer naar het type sturen, omdat het alternatief van het verzenden van geen verkeer niet beter is. Traffic Manager failover niet naar een ander eindpunt, zelfs wanneer de toegewezen regio is een "ouder" van de regio toegewezen aan het eindpunt dat ging ongezond (bijvoorbeeld, als een eindpunt dat regio Spanje heeft ongezond gaat, we niet mislukken naar een ander eindpunt dat heeft de regio Europa toegewezen aan het). Dit wordt gedaan om ervoor te zorgen dat Traffic Manager de geografische grenzen respecteert die een klant in zijn profiel heeft ingesteld. Om het voordeel te krijgen dat het niet overgaat naar een ander eindpunt wanneer een eindpunt niet in orde wordt, wordt aanbevolen geografische regio's toegewezen te krijgen aan geneste profielen met meerdere eindpunten erin in plaats van afzonderlijke eindpunten. Als een eindpunt in het geneste onderliggende profiel op deze manier mislukt, kan het verkeer mislukken naar een ander eindpunt in hetzelfde geneste onderliggende profiel.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Zijn er beperkingen op de API-versie die dit routeringtype ondersteunt?

Ja, alleen API-versie 2017-03-01 en nieuwer ondersteunt het type Geografische routering. Oudere API-versies kunnen niet worden gebruikt om profielen van het type geografische routering te maken of geografische regio's toe te wijzen aan eindpunten. Als een oudere API-versie wordt gebruikt om profielen uit een Azure-abonnement op te halen, wordt geen enkel profiel van een geografisch routeringstype geretourneerd. Bovendien heeft elk profiel dat is geretourneerd met eindpunten met een geografische regiotoewijzing, bij het gebruik van oudere API-versies de geografische regiotoewijzing niet weergegeven.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Routeringsmethode voor subnetverkeer van Traffic Manager

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Wat zijn enkele use cases waarbij subnet routing nuttig is?

Subnet routing u de ervaring die u levert voor specifieke sets van gebruikers geïdentificeerd door de bron IP van hun DNS-verzoeken IP-adres te differentiëren. Een voorbeeld zou zijn om verschillende inhoud weer te geven als gebruikers verbinding maken met een website vanuit uw hoofdkantoor. Een andere zou zijn het beperken van gebruikers van bepaalde ISP's om alleen toegang tot eindpunten die alleen IPv4-verbindingen ondersteunen als deze ISP's sub-par prestaties hebben wanneer IPv6 wordt gebruikt.
Een andere reden om subnetrouteringsmethode te gebruiken, is in combinatie met andere profielen in een geneste profielset. Als u bijvoorbeeld de geografische routeringsmethode wilt gebruiken voor het geofencing van uw gebruikers, maar voor een specifieke ISP die u een andere routeringsmethode wilt doen, u een profiel met een Subnet-routeringsmethode als bovenliggend profiel hebben en die ISP overschrijven om een specifiek kind te gebruiken profiel en hebben de standaard Geografisch profiel voor iedereen.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Hoe weet Traffic Manager het IP-adres van de eindgebruiker?

Eindgebruikers gebruiken doorgaans een DNS-resolver om de DNS-lookup namens hen uit te voeren. De uitgaande IP van dergelijke resolvers is wat Traffic Manager ziet als de bron IP. Daarnaast wordt er ook gekeken of er EDNS0 Extended Client Subnet (ECS)-informatie is die met de aanvraag is doorgegeven. Als ECS-informatie aanwezig is, is dat het adres dat wordt gebruikt om de routering te bepalen. Bij afwezigheid van ECS-informatie wordt het bron-IP van de query gebruikt voor routeringsdoeleinden.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Hoe kan ik IP-adressen opgeven bij het gebruik van Subnet-routering?

De IP-adressen die aan een eindpunt moeten worden gekoppeld, kunnen op twee manieren worden opgegeven. Ten eerste u de quad gestippelde octet-notatie met een begin- en eindadres gebruiken om het bereik op te geven (bijvoorbeeld 1.2.3.4-5.6.7.7 of 3.4.5.6-3.4.5.6). Ten tweede u de CIDR-notatie gebruiken om het bereik op te geven (bijvoorbeeld 1.2.3.0/24). U meerdere bereiken opgeven en beide notatietypen in een bereikset gebruiken. Er gelden enkele beperkingen.

-    U geen overlapping van adresbereiken hebben, omdat elk IP-adres moet worden toegewezen aan slechts één eindpunt
-    Het beginadres mag niet meer zijn dan het eindadres
-    In het geval van de CIDR-notatie moet het IP-adres vóór de "/" het startadres van dat bereik zijn (bijvoorbeeld 1.2.3.0/24 is geldig, maar 1.2.3.4.4/24 is NIET geldig)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Hoe kan ik een terugvaleindpunt opgeven bij het gebruik van Subnet-routering?

In een profiel met Subnet routing, als u een eindpunt hebt zonder subnetten toegewezen aan het, zal elk verzoek dat niet overeenkomt met andere eindpunten hier worden gericht. Het wordt ten zeerste aanbevolen dat u zo'n fallback-eindpunt in uw profiel hebt, aangezien Traffic Manager een NXDOMAIN-antwoord retourneert als er een verzoek binnenkomt en het niet is toegewezen aan eindpunten of als het is toegewezen aan een eindpunt, maar dat eindpunt niet in orde is.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Wat gebeurt er als een eindpunt is uitgeschakeld in een subnetrouteringstypeprofiel?

In een profiel met Subnet-routering, als u een eindpunt hebt dat is uitgeschakeld, gedraagt Traffic Manager zich alsof dat eindpunt en de subnettoewijzingen die het heeft, niet bestaan. Als een query die zou zijn gekoppeld aan de IP-adrestoewijzing is ontvangen en het eindpunt is uitgeschakeld, retourneert Traffic Manager een terugvaleindpunt (een query zonder toewijzingen) of als een dergelijk eindpunt niet aanwezig is, retourneert u een NXDOMAIN-antwoord.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Verkeersbeheer multivalueringsmethode voor verkeer

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Wat zijn enkele use cases waarbij MultiValue routing nuttig is?

MultiValue-routering retourneert meerdere gezonde eindpunten in één queryantwoord. Het belangrijkste voordeel hiervan is dat, als een eindpunt ongezond is, de client meer opties heeft om opnieuw te proberen zonder een andere DNS-oproep te doen (wat dezelfde waarde uit een upstream-cache zou kunnen retourneren). Dit is van toepassing op beschikbaarheidsgevoelige toepassingen die de downtime willen minimaliseren.
Een ander gebruik voor MultiValue routing methode is als een eindpunt is "dual-homed" om zowel IPv4 en IPv6 adressen en u wilt de beller beide opties om uit te kiezen wanneer het een verbinding met het eindpunt initieert geven.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Hoeveel eindpunten worden geretourneerd wanneer MultiValue-routering wordt gebruikt?

U het maximum aantal eindpunten opgeven dat moet worden geretourneerd en MultiValue retourneert niet meer dan zoveel gezonde eindpunten wanneer een query wordt ontvangen. De maximaal mogelijke waarde voor deze configuratie is 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Krijg ik dezelfde set eindpunten wanneer MultiValue-routering wordt gebruikt?

We kunnen niet garanderen dat dezelfde set eindpunten in elke query wordt geretourneerd. Dit wordt ook beïnvloed door het feit dat sommige eindpunten ongezond kunnen worden op welk punt ze niet zullen worden opgenomen in de reactie

## <a name="real-user-measurements"></a>Real-user-metingen

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Wat zijn de voordelen van het gebruik van Real User Measurements?

Wanneer u de methode voor prestatieroutering gebruikt, kiest Traffic Manager de beste Azure-regio waarmee uw eindgebruiker verbinding kan maken door het bron-IP- en EDNS-clientsubnet te inspecteren (indien doorgegeven) en deze te controleren op basis van de netwerklatentie-intelligentie die de service onderhoudt. Real User Measurements verbetert dit voor uw eindgebruiker door dat hun ervaring bijdraagt aan deze latentietabel, naast het feit dat deze tabel de eindgebruikersnetwerken adequaat overspant van waaruit uw eindgebruikers verbinding maken met Azure. Dit leidt tot een grotere nauwkeurigheid in de routering van uw eindgebruiker.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Kan ik echte gebruikersmetingen gebruiken met niet-Azure-regio's?

Real User Measurements meet en rapporteert alleen over de latentie om Azure-regio's te bereiken. Als u op prestaties gebaseerde routering gebruikt met eindpunten die worden gehost in niet-Azure-regio's, u nog steeds profiteren van deze functie door meer latentie-informatie te hebben over de representatieve Azure-regio die u had geselecteerd om aan dit eindpunt te worden gekoppeld.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Welke routeringsmethode profiteert van real user measurements?

De aanvullende informatie die wordt verkregen via real user measurements zijn alleen van toepassing op profielen die de methode voor prestatieroutering gebruiken. De koppeling Echte gebruikersmetingen is beschikbaar vanaf alle profielen wanneer u deze bekijkt via de Azure-portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Moet ik elke profielafzonderlijk echte gebruikersmetingen inschakelen?

Nee, u hoeft het slechts één keer per abonnement in te schakelen en alle gemeten en gerapporteerde latentie-informatie is beschikbaar voor alle profielen.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Hoe schakel ik echte gebruikersmetingen voor mijn abonnement uit?

U stoppen met het maken van kosten in verband met real user measurements wanneer u stopt met het verzamelen en terugsturen van latentiemetingen van uw clienttoepassing. Wanneer u bijvoorbeeld JavaScript insluit in webpagina's, u deze functie niet meer gebruiken door het JavaScript te verwijderen of door de aanroep uit te schakelen wanneer de pagina wordt weergegeven.

U ook echte gebruikersmetingen uitschakelen door uw sleutel te verwijderen. Zodra u de sleutel verwijdert, worden alle metingen die met die sleutel naar Traffic Manager worden verzonden, verwijderd.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Kan ik echte gebruikersmetingen gebruiken met andere clienttoepassingen dan webpagina's?

Ja, Real User Measurements is ontworpen om gegevens in te nemen die zijn verzameld via verschillende soorten eindgebruikersclients. Deze veelgestelde vragen worden bijgewerkt wanneer nieuwe typen clienttoepassingen worden ondersteund.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hoeveel metingen worden er elke keer verricht wanneer mijn webpagina met echte gebruikersmetingen wordt weergegeven?

Wanneer Real User Measurements wordt gebruikt met de meegeleverde measurement JavaScript, resulteert elke paginarendering in zes metingen die worden uitgevoerd. Deze worden vervolgens teruggemeld aan de Traffic Manager-service. Voor deze functie worden kosten in rekening gebracht op basis van het aantal metingen dat is gerapporteerd aan de Traffic Manager-service. Als de gebruiker bijvoorbeeld wegnavigeert van uw webpagina terwijl de metingen worden uitgevoerd, maar voordat deze zijn gerapporteerd, worden deze metingen niet in aanmerking genomen voor factureringsdoeleinden.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Is er een vertraging voordat Real User Measurements script wordt uitgevoerd in mijn webpagina?

Nee, er is geen geprogrammeerde vertraging voordat het script wordt ingeroepen.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Kan ik echte gebruikersmetingen gebruiken met alleen de Azure-regio's die ik wil meten?

Nee, elke keer dat het wordt aangeroepen, meet het script Echte gebruikersmetingen een set van zes Azure-regio's, zoals bepaald door de service. Deze set verandert tussen verschillende aanroepen en wanneer een groot aantal van dergelijke aanroepen plaatsvindt, de meting dekking oververschillende Azure-regio's.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Kan ik het aantal metingen beperken tot een bepaald aantal?

De meting JavaScript is ingebed in uw webpagina en u bent in volledige controle over wanneer te starten en te stoppen met het gebruik ervan. Zolang de Traffic Manager-service een aanvraag ontvangt voor een lijst met Azure-regio's die moeten worden gemeten, wordt een reeks regio's geretourneerd.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Kan ik de metingen van mijn clientapplicatie zien als onderdeel van Real User Measurements?

Aangezien de meetlogica wordt uitgevoerd vanaf uw clienttoepassing, hebt u volledige controle over wat er gebeurt, inclusief het zien van de latentiemetingen. Traffic Manager rapporteert geen geaggregeerde weergave van de metingen die zijn ontvangen onder de sleutel die aan uw abonnement is gekoppeld.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Kan ik het meetscript van Traffic Manager wijzigen?

Terwijl u de controle hebt over wat er op uw webpagina is ingesloten, raden we u ten zeerste af om wijzigingen aan te brengen in het meetscript om ervoor te zorgen dat het de latencies correct meet en rapporteert.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Is het mogelijk voor anderen om de sleutel te zien die ik gebruik met Real User Measurements?

Wanneer u het meetscript insluit op een webpagina, kunnen anderen het script en de RUM-toets (Real User Measurements) zien. Maar het is belangrijk om te weten dat deze sleutel verschilt van uw abonnements-id en wordt gegenereerd door Traffic Manager om alleen voor dit doel te worden gebruikt. Als u weet dat uw RUM-sleutel de veiligheid van uw Azure-account niet in gevaar brengt.

### <a name="can-others-abuse-my-rum-key"></a>Kunnen anderen misbruik maken van mijn RUM-sleutel?

Hoewel het voor anderen mogelijk is om uw sleutel te gebruiken om verkeerde informatie naar Azure te verzenden, zullen een paar verkeerde metingen de routering niet veranderen omdat deze in aanmerking wordt genomen, samen met alle andere metingen die we ontvangen. Als u uw sleutels moet wijzigen, u de sleutel opnieuw genereren op welk punt de oude sleutel wordt weggegooid.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Moet ik de meting JavaScript in al mijn webpagina's plaatsen?

Real User Measurements levert meer waarde naarmate het aantal metingen toeneemt. Dat gezegd hebbende, het is uw beslissing over de vraag of je nodig hebt om het te zetten in al uw webpagina's of een select aantal. Onze aanbeveling is om te beginnen door het in uw meest bezochte pagina waar een gebruiker wordt verwacht te blijven op die pagina vijf seconden of meer.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Kan informatie over mijn eindgebruikers door Traffic Manager worden geïdentificeerd als ik echte gebruikersmetingen gebruik?

Wanneer de meegeleverde meting JavaScript wordt gebruikt, heeft Traffic Manager inzicht in het IP-adres van de eindgebruiker en het bron-IP-adres van de lokale DNS-resolver die ze gebruiken. Traffic Manager gebruikt het IP-adres van de client alleen nadat het is afgekapt om de specifieke eindgebruiker die de metingen heeft verzonden niet te kunnen identificeren.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Moet de webpagina die echte gebruikersmetingen meet, Traffic Manager gebruiken voor routering?

Nee, het hoeft geen Traffic Manager te gebruiken. De routeringskant van Traffic Manager werkt los van het gedeelte Real User Measurement en hoewel het een geweldig idee is om ze allebei in dezelfde webeigenschap te hebben, hoeven ze dat niet te zijn.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Moet ik een service hosten op Azure-regio's die kunnen worden gebruikt met echte gebruikersmetingen?

Nee, u hoeft geen servercomponent op Azure te hosten, omdat real-gebruikersmetingen werken. De single pixel image die wordt gedownload door de meting JavaScript en de service die deze uitvoert in verschillende Azure-regio's wordt gehost en beheerd door Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Neemt mijn Azure-bandbreedtegebruik toe wanneer ik echte gebruikersmetingen gebruik?

Zoals vermeld in het vorige antwoord, zijn de server-side componenten van Real User Measurements eigendom van en beheerd door Azure. Dit betekent dat uw Azure-bandbreedtegebruik niet zal toenemen omdat u echte gebruikersmetingen gebruikt. Dit omvat geen bandbreedtegebruik buiten wat Azure in rekening brengt. We minimaliseren de bandbreedte die wordt gebruikt door slechts één pixelafbeelding te downloaden om de latentie naar een Azure-gebied te meten. 

## <a name="traffic-view"></a>Verkeersweergave

### <a name="what-does-traffic-view-do"></a>Wat doet Traffic View?

Traffic View is een functie van Traffic Manager die u helpt meer te weten te komen over uw gebruikers en hoe hun ervaring is. Het maakt gebruik van de query's die door Traffic Manager zijn ontvangen en de netwerklatentie-intelligentietabellen die de service onderhoudt om u het volgende te bieden:

- De regio's van waaruit uw gebruikers verbinding maken met uw eindpunten in Azure.
- Het aantal gebruikers dat verbinding maakt vanuit deze regio's.
- De Azure-regio's waarnaar ze worden doorgestuurd.
- Hun latentie-ervaring voor deze Azure-regio's.

Deze informatie is beschikbaar voor u om te consumeren via geografische kaart overlay en tabelvormige weergaven in de portal in aanvulling op beschikbaar als ruwe gegevens voor u om te downloaden.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Hoe kan ik profiteren van het gebruik van Traffic View?

Traffic View geeft u het algemene overzicht van het verkeer dat uw Traffic Manager-profielen ontvangen. In het bijzonder kan het worden gebruikt om te begrijpen waar uw user base verbinding maakt van en even belangrijk wat hun gemiddelde latentie ervaring is. U deze informatie vervolgens gebruiken om gebieden te vinden waarin u zich moet concentreren, bijvoorbeeld door uw Azure-voetafdruk uit te breiden naar een regio die gebruikers met een lagere latentie kan bedienen. Een ander inzicht dat u afleiden uit het gebruik van Traffic View is het zien van de patronen van het verkeer naar verschillende regio's, die op hun beurt kan u helpen beslissingen te nemen over het verhogen of verminderen van uitvinden in die regio's.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Hoe verschilt de traffic view van de Traffic Manager-statistieken die beschikbaar zijn via Azure-monitor?

Azure Monitor kan worden gebruikt om op geaggregeerd niveau inzicht te krijgen in het verkeer dat door uw profiel en de eindpunten wordt ontvangen. Het stelt u ook in staat om de status van de eindpunten te volgen door de resultaten van de statuscontrole bloot te leggen. Wanneer u verder moet gaan dan deze en de ervaring van uw eindgebruiker die op regionaal niveau verbinding maakt met Azure, moet u begrijpen dat verkeer wordt gebruikt om dat te bereiken.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Gebruikt Traffic View EDNS Client Subnet-informatie?

De DNS-query's die worden weergegeven door Azure Traffic Manager houden wel rekening met ECS-informatie om de nauwkeurigheid van de routering te verhogen. Maar bij het maken van de gegevensset die aangeeft waar de gebruikers verbinding mee maken, gebruikt Traffic View alleen het IP-adres van de DNS-resolver.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hoeveel dagen gegevens gebruikt Traffic View?

Traffic View maakt de uitvoer door de gegevens te verwerken van de zeven dagen die voorafgaan aan de dag ervoor wanneer deze door u worden bekeken. Dit is een bewegend venster en de meest recente gegevens worden elke keer gebruikt.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hoe gaat Traffic View om met externe eindpunten?

Wanneer u externe eindpunten gebruikt die buiten Azure-regio's worden gehost in een Traffic Manager-profiel, u ervoor kiezen om deze toegewezen te krijgen aan een Azure-regio die een proxy is voor de latentiekenmerken (dit is in feite nodig als u de methode voor prestatieroutering gebruikt). Als deze Azure-regiotoewijzing is, worden de latentiestatistieken van die Azure-regio gebruikt bij het maken van de Traffic View-uitvoer. Als er geen Azure-gebied is opgegeven, is de latentie-informatie leeg in de gegevens voor die externe eindpunten.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Moet ik Traffic View inschakelen voor elk profiel in mijn abonnement?

Tijdens de previewperiode is traffic view ingeschakeld op abonnementsniveau. Als onderdeel van de verbeteringen die we vóór de algemene beschikbaarheid hebben aangebracht, u traffic view nu op profielniveau inschakelen, zodat u deze functie gedetailleerder inschakelen. De verkeersweergave wordt standaard uitgeschakeld voor een profiel.

>[!NOTE]
>Als u Traffic View tijdens de preview-tijd op abonnementsniveau hebt ingeschakeld, moet u deze nu opnieuw inschakelen voor elk profiel onder dat abonnement.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hoe kan ik Traffic View uitschakelen?

U Traffic View uitschakelen voor elk profiel met behulp van de Portal of REST API. 

### <a name="how-does-traffic-view-billing-work"></a>Hoe werkt Traffic View-facturering?

De tarifering van traffic view is gebaseerd op het aantal gegevenspunten dat wordt gebruikt om de uitvoer te maken. Momenteel wordt alleen het gegevenstype ondersteund, de query's die uw profiel ontvangt. Bovendien worden er alleen kosten in rekening gebracht voor de verwerking die is uitgevoerd wanneer u Traffic View hebt ingeschakeld. Dit betekent dat, als u Traffic View voor enige tijd in een maand inschakelt en deze op andere tijden uitschakelt, alleen de gegevenspunten worden verwerkt terwijl u de functie hebt laten tellen voor uw factuur.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-eindpunten

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Kan ik Traffic Manager gebruiken met eindpunten van meerdere abonnementen?

Het gebruik van eindpunten van meerdere abonnementen is niet mogelijk met Azure Web Apps. Azure Web Apps vereist dat elke aangepaste domeinnaam die wordt gebruikt met Web Apps alleen binnen één abonnement wordt gebruikt. Het is niet mogelijk om Web Apps te gebruiken van meerdere abonnementen met dezelfde domeinnaam.

Voor andere eindpunttypen is het mogelijk om Traffic Manager te gebruiken met eindpunten van meer dan één abonnement. In Resourcemanager kunnen eindpunten van elk abonnement worden toegevoegd aan Traffic Manager, zolang de persoon die het Traffic Manager-profiel configureert, de toegang tot het eindpunt heeft gelezen. Deze machtigingen kunnen worden verleend met behulp van [Azure Resource Manager-functiegebaseerde toegangscontrole (RBAC).](../role-based-access-control/role-assignments-portal.md)

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Kan ik Traffic Manager gebruiken met 'Staging'-sleuven van Cloud Service?

Ja. 'Staging'-sleuven voor Cloud Service kunnen worden geconfigureerd in Traffic Manager als externe eindpunten. Er worden nog steeds statuscontroles in rekening gebracht met het Azure Endpoints-tarief.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Ondersteunt Traffic Manager IPv6-eindpunten?

Traffic Manager biedt momenteel geen IPv6-adresseerbare naamservers. Traffic Manager kan echter nog steeds worden gebruikt door IPv6-clients die verbinding maken met IPv6-eindpunten. Een client doet geen DNS-verzoeken rechtstreeks aan Traffic Manager. In plaats daarvan maakt de client gebruik van een recursieve DNS-service. Een IPv6-client stuurt aanvragen naar de recursieve DNS-service via IPv6. Dan moet de recursieve service contact kunnen opnemen met de naamservers van Traffic Manager via IPv4.

Traffic Manager reageert met de DNS-naam of het IP-adres van het eindpunt. Om een IPv6-eindpunt te ondersteunen, zijn er twee opties. U het eindpunt toevoegen als een DNS-naam met een bijbehorende AAAA-record en Traffic Manager controleert dat eindpunt en retourneert het als een CNAME-recordtype in het queryantwoord. U dat eindpunt ook rechtstreeks toevoegen met het IPv6-adres en Traffic Manager retourneert een AAAA-typerecord in het queryantwoord.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Kan ik Traffic Manager gebruiken met meer dan één web-app in dezelfde regio?

Verkeersbeheer wordt doorgaans gebruikt om verkeer te leiden naar toepassingen die in verschillende regio's zijn geïmplementeerd. Het kan echter ook worden gebruikt wanneer een toepassing meer dan één implementatie in dezelfde regio heeft. Met de Azure-eindpunten voor Traffic Manager kunnen niet meer dan één Web App-eindpunt uit dezelfde Azure-regio worden toegevoegd aan hetzelfde Traffic Manager-profiel.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Hoe verplaats ik de Azure-eindpunten van mijn Traffic Manager-profiel naar een andere brongroep of -abonnement?

Azure-eindpunten die zijn gekoppeld aan een Traffic Manager-profiel worden bijgehouden met behulp van hun resource-id's. Wanneer een Azure-bron die wordt gebruikt als eindpunt (bijvoorbeeld Openbaar IP, Classic Cloud Service, WebApp of een ander Traffic Manager-profiel dat op een geneste manier wordt gebruikt) wordt verplaatst naar een andere resourcegroep of -abonnement, wordt de bron-id gewijzigd. In dit scenario moet u momenteel het Traffic Manager-profiel bijwerken door eerst de eindpunten aan het profiel te verwijderen en vervolgens weer toe te voegen.

## <a name="traffic-manager-endpoint-monitoring"></a>Eindpuntbewaking in Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Is Traffic Manager bestand tegen azure-regiofouten?

Traffic Manager is een belangrijk onderdeel van de levering van zeer beschikbare toepassingen in Azure.
Om een hoge beschikbaarheid te kunnen leveren, moet Traffic Manager een uitzonderlijk hoog beschikbaarheidsniveau hebben en bestand zijn tegen regionale storingen.

Door het ontwerp zijn Traffic Manager-componenten bestand tegen een volledige fout van een Azure-regio. Deze weerbaarheid is van toepassing op alle Traffic Manager-componenten: de DNS-naamservers, de API, de opslaglaag en de endpoint-bewakingsservice.

In het onwaarschijnlijke geval van een storing in een hele Azure-regio zal Traffic Manager naar verwachting normaal blijven functioneren. Toepassingen die in meerdere Azure-regio's zijn geïmplementeerd, kunnen afhankelijk zijn van Traffic Manager om verkeer naar een beschikbare instantie van hun toepassing te leiden.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Welke invloed heeft de locatie van de resourcegroep op Traffic Manager?

Traffic Manager is één wereldwijde service. Het is niet regionaal. De keuze van de locatie van de resourcegroep maakt geen verschil voor Traffic Manager-profielen die in die resourcegroep zijn geïmplementeerd.

Azure Resource Manager vereist dat alle resourcegroepen een locatie opgeven, die de standaardlocatie bepaalt voor resources die in die resourcegroep zijn geïmplementeerd. Wanneer u een Traffic Manager-profiel maakt, wordt het gemaakt in een resourcegroep. Alle Traffic Manager-profielen gebruiken **globaal** als locatie, en overschrijven de standaardgroep resourcegroep.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hoe bepaal ik de huidige status van elk eindpunt?

De huidige bewakingsstatus van elk eindpunt wordt, naast het algemene profiel, weergegeven in de Azure-portal. Deze informatie is ook beschikbaar via de Traffic Monitor [REST API,](https://msdn.microsoft.com/library/azure/mt163667.aspx) [PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/az.trafficmanager)en [cross-platform Azure CLI](../cli-install-nodejs.md).

U Azure Monitor ook gebruiken om de status van uw eindpunten bij te houden en een visuele weergave ervan te bekijken. Zie de [Azure Monitoring-documentatie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)voor meer informatie over het gebruik van Azure Monitor.

### <a name="can-i-monitor-https-endpoints"></a>Kan ik HTTPS-eindpunten controleren?

Ja. Traffic Manager ondersteunt indringende via HTTPS. Configureer **HTTPS** als het protocol in de bewakingsconfiguratie.

Verkeersbeheerder kan geen certificaatvalidatie bieden, waaronder:

* Servercertificaten worden niet gevalideerd
* SNI-servercertificaten worden niet gevalideerd
* Clientcertificaten worden niet ondersteund

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Gebruik ik een IP-adres of een DNS-naam bij het toevoegen van een eindpunt?

Traffic Manager ondersteunt het toevoegen van eindpunten met behulp van drie manieren om ze te verwijzen - als een DNS-naam, als een IPv4-adres en als een IPv6-adres. Als het eindpunt wordt toegevoegd als een IPv4- of IPv6-adres, is het queryantwoord respectievelijk van recordtype A of AAAA. Als het eindpunt is toegevoegd als DNS-naam, is het queryantwoord van recordtype CNAME. Het toevoegen van eindpunten als IPv4- of IPv6-adres is alleen toegestaan als het eindpunt van het type **Extern**is .
Alle routeringsmethoden en bewakingsinstellingen worden ondersteund door de drie typen endpoint-adressering.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Welke typen IP-adressen kan ik gebruiken bij het toevoegen van een eindpunt?

Met Traffic Manager u IPv4- of IPv6-adressen gebruiken om eindpunten op te geven. Er zijn een paar beperkingen die hieronder worden vermeld:

- Adressen die overeenkomen met gereserveerde privé-IP-adresruimten zijn niet toegestaan. Deze adressen omvatten die uitgeroepen in RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 en RFC 5771
- Het adres mag geen poortnummers bevatten (u de poorten opgeven die moeten worden gebruikt in de profielconfiguratie-instellingen)
- Geen twee eindpunten in hetzelfde profiel mogen hetzelfde doel-IP-adres hebben

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Kan ik verschillende endpoint-adresseringstypen in één profiel gebruiken?

Nee, Traffic Manager staat u niet toe om endpoint-adresseringstypen binnen een profiel te mengen, behalve voor het geval van een profiel met multivalue-routeringstype waarbij u IPv4- en IPv6-adresseringstypen mengen

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Wat gebeurt er als het recordtype van een binnenkomende query verschilt van het recordtype dat is gekoppeld aan het adresseringstype van de eindpunten?

Wanneer een query wordt ontvangen tegen een profiel, vindt Traffic Manager eerst het eindpunt dat moet worden geretourneerd volgens de opgegeven routeringsmethode en de status van de eindpunten. Vervolgens wordt gekeken naar het recordtype dat is aangevraagd in de binnenkomende query en het recordtype dat aan het eindpunt is gekoppeld voordat een antwoord wordt teruggegeven op basis van de onderstaande tabel.

Voor profielen met een andere routeringsmethode dan MultiValue:

|Binnenkomende queryaanvraag|     Eindpunttype|     Reactie aangeboden|
|--|--|--|
|ALLE |    A / AAAA / CNAME |    Doeleindpunt| 
|A |    A / CNAME |    Doeleindpunt|
|A |    AAAA |    NODATA NODATA |
|AAAA |    AAAA / CNAME |    Doeleindpunt|
|AAAA |    A |    NODATA NODATA |
|CNAME |    CNAME |    Doeleindpunt|
|CNAME     |A / AAAA |    NODATA NODATA |
|

Voor profielen met routeringsmethode ingesteld op MultiValue:

|Binnenkomende queryaanvraag|     Eindpunttype |    Reactie aangeboden|
|--|--|--|
|ALLE |    Mix van A en AAAA |    Doeleindpunten|
|A |    Mix van A en AAAA |    Alleen doeleindpunten van type A|
|AAAA    |Mix van A en AAAA|     Alleen doeleindpunten van type AAAA|
|CNAME |    Mix van A en AAAA |    NODATA NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Kan ik een profiel met IPv4/ IPv6 geadresseerde eindpunten gebruiken in een genest profiel?

Ja, u met de uitzondering dat een profiel van het type MultiValue geen bovenliggend profiel in een geneste profielset kan zijn.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Ik stopte een eindpunt van een webtoepassing in mijn Traffic Manager-profiel, maar ik ontvang geen verkeer, zelfs niet nadat ik het opnieuw heb opgestart. Hoe kan ik dit oplossen?

Wanneer een eindpunt van een Azure-webtoepassing wordt gestopt, stopt Traffic Manager met het controleren van de status en start de statuscontroles pas nadat wordt gedetecteerd dat het eindpunt opnieuw is gestart. Als u deze vertraging wilt voorkomen, schakelt u dat eindpunt in het profiel Traffic Manager uit en schakelt u dit vervolgens opnieuw in nadat u het eindpunt opnieuw hebt opgestart.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Kan ik Traffic Manager gebruiken, zelfs als mijn toepassing geen ondersteuning heeft voor HTTP of HTTPS?

Ja. U TCP opgeven omdat het monitoringprotocol en Traffic Manager een TCP-verbinding kunnen starten en kunnen wachten op een antwoord vanaf het eindpunt. Als het eindpunt binnen de time-outperiode op de verbindingsaanvraag antwoordt met een antwoord om de verbinding tot stand te brengen, wordt dat eindpunt gemarkeerd als gezond.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Welke specifieke antwoorden zijn vereist vanaf het eindpunt bij het gebruik van TCP-monitoring?

Wanneer TCP-bewaking wordt gebruikt, start Traffic Manager een TCP-handshake in drie richtingen door een SYN-aanvraag naar eindpunt op de opgegeven poort te verzenden. Het wacht dan op een SYN-ACK-antwoord van het eindpunt gedurende een bepaalde periode (opgegeven in de time-outinstellingen).

- Als een SYN-ACK-antwoord wordt ontvangen binnen de time-outperiode die is opgegeven in de controle-instellingen, wordt dat eindpunt als gezond beschouwd. Een FIN of FIN-ACK is de verwachte reactie van de Traffic Manager wanneer deze regelmatig een socket beëindigt.
- Als een SYN-ACK-antwoord wordt ontvangen na de opgegeven time-out, reageert de Traffic Manager met een RST om de verbinding opnieuw in te stellen.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Hoe snel haalt Traffic Manager mijn gebruikers weg van een ongezond eindpunt?

Traffic Manager biedt meerdere instellingen waarmee u het failovergedrag van uw Traffic Manager-profiel als volgt beheren:

- U opgeven dat de Traffic Manager de eindpunten vaker sondeert door het indringende interval op 10 seconden in te stellen. Dit zorgt ervoor dat elk eindpunt dat ongezond wordt zo snel mogelijk kan worden gedetecteerd. 
- u opgeven hoe lang u moet wachten voordat een time-out van een aanvraag voor een statuscontrole is (de minimale time-outwaarde is 5 sec).
- U opgeven hoeveel fouten kunnen optreden voordat het eindpunt als ongezond wordt gemarkeerd. Deze waarde kan laag zijn als 0, in welk geval het eindpunt als ongezond is gemarkeerd zodra de eerste statuscontrole mislukt. Echter, met behulp van de minimale waarde van 0 voor het getolereerde aantal fouten kan leiden tot eindpunten worden genomen uit de rotatie als gevolg van eventuele tijdelijke problemen die kunnen optreden op het moment van indringende.
- u de time-to-live (TTL) opgeven voordat het DNS-antwoord zo laag is als 0. Dit betekent dat DNS-resolvers het antwoord niet in de cache kunnen opslaan en dat elke nieuwe query een antwoord krijgt dat de meest up-to-date gezondheidsinformatie bevat die de Traffic Manager heeft.

Met behulp van deze instellingen kan Traffic Manager failovers bieden minder dan 10 seconden nadat een eindpunt ongezond wordt en een DNS-query wordt uitgevoerd tegen het bijbehorende profiel.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hoe kan ik verschillende bewakingsinstellingen opgeven voor verschillende eindpunten in een profiel?

De bewakingsinstellingen van Traffic Manager bevinden zich op een profielniveau per profiel. Als u een andere bewakingsinstelling voor slechts één eindpunt moet gebruiken, kan dit worden gedaan door dat eindpunt te hebben als een [genest profiel](traffic-manager-nested-profiles.md) waarvan de bewakingsinstellingen verschillen van het bovenliggende profiel.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Hoe kan ik HTTP-headers toewijzen aan de statuscontroles van Traffic Manager aan mijn eindpunten?

Met Traffic Manager u aangepaste kopteksten opgeven in de HTTP(S)-statuscontroles die wordt gestart naar uw eindpunten. Als u een aangepaste koptekst wilt opgeven, u dat doen op profielniveau (van toepassing op alle eindpunten) of deze opgeven op eindpuntniveau. Als een koptekst op beide niveaus wordt gedefinieerd, overschrijft de op eindpuntniveau opgegeven koptekst het profielniveau één.
Een veelgebruikte use case hiervoor is het opgeven van hostheaders, zodat Traffic Manager-aanvragen correct kunnen worden doorgestuurd naar een eindpunt dat wordt gehost in een omgeving met meerdere tenant's. Een andere use case hiervan is het identificeren van Traffic Manager-aanvragen uit de HTTP(S)-aanvraaglogboeken van een eindpunt

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Welke hostheader gebruiken eindpuntstatuscontroles?

Als er geen aangepaste hostheaderinstelling is opgegeven, is de hostkopdie door Traffic Manager wordt gebruikt de DNS-naam van het eindpuntdoel dat in het profiel is geconfigureerd, als dat beschikbaar is.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Wat zijn de IP-adressen waar de gezondheidscontroles vandaan komen?

Klik [hier](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) om het JSON-bestand te bekijken met de IP-adressen waaruit traffic manager-statuscontroles afkomstig kunnen zijn. Bekijk de IP's in het JSON-bestand om ervoor te zorgen dat binnenkomende verbindingen van deze IP-adressen op de eindpunten zijn toegestaan om de status van de status ervan te controleren.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hoeveel gezondheidscontroles voor mijn eindpunt kan ik van Traffic Manager verwachten?

Het aantal statuscontroles van Traffic Manager dat uw eindpunt bereikt, is afhankelijk van het volgende:

- de waarde die u hebt ingesteld voor het controle-interval (kleiner interval betekent dat er meer aanvragen op uw eindpunt landen in een bepaalde periode).
- het aantal locaties waar de statuscontroles vandaan komen (de IP-adressen van waaruit u verwachten dat deze controles worden vermeld in de voorgaande faq).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Hoe kan ik een melding krijgen als een van mijn eindpunten naar beneden gaat?

Een van de statistieken van Traffic Manager is de status van eindpunten in een profiel. U dit zien als een totaal van alle eindpunten in een profiel (bijvoorbeeld 75% van uw eindpunten zijn gezond), of, op een niveau per eindpunt. Traffic Manager-statistieken worden weergegeven via Azure Monitor en u de [waarschuwingsmogelijkheden](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) gebruiken om meldingen te ontvangen wanneer de status van uw eindpunt wordt gewijzigd. Zie [Statistieken en waarschuwingen voor Verkeersbeheer](traffic-manager-metrics-alerts.md)voor meer informatie.  

## <a name="traffic-manager-nested-profiles"></a>Geneste profielen van verkeersbeheerder

### <a name="how-do-i-configure-nested-profiles"></a>Hoe configureer ik geneste profielen?

Geneste Traffic Manager-profielen kunnen worden geconfigureerd met behulp van zowel Azure Resource Manager als de klassieke Azure REST API's, Azure PowerShell-cmdlets en azure CLI-opdrachten voor meerdere platforms. Ze worden ook ondersteund via de nieuwe Azure-portal.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Hoeveel nestlagen ondersteunt Traffic Manger?

U profielen tot 10 niveaus diep nesten. 'Loops' zijn niet toegestaan.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Kan ik andere eindpunttypen mengen met geneste onderliggende profielen in hetzelfde Traffic Manager-profiel?

Ja. Er zijn geen beperkingen voor het combineren van eindpunten van verschillende typen binnen een profiel.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hoe is het factureringsmodel van toepassing op geneste profielen?

Er is geen negatieve prijsimpact van het gebruik van geneste profielen.

Traffic Manager-facturering bestaat uit twee componenten: statuscontroles van eindpunten en miljoenen DNS-query's

* Statuscontroles voor eindpunt: er worden geen kosten in rekening gebracht voor een onderliggend profiel wanneer het is geconfigureerd als eindpunt in een bovenliggend profiel. Het controleren van de eindpunten in het onderliggende profiel wordt op de gebruikelijke manier gefactureerd.
* DNS-query's: Elke query wordt slechts één keer geteld. Een query ten opzichte van een bovenliggend profiel dat een eindpunt uit een onderliggend profiel retourneert, wordt alleen op het bovenliggende profiel geteld.

Zie de [prijspagina Traffic Manager voor](https://azure.microsoft.com/pricing/details/traffic-manager/)meer informatie.

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Is er een prestatie-impact voor geneste profielen?

Nee. Er is geen impact op de prestaties bij het gebruik van geneste profielen.

De naamservers van Traffic Manager doorkruisen de profielhiërarchie intern bij het verwerken van elke DNS-query. Een DNS-query naar een bovenliggend profiel kan een DNS-antwoord ontvangen met een eindpunt uit een onderliggend profiel. Er wordt één CNAME-record gebruikt, ongeacht of u één profiel of geneste profielen gebruikt. Het is niet nodig om voor elk profiel in de hiërarchie een CNAME-record te maken.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hoe berekent Traffic Manager de status van een genest eindpunt in een bovenliggend profiel?

Het bovenliggende profiel voert niet rechtstreeks gezondheidscontroles uit op het kind. In plaats daarvan wordt de status van de eindpunten van het kindprofiel gebruikt om de algehele status van het onderliggende profiel te berekenen. Deze informatie wordt doorgegeven in de geneste profielhiërarchie om de status van het geneste eindpunt te bepalen. Het bovenliggende profiel gebruikt deze geaggregeerde status om te bepalen of het verkeer naar het kind kan worden geleid.

In de volgende tabel wordt het gedrag beschreven van statuscontroles van Traffic Manager voor een genest eindpunt.

| Status van onderliggende profielmonitor | Status bovenliggende eindpuntmonitor | Opmerkingen |
| --- | --- | --- |
| Uitgeschakeld. Het onderliggende profiel is uitgeschakeld. |Gestopt |De bovenliggende eindpuntstatus is Gestopt, niet uitgeschakeld. De status Uitgeschakeld is gereserveerd om aan te geven dat u het eindpunt in het bovenliggende profiel hebt uitgeschakeld. |
| Gedegradeerd. Ten minste één eindpunt voor onderliggend profiel is in een gedegradeerde status. |Online: het aantal online eindpunten in het onderliggende profiel is ten minste de waarde van MinChildEndpoints.<BR>CheckEndpoint: het aantal Online plus CheckingEndpoint-eindpunten in het onderliggende profiel is ten minste de waarde van MinChildEndpoints.<BR>Gedegradeerd: anders. |Verkeer wordt doorgestuurd naar een eindpunt van status CheckingEndpoint. Als MinChildEndpoints te hoog is ingesteld, wordt het eindpunt altijd afgebroken. |
| Online. Ten minste één eindpunt voor onderliggende profielen is een status Online. Er is geen eindpunt in de gedegradeerde status. |Zie hierboven. | |
| Punten controleren. Ten minste één eindpunt voor onderliggend e-profiel is 'CheckingEndpoint'. Er zijn geen eindpunten 'Online' of 'Gedegradeerd' |Hetzelfde als hierboven. | |
| Inactief. Alle onderliggende profieleindpunten zijn uitgeschakeld of gestopt, of dit profiel heeft geen eindpunten. |Gestopt | |

## <a name="next-steps"></a>Volgende stappen:

- Meer informatie over eindpuntbewaking van Traffic Manager [en automatische failover](../traffic-manager/traffic-manager-monitoring.md).
- Meer informatie over [verkeersrouteringsmethoden voor](../traffic-manager/traffic-manager-routing-methods.md)Traffic Manager .

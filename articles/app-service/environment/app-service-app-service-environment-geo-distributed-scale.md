---
title: Geo gedistribueerde schaal
description: Meer informatie over het horizontaal schalen van apps met behulp van geodistributie met Traffic Manager- en App-serviceomgevingen.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 7ab04e23b838f2dfd39b73476db7492947d62e6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688818"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Geografisch gedistribueerde schaal met App Service-omgevingen
## <a name="overview"></a>Overzicht

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Toepassingsscenario's waarvoor een zeer hoge schaal nodig is, kunnen de beschikbare rekencapaciteit van een app overschrijden.  Stemtoepassingen, sportevenementen en entertainmentevenementen op televisie zijn allemaal voorbeelden van scenario's die extreem op grote schaal vereisen. Aan hoge schaalvereisten kan worden voldaan door apps horizontaal uit te schalen, waarbij meerdere app-implementaties worden uitgevoerd binnen één regio en in verschillende regio's, om extreme belastingsvereisten aan te kunnen.

App Service Environments zijn een ideaal platform voor horizontale uitschaiking.  Zodra een app-serviceomgevingconfiguratie is geselecteerd die een bekend verzoekpercentage kan ondersteunen, kunnen ontwikkelaars extra App-serviceomgevingen op "cookiecutter"-manier implementeren om een gewenste piekbelastingscapaciteit te bereiken.

Stel dat een app die wordt uitgevoerd op een app-serviceomgevingconfiguratie is getest om 20.000 aanvragen per seconde (RPS) te verwerken.  Als de gewenste piekbelastingscapaciteit 100K RPS is, kunnen vijf (5) App-serviceomgevingen worden gemaakt en geconfigureerd om ervoor te zorgen dat de toepassing de maximale verwachte belasting aankan.

Aangezien klanten meestal toegang hebben tot apps met behulp van een aangepast (of ijdelheids)domein, hebben ontwikkelaars een manier nodig om app-aanvragen te distribueren over alle app-serviceomgevinginstanties.  Een geweldige manier om dit te bereiken is door het aangepaste domein op te lossen met behulp van een [Azure Traffic Manager-profiel.][AzureTrafficManagerProfile]  Het Traffic Manager-profiel kan worden geconfigureerd om te wijzen op alle afzonderlijke App-serviceomgevingen.  Traffic Manager verzorgt automatisch de distributie van klanten in alle App-serviceomgevingen op basis van de taakverdelingsinstellingen in het Traffic Manager-profiel.  Deze aanpak werkt ongeacht of alle App-serviceomgevingen in één Azure-regio worden geïmplementeerd of wereldwijd worden geïmplementeerd in meerdere Azure-regio's.

Bovendien, omdat klanten toegang hebben tot apps via het ijdelheidsdomein, zijn klanten niet op de hoogte van het aantal App-serviceomgevingen waarop een app wordt uitgevoerd.  Als gevolg hiervan kunnen ontwikkelaars snel en eenvoudig App-serviceomgevingen toevoegen en verwijderen op basis van waargenomen verkeersbelasting.

Het conceptuele diagram hieronder toont een app die horizontaal is uitgevouwen over drie App-serviceomgevingen binnen één regio.

![Conceptuele architectuur][ConceptualArchitecture] 

De rest van dit onderwerp loopt door de stappen die betrokken zijn bij het opzetten van een gedistribueerde topologie voor de voorbeeld-app met behulp van meerdere App Service-omgevingen.

## <a name="planning-the-topology"></a>Het plannen van de Topologie
Voordat u een gedistribueerde app-voetafdruk uitbouwt, helpt het om een paar stukjes informatie van tevoren te hebben.

* **Aangepast domein voor de app:**  Wat is de aangepaste domeinnaam die klanten zullen gebruiken om toegang te krijgen tot de app?  Voor de voorbeeld-app is de aangepaste domeinnaam`www.scalableasedemo.com`
* **Domein Traffic Manager:**  Er moet een domeinnaam worden gekozen bij het maken van een [Azure Traffic Manager-profiel.][AzureTrafficManagerProfile]  Deze naam wordt gecombineerd met het *trafficmanager.net* achtervoegsel om een domeinvermelding te registreren die wordt beheerd door Traffic Manager.  Voor de voorbeeld-app is de gekozen naam *schaalbare-ase-demo.*  Als gevolg hiervan is de volledige domeinnaam die wordt beheerd door Traffic Manager *scalable-ase-demo.trafficmanager.net.*
* **Strategie voor het schalen van de app-voetafdruk:**  Wordt de voetafdruk van de toepassing verdeeld over meerdere App-serviceomgevingen in één regio?  Meerdere regio's?  Een mix-and-match van beide benaderingen?  De beslissing moet gebaseerd zijn op verwachtingen van waar het klantverkeer vandaan komt, evenals hoe goed de rest van de ondersteunende back-endinfrastructuur van een app kan worden geschaald.  Met een 100% stateloze toepassing kan een app bijvoorbeeld massaal worden geschaald met een combinatie van meerdere App-serviceomgevingen per Azure-regio, vermenigvuldigd met App-serviceomgevingen die zijn geïmplementeerd in meerdere Azure-regio's.  Met meer dan 15 openbare Azure-regio's die beschikbaar zijn om uit te kiezen, kunnen klanten echt een wereldwijde hyperschaal-applicatiefootprint opbouwen.  Voor de voorbeeld-app die voor dit artikel wordt gebruikt, zijn drie App-serviceomgevingen gemaakt in één Azure-regio (South Central US).
* **Naamgevingsconventie voor de App-serviceomgevingen:**  Elke App Service-omgeving vereist een unieke naam.  Naast een of twee App-serviceomgevingen is het handig om een naamgevingsconventie te hebben om elke App Service-omgeving te identificeren.  Voor de voorbeeldapp werd een eenvoudige het noemen overeenkomst gebruikt.  De namen van de drie App Service Environments zijn *fe1ase,* *fe2ase*en *fe3ase*.
* **Naamgevingsconventie voor de apps:**  Aangezien meerdere exemplaren van de app worden geïmplementeerd, is een naam nodig voor elk exemplaar van de geïmplementeerde app.  Een weinig bekende, maar zeer handige functie van App Service Omgevingen is dat dezelfde app naam kan worden gebruikt in meerdere App Service Omgevingen.  Aangezien elke App Service-omgeving een uniek domeinachtervoegsel heeft, kunnen ontwikkelaars ervoor kiezen om exact dezelfde appnaam in elke omgeving opnieuw te gebruiken.  Een ontwikkelaar kan bijvoorbeeld apps als volgt laten noemen: *myapp.foo1.p.azurewebsites.net,* *myapp.foo2.p.azurewebsites.net,* *myapp.foo3.p.azurewebsites.net,* enz.  Voor de voorbeeld-app heeft elk app-exemplaar ook een unieke naam.  De gebruikte namen van app-instantie zijn *webfrontend1,* *webfrontend2*en *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Het profiel van de verkeersmanager instellen
Zodra meerdere exemplaren van een app zijn geïmplementeerd in meerdere App-serviceomgevingen, kunnen de afzonderlijke app-exemplaren worden geregistreerd bij Traffic Manager.  Voor de voorbeeld-app is een Traffic Manager-profiel nodig voor *scalable-ase-demo.trafficmanager.net* dat klanten kan doorsturen naar een van de volgende geïmplementeerde app-instanties:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Een exemplaar van de voorbeeld-app die is geïmplementeerd in de eerste App-serviceomgeving.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  Een exemplaar van de voorbeeld-app die is geïmplementeerd in de tweede App-serviceomgeving.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  Een exemplaar van de voorbeeld-app die is geïmplementeerd in de derde App-serviceomgeving.

De eenvoudigste manier om meerdere Azure App Service-eindpunten te registreren, allemaal uitgevoerd in **dezelfde** Azure-regio, is met de [Powershell Azure Resource Manager Traffic Manager-ondersteuning][ARMTrafficManager].  

De eerste stap is het maken van een Azure Traffic Manager-profiel.  De onderstaande code laat zien hoe het profiel is gemaakt voor de voorbeeld-app:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Merk op hoe de parameter *RelativeDnsName* is ingesteld op *schaalbare-ase-demo*.  Zo wordt de domeinnaam *scalable-ase-demo.trafficmanager.net* gemaakt en gekoppeld aan een Traffic Manager-profiel.

De parameter *TrafficRoutingMethod* definieert het taakverdelingsbeleid dat Traffic Manager gebruikt om te bepalen hoe de klantbelasting over alle beschikbare eindpunten kan worden gespreid.  In dit voorbeeld is gekozen voor de *gewogen* methode.  Dit zal resulteren in verzoeken van klanten worden verspreid over alle geregistreerde toepassingseindpunten op basis van de relatieve gewichten die zijn gekoppeld aan elk eindpunt. 

Met het profiel dat is gemaakt, wordt elke app-instantie aan het profiel toegevoegd als een native Azure-eindpunt.  In de onderstaande code wordt een verwijzing naar elke front-end web-app opgehaald en wordt elke app toegevoegd als eindpunt voor Traffic Manager via de parameter *TargetResourceId.*

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Merk op hoe er één oproep is naar *Add-AzureTrafficManagerEndpointConfig* voor elk afzonderlijk app-exemplaar.  De parameter *TargetResourceId* in elke Powershell-opdracht verwijst naar een van de drie geïmplementeerde app-exemplaren.  Het Traffic Manager-profiel verspreidt de belasting over alle drie de eindpunten die in het profiel zijn geregistreerd.

Alle drie de eindpunten gebruiken dezelfde waarde (10) voor de parameter *Gewicht.*  Dit resulteert in Traffic Manager die verzoeken van klanten relatief gelijkmatig over alle drie app-exemplaren verspreidt. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Het aangepaste domein van de app aanwijzen op het domein Traffic Manager
De laatste stap die nodig is, is het aanwijzen van het aangepaste domein van de app op het domein Traffic Manager.  Voor de voorbeeld-app `www.scalableasedemo.com` `scalable-ase-demo.trafficmanager.net`betekent dit wijzen naar .  Deze stap moet worden voltooid met de domeinregistrar die het aangepaste domein beheert.  

Met behulp van de domeinbeheertools van uw registrar moet een CNAME-records worden gemaakt die het aangepaste domein in het domein Traffic Manager aanwijzen.  De onderstaande afbeelding toont een voorbeeld van hoe deze CNAME-configuratie eruit ziet:

![CNAME voor aangepast domein][CNAMEforCustomDomain] 

Hoewel niet behandeld in dit onderwerp, bedenk dan dat elke afzonderlijke app-instantie moet het aangepaste domein geregistreerd met het ook.  Als een aanvraag anders een app-exemplaar aangaat en de toepassing niet het aangepaste domein bij de app heeft geregistreerd, mislukt het verzoek.  

In dit voorbeeld is `www.scalableasedemo.com`het aangepaste domein en elke toepassingsinstantie is het aangepaste domein eraan gekoppeld.

![Aangepast domein][CustomDomain] 

Zie het volgende artikel over het registreren van [aangepaste domeinen][RegisterCustomDomain]voor een samenvatting van het registreren van een aangepast domein met Azure App Service-apps.

## <a name="trying-out-the-distributed-topology"></a>Het uitproberen van de Distributed Topology
Het eindresultaat van de Traffic Manager- `www.scalableasedemo.com` en DNS-configuratie is dat aanvragen voor de volgende reeks worden doorlopen:

1. Een browser of apparaat maakt een DNS-lookup voor`www.scalableasedemo.com`
2. De CNAME-vermelding bij de domeinregistrar zorgt ervoor dat de DNS-lookup wordt doorgestuurd naar Azure Traffic Manager.
3. Er wordt een DNS-lookup gemaakt voor *scalable-ase-demo.trafficmanager.net* tegen een van de DNS-servers van Azure Traffic Manager.
4. Op basis van het taakverdelingsbeleid (de parameter *TrafficRoutingMethod* die eerder is gebruikt bij het maken van het Traffic Manager-profiel), selecteert Traffic Manager een van de geconfigureerde eindpunten en stuurt het FQDN van dat eindpunt terug naar de browser of het apparaat.
5. Aangezien de FQDN van het eindpunt de url is van een app-exemplaar dat wordt uitgevoerd op een App-serviceomgeving, vraagt de browser of het apparaat een Microsoft Azure DNS-server om de FQDN op een IP-adres op te lossen. 
6. De browser of het apparaat stuurt het HTTP/S-verzoek naar het IP-adres.  
7. De aanvraag wordt weergegeven in een van de app-exemplaren die worden uitgevoerd op een van de app-serviceomgevingen.

De onderstaande consoleafbeelding toont een DNS-lookup voor het aangepaste domein van de voorbeeld-app die is opgelost voor een app-exemplaar dat wordt uitgevoerd op een van de drie voorbeeld-app-serviceomgevingen (in dit geval de tweede van de drie App-serviceomgevingen):

![DNS-lookup][DNSLookup] 

## <a name="additional-links-and-information"></a>Aanvullende koppelingen en -informatie
Documentatie over de ondersteuning voor Powershell [Azure Resource Manager Traffic Manager][ARMTrafficManager].  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 

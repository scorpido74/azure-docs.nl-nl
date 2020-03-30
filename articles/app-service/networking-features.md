---
title: Netwerkfuncties
description: Meer informatie over de netwerkfuncties in Azure App Service en welke functies u nodig hebt voor uw netwerkbehoeften voor beveiliging of functionaliteit.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bc3d35830d833c0223a400140c53e583d2f6ed37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475422"
---
# <a name="app-service-networking-features"></a>App Service-netwerkfuncties

Toepassingen in de Azure App Service kunnen op meerdere manieren worden geïmplementeerd. Standaard zijn door App Service gehoste apps direct internettoegankelijk en kunnen ze alleen gehoste eindpunten via internet bereiken. Veel klanttoepassingen moeten echter het in- en uitgaande netwerkverkeer beheren. Er zijn verschillende functies beschikbaar in de App-service om aan die behoeften te voldoen. De uitdaging is te weten welke functie moet worden gebruikt om een bepaald probleem op te lossen. Dit document is bedoeld om klanten te helpen bepalen welke functie moet worden gebruikt op basis van enkele voorbeeldgebruiksvoorbeelden.

Er zijn twee primaire implementatietypen voor de Azure App Service. Er is de multi-tenant openbare dienst, die App Service-abonnementen host in de Gratis, Gedeelde, Basis,Standaard, Premium en Premiumv2-prijzen SKU's. Dan is er de single tenant App Service Environment (ASE), die geïsoleerde SKU App Service-abonnementen rechtstreeks in uw Azure Virtual Network (VNet) host. De functies die u gebruikt, variëren als u zich in de multi-tenantservice of in een ASE bevindt. 

## <a name="multi-tenant-app-service-networking-features"></a>Netwerkfuncties voor apps met meerdere tenantn 

De Azure App Service is een gedistribueerd systeem. De rollen die binnenkomende HTTP/HTTPS-aanvragen verwerken, worden front-ends genoemd. De rollen die de werklast van de klant hosten, worden werknemers genoemd. Alle rollen in een App Service-implementatie bestaan in een netwerk met meerdere tenant's. Omdat er veel verschillende klanten in dezelfde app-serviceschaaleenheid zitten, u het App Service-netwerk niet rechtstreeks met uw netwerk verbinden. In plaats van de netwerken met elkaar te verbinden, hebben we functies nodig om de verschillende aspecten van applicatiecommunicatie te behandelen. De functies die aanvragen naar uw app afhandelen, kunnen niet worden gebruikt om problemen op te lossen bij het voeren van gesprekken vanuit uw app. Ook de functies die problemen oplossen voor gesprekken van uw app kunnen niet worden gebruikt om problemen op te lossen naar uw app.  

| Binnenkomende functies | Uitgaande functies |
|---------------------|-------------------|
| App-toegewezen adres | Hybride verbindingen |
| Toegangsbeperkingen | Vereiste VNet-gateway-integratie |
| Service-eindpunten | VNet-integratie |

Tenzij anders vermeld, kunnen alle functies samen worden gebruikt. U de functies mengen om uw verschillende problemen op te lossen.

## <a name="use-case-and-features"></a>Gebruikscase en functies

Voor een bepaalde use case, kunnen er een paar manieren om het probleem op te lossen.  De juiste functie om te gebruiken is soms te wijten aan redenen die verder gaan dan alleen de use case zelf. In de volgende inkomende gebruiksvoorbeelden wordt uitgelegd hoe u netwerkfuncties van App Service gebruiken om problemen op te lossen rond het regelen van verkeer dat naar uw app gaat. 
 
| Binnenkomende gebruiksaanvragen | Functie |
|---------------------|-------------------|
| Ondersteuning voor IP-gebaseerde SSL-behoeften voor uw app | app toegewezen adres |
| Niet gedeeld, speciaal binnenkomend adres voor uw app | app toegewezen adres |
| Toegang tot uw app beperken vanaf een reeks goed gedefinieerde adressen | Toegangsbeperkingen |
| Toegang tot mijn app beperken vanuit bronnen in een VNet | Service-eindpunten </br> ILB ASE </br> Privéeindpunt (voorbeeld) |
| Mijn app blootleggen op een privé-IP in mijn VNet | ILB ASE </br> privé-IP voor binnenkomen op een toepassingsgateway met serviceeindpunten </br> Serviceeindpunt (voorbeeld) |
| Mijn app beveiligen met een WAF | Application Gateway + ILB ASE </br> Application Gateway met service-eindpunten </br> Azure-voordeur met toegangsbeperkingen |
| Verkeer over de balans naar mijn apps in verschillende regio's | Azure-voordeur met toegangsbeperkingen | 
| Load balansverkeer in dezelfde regio | [Application Gateway met service-eindpunten][appgwserviceendpoints] | 

In de volgende uitgaande use cases wordt uitgelegd hoe u netwerkfuncties van App Service gebruiken om uitgaande toegangsbehoeften voor uw app op te lossen. 

| Uitgaande use cases | Functie |
|---------------------|-------------------|
| Toegang tot bronnen in een Azure Virtual Network in dezelfde regio | VNet-integratie </br> Ase |
| Toegang tot bronnen in een Azure Virtual Network in een andere regio | Vereiste VNet-gateway-integratie </br> ASE en VNet peering |
| Toegang tot bronnen die zijn beveiligd met serviceeindpunten | VNet-integratie </br> Ase |
| Toegang tot bronnen in een privénetwerk dat niet is verbonden met Azure | Hybride verbindingen |
| Toegang tot bronnen op de circuits van ExpressRoute | VNet-integratie </br> Ase | 
| Uitgaand verkeer beveiligen vanuit uw web-app | VNet-integratie- en netwerkbeveiligingsgroepen </br> Ase | 
| Uitgaand verkeer routeren vanuit uw web-app | VNet-integratie- en routetabellen </br> Ase | 


### <a name="default-networking-behavior"></a>Standaardnetwerkgedrag

De schaaleenheden azure app-service ondersteunen veel klanten in elke implementatie. De gratis en gedeelde SKU-plannen hosten de werkbelasting van klanten voor werknemers met meerdere huurders. In de basis- en bovenstaande plannen worden de workloads van klanten host die zijn toegewezen aan slechts één App Service-abonnement (ASP). Als u een Standard App Service-abonnement had, worden alle apps in dat abonnement op dezelfde werknemer uitgevoerd. Als u de werknemer uitschaalt, worden alle apps in die ASP gerepliceerd op een nieuwe werknemer voor elk exemplaar in uw ASP. De werknemers die worden gebruikt voor Premiumv2 verschillen van de werknemers die worden gebruikt voor de andere plannen. Elke app-service-implementatie heeft één IP-adres dat wordt gebruikt voor al het binnenkomende verkeer naar de apps in die App Service-implementatie. Er zijn echter overal van 4 tot 11 adressen die worden gebruikt voor het maken van uitgaande gesprekken. Deze adressen worden gedeeld door alle apps in die App Service-implementatie. De uitgaande adressen zijn verschillend op basis van de verschillende werktypen. Dat betekent dat de adressen die worden gebruikt door de Gratis, Gedeelde, Basis-, Standaard- en Premium-ASP's anders zijn dan de adressen die worden gebruikt voor uitgaande gesprekken van de Premiumv2 ASP's. Als u in de eigenschappen voor uw app kijkt, u de binnenkomende en uitgaande adressen zien die door uw app worden gebruikt. Als u een afhankelijkheid met een IP ACL moet vergrendelen, gebruikt u de mogelijkeOutboundAddresses. 

![App-eigenschappen](media/networking-features/app-properties.png)

App Service heeft een aantal eindpunten die worden gebruikt om de service te beheren.  Deze adressen worden gepubliceerd in een apart document en staan ook in de IP-servicetag van AppServiceManagement. De AppServiceManagement-tag wordt alleen gebruikt met een App Service Environment (ASE) waar u dergelijk verkeer moet toestaan. De inkomende adressen van de App-service worden bijgehouden in de IP-servicetag van AppService. Er is geen IP-servicetag die de uitgaande adressen bevat die door App Service worden gebruikt. 

![In- en uitgaand diagram voor app-service](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>App-toegewezen adres 

De toegewezen adresfunctie voor apps is een uitloper van de IP-gebaseerde SSL-mogelijkheid en wordt geopend door SSL in te stellen met uw app. Deze functie kan worden gebruikt voor IP-gebaseerde SSL-gesprekken, maar het kan ook worden gebruikt om uw app een adres te geven dat alleen deze heeft. 

![App-toegewezen adresdiagram](media/networking-features/app-assigned-address.png)

Wanneer u een toegewezen adres voor apps gebruikt, gaat uw verkeer nog steeds door dezelfde front-endrollen die al het binnenkomende verkeer naar de schaaleenheid App-service verwerken. Het adres dat aan uw app is toegewezen, wordt echter alleen door uw app gebruikt. De use cases voor deze functie zijn:

* Ondersteuning voor IP-gebaseerde SSL-behoeften voor uw app
* Stel een speciaal adres in voor uw app dat niet met iets anders wordt gedeeld

U leren hoe u een adres in stelt in uw app met de zelfstudie over [HET configureren van IP-gebaseerde SSL.][appassignedaddress] 

### <a name="access-restrictions"></a>Toegangsbeperkingen 

Met de mogelijkheid Toegangsbeperkingen u **binnenkomende** aanvragen filteren op basis van het IP-adres van de oorsprong. De filteractie vindt plaats op de front-endrollen die stroomopwaarts staan van de werknemersrollen waarin uw apps worden uitgevoerd. Aangezien de front-endrollen stroomopwaarts van de werknemers zijn, kan de mogelijkheid Toegangsbeperkingen worden beschouwd als bescherming op netwerkniveau voor uw apps. Met de functie u een lijst maken met adresblokken toestaan en weigeren die in prioriteitsvolgorde worden geëvalueerd. Het is vergelijkbaar met de NSG-functie (Network Security Group) die in Azure Networking bestaat.  U deze functie gebruiken in een ASE of in de multi-tenant service. Bij gebruik met een ILB ASE u de toegang vanuit privéadresblokken beperken.

![Toegangsbeperkingen](media/networking-features/access-restrictions.png)

De functie Toegangsbeperkingen helpt bij scenario's waarin u de IP-adressen wilt beperken die kunnen worden gebruikt om uw app te bereiken. Onder de use cases voor deze functie zijn:

* Toegang tot uw app beperken vanaf een reeks goed gedefinieerde adressen 
* Beperk de toegang tot het komen via een load-balancing service, zoals Azure Front Door. Als u uw binnenkomend verkeer naar Azure Front Door wilt vergrendelen, maakt u regels om verkeer toe te staan vanaf 147.243.0.0.0/16 en 2a01:111:2050::/44. 

![Toegangsbeperkingen met voordeur](media/networking-features/access-restrictions-afd.png)

Als u de toegang tot uw app wilt vergrendelen, zodat deze alleen kan worden bereikt via bronnen in uw Azure Virtual Network (VNet), hebt u een statisch openbaar adres nodig op wat uw bron ook in uw VNet is. Als de resources geen openbaar adres hebben, moet u in plaats daarvan de functie Serviceeindpunten gebruiken. Meer informatie over het inschakelen van deze functie met de zelfstudie over [Het configureren van toegangsbeperkingen][iprestrictions].

### <a name="service-endpoints"></a>Service-eindpunten

Met serviceeindpunten u **binnenkomende** toegang tot uw app vergrendelen, zodat het bronadres afkomstig moet zijn van een reeks subnetten die u selecteert. Deze functie werkt in combinatie met de IP Access-beperkingen. Serviceeindpunten worden ingesteld in dezelfde gebruikerservaring als de IP-toegangsbeperkingen. U een lijst met toegangsregels voor toestaan/weigeren maken die zowel openbare adressen als subnetten in uw VNets bevatten. Deze functie ondersteunt scenario's zoals:

![serviceeindpunten](media/networking-features/service-endpoints.png)

* Een toepassingsgateway instellen met uw app om binnenkomend verkeer naar uw app te vergrendelen
* De toegang tot uw app beperken tot bronnen in uw VNet. Dit kunnen VM's, AS's of zelfs andere apps zijn die VNet-integratie gebruiken 

![serviceeindpunten met toepassingsgateway](media/networking-features/service-endpoints-appgw.png)

Meer informatie over het configureren van serviceeindpunten met uw app vindt u in de zelfstudie over Het [configureren van serviceeindpunttoegangsbeperkingen][serviceendpoints]

### <a name="private-endpoint-preview"></a>Privéeindpunt (voorbeeld)

Private Endpoint is een netwerkinterface die u privé en veilig verbindt met uw webapp via Azure Private Link. Private Endpoint maakt gebruik van een privé-IP-adres van uw VNet, waardoor de web-app effectief in uw VNet wordt opgenomen. Deze functie is alleen bedoeld voor **binnenkomende** stromen naar uw web-app.
[Privéeindpunten gebruiken voor Azure Web App (voorbeeld)][privateendpoints]
 
### <a name="hybrid-connections"></a>Hybride verbindingen

Met Hybride verbindingen van App Service kunnen uw apps **uitgaande** gesprekken voeren naar opgegeven TCP-eindpunten. Het eindpunt kan on-premises zijn, in een VNet of waar dan ook waarmee uitgaand verkeer naar Azure op poort 443 mogelijk is. De functie vereist de installatie van een relay agent genaamd de Hybrid Connection Manager (HCM) op een Windows Server 2012 of nieuwere host. De HCM moet Azure Relay kunnen bereiken in poort 443. De HCM kan worden gedownload van de Gebruikersinterface voor hybride verbindingen van de App Service in de portal. 

![Netwerkstroom hybride verbindingen](media/networking-features/hybrid-connections.png)

De functie Hybride verbindingen van App Service is gebouwd op de azure relay-hybrideverbindingen. App Service maakt gebruik van een gespecialiseerde vorm van de functie die alleen het maken van uitgaande gesprekken vanuit uw app naar een TCP-host en -poort ondersteunt. Deze host en poort hoeven alleen op te lossen op de host waar de HCM is geïnstalleerd. Wanneer de app in App Service een DNS-lookup doet op de host en poort die is gedefinieerd in uw hybride verbinding, wordt het verkeer automatisch omgeleid om door de hybride verbinding en de hybride verbindingsbeheer te gaan. Lees de documentatie over hybride verbindingen van [App Service voor][hybridconn] meer informatie over hybride verbindingen

Deze functie wordt vaak gebruikt om:

* Toegang tot bronnen in privénetwerken die niet zijn verbonden met Azure met een VPN of ExpressRoute
* Ondersteuning voor het verhogen en verplaatsen van on-premises apps naar App Service zonder dat u ook ondersteunende databases hoeft te verplaatsen  
* Zorg veilig voor toegang tot één host en poort per hybride verbinding. De meeste netwerkfuncties bieden open toegang tot een netwerk en met hybride verbindingen hebt u alleen de enkele host en poort die u bereiken.
* Omvallen scenario's die niet worden gedekt door andere uitgaande connectiviteitsmethoden
* Ontwikkeling uitvoeren in App Service waar de apps eenvoudig on-premises resources kunnen gebruiken 

Omdat de functie toegang biedt tot on-premises bronnen zonder een inkomende firewallgat, is deze populair bij ontwikkelaars. De andere uitgaande App Service-netwerkfuncties zijn zeer Azure Virtual Networking-gerelateerd. Hybrid Connections heeft geen afhankelijkheid van het doorlopen van een VNet en kan worden gebruikt voor een breder scala aan netwerkbehoeften. Het is belangrijk op te merken dat de App Service Hybrid Connections functie niet schelen of weten wat je doet op de top van het. Dat wil zeggen dat u het gebruiken om toegang te krijgen tot een database, een webservice of een willekeurige TCP-socket op een mainframe. De functie tunnels in wezen TCP-pakketten. 

Terwijl hybride verbindingen is populair voor ontwikkeling, het wordt ook gebruikt in tal van productie-toepassingen ook. Het is geweldig voor toegang tot een webservice of database, maar is niet geschikt voor situaties waarbij veel verbindingen worden gemaakt. 

### <a name="gateway-required-vnet-integration"></a>Vereiste VNet-gateway-integratie 

Met de vereiste App Service VNet-integratiefunctie kan uw app **uitgaande** aanvragen indienen in een Azure Virtual Network. De functie werkt door de host te verbinden waarop uw app wordt uitgevoerd op een Virtual Network-gateway op uw VNet met een point-to-site VPN. Wanneer u de functie configureert, krijgt uw app een van de point-to-site-adressen die aan elke instantie zijn toegewezen. Met deze functie hebt u toegang tot bronnen in VNets voor Klassieke of Resource Manager in elke regio. 

![Vereiste VNet-gateway-integratie](media/networking-features/gw-vnet-integration.png)

Deze functie lost het probleem op van de toegang tot bronnen in andere VNets en kan zelfs worden gebruikt om verbinding te maken via een VNet met andere VNets of zelfs on-premises. Het werkt niet met ExpressRoute aangesloten VNets, maar wel met Site-to-site VPN connected networks. Het is normaal gesproken ongepast om deze functie te gebruiken vanuit een app in een App Service Environment (ASE), omdat de ASE al in uw VNet staat. De use cases die deze functie oplost zijn:

* Toegang tot bronnen op privé-IP's in uw virtuele Azure-netwerken 
* Toegang tot on-premises bronnen als er een site-to-site VPN is 
* Toegang tot bronnen in peered VNets 

Wanneer deze functie is ingeschakeld, gebruikt uw app de DNS-server waarmee de bestemming VNet is geconfigureerd. U meer lezen over deze functie in de documentatie over [App Service VNet Integration][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>VNet-integratie

De vereiste VNet-integratiefunctie voor gateway's is erg handig, maar lost nog steeds geen toegang tot bronnen op via ExpressRoute. Naast het nodig hebben om via ExpressRoute-verbindingen te bereiken, is er ook behoefte aan apps om te kunnen bellen naar serviceendpoint beveiligde services. Om beide extra behoeften op te lossen, is een andere VNet-integratiemogelijkheid toegevoegd. Met de nieuwe VNet-integratiefunctie u de back-end van uw app in een subnet plaatsen in een VNet resourcemanager in dezelfde regio. Deze functie is niet beschikbaar in een App Service-omgeving, die al in een VNet staat. Deze functie maakt het mogelijk:

* Toegang tot resources in VNets van Resource Manager in dezelfde regio
* Toegang tot bronnen die zijn beveiligd met serviceeindpunten 
* Toegang tot bronnen die toegankelijk zijn via ExpressRoute- of VPN-verbindingen
* Alle uitgaande verkeer beveiligen 
* Force tunneling al uitgaand verkeer. 

![VNet-integratie](media/networking-features/vnet-integration.png)

Lees de documenten over [App Service VNet Integration][vnetintegration]voor meer informatie over deze functie.

## <a name="app-service-environment"></a>App Service-omgeving 

Een App Service Environment (ASE) is een enkele tenant implementatie van de Azure App Service die wordt uitgevoerd in uw VNet. De ASE maakt use cases mogelijk zoals:

* Toegang tot bronnen in uw VNet
* Toegang tot bronnen op ExpressRoute
* Uw apps blootstellen met een privéadres in uw VNet 
* Toegang tot bronnen op alle serviceeindpunten 

Met een ASE hoeft u geen functies zoals VNet Integration of service-eindpunten te gebruiken, omdat de ASE al in uw VNet staat. Als u toegang wilt krijgen tot bronnen zoals SQL of Storage via serviceeindpunten, schakelt u serviceeindpunten in op het ASE-subnet. Als u toegang wilt tot bronnen in het VNet, is er geen extra configuratie vereist.  Als u toegang wilt tot bronnen via ExpressRoute, bevindt u zich al in het VNet en hoeft u niets te configureren op de ASE of de apps erin. 

Omdat de apps in een ILB ASE kunnen worden blootgesteld op een privé IP-adres, u eenvoudig WAF-apparaten toevoegen om alleen de apps die u wilt bloot te stellen aan het internet en de rest veilig te houden. Het leent zich voor een eenvoudige ontwikkeling van multi-tier applicaties. 

Er zijn een aantal dingen die nog niet mogelijk zijn van de multi-tenant service die afkomstig zijn van een ASE. Die omvatten dingen als:

* Uw apps blootleggen op een privé-IP-adres
* Alle uitgaande verkeer beveiligen met netwerkbesturingselementen die geen deel uitmaken van uw app 
* Uw apps hosten in één tenantservice 
* Opschalen naar veel meer exemplaren dan mogelijk is in de multi-tenant service 
* Privé-CA-clientcertificaten laden voor gebruik door uw apps met beveiligde CA-eindpunten 
* Force TLS 1.1 in alle apps die in het systeem worden gehost zonder dat u op app-niveau uitschakelen 
* Geef een speciaal uitgaand adres op voor alle apps in uw ASE die niet worden gedeeld met klanten 

![ASE in een VNet](media/networking-features/app-service-environment.png)

De ASE biedt het beste verhaal rond geïsoleerde en toegewijde app-hosting, maar komt wel met een aantal managementuitdagingen. Enkele dingen om te overwegen voordat u een operationele ASE gebruikt, zijn:
 
 * Een ASE loopt binnen uw VNet, maar heeft wel afhankelijkheden buiten het VNet. Die afhankelijkheden moeten worden toegestaan. Lees meer in [Netwerkoverwegingen voor een App Service-omgeving][networkinfo]
 * Een ASE schaalt niet meteen op zoals de multi-tenant service. U moet anticiperen op schaalbehoeften in plaats van reactief schalen. 
 * Een ASE heeft een hogere up front kosten in verband met het. Om het meeste uit uw ASE te halen, moet u van plan zijn om veel workloads in één ASE te stoppen in plaats van het te laten gebruiken voor kleine inspanningen
 * De apps in een ASE kunnen de toegang tot sommige apps in een ASE niet beperken en niet tot andere.
 * De ASE is in een subnet en alle netwerkregels gelden voor al het verkeer van en naar die ASE. Als u binnenkomende verkeersregels voor slechts één app wilt toewijzen, gebruikt u Toegangsbeperkingen. 

## <a name="combining-features"></a>Functies combineren 

De functies die zijn vermeld voor de multi-tenant service kunnen samen worden gebruikt om uitgebreidere use cases op te lossen. Twee van de meest voorkomende use cases worden hier beschreven, maar het zijn slechts voorbeelden. Door te begrijpen wat de verschillende functies doen, u bijna al uw systeemarchitectuurbehoeften oplossen.

### <a name="inject-app-into-a-vnet"></a>App injecteren in een VNet

Een veelgebruikte aanvraag is hoe u uw app in een VNet plaatst. Als u uw app in een VNet plaatst, worden de binnenkomende en uitgaande eindpunten voor een app binnen een VNet opgenomen. De ASE biedt de beste oplossing om dit probleem op te lossen, maar u het meeste krijgen van wat nodig is in de multi-tenant service door functies te combineren. U bijvoorbeeld intranettoepassingen met privé-inkomende en uitgaande adressen hosten door:

* Een toepassingsgateway maken met privé-in- en uitgaand adres
* Binnenkomend verkeer naar uw app beveiligen met serviceeindpunten 
* Gebruik de nieuwe VNet-integratie, zodat de back-end van uw app in uw VNet staat 

Deze implementatiestijl geeft u geen speciaal adres voor uitgaand verkeer naar het internet of geeft u de mogelijkheid om al het uitgaande verkeer van uw app te vergrendelen.  Deze implementatiestijl zou u een groot deel van wat je anders zou krijgen met een ASE. 

### <a name="create-multi-tier-applications"></a>Toepassingen met meerdere lagen maken

Een multi-tier applicatie is een toepassing waarbij de API backend apps alleen toegankelijk zijn vanaf de front-end laag. Als u een toepassing met meerdere lagen wilt maken, u het als:

* VNet-integratie gebruiken om de backend van uw front-end web-app te verbinden met een subnet in een VNet
* Serviceeindpunten gebruiken om binnenkomend verkeer naar uw API-app te beveiligen om alleen afkomstig te zijn van het subnet dat wordt gebruikt door uw front-end web-app

![multi-tier app](media/networking-features/multi-tier-app.png)

U meerdere front-end apps dezelfde API-app laten gebruiken door VNet-integratie te gebruiken van de andere front-end-apps en serviceeindpunten uit de API-app met hun subnetten.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint

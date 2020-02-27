---
title: Netwerkfuncties
description: Meer informatie over de netwerk functies in Azure App Service en welke functies u nodig hebt voor uw netwerk behoeften voor beveiliging of functionaliteit.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 02/27/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0fd904b15a830e2b261057a11d1a8f3a4d584fe1
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649223"
---
# <a name="app-service-networking-features"></a>App Service-netwerk functies

Toepassingen in de Azure App Service kunnen op verschillende manieren worden geïmplementeerd. App Service gehoste apps zijn standaard rechtstreeks toegankelijk voor Internet en kunnen alleen Internet gehoste eind punten bereiken. Veel klant toepassingen moeten echter het inkomende en uitgaande netwerk verkeer beheren. Er zijn verschillende functies die beschikbaar zijn in de App Service om aan deze behoeften te voldoen. De uitdaging is te weten welke functie moet worden gebruikt om een bepaald probleem op te lossen. Dit document is bedoeld om klanten te helpen bepalen welke functie moet worden gebruikt op basis van enkele voor beelden van gebruiks voorbeelden.

Er zijn twee primaire implementatie typen voor de Azure App Service. Er is een open bare multi tenant-service, die als host fungeert voor App Service plannen in de gratis, gedeelde, Basic, Standard, Premium en Premiumv2 prijzen-Sku's. Vervolgens is er één Tenant App Service Environment (ASE), die als host fungeert voor geïsoleerde SKU App Service plannen rechtstreeks in uw Azure-Virtual Network (VNet). De functies die u gebruikt, zijn afhankelijk van of u zich in de multi tenant-service of in een ASE bevindt. 

## <a name="multi-tenant-app-service-networking-features"></a>Multi tenant-App Service-netwerk functies 

Het Azure App Service is een gedistribueerd systeem. De rollen die binnenkomende HTTP/HTTPS-aanvragen verwerken, worden front-ends genoemd. De rollen die als host fungeren voor de werk belasting van de klant, worden werk nemers genoemd. Alle rollen in een App Service-implementatie bestaan in een netwerk met meerdere tenants. Omdat er veel verschillende klanten zijn in dezelfde App Service Scale-eenheid, kunt u het App Service netwerk niet rechtstreeks verbinden met uw netwerk. In plaats van de netwerken te verbinden, hebben we functies nodig om de verschillende aspecten van toepassings communicatie te kunnen afhandelen. De functies voor het verwerken van aanvragen voor uw app kunnen niet worden gebruikt om problemen op te lossen bij het aanroepen van uw app. Op dezelfde manier kunnen de functies voor het oplossen van problemen met aanroepen vanuit uw app niet worden gebruikt om problemen met uw app op te lossen.  

| Binnenkomende functies | Uitgaande functies |
|---------------------|-------------------|
| Toegewezen adres van de app | Hybride verbindingen |
| Toegangs beperkingen | Vereiste VNet-integratie voor gateway |
| Service-eind punten | VNet-integratie |

Tenzij anders vermeld, kunnen alle functies tegelijk worden gebruikt. U kunt de functies combi neren om uw verschillende problemen op te lossen.

## <a name="use-case-and-features"></a>Use-case en-functies

U kunt het probleem op een aantal manieren oplossen voor een gegeven use-case.  De juiste functie die u moet gebruiken, is soms te wijten aan een andere reden dan alleen de use-case zelf. In de volgende inkomende use-cases wordt uitgelegd hoe u App Service-netwerk functies kunt gebruiken om problemen op te lossen bij het beheren van verkeer dat naar uw app gaat. 
 
| Inkomende use cases | Functie |
|---------------------|-------------------|
| Ondersteuning voor op IP gebaseerde SSL-behoeften voor uw app | Toegewezen adres van de app |
| Niet gedeeld, toegewezen inkomend adres voor uw app | Toegewezen adres van de app |
| De toegang tot uw app beperken vanuit een reeks goed gedefinieerde adressen | Toegangs beperkingen |
| Mijn app beschikbaar maken voor privé Ip's in mijn VNet | ILB ASE </br> Application Gateway met service-eindpunten |
| Toegang tot mijn app beperken van resources in een VNet | Service-eind punten </br> ILB ASE |
| Mijn app beschikbaar maken op een privé-IP in mijn VNet | ILB ASE </br> persoonlijk IP-adres voor inkomend verkeer op een Application Gateway met Service-eind punten |
| Mijn app beveiligen met een WAF | Application Gateway + ILB ASE </br> Application Gateway met service-eindpunten </br> Azure front deur met toegangs beperkingen |
| Taak verdeling van verkeer naar mijn apps in verschillende regio's | Azure front deur met toegangs beperkingen | 
| Taak verdeling van verkeer in dezelfde regio | [Application Gateway met Service-eind punten][appgwserviceendpoints] | 

In de volgende uitgaande use-cases wordt uitgelegd hoe u App Service-netwerk functies kunt gebruiken om uw app te verhelpen. 

| Uitgaande use cases | Functie |
|---------------------|-------------------|
| Toegang tot resources in een Azure-Virtual Network in dezelfde regio | VNet-integratie </br> ASE |
| Toegang tot resources in een Azure-Virtual Network in een andere regio | Vereiste VNet-integratie voor gateway </br> ASE en VNet-peering |
| Toegang tot resources die zijn beveiligd met Service-eind punten | VNet-integratie </br> ASE |
| Toegang tot bronnen in een particulier netwerk dat niet is verbonden met Azure | Hybride verbindingen |
| Toegang tot resources via ExpressRoute-circuits | VNet-integratie </br> ASE | 
| Uitgaand verkeer van uw web-app beveiligen | VNet-integratie en netwerk beveiligings groepen </br> ASE | 
| Uitgaand verkeer van uw web-app door sturen | VNet-integratie en route tabellen </br> ASE | 


### <a name="default-networking-behavior"></a>Standaard netwerk gedrag

De Azure App Service schaal eenheden ondersteunen veel klanten in elke implementatie. Met de gratis en gedeelde SKU worden de workloads van de klant gehost op werk nemers met meerdere tenants. De basis en hierboven plannen voor de werk belasting van hosters die uitsluitend zijn toegewezen aan één App Service plan (ASP). Als u een Standard-App Service plan had, worden alle apps in dat plan uitgevoerd op dezelfde werk nemer. Als u de werk nemer uitbreidt, worden alle apps in die ASP gerepliceerd op een nieuwe werk nemer voor elk exemplaar in uw ASP. De werk nemers die worden gebruikt voor Premiumv2 verschillen van de werk nemers die worden gebruikt voor de andere plannen. Elke App Service-implementatie heeft één IP-adres dat wordt gebruikt voor al het inkomende verkeer naar de apps in die App Service-implementatie. Er zijn echter ook geen vier tot 11 adressen die worden gebruikt voor het maken van uitgaande oproepen. Deze adressen worden gedeeld door alle apps in die App Service implementatie. De uitgaande adressen verschillen op basis van de verschillende typen werk nemers. Dit betekent dat de adressen die worden gebruikt door de ASPs gratis, gedeeld, basis, standaard en Premium anders zijn dan de adressen die worden gebruikt voor uitgaande oproepen van de Premiumv2-ASPs. Als u de eigenschappen voor uw app bekijkt, ziet u de inkomende en uitgaande adressen die worden gebruikt door uw app. Als u een afhankelijkheid met een IP-ACL wilt vergren delen, gebruikt u de possibleOutboundAddresses. 

![App-eigenschappen](media/networking-features/app-properties.png)

App Service heeft een aantal eind punten die worden gebruikt om de service te beheren.  Deze adressen worden gepubliceerd in een afzonderlijk document en bevinden zich ook in het AppServiceManagement IP-service label. De label AppServiceManagement wordt alleen gebruikt met een App Service Environment (ASE) waarin u dergelijk verkeer moet toestaan. De App Service inkomende adressen worden gevolgd in de AppService IP-service-tag. Er is geen IP-servicetag die de uitgaande adressen bevat die door App Service worden gebruikt. 

![App Service binnenkomend en uitgaand diagram](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Toegewezen adres van de app 

De functie voor het toegewezen adres van de app is een offshoot van de op IP gebaseerde SSL-mogelijkheid en wordt gebruikt door SSL in te stellen met uw app. Deze functie kan worden gebruikt voor SSL-aanroepen op basis van IP, maar kan ook worden gebruikt om uw app een adres te geven dat alleen het heeft. 

![Diagram van toegewezen adres van app](media/networking-features/app-assigned-address.png)

Wanneer u een aan een app toegewezen adres gebruikt, loopt uw verkeer nog steeds door dezelfde front-end-rollen die al het binnenkomende verkeer naar de App Service Scale-eenheid verwerken. Het adres dat is toegewezen aan uw app, wordt echter alleen gebruikt door uw app. De use cases voor deze functie zijn:

* Ondersteuning voor op IP gebaseerde SSL-behoeften voor uw app
* Stel een toegewezen adres in voor uw app die niet met iets anders is gedeeld

U kunt meer informatie over het instellen van een adres in uw app met behulp van de zelf studie over het [configureren van SSL op basis van IP][appassignedaddress]. 

### <a name="access-restrictions"></a>Toegangs beperkingen 

Met de mogelijkheid tot toegangs beperkingen kunt u **inkomende** aanvragen filteren op basis van het IP-adres van de oorsprong. De filter actie vindt plaats op de front-end-rollen die upstream zijn van de werk rollen waar uw apps worden uitgevoerd. Aangezien de front-end-rollen upstream van de werk nemers zijn, kan de mogelijkheid tot toegangs beperkingen worden beschouwd als beveiliging op netwerk niveau voor uw apps. Met deze functie kunt u een lijst met adressen blokken voor toestaan en weigeren maken die in volg orde van prioriteit worden geëvalueerd. Het is vergelijkbaar met de functie voor netwerk beveiligings groepen (NSG) die zich in azure-netwerken bevindt.  U kunt deze functie gebruiken in een ASE of in de multi tenant-service. Wanneer u gebruikt met een ILB-ASE, kunt u de toegang beperken tot persoonlijke adres blokken.

![Toegangs beperkingen](media/networking-features/access-restrictions.png)

De functie toegangs beperkingen helpt bij scenario's waarin u de IP-adressen wilt beperken die kunnen worden gebruikt om uw app te bereiken. De gebruiks voorbeelden voor deze functie zijn:

* De toegang tot uw app beperken vanuit een reeks goed gedefinieerde adressen 
* Beperk de toegang tot een service voor taak verdeling, zoals Azure front deur. Als u uw inkomende verkeer naar Azure front-deur wilt vergren delen, maakt u regels om verkeer toe te staan van 147.243.0.0/16 en 2a01:111:2050::/44. 

![Toegangs beperkingen met de voor deur](media/networking-features/access-restrictions-afd.png)

Als u de toegang tot uw app wilt vergren delen zodat deze alleen kan worden bereikt vanuit resources in uw Azure Virtual Network (VNet), hebt u een statisch openbaar adres nodig, ongeacht uw bron in uw VNet. Als de resources geen openbaar adres hebben, moet u in plaats daarvan de functie Service-eind punten gebruiken. Meer informatie over het inschakelen van deze functie met de zelf studie over het [configureren van toegangs beperkingen][iprestrictions].

### <a name="service-endpoints"></a>Service-eindpunten

Met Service-eind punten kunt u **inkomende** toegang tot uw app vergren delen, zodat het bron adres moet afkomstig zijn van een reeks subnetten die u selecteert. Deze functie werkt in combi natie met de beperkingen voor de IP-toegang. Service-eind punten worden ingesteld in dezelfde gebruikers ervaring als de IP-toegangs beperkingen. U kunt een lijst met toegestane/geweigerde toegangs regels maken die open bare adressen en subnetten in uw VNets bevatten. Deze functie biedt ondersteuning voor scenario's zoals:

![Service-eind punten](media/networking-features/service-endpoints.png)

* Een Application Gateway met uw app instellen om inkomend verkeer naar uw app te vergren delen
* De toegang tot uw app beperken tot resources in uw VNet. Dit kunnen Vm's, as of zelfs andere apps zijn die gebruikmaken van VNet-integratie 

![Service-eind punten met Application Gateway](media/networking-features/service-endpoints-appgw.png)

Meer informatie over het configureren van service-eind punten met uw app vindt u in de zelf studie over het [configureren van toegangs beperkingen voor service-eind punten][serviceendpoints]
 
### <a name="hybrid-connections"></a>Hybride verbindingen

Met App Service Hybride verbindingen kunnen uw apps **uitgaande** aanroepen naar opgegeven TCP-eind punten maken. Het eind punt kan on-premises zijn, in een VNet of op een wille keurige locatie waarmee uitgaand verkeer naar Azure wordt toegestaan op poort 443. Voor de functie is de installatie van een relay-agent met de naam Hybrid Connection Manager (HCM) op een Windows Server 2012-host of nieuwer. De HCM moet kunnen bereiken Azure Relay op poort 443. De HCM kan worden gedownload van de App Service Hybride verbindingen gebruikers interface in de portal. 

![Netwerk stroom Hybride verbindingen](media/networking-features/hybrid-connections.png)

De functie App Service Hybride verbindingen is gebaseerd op de Azure Relay Hybride verbindingen-mogelijkheid. App Service maakt gebruik van een speciale vorm van de functie die alleen ondersteuning biedt voor het maken van uitgaande oproepen vanuit uw app naar een TCP-host en-poort. Deze host en poort hoeven alleen te worden omgezet op de host waarop de HCM is geïnstalleerd. Wanneer de app in App Service een DNS-zoek opdracht op de host en poort die in uw hybride verbinding is gedefinieerd, wordt het verkeer automatisch omgeleid om door de hybride verbinding te gaan en de Hybrid Connection Manager. Raadpleeg de documentatie op [App Service hybride verbindingen][hybridconn] voor meer informatie over hybride verbindingen.

Deze functie wordt vaak gebruikt voor het volgende:

* Toegang tot bronnen in particuliere netwerken die niet zijn verbonden met Azure met een VPN-of ExpressRoute
* Ondersteuning voor liften en verschuivingen van on-premises apps naar App Service zonder dat ook ondersteunende data bases moeten worden verplaatst  
* Veilig toegang bieden tot één host en poort per hybride verbinding. De meeste netwerk functies openen toegang tot een netwerk en met Hybride verbindingen u slechts één host en poort hebt die u kunt bereiken.
* Bedekkings scenario's die niet worden gedekt door andere methoden voor uitgaande connectiviteit
* Ontwikkeling in App Service waarbij de apps eenvoudig on-premises resources kunnen gebruiken 

Omdat de functie toegang tot on-premises bronnen mogelijk maakt zonder een uitgaand firewall-gat, is het populair bij ontwikkel aars. De andere uitgaande App Service-netwerk functies zijn zeer samenhangende virtuele netwerken in Azure. Hybride verbindingen heeft geen afhankelijkheid van het gebruik van een VNet en kan worden gebruikt voor een groter aantal netwerk behoeften. Het is belang rijk te weten dat de functie van App Service Hybride verbindingen geen bedoelt of weet wat u eraan doet. Dat wil zeggen dat u deze kunt gebruiken voor toegang tot een Data Base, een webservice of een wille keurige TCP-socket op een mainframe. De functie tunnelt in feite TCP-pakketten. 

Hoewel Hybride verbindingen populair is voor ontwikkeling, wordt het ook gebruikt in talloze productie toepassingen. Het is ideaal voor het verkrijgen van toegang tot een webservice of Data Base, maar is niet geschikt voor situaties waarbij veel verbindingen worden gemaakt. 

### <a name="gateway-required-vnet-integration"></a>Vereiste VNet-integratie voor gateway 

Met de gateway die is vereist App Service VNet-integratie functie kan uw app **uitgaande** aanvragen indienen in een Azure-Virtual Network. De functie werkt door verbinding te maken met de host waarop uw app wordt uitgevoerd op een Virtual Network gateway op uw VNet met een punt-naar-site-VPN. Wanneer u de functie configureert, haalt uw app een van de punt-naar-site-adressen die zijn toegewezen aan elk exemplaar. Met deze functie kunt u toegang krijgen tot resources in een klassieke of Resource Manager-VNets in elke regio. 

![Vereiste VNet-integratie voor gateway](media/networking-features/gw-vnet-integration.png)

Met deze functie kunt u het probleem met het openen van bronnen in andere VNets oplossen en zelfs gebruiken om via een VNet verbinding te maken met een ander VNets of zelfs on-premises. Het werkt niet met ExpressRoute verbonden VNets, maar wel met site-naar-site-VPN verbonden netwerken. Het is normaal gesp roken niet geschikt om deze functie te gebruiken vanuit een app in een App Service Environment (ASE), omdat de ASE zich al in uw VNet bevindt. De use cases die deze functie oplost, zijn:

* Toegang tot bronnen op privé Ip's in uw virtuele Azure-netwerken 
* On-premises toegang tot bronnen als er sprake is van een site-naar-site-VPN 
* Toegang tot resources in de peered VNets 

Als deze functie is ingeschakeld, gebruikt uw app de DNS-server waarop het doel-VNet is geconfigureerd. Meer informatie over deze functie vindt u in de documentatie over [app service VNet-integratie][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>VNet-integratie

De gateway vereist VNet-integratie functie is zeer nuttig, maar er is nog steeds geen oplossing voor het openen van resources over ExpressRoute. Op het niveau van de ExpressRoute-verbindingen is het nodig dat apps aanroepen van beveiligde services voor service-eind punten kunnen maken. Voor het oplossen van beide extra behoeften is een andere VNet-integratie mogelijkheid toegevoegd. Met de nieuwe functie VNet-integratie kunt u de back-end van uw app in een subnet in een resource manager-VNet in dezelfde regio plaatsen. Deze functie is niet beschikbaar vanuit een App Service Environment, die zich al in een VNet bevindt. Met deze functie kunt u:

* Toegang tot resources in de Resource Manager-VNets in dezelfde regio
* Toegang tot resources die zijn beveiligd met Service-eind punten 
* Toegang tot bronnen die toegankelijk zijn via ExpressRoute of VPN-verbindingen
* Alle uitgaande verkeer beveiligen 
* Geforceerde tunneling voor al het uitgaande verkeer. 

![VNet-integratie](media/networking-features/vnet-integration.png)

Lees de documenten over [app service VNet-integratie][vnetintegration]voor meer informatie over deze functie.

## <a name="app-service-environment"></a>App Service-omgeving 

Een App Service Environment (ASE) is een implementatie met één Tenant van de Azure App Service die wordt uitgevoerd in uw VNet. De ASE maakt gebruik van cases zoals:

* Toegang tot resources in uw VNet
* Toegang tot resources via ExpressRoute
* Uw apps beschikbaar maken met een privé adres in uw VNet 
* Toegang tot resources via service-eind punten 

Met een ASE hoeft u geen functies zoals VNet-integratie of service-eind punten te gebruiken omdat de ASE zich al in uw VNet bevindt. Als u toegang wilt krijgen tot bronnen zoals SQL of opslag via service-eind punten, schakelt u service-eind punten in op het ASE-subnet. Als u toegang wilt krijgen tot resources in het VNet, is er geen aanvullende configuratie vereist.  Als u toegang wilt krijgen tot resources in ExpressRoute, bent u al in het VNet en hoeft u niets te configureren voor de ASE of de apps in de service. 

Omdat de apps in een ILB-ASE kunnen worden weer gegeven op een privé-IP-adres, kunt u eenvoudig WAF-apparaten toevoegen om alleen de apps die u wilt Internet beschikbaar te maken en de rest veilig te houden. Het is een goed proces voor het ontwikkelen van toepassingen met meerdere lagen. 

Er zijn een aantal dingen die nog niet mogelijk zijn van de multi tenant-service uit een ASE. Dit zijn onder andere zaken als:

* Uw apps beschikbaar maken op een privé-IP-adres
* Alle uitgaand verkeer beveiligen met netwerk besturings elementen die geen deel uitmaken van uw app 
* Uw apps hosten in één Tenant service 
* U kunt Maxi maal meer exemplaren schalen dan mogelijk zijn in de multi tenant-service 
* Certificaten voor persoonlijke certificerings instanties laden voor gebruik door uw apps met beveiligde eind punten voor persoonlijke certificerings instanties 
* TLS 1,1 afdwingen voor alle apps die worden gehost in het systeem zonder enige mogelijkheid om uit te scha kelen op het niveau van de app 
* Geef een toegewezen uitgaand adres op voor alle apps in uw ASE die niet met klanten worden gedeeld 

![ASE in een VNet](media/networking-features/app-service-environment.png)

De ASE biedt het beste verhaal rond geïsoleerde en toegewezen app-hosting, maar biedt wel enkele beheer problemen. Enkele zaken die u moet overwegen voordat u een operationele ASE gebruikt, zijn:
 
 * Een ASE wordt binnen uw VNet uitgevoerd, maar heeft afhankelijkheden buiten het VNet. Deze afhankelijkheden moeten zijn toegestaan. Meer informatie vindt u in [netwerk overwegingen voor een app service Environment][networkinfo]
 * Een ASE schaalt niet direct zoals de multi tenant-service. U moet anticiperen op schaal behoeften in plaats van opnieuw te schalen. 
 * Aan een ASE zijn hogere kosten verbonden. Om optimaal gebruik te kunnen gaan van uw ASE, moet u een planning nemen voor het nemen van veel werk belastingen in één ASE in plaats van deze voor kleine inspanningen te gebruiken
 * De apps in een ASE kunnen de toegang tot sommige apps in een ASE niet beperken en andere niet.
 * De ASE bevindt zich in een subnet en alle netwerk regels zijn van toepassing op alle verkeer van en naar die ASE. Als u regels voor binnenkomend verkeer voor slechts één app wilt toewijzen, gebruikt u toegangs beperkingen. 

## <a name="combining-features"></a>Functies combi neren 

De functies die worden vermeld voor de multi tenant-service kunnen samen worden gebruikt voor het oplossen van meer uitgebreide use-cases. Twee van de meest voorkomende gebruiks voorbeelden worden hier beschreven, maar ze zijn slechts voor beelden. Als u wilt weten wat de verschillende functies doen, kunt u bijna al uw systeem architectuur vereisten oplossen.

### <a name="inject-app-into-a-vnet"></a>App in een VNet injecteren

Een gemeen schappelijke aanvraag is informatie over het plaatsen van uw app in een VNet. Als u uw app in een VNet plaatst, worden de binnenkomende en uitgaande eind punten voor een app binnen een VNet. De ASE biedt de beste oplossing om dit probleem op te lossen, maar u kunt de meeste van wat nodig is met in de multi tenant-service door functies te combi neren. U kunt bijvoorbeeld alleen toepassingen op intranet hosten met persoonlijke inkomende en uitgaande adressen:

* Een Application Gateway maken met privé-en inkomend adres
* Inkomend verkeer naar uw app beveiligen met Service-eind punten 
* Gebruik de nieuwe VNet-integratie zodat de back-end van uw app zich in uw VNet bevindt 

Met deze implementatie stijl krijgt u geen toegewezen adres voor uitgaand verkeer naar Internet of hebt u de mogelijkheid om al het uitgaande verkeer van uw app te vergren delen.  Met deze implementatie stijl krijgt u een groot deel van wat u alleen op een andere manier zou kunnen bereiken met een ASE. 

### <a name="create-multi-tier-applications"></a>Toepassingen met meerdere lagen maken

Een toepassing met meerdere lagen is een toepassing waarbij de API-back-end-apps alleen kunnen worden geopend vanuit de front-end-laag. Als u een toepassing met meerdere lagen wilt maken, kunt u het volgende doen:

* VNet-integratie gebruiken om de back-end van uw front-end-web-app te verbinden met een subnet in een VNet
* Gebruik service-eind punten om inkomend verkeer naar uw API-app te beveiligen tot alleen afkomstig van het subnet dat door de front-end-web-app wordt gebruikt

![app met meerdere lagen](media/networking-features/multi-tier-app.png)

U kunt meerdere front-end-apps dezelfde API-app gebruiken met behulp van VNet-integratie van de andere front-end-apps en service-eind punten van de API-app met hun subnetten.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints

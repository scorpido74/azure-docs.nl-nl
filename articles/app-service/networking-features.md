---
title: Netwerkfuncties
description: Meer informatie over de netwerk functies in Azure App Service en welke functies u nodig hebt voor uw netwerk behoeften voor beveiliging of functionaliteit.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 860b1ac1713ac7afb7db2643d68974b399b5236b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207041"
---
# <a name="app-service-networking-features"></a>App Service-netwerk functies

Toepassingen in de Azure App Service kunnen op verschillende manieren worden geïmplementeerd. App Service gehoste apps zijn standaard rechtstreeks toegankelijk voor Internet en kunnen alleen Internet gehoste eind punten bereiken. Veel klant toepassingen moeten echter het inkomende en uitgaande netwerk verkeer beheren. Er zijn verschillende functies die beschikbaar zijn in de App Service om aan deze behoeften te voldoen. De uitdaging is te weten welke functie moet worden gebruikt om een bepaald probleem op te lossen. Dit document is bedoeld om klanten te helpen bepalen welke functie moet worden gebruikt op basis van enkele voor beelden van gebruiks voorbeelden.

Er zijn twee primaire implementatie typen voor de Azure App Service. Er is een open bare multi tenant-service, die als host fungeert voor App Service plannen in de gratis, gedeelde, Basic-, Standard-, Premium-, Premiumv2-en Premiumv3-prijzen-Sku's. Vervolgens is er één Tenant App Service Environment (ASE), die als host fungeert voor geïsoleerde SKU App Service plannen rechtstreeks in uw Azure-Virtual Network (VNet). De functies die u gebruikt, zijn afhankelijk van of u zich in de multi tenant-service of in een ASE bevindt. 

## <a name="multi-tenant-app-service-networking-features"></a>Multi tenant-App Service-netwerk functies 

Het Azure App Service is een gedistribueerd systeem. De rollen die binnenkomende HTTP/HTTPS-aanvragen verwerken, worden front-ends genoemd. De rollen die als host fungeren voor de werk belasting van de klant, worden werk nemers genoemd. Alle rollen in een App Service-implementatie bestaan in een netwerk met meerdere tenants. Omdat er veel verschillende klanten zijn in dezelfde App Service Scale-eenheid, kunt u het App Service netwerk niet rechtstreeks verbinden met uw netwerk. In plaats van de netwerken te verbinden, hebben we functies nodig om de verschillende aspecten van toepassings communicatie te kunnen afhandelen. De functies voor het verwerken van aanvragen voor uw app kunnen niet worden gebruikt om problemen op te lossen bij het aanroepen van uw app. Op dezelfde manier kunnen de functies voor het oplossen van problemen met aanroepen vanuit uw app niet worden gebruikt om problemen met uw app op te lossen.  

| Binnenkomende functies | Uitgaande functies |
|---------------------|-------------------|
| Toegewezen adres van de app | Hybride verbindingen |
| Toegangs beperkingen | Vereiste VNet-integratie voor gateway |
| Service-eindpunten | VNet-integratie |

Tenzij anders vermeld, kunnen alle functies tegelijk worden gebruikt. U kunt de functies combi neren om uw verschillende problemen op te lossen.

## <a name="use-case-and-features"></a>Use-case en-functies

U kunt het probleem op een aantal manieren oplossen voor een gegeven use-case.  De juiste functie die u moet gebruiken, is soms te wijten aan een andere reden dan alleen de use-case zelf. In de volgende inkomende use-cases wordt uitgelegd hoe u App Service-netwerk functies kunt gebruiken om problemen op te lossen bij het beheren van verkeer dat naar uw app gaat. 
 
| Inkomende use cases | Functie |
|---------------------|-------------------|
| Ondersteuning voor op IP gebaseerde SSL-behoeften voor uw app | toegewezen adres van de app |
| Niet gedeeld, toegewezen inkomend adres voor uw app | toegewezen adres van de app |
| De toegang tot uw app beperken vanuit een reeks goed gedefinieerde adressen | Toegangs beperkingen |
| Toegang tot mijn app beperken van resources in een VNet | Service-eindpunten </br> ILB ASE </br> Privé-eindpunten |
| Mijn app beschikbaar maken op een privé-IP in mijn VNet | ILB ASE </br> Privé-eindpunten </br> persoonlijk IP-adres voor inkomend verkeer op een Application Gateway met Service-eind punten |
| Mijn app beveiligen met een Web Application firewall (WAF) | Application Gateway + ILB ASE </br> Application Gateway met persoonlijke eind punten </br> Application Gateway met Service-eind punten </br> Azure front deur met toegangs beperkingen |
| Taak verdeling van verkeer naar mijn apps in verschillende regio's | Azure front deur met toegangs beperkingen | 
| Taak verdeling van verkeer in dezelfde regio | [Application Gateway met service-eindpunten][appgwserviceendpoints] | 

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

De Azure App Service schaal eenheden ondersteunen veel klanten in elke implementatie. Met de gratis en gedeelde SKU worden de workloads van de klant gehost op werk nemers met meerdere tenants. De basis en hierboven plannen voor de werk belasting van hosters die uitsluitend zijn toegewezen aan één App Service plan (ASP). Als u een Standard-App Service plan had, worden alle apps in dat plan uitgevoerd op dezelfde werk nemer. Als u de werk nemer uitbreidt, worden alle apps in die ASP gerepliceerd op een nieuwe werk nemer voor elk exemplaar in uw ASP. 

#### <a name="outbound-addresses"></a>Uitgaande adressen

De virtuele machines van de werk nemers worden in grote hoeveel heden onderverdeeld door de App Service prijs plannen. De gratis, gedeeld, basis, standaard en Premium gebruiken allemaal hetzelfde VM-type werk nemer. Premiumv2 bevindt zich op een ander VM-type. Premiumv3 is nog een ander VM-type. Bij elke wijziging in de VM-familie is er een andere set uitgaande adressen. Als u van standaard naar Premiumv2 schaalt, worden uw uitgaande adressen gewijzigd. Als u schaalt van Premiumv2 naar Premiumv3, worden uw uitgaande adressen gewijzigd. Er zijn enkele oudere schaal eenheden waarmee zowel de inkomende als uitgaande adressen worden gewijzigd wanneer u van Standard naar Premiumv2 schaalt. Er zijn een aantal adressen die worden gebruikt voor het maken van uitgaande oproepen. De uitgaande adressen die door uw app worden gebruikt voor het maken van uitgaande oproepen, worden weer gegeven in de eigenschappen voor uw app. Deze adressen worden gedeeld door alle apps die worden uitgevoerd op dezelfde VM-familie van werk nemers in die App Service implementatie. Als u alle mogelijke adressen wilt zien die uw app in deze schaal eenheid kan gebruiken, is er een andere eigenschap met de naam possibleOutboundAddresses die deze bevat. 

![App-eigenschappen](media/networking-features/app-properties.png)

App Service heeft een aantal eind punten die worden gebruikt om de service te beheren.  Deze adressen worden gepubliceerd in een afzonderlijk document en bevinden zich ook in het AppServiceManagement IP-service label. De label AppServiceManagement wordt alleen gebruikt met een App Service Environment waar u dit verkeer moet toestaan. De App Service inkomende adressen worden gevolgd in de AppService IP-service-tag. Er is geen IP-servicetag die de uitgaande adressen bevat die door App Service worden gebruikt. 

![App Service binnenkomend en uitgaand diagram](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Toegewezen adres van de app 

De functie voor het toegewezen adres van de app is een offshoot van de op IP gebaseerde SSL-mogelijkheid en wordt gebruikt door SSL in te stellen met uw app. Deze functie kan worden gebruikt voor SSL-aanroepen op basis van IP, maar kan ook worden gebruikt om uw app een adres te geven dat alleen het heeft. 

![Diagram van toegewezen adres van app](media/networking-features/app-assigned-address.png)

Wanneer u een aan een app toegewezen adres gebruikt, loopt uw verkeer nog steeds door dezelfde front-end-rollen die al het binnenkomende verkeer naar de App Service Scale-eenheid verwerken. Het adres dat is toegewezen aan uw app, wordt echter alleen gebruikt door uw app. De use cases voor deze functie zijn:

* Ondersteuning voor op IP gebaseerde SSL-behoeften voor uw app
* Stel een toegewezen adres in voor uw app die niet met iets anders is gedeeld

U kunt meer informatie over het instellen van een adres in uw app met behulp van de zelf studie over het [toevoegen van een TLS/SSL-certificaat in azure app service][appassignedaddress]. 

### <a name="access-restrictions"></a>Toegangs beperkingen 

Met de mogelijkheid tot toegangs beperkingen kunt u **inkomende** aanvragen filteren op basis van het oorspronkelijke IP-adres. De filter actie vindt plaats op de front-end-rollen die upstream zijn van de werk rollen waar uw apps worden uitgevoerd. Aangezien de front-end-rollen upstream van de werk nemers zijn, kan de mogelijkheid tot toegangs beperkingen worden beschouwd als beveiliging op netwerk niveau voor uw apps. Met deze functie kunt u een lijst met adressen blokken voor toestaan en weigeren maken die in volg orde van prioriteit worden geëvalueerd. Het is vergelijkbaar met de functie voor netwerk beveiligings groepen (NSG) die zich in azure-netwerken bevindt.  U kunt deze functie gebruiken in een ASE of in de multi tenant-service. Wanneer u gebruikt met een ILB-ASE, kunt u de toegang beperken tot persoonlijke adres blokken.

![Toegangs beperkingen](media/networking-features/access-restrictions.png)

De functie toegangs beperkingen helpt bij scenario's waarin u de IP-adressen wilt beperken die kunnen worden gebruikt om uw app te bereiken. De gebruiks voorbeelden voor deze functie zijn:

* De toegang tot uw app beperken vanuit een reeks goed gedefinieerde adressen 
* Beperk de toegang tot een service voor taak verdeling, zoals Azure front deur. Als u uw inkomende verkeer naar Azure front-deur wilt vergren delen, maakt u regels om verkeer toe te staan van 147.243.0.0/16 en 2a01:111:2050::/44. 

![Toegangs beperkingen met de voor deur](media/networking-features/access-restrictions-afd.png)

Als u de toegang tot uw app wilt vergren delen zodat deze alleen kan worden bereikt vanuit resources in uw Azure Virtual Network (VNet), hebt u een statisch openbaar adres nodig, ongeacht uw bron in uw VNet. Als de resources geen openbaar adres hebben, moet u in plaats daarvan de functie Service-eind punten gebruiken. Meer informatie over het inschakelen van deze functie met de zelf studie over het [configureren van toegangs beperkingen][iprestrictions].

### <a name="service-endpoints"></a>Service-eindpunten

Met Service-eind punten kunt u **inkomende** toegang tot uw app vergren delen, zodat het bron adres moet afkomstig zijn van een reeks subnetten die u selecteert. Deze functie werkt in combi natie met de beperkingen voor de IP-toegang. Service-eind punten zijn niet compatibel met externe fout opsporing. Als u fout opsporing op afstand wilt gebruiken met uw app, mag uw client zich niet in een subnet bevinden waarop Service-eind punten zijn ingeschakeld. Service-eind punten worden ingesteld in dezelfde gebruikers ervaring als de IP-toegangs beperkingen. U kunt een lijst met toegestane/geweigerde toegangs regels maken die open bare adressen en subnetten in uw VNets bevatten. Deze functie biedt ondersteuning voor scenario's zoals:

![Service-eind punten](media/networking-features/service-endpoints.png)

* Een Application Gateway met uw app instellen om inkomend verkeer naar uw app te vergren delen
* De toegang tot uw app beperken tot resources in uw VNet. Dit kunnen Vm's, as of zelfs andere apps zijn die gebruikmaken van VNet-integratie 

![Service-eind punten met Application Gateway](media/networking-features/service-endpoints-appgw.png)

Meer informatie over het configureren van service-eind punten met uw app vindt u in de zelf studie over het [configureren van toegangs beperkingen voor service-eind punten][serviceendpoints]

### <a name="private-endpoints"></a>Privé-eindpunten

Privé-eind punt is een netwerk interface waarmee u privé en veilig verbinding maakt met uw web-app met behulp van een persoonlijke Azure-koppeling. Persoonlijk eind punt maakt gebruik van een privé-IP-adres uit uw VNet, waardoor de web-app effectief in uw VNet wordt gezet. Deze functie is alleen voor **inkomende** stromen naar uw web-app.
[Privé-eind punten gebruiken voor Azure-web-app][privateendpoints]

Met persoonlijke eind punten kunnen scenario's zoals:

* Toegang tot mijn app beperken van resources in een VNet 
* Mijn app beschikbaar maken op een privé-IP in mijn VNet 
* Mijn app beveiligen met een WAF 

Met persoonlijke eind punten voor komt u dat gegevens exfiltration als enige wat u in het persoonlijke eind punt kunt bereiken, de app is waarvoor deze is geconfigureerd. 
 
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

Omdat de functie toegang tot on-premises bronnen mogelijk maakt zonder een uitgaand firewall-gat, is het populair bij ontwikkel aars. De andere uitgaande App Service-netwerk functies zijn gerelateerde virtuele netwerken in Azure. Hybride verbindingen heeft geen afhankelijkheid van het gebruik van een VNet en kan worden gebruikt voor een groter aantal netwerk behoeften. Het is belang rijk te weten dat de functie van App Service Hybride verbindingen geen bedoelt of weet wat u eraan doet. Dat wil zeggen dat u deze kunt gebruiken voor toegang tot een Data Base, een webservice of een wille keurige TCP-socket op een mainframe. De functie tunnelt in feite TCP-pakketten. 

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

De gateway vereist VNet-integratie functie is handig, maar er is nog steeds geen oplossing voor het openen van resources over ExpressRoute. Op het niveau van de ExpressRoute-verbindingen is het nodig dat apps aanroepen van beveiligde services voor service-eind punten kunnen maken. Voor het oplossen van beide extra behoeften is een andere VNet-integratie mogelijkheid toegevoegd. Met de nieuwe functie VNet-integratie kunt u de back-end van uw app in een subnet in een resource manager-VNet in dezelfde regio plaatsen. Deze functie is niet beschikbaar vanuit een App Service Environment, die zich al in een VNet bevindt. Met deze functie kunt u:

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

Een toepassing met meerdere lagen is een toepassing waarbij de API-back-end-apps alleen kunnen worden geopend vanuit de front-end-laag. Er zijn twee manieren om een toepassing met meerdere lagen te maken. Beide beginnen met VNet-integratie om uw front-end-web-app te verbinden met een subnet in een VNet. Hiermee wordt uw web-app in staat om aanroepen naar uw VNet te maken. Nadat de front-end-app is verbonden met het VNet, moet u kiezen hoe u de toegang tot uw API-toepassing wilt vergren delen.  U kunt:

* host zowel een front-end-als een API-app in dezelfde ILB-ASE en de front-end-app beschikbaar maken op internet met een toepassings gateway
* host de front-end in de multi tenant-service en de back-end in een ILB-ASE
* host zowel een front-end-als een API-app in de multi tenant-service

Als u de front-end-en API-app host voor een toepassing met meerdere lagen, kunt u het volgende doen:

Maak uw API-toepassing beschikbaar met persoonlijke eind punten in uw VNet

![persoonlijke eind punten app met twee lagen](media/networking-features/multi-tier-app-private-endpoint.png)

Gebruik service-eind punten om inkomend verkeer naar uw API-app te beveiligen tot alleen afkomstig van het subnet dat door de front-end-web-app wordt gebruikt

![beveiligde app voor service-eind punten](media/networking-features/multi-tier-app.png)

De afwegingen tussen de twee technieken zijn:

* met Service-eind punten hoeft u alleen verkeer naar uw API-app te beveiligen voor het integratie subnet. Hiermee wordt de API-app beveiligd, maar u kunt nog steeds een Data exfiltration mogelijk van uw front-end-app naar andere apps in de App Service.
* met persoonlijke eind punten hebt u twee subnetten tijdens het afspelen. Dit wordt toegevoegd aan de complexiteit. Het persoonlijke eind punt is ook een resource op het hoogste niveau en voegt meer toe om te beheren. Het voor deel van het gebruik van privé-eind punten is dat u geen gegevens exfiltration mogelijkheid hebt. 

Beide technieken werken met meerdere front-ends. Service-eind punten zijn op kleine schaal eenvoudiger te gebruiken omdat u eenvoudigweg service-eind punten voor de API-app op het subnet van de front-end-integratie inschakelt. Bij het toevoegen van meer front-end-apps moet u elke API-app aanpassen om service-eind punten te hebben met het integratie subnet. Met persoonlijke eind punten hebt u meer complexiteit, maar u hoeft niets te wijzigen op uw API-apps nadat u een persoonlijk eind punt hebt ingesteld. 

### <a name="line-of-business-applications"></a>Line-of-business-toepassingen

LOB-toepassingen (line-of-Business) zijn interne toepassingen die normaal gesp roken niet toegankelijk zijn voor toegang via internet. Deze toepassingen worden aangeroepen vanuit bedrijfs netwerken waar toegang strikt kan worden beheerd. Als u een ILB-ASE gebruikt, is het eenvoudig om uw line-of-business-toepassingen te hosten. Als u de multi tenant-service gebruikt, kunt u privé-eind punten of service-eind punten gebruiken in combi natie met een Application Gateway. Er zijn twee redenen om een Application Gateway te gebruiken met Service-eind punten in plaats van privé-eind punten:

* u hebt WAF-beveiliging nodig voor uw LOB-apps
* u wilt taak verdeling naar meerdere exemplaren van uw LOB-apps

Als dat niet het geval is, kunt u beter persoonlijke eind punten gebruiken. Met persoonlijke eind punten die beschikbaar zijn in App Service, kunt u uw apps zichtbaar maken op privé-adressen in uw VNet. Het persoonlijke eind punt dat u in uw VNet plaatst, kan worden bereikt via ExpressRoute-en VPN-verbindingen. Als u persoonlijke eind punten configureert, worden uw apps beschikbaar op een privé adres, maar moet u DNS configureren om het adres van on-premises te bereiken. Om dit te laten werken, moet u de Azure DNS privé zone met uw persoonlijke eind punten door sturen naar uw on-premises DNS-servers. Azure DNS particuliere zones bieden geen ondersteuning voor het door sturen van zones, maar u kunt wel ondersteuning bieden voor het gebruik van een DNS-server voor dat doel. Met deze sjabloon, [DNS-doorstuur server](https://azure.microsoft.com/resources/templates/301-dns-forwarder/), wordt het gemakkelijker om uw Azure DNS privé zone door te sturen naar uw on-PREMISES DNS-servers.

## <a name="app-service-ports"></a>App Service poorten

Als u de App Service scant, vindt u verschillende poorten die beschikbaar zijn voor binnenkomende verbindingen. Het is niet mogelijk om de toegang tot deze poorten in de multi tenant-service te blok keren of te beheren. De volgende poorten worden weer gegeven:

| Gebruik | Poorten |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Beheer | 454, 455 |
|  FTP-FTPS    | 21, 990, 10001-10020 |
|  Visual Studio externe fout opsporing  |  4020, 4022, 4024 |
|  Web Deploy-Service | 8172 |
|  Gebruik van infra structuur | 7654, 1221 |

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

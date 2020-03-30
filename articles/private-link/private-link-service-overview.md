---
title: Wat is Azure Private Link-service?
description: Meer informatie over Azure Private Link-service.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 2cc6c577abdb3698ef6aca1f1f04d239f09d119c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280428"
---
# <a name="what-is-azure-private-link-service"></a>Wat is Azure Private Link-service?

Azure Private Link-service is de verwijzing naar uw eigen service die wordt aangedreven door Azure Private Link. Uw service die achter [Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) wordt uitgevoerd, kan worden ingeschakeld voor private link-toegang, zodat consumenten tot uw service deze privé kunnen openen via hun eigen VNets. Uw klanten kunnen een privéeindpunt maken in hun VNet en het toewijzen aan deze service. In dit artikel worden concepten uitgelegd die verband houden met de kant van de serviceprovider. 

## <a name="workflow"></a>Werkstroom

![Private Link-servicewerkstroom](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>Uw private linkservice maken

- Configureer uw toepassing om achter een standaard load balancer in uw virtuele netwerk te lopen. Als u uw toepassing al hebt geconfigureerd achter een standaard load balancer, u deze stap overslaan.   
- Maak een Private Link Service die verwijst naar de load balancer hierboven. Kies in het selectieproces van de load balancer de IP-configuratie aan de frontend waar u het verkeer wilt ontvangen. Kies een subnet voor NAT IP-adressen voor de Private Link Service. Het wordt aanbevolen om ten minste acht NAT IP-adressen beschikbaar te hebben in het subnet. Al het consumentenverkeer lijkt afkomstig te zijn van deze pool van particuliere IP-adressen aan de dienstverlener. Kies de juiste eigenschappen/instellingen voor de Private Link Service.    

    > [!NOTE]
    > Azure Private Link Service wordt alleen ondersteund op Standard Load Balancer. 
    
### <a name="share-your-service"></a>Uw service delen

Nadat u een Private Link-service hebt gemaakt, genereert Azure een wereldwijd unieke naam die 'alias' wordt genoemd op basis van de naam die u voor uw service verstrekt. U de alias of bron-URI van uw service offline delen met uw klanten. Consumenten kunnen een Private Link-verbinding starten met de alias of de bron URI.
 
### <a name="manage-your-connection-requests"></a>Uw verbindingsaanvragen beheren

Nadat een consument een verbinding heeft gestart, kan de serviceprovider het verbindingsverzoek accepteren of weigeren. Alle verbindingsaanvragen worden vermeld onder de eigenschap **privateendpointconnections** op de Private Link-service.
 
### <a name="delete-your-service"></a>Uw service verwijderen

Als de Private Link-service niet meer in gebruik is, u deze verwijderen. Zorg er echter voor dat er geen privéeindpuntverbindingen aan verbonden zijn voordat u de service verwijdert. U alle verbindingen weigeren en de service verwijderen.

## <a name="properties"></a>Eigenschappen

Een Private Link-service geeft de volgende eigenschappen op: 

|Eigenschap |Uitleg  |
|---------|---------|
|Voorzieningsstaat (provisioningState)  |Een alleen-lezen eigenschap die de huidige inrichtingsstatus voor Private Link-service weergeeft. De toepasselijke provisioning states zijn: "Verwijderen; Mislukt; Geslaagd; Updaten". Wanneer de inrichtingsstatus is 'Geslaagd', hebt u uw Private Link-service met succes ingericht.        |
|Alias (alias)     | Alias is een wereldwijd unieke alleen-lezen tekenreeks voor uw service. Het helpt u de klantgegevens voor uw service te maskeren en creëert tegelijkertijd een eenvoudig te delen naam voor uw service. Wanneer u een Private Link-service maakt, genereert Azure de alias voor uw service die u met uw klanten delen. Uw klanten kunnen deze alias gebruiken om een verbinding met uw service aan te vragen.          |
|Zichtbaarheid (zichtbaarheid)     | Zichtbaarheid is de eigenschap die de belichtingsinstellingen voor uw Private Link-service regelt. Serviceproviders kunnen ervoor kiezen om de blootstelling aan hun service aan abonnementen te beperken met RBAC-machtigingen (Role-based Access Control), een beperkte set abonnementen of alle Azure-abonnementen.          |
|Automatische goedkeuring (autogoedkeuring)    |   Automatische goedkeuring regelt de geautomatiseerde toegang tot de Private Link-service. De abonnementen die in de lijst met automatische goedkeuring zijn opgegeven, worden automatisch goedgekeurd wanneer een verbinding wordt aangevraagd bij privéeindpunten in die abonnementen.          |
|Load Balancer Frontend IP-configuratie (loadBalancerFrontendIpConfigurations)    |    Private Link-service is gekoppeld aan het IP-adres van een standaardloadbalans. Al het verkeer dat bestemd is voor de dienst bereikt de voorkant van de SLB. U SLB-regels configureren om dit verkeer te leiden naar de juiste backendpools waar uw toepassingen worden uitgevoerd. Load balancer frontend IP-configuraties zijn anders dan NAT IP-configuraties.      |
|NAT IP-configuratie (ipConfigurations)    |    Deze eigenschap verwijst naar de IP-configuratie van NAT (Network Address Translation) voor de Private Link-service. Het NAT IP kan worden gekozen uit elk subnet in het virtuele netwerk van een serviceprovider. Private Link-service voert de bestemmingskant NAT-ing uit op het Private Link-verkeer. Dit zorgt ervoor dat er geen IP-conflict is tussen bron (consumentenkant) en bestemmingsruimte (serviceprovider). Aan de bestemmingszijde (kant van de serviceprovider) wordt het NAT IP-adres weergegeven als Bron-IP voor alle pakketten die door uw service en bestemmings-IP worden ontvangen voor alle pakketten die door uw service worden verzonden.       |
|Privéeindpuntverbindingen (privateEndpointConnections)     |  Deze eigenschap bevat de privéeindpunten die verbinding maken met de Private Link-service. Meerdere privéeindpunten kunnen verbinding maken met dezelfde Private Link-service en de serviceprovider kan de status voor afzonderlijke privéeindpunten beheren.        |
|TCP-proxy V2 (EnableProxyProtocol)     |  Met deze eigenschap kan de serviceprovider tcp proxy v2 gebruiken om verbindingsgegevens over de serviceconsument op te halen. Service Provider is verantwoordelijk voor het opzetten van ontvanger configs te kunnen ontsmet de proxy protocol v2 header.        |
|||


### <a name="details"></a>Details

- Private Link-service is toegankelijk vanaf goedgekeurde privéeindpunten in dezelfde regio. Het privéeindpunt kan worden bereikt vanaf hetzelfde virtuele netwerk, regionaal getouwde VNets, wereldwijd peered VNets en on-premises via private VPN- of ExpressRoute-verbindingen. 
 
- Bij het maken van een Private Link Service wordt een netwerkinterface gemaakt voor de levenscyclus van de bron. Deze interface is niet beheersbaar door de klant.
 
- De Private Link Service moet worden geïmplementeerd in dezelfde regio als het virtuele netwerk en de Standard Load Balancer.  
 
- Eén Private Link-service is toegankelijk vanaf meerdere privéeindpunten van verschillende VNets, abonnementen en/of Active Directory-tenants. De verbinding wordt tot stand gebracht via een verbindingsworkflow. 
 
- Meerdere Private Link-services kunnen op dezelfde Standaard Load Balancer worden gemaakt met behulp van verschillende front-end IP-configuraties. Er zijn grenzen aan het aantal Private Link-services dat u per Standaard Load Balancer en per abonnement maken. Zie [Azure-limieten voor](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)meer informatie.
 
- Private Link-service kan meerdere NAT IP-configuraties hebben die eraan zijn gekoppeld. Als u meer dan één NAT IP-configuratie kiest, kunnen serviceproviders worden geschaald. Tegenwoordig kunnen serviceproviders maximaal acht NAT IP-adressen per Private Link-service toewijzen. Met elk NAT IP-adres u meer poorten toewijzen voor uw TCP-verbindingen en zo uitschalen. Nadat u meerdere NAT IP-adressen aan een Private Link-service hebt toegevoegd, u de NAT IP-adressen niet verwijderen. Dit wordt gedaan om ervoor te zorgen dat actieve verbindingen niet worden beïnvloed tijdens het verwijderen van de NAT IP-adressen.


## <a name="alias"></a>Alias

**Alias** is een wereldwijd unieke naam voor uw service. Het helpt u de klantgegevens voor uw service te maskeren en creëert tegelijkertijd een eenvoudig te delen naam voor uw service. Wanneer u een Private Link-service maakt, genereert Azure een alias voor uw service die u met uw klanten delen. Uw klanten kunnen deze alias gebruiken om een verbinding met uw service aan te vragen.

De alias bestaat uit drie delen: *Voorvoegsel*. *GUID*. *Achtervoegsel*

- Voorvoegsel is de servicenaam. U kiezen voor uw eigen voorvoegsel. Nadat 'Alias' is gemaakt, u deze niet wijzigen, dus selecteer uw voorvoegsel op de juiste manier.  
- GUID zal worden geleverd door platform. Dit helpt om de naam wereldwijd uniek te maken. 
- Achtervoegsel wordt toegevoegd door Azure: *region*.azure.privatelinkservice 

Volledige alias: *Voorvoegsel*. {GUID}. *regio*.azure.privatelinkservice  

## <a name="control-service-exposure"></a>Serviceblootstelling regelen

Private Link-service biedt u opties om de belichting van uw service te beheren via de instelling 'Zichtbaarheid'. U de service privé maken voor consumptie van verschillende VNets die u bezit (alleen RBAC-machtigingen), de blootstelling beperken tot een beperkte set abonnementen die u vertrouwt of openbaar maken, zodat alle Azure-abonnementen verbindingen kunnen aanvragen op de Private Link Service. Uw zichtbaarheidsinstellingen bepalen of een consument verbinding kan maken met uw service of niet. 

## <a name="control-service-access"></a>Toegang tot de service beheren

Consumenten die door de zichtbaarheidsinstelling worden blootgesteld (gecontroleerd door zichtbaarheidsinstelling) kunnen een privéeindpunt in hun VNets maken en een verbinding met uw Private Link-service aanvragen. De privé-eindpuntverbinding wordt gemaakt in de status 'In behandeling' op het object Private Link-service. De dienstverlener is verantwoordelijk voor het handelen op het verbindingsverzoek. U de verbinding goedkeuren, de verbinding weigeren of de verbinding verwijderen. Alleen verbindingen die zijn goedgekeurd, kunnen verkeer naar de Privé-linkservice verzenden.

De actie van het goedkeuren van de verbindingen kan worden geautomatiseerd met behulp van de eigenschap automatische goedkeuring op de Private Link-service. Automatische goedkeuring is een mogelijkheid voor serviceproviders om een reeks abonnementen vooraf goed te keuren voor geautomatiseerde toegang tot hun service. Klanten moeten hun abonnementen offline delen om serviceproviders toe te voegen aan de autogoedkeuringslijst. Automatische goedkeuring is een subset van de zichtbaarheidsarray. Zichtbaarheid regelt de belichtingsinstellingen, terwijl automatische goedkeuring de goedkeuringsinstellingen voor uw service regelt. Als een klant een verbinding aanvraagt van een abonnement in de autogoedkeuringslijst, wordt de verbinding automatisch goedgekeurd en wordt de verbinding tot stand gebracht. Serviceproviders hoeven de aanvraag niet meer handmatig goed te keuren. Als een klant daarentegen een verbinding aanvraagt van een abonnement in de zichtbaarheidsarray en niet in de array voor automatische goedkeuring, bereikt de aanvraag de serviceprovider, maar moet de serviceprovider de verbindingen handmatig goedkeuren.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Verbindingsgegevens verkrijgen met TCP Proxy v2

Bij het gebruik van private link-service is het bron-IP-adres van de pakketten afkomstig van privéeindpunt netwerkadres vertaald (NAT) aan de kant van de serviceprovider met behulp van het NAT IP-adres dat is toegewezen aan het virtuele netwerk van de provider. Vandaar dat de aanvragen het toegewezen NAT IP-adres ontvangen in plaats van het werkelijke bron-IP-adres van de gebruikers van de dienst. Als uw toepassing daadwerkelijk bron-IP-adres van consumentenzijde nodig heeft, u het proxyprotocol op uw service inschakelen en de informatie ophalen uit de header proxyprotocol. Naast het IP-adres van de bron, draagt proxy protocol header ook de LinkID van het privé eindpunt. De combinatie van source IP-adres en LinkID kan serviceproviders helpen hun consumenten op unieke wijze te identificeren. Voor meer informatie over Proxy Protocol, bezoek [hier](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Deze informatie wordt als volgt gecodeerd met behulp van een aangepaste TLV-vector (Type-Length-Value):

Aangepaste TLV-details:

|Veld |Lengte (Octetten)  |Beschrijving  |
|---------|---------|----------|
|Type  |1        |PP2_TYPE_AZURE (0xEE)|
|Lengte  |2      |Waardesduur|
|Waarde  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 bytes) die de LINKID van het privéeindpunt vertegenwoordigt. Gecodeerd in kleine endian formaat.|

 > [!NOTE]
 > De serviceprovider is verantwoordelijk om ervoor te zorgen dat de service achter de standaard load balancer is geconfigureerd om de proxyprotocolkop te parseren volgens de [specificatie](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) wanneer het proxyprotocol is ingeschakeld op de privékoppelingsservice. De aanvraag mislukt als de instelling van het proxyprotocol is ingeschakeld op de privékoppelingsservice, maar de service van de serviceprovider is niet geconfigureerd om de header te ontstemmen. Op dezelfde manier mislukt de aanvraag als de service van de serviceprovider een proxyprotocol-header verwacht terwijl de instelling niet is ingeschakeld op de privékoppelingsservice. Zodra de instelling van het proxyprotocol is ingeschakeld, wordt de header van het proxyprotocol ook opgenomen in HTTP/TCP-statussondes van host naar de backend virtuele machines, ook al zijn er geen clientinformatie in de header. 

## <a name="limitations"></a>Beperkingen

Hieronder volgen de bekende beperkingen bij het gebruik van de Private Link-service:
- Alleen ondersteund op Standard Load Balancer 
- Ondersteunt alleen IPv4-verkeer
- Ondersteunt alleen TCP-verkeer

## <a name="next-steps"></a>Volgende stappen
- [Een privékoppelingsservice maken met Azure PowerShell](create-private-link-service-powershell.md)
- [Een privékoppelingsservice maken met Azure CLI](create-private-link-service-cli.md)

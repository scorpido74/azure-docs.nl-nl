---
title: Een virtueel netwerk configureren - Premium Azure-cache voor Redis
description: Meer informatie over het maken en beheren van ondersteuning voor virtueel netwerk voor uw Azure-cache voor Azure-cache in Premium-lagen voor Redis-exemplaren
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 6c7c041565f6376e7f8b8b84f5076b30c1eec7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278114"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Ondersteuning voor virtueel netwerk configureren voor een Premium Azure-cache voor Redis
Azure Cache voor Redis heeft verschillende cache-aanbiedingen, die flexibiliteit bieden in de keuze van cachegrootte en functies, waaronder premium-laagfuncties zoals clustering, persistentie en ondersteuning voor virtueel netwerk. Een VNet is een privénetwerk in de cloud. Wanneer een Azure-cache voor Redis-instantie is geconfigureerd met een VNet, is deze niet openbaar adresseerbaar en alleen toegankelijk vanaf virtuele machines en toepassingen binnen het VNet. In dit artikel wordt beschreven hoe u ondersteuning voor virtueel netwerk configureert voor een premium Azure-cache voor bijvoorbeeld Redis.

> [!NOTE]
> Azure Cache voor Redis ondersteunt zowel klassieke als Resource Manager VNets.
> 
> 

Zie [Inleiding tot de Azure-cache voor Redis Premium-laag voor](cache-premium-tier-intro.md)meer informatie over andere functies met een premiumcache.

## <a name="why-vnet"></a>Waarom VNet?
[De Implementatie van Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) biedt verbeterde beveiliging en isolatie voor uw Azure-cache voor Redis, evenals subnetten, toegangsbeheerbeleid en andere functies om de toegang verder te beperken.

## <a name="virtual-network-support"></a>Ondersteuning voor virtuele netwerken
VNet-ondersteuning (Virtual Network) is geconfigureerd op de **nieuwe Azure-cache voor redis-blade** tijdens het maken van caches. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Zodra u een premium prijscategorie hebt geselecteerd, u de Integratie van Redis VNet configureren door een VNet te selecteren dat zich in hetzelfde abonnement en dezelfde locatie bevindt als uw cache. Als u een nieuw VNet wilt gebruiken, maakt u deze eerst door de stappen te volgen in [Een virtueel netwerk maken met de Azure-portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) of Een virtueel netwerk [(klassiek) maken met behulp van de Azure-portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) en vervolgens terug te keren naar de **nieuwe Azure-cache voor het** blade van Redis om uw premiumcache te maken en te configureren.

Als u het VNet wilt configureren voor uw nieuwe cache, klikt u op **Virtueel netwerk** in het **nieuwe Azure-cache voor redis-blad** en selecteert u de gewenste VNet in de vervolgkeuzelijst.

![Virtueel netwerk][redis-cache-vnet]

Selecteer het gewenste subnet in de vervolgkeuzelijst **Subnet.**  Geef desgewenst een **statisch IP-adres op.** Het veld **Statisch IP-adres** is optioneel en als er geen is opgegeven, wordt er een gekozen uit het geselecteerde subnet.

> [!IMPORTANT]
> Wanneer u een Azure-cache voor Redis implementeert in een VNet voor Resourcebeheer, moet de cache zich in een speciaal subnet bevinden dat geen andere bronnen bevat, behalve Azure Cache voor Redis-exemplaren. Als een poging wordt gedaan om een Azure-cache voor Redis te implementeren in een VNet voor Resourcebeheer naar een subnet dat andere bronnen bevat, mislukt de implementatie.
> 
> 

![Virtueel netwerk][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure reserveert een aantal IP-adressen binnen elk subnet en deze adressen kunnen niet worden gebruikt. De eerste en laatste IP-adressen van de subnetten zijn gereserveerd voor protocolconformiteit, samen met nog drie adressen die worden gebruikt voor Azure-services. Zie [Zijn er beperkingen voor het gebruik van IP-adressen binnen deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Naast de IP-adressen die worden gebruikt door de Azure VNET-infrastructuur, gebruikt elke Redis-instantie in het subnet twee IP-adressen per shard en één extra IP-adres voor de load balancer. Een niet-geclusterde cache wordt beschouwd als één scherf.
> 
> 

Nadat de cache is gemaakt, u de configuratie voor het VNet bekijken door in het **menu Resource**op **Virtueel netwerk** te klikken.

![Virtueel netwerk][redis-cache-vnet-info]

Als u verbinding wilt maken met uw Azure-cache voor het exemplaar Redis wanneer u een VNet gebruikt, geeft u de hostnaam van uw cache op in de verbindingstekenreeks zoals weergegeven in het volgende voorbeeld:

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-cache-for-redis-vnet-faq"></a>Veelgestelde vragen over Azure Cache voor Redis VNet
De volgende lijst bevat antwoorden op veelgestelde vragen over de Azure-cache voor Redis-schaling.

* Wat zijn enkele veelvoorkomende foutieve configuratieproblemen met Azure Cache voor Redis en VNets?
* [Hoe kan ik controleren of mijn cache werkt in een VNET?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Waarom krijg ik een foutmelding als ik verbinding probeer te maken met mijn Azure-cache voor Redis in een VNET?
* [Kan ik VNets gebruiken met een standaard of basiscache?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Waarom mislukt het maken van een Azure-cache voor Redis in sommige subnetten, maar andere niet?
* [Wat zijn de vereisten voor subnetadresruimte?](#what-are-the-subnet-address-space-requirements)
* [Werken alle cachefuncties bij het hosten van een cache in een VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Wat zijn enkele veelvoorkomende foutieve configuratieproblemen met Azure Cache voor Redis en VNets?
Wanneer Azure Cache voor Redis wordt gehost in een VNet, worden de poorten in de volgende tabellen gebruikt. 

>[!IMPORTANT]
>Als de poorten in de volgende tabellen zijn geblokkeerd, werkt de cache mogelijk niet goed. Het blokkeren van een of meer van deze poorten is het meest voorkomende foutconfiguratieprobleem bij het gebruik van Azure Cache voor Redis in een VNet.
> 
> 

- [Vereisten voor de uitgaande poort](#outbound-port-requirements)
- [Vereisten voor inkomende poort](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Vereisten voor de uitgaande poort

Er zijn negen uitgaande havenvereisten. Uitgaande aanvragen in deze bereiken zijn ofwel uitgaand naar andere services die nodig zijn om de cache te laten functioneren of intern naar het Subnet Van Redis voor internodecommunicatie. Voor georeplicatie bestaan aanvullende outbound-vereisten voor communicatie tussen subnetten van de primaire en secundaire cache.

| Poort(en) | Richting | Transportprotocol | Doel | Lokaal IP | Extern IP-adres |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Uitgaand |TCP |Redis afhankelijkheden van Azure Storage/PKI (Internet) | (Redis subnet) |* |
| 443 | Uitgaand | TCP | Afhankelijkheid van Redis van Azure Key Vault | (Redis subnet) | AzureKeyVault <sup>1</sup> |
| 53 |Uitgaand |TCP/UDP |Redis afhankelijkheden van DNS (Internet/VNet) | (Redis subnet) | 168.63.129.16 en 169.254.169.254 <sup>2</sup> en elke aangepaste DNS-server voor het subnet <sup>3</sup> |
| 8443 |Uitgaand |TCP |Interne communicatie voor Redis | (Redis subnet) | (Redis subnet) |
| 10221-10231 |Uitgaand |TCP |Interne communicatie voor Redis | (Redis subnet) | (Redis subnet) |
| 20226 |Uitgaand |TCP |Interne communicatie voor Redis | (Redis subnet) |(Redis subnet) |
| 13000-13999 |Uitgaand |TCP |Interne communicatie voor Redis | (Redis subnet) |(Redis subnet) |
| 15000-15999 |Uitgaand |TCP |Interne communicatie voor Redis en Geo-Replicatie | (Redis subnet) |(Redis subnet) (Geo-replica peer subnet) |
| 6379-6380 |Uitgaand |TCP |Interne communicatie voor Redis | (Redis subnet) |(Redis subnet) |

<sup>1</sup> U de servicetag 'AzureKeyVault' gebruiken met Resource Manager Network Security Groups.

<sup>2</sup> Deze IP-adressen die eigendom zijn van Microsoft worden gebruikt om de Host VM aan te pakken die Azure DNS bedient.

<sup>3</sup> Niet nodig voor subnetten zonder aangepaste DNS-server of nieuwere redis-caches die aangepaste DNS negeren.

#### <a name="geo-replication-peer-port-requirements"></a>Vereisten voor geo-replicatie peer-poort

Als u georeplicatie tussen caches in Azure Virtual Networks gebruikt, moet u er rekening mee houden dat de aanbevolen configuratie is om poorten 15000-15999 voor het hele subnet te deblokkeren in zowel inkomende als uitgaande richtingen naar beide caches, zodat alle replicacomponenten in het subnet kan rechtstreeks met elkaar communiceren, zelfs in het geval van een toekomstige geo-failover.

#### <a name="inbound-port-requirements"></a>Vereisten voor inkomende poort

Er zijn acht vereisten voor inkomende havenbereik. Binnenkomende aanvragen in deze bereiken zijn binnenkomend van andere services die worden gehost in dezelfde VNET of intern in de Redis-subnetcommunicatie.

| Poort(en) | Richting | Transportprotocol | Doel | Lokaal IP | Extern IP-adres |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Inkomend |TCP |Clientcommunicatie met Redis, Azure load balancing | (Redis subnet) | (Redis-subnet), virtueel netwerk, Azure Load Balancer <sup>1</sup> |
| 8443 |Inkomend |TCP |Interne communicatie voor Redis | (Redis subnet) |(Redis subnet) |
| 8500 |Inkomend |TCP/UDP |Azure load balancing Azure | (Redis subnet) |Azure Load Balancer |
| 10221-10231 |Inkomend |TCP |Interne communicatie voor Redis | (Redis subnet) |(Redis-subnet), Azure Load Balancer |
| 13000-13999 |Inkomend |TCP |Clientcommunicatie met Redis-clusters, Azure-taakverdeling | (Redis subnet) |Virtueel netwerk, Azure Load Balancer |
| 15000-15999 |Inkomend |TCP |Clientcommunicatie met Redis-clusters, Azure load Balancing en Geo-Replicatie | (Redis subnet) |Virtueel netwerk, Azure Load Balancer (Subnet van georeplicapeer) |
| 16001 |Inkomend |TCP/UDP |Azure load balancing Azure | (Redis subnet) |Azure Load Balancer |
| 20226 |Inkomend |TCP |Interne communicatie voor Redis | (Redis subnet) |(Redis subnet) |

<sup>1</sup> U de servicetag 'AzureLoadBalancer' (Resource Manager) (of 'AZURE_LOADBALANCER' voor klassiek) gebruiken voor het opstellen van de NSG-regels.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Aanvullende VNET-vereisten voor netwerkconnectiviteit

Er zijn vereisten voor netwerkconnectiviteit voor Azure Cache voor Redis die in eerste instantie mogelijk niet in een virtueel netwerk worden gehaald. Azure Cache voor Redis vereist dat alle volgende items goed functioneren wanneer ze worden gebruikt in een virtueel netwerk.

* Uitgaande netwerkconnectiviteit met Azure Storage-eindpunten wereldwijd. Dit omvat eindpunten in dezelfde regio als de Azure-cache voor redis-instantie, evenals opslageindpunten in **andere** Azure-regio's. Azure Storage-eindpunten worden opgelost onder de volgende DNS-domeinen: *table.core.windows.net,* *blob.core.windows.net,* *queue.core.windows.net*en *file.core.windows.net*. 
* Uitgaande netwerkconnectiviteit met *ocsp.msocsp.com,* *mscrl.microsoft.com*en *crl.microsoft.com*. Deze connectiviteit is nodig om SSL-functionaliteit te ondersteunen.
* De DNS-configuratie voor het virtuele netwerk moet in staat zijn om alle eindpunten en domeinen op te lossen die in de eerdere punten worden genoemd. Aan deze DNS-vereisten kan worden voldaan door ervoor te zorgen dat een geldige DNS-infrastructuur is geconfigureerd en onderhouden voor het virtuele netwerk.
* Uitgaande netwerkconnectiviteit met de volgende Azure Monitoring-eindpunten, die worden opgelost onder de volgende DNS-domeinen: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net , shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Hoe kan ik controleren of mijn cache werkt in een VNET?

>[!IMPORTANT]
>Wanneer u verbinding maakt met een Azure-cache voor Redis-instantie die wordt gehost in een VNET, moeten uw cacheclients zich in hetzelfde VNET of in een VNET bevinden met VNET-peering die is ingeschakeld binnen dezelfde Azure-regio. Global VNET Peering wordt momenteel niet ondersteund. Dit geldt ook voor alle testtoepassingen of diagnostische pingtools. Ongeacht waar de clienttoepassing wordt gehost, moeten netwerkbeveiligingsgroepen zodanig zijn geconfigureerd dat het netwerkverkeer van de client het exemplaar Van Redis kan bereiken.
>
>

Zodra de poortvereisten zijn geconfigureerd zoals beschreven in de vorige sectie, u controleren of uw cache werkt door de volgende stappen uit te voeren.

- [Start](cache-administration.md#reboot) alle cacheknooppunten opnieuw op. Als niet alle vereiste cacheafhankelijkheden kunnen worden bereikt (zoals gedocumenteerd [in inkomende poortvereisten](cache-how-to-premium-vnet.md#inbound-port-requirements) en [uitgaande poortvereisten),](cache-how-to-premium-vnet.md#outbound-port-requirements)kan de cache niet opnieuw worden opgestart.
- Zodra de cacheknooppunten opnieuw zijn gestart (zoals gerapporteerd door de cachestatus in de Azure-portal), u de volgende tests uitvoeren:
  - ping het cacheeindpunt (met poort 6380) van een machine die zich binnen dezelfde VNET bevindt als de cache, met behulp van [tcping](https://www.elifulkerson.com/projects/tcping.php). Bijvoorbeeld:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Als `tcping` het hulpprogramma meldt dat de poort is geopend, is de cache beschikbaar voor verbinding met clients in het VNET.

  - Een andere manier om te testen is het maken van een test cache client (dat kan een eenvoudige console applicatie met behulp van StackExchange.Redis) die verbinding maakt met de cache en voegt en haalt een aantal items uit de cache. Installeer de voorbeeldclienttoepassing op een VM die zich in dezelfde VNET-cache bevindt en voer deze uit om de verbinding met de cache te verifiëren.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Waarom krijg ik een foutmelding als ik verbinding probeer te maken met mijn Azure-cache voor Redis in een VNET?

Wanneer u verbinding probeert te maken met een Azure-cache voor Redis in een VNET, ziet u een certificaatvalidatiefout als volgt:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

De oorzaak kan zijn dat u verbinding maakt met de host via het IP-adres. We raden u aan de hostnaam te gebruiken. Met andere woorden, gebruik het volgende:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Vermijd het gebruik van het IP-adres dat lijkt op de volgende verbindingstekenreeks:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Als u de DNS-naam niet oplossen, bevatten `sslHost` sommige clientbibliotheken configuratieopties zoals die worden geleverd door de StackExchange.Redis-client. Hiermee u de hostnaam overschrijven die wordt gebruikt voor certificaatvalidatie. Bijvoorbeeld:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Kan ik VNets gebruiken met een standaard of basiscache?
VNets kunnen alleen worden gebruikt met premium caches.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Waarom mislukt het maken van een Azure-cache voor Redis in sommige subnetten, maar andere niet?
Als u een Azure-cache voor Redis implementeert in een VNet voor Resourcebeheer, moet de cache zich in een speciaal subnet bevinden dat geen ander resourcetype bevat. Als een poging wordt gedaan om een Azure-cache voor Redis te implementeren in een VNet-subnet van Resource Manager dat andere bronnen bevat, mislukt de implementatie. U moet de bestaande bronnen in het subnet verwijderen voordat u een nieuwe Azure-cache voor Redis maken.

U meerdere typen resources implementeren op een klassieke VNet, zolang u voldoende IP-adressen beschikbaar hebt.

### <a name="what-are-the-subnet-address-space-requirements"></a>Wat zijn de vereisten voor subnetadresruimte?
Azure reserveert een aantal IP-adressen binnen elk subnet en deze adressen kunnen niet worden gebruikt. De eerste en laatste IP-adressen van de subnetten zijn gereserveerd voor protocolconformiteit, samen met nog drie adressen die worden gebruikt voor Azure-services. Zie [Zijn er beperkingen voor het gebruik van IP-adressen binnen deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Naast de IP-adressen die worden gebruikt door de Azure VNET-infrastructuur, gebruikt elke Redis-instantie in het subnet twee IP-adressen per shard en één extra IP-adres voor de load balancer. Een niet-geclusterde cache wordt beschouwd als één scherf.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Werken alle cachefuncties bij het hosten van een cache in een VNET?
Wanneer uw cache deel uitmaakt van een VNET, hebben alleen clients in het VNET toegang tot de cache. Als gevolg hiervan werken de volgende functies voor cachebeheer op dit moment niet.

* Redis Console - Omdat Redis Console wordt uitgevoerd in uw lokale browser, die zich buiten het VNET bevindt, kan deze geen verbinding maken met uw cache.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>ExpressRoute gebruiken met Azure-cache voor Redis

Klanten kunnen een [Azure ExpressRoute-circuit](https://azure.microsoft.com/services/expressroute/) verbinden met hun virtuele netwerkinfrastructuur, waardoor hun on-premises netwerk wordt uitgebreid naar Azure. 

Standaard voert een nieuw gemaakt ExpressRoute-circuit geen gedwongen tunneling uit (advertentie van een standaardroute, 0.0.0.0/0) op een VNET. Als gevolg hiervan is uitgaande internetverbinding rechtstreeks toegestaan vanuit vnet en kunnen clienttoepassingen verbinding maken met andere Azure-eindpunten, waaronder Azure Cache voor Redis.

Nochtans moet een gemeenschappelijke klantconfiguratie gedwongen tunneling gebruiken (een standaardroute adverteren) die uitgaand internetverkeer dwingt om in plaats daarvan on-premises te stromen. Deze verkeersstroom verbreekt de verbinding met Azure Cache voor Redis als het uitgaande verkeer vervolgens on-premises wordt geblokkeerd, zodat de instantie Azure Cache voor Redis niet in staat is om te communiceren met de afhankelijkheden.

De oplossing is om een (of meer) door de gebruiker gedefinieerde routes (UDR's) te definiëren op het subnet dat de Azure-cache voor Redis bevat. Een UDR definieert subnetspecifieke routes die worden gehonoreerd in plaats van de standaardroute.

Indien mogelijk wordt aanbevolen om de volgende configuratie te gebruiken:

* De ExpressRoute-configuratie adverteert 0.0.0.0/0 en standaard forcetunnels al het uitgaande verkeer on-premises.
* De UDR die is toegepast op het subnet met de Azure-cache voor Redis, definieert 0.0.0.0/0 met een werkroute voor TCP/IP-verkeer naar het openbare internet; bijvoorbeeld door het volgende hoptype in te stellen op 'Internet'.

Het gecombineerde effect van deze stappen is dat het subnetniveau UDR voorrang heeft op de Gedwongen tunneling van ExpressRoute, waardoor uitgaande internettoegang vanuit de Azure-cache voor Redis wordt gewaarborgd.

Verbinding maken met een Azure-cache voor Redis-instantie vanuit een on-premises toepassing met ExpressRoute is geen typisch gebruiksscenario vanwege prestatieredenen (voor de beste prestaties moet Azure Cache voor Redis-clients zich in dezelfde regio bevinden als de Azure-cache voor Redis) .

>[!IMPORTANT] 
>De routes die in een UDR zijn **gedefinieerd, moeten** specifiek genoeg zijn om voorrang te krijgen op routes die worden geadverteerd door de ExpressRoute-configuratie. In het volgende voorbeeld wordt het brede adresbereik 0.0.0/0/0 gebruikt en als zodanig kan deze mogelijk per ongeluk worden overschreven door routeadvertenties met specifiekere adresbereiken.

>[!WARNING]  
>Azure Cache voor Redis wordt niet ondersteund met ExpressRoute-configuraties die **ten onrechte routes cross-advertiseeren van het openbare peeringpad naar het privé-peeringpad.** ExpressRoute-configuraties met openbare peering geconfigureerd, ontvangen routeadvertenties van Microsoft voor een grote set Microsoft Azure IP-adresbereiken. Als deze adresbereiken onjuist worden geadverteerd op het privé-peeringpad, is het resultaat dat alle uitgaande netwerkpakketten uit de Azure Cache voor het subnet van het exemplaar Redis ten onrechte worden getunneld naar het on-premises netwerk van een klant Infrastructuur. Deze netwerkstroom maakt een stuk breekt met Azure Cache voor Redis. De oplossing voor dit probleem is om cross-advertising routes te stoppen van de openbare peering pad naar de prive-peering pad.


Achtergrondinformatie over door de gebruiker gedefinieerde routes is beschikbaar in dit [overzicht](../virtual-network/virtual-networks-udr-overview.md).

Zie [ExpressRoute technisch overzicht](../expressroute/expressroute-introduction.md)voor meer informatie over ExpressRoute.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het gebruik van meer premium cachefuncties.

* [Inleiding tot de azure-cache voor de laag Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

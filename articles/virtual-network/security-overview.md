---
title: Overzicht azure-netwerkbeveiligingsgroepen
titlesuffix: Azure Virtual Network
description: Meer informatie over netwerkbeveiligingsgroepen. Met netwerkbeveiligingsgroepen u netwerkverkeer filteren tussen Azure-bronnen.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 8f3497f113981ae563023750ad8979c88c640f5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123339"
---
# <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
<a name="network-security-groups"></a>

U kunt netwerkverkeer naar en van Azure-resources in een virtueel Azure-netwerk filteren met een netwerkbeveiligingsgroep. Een netwerkbeveiligingsgroep bevat beveiligingsregels waarmee binnenkomend netwerkverkeer naar, of uitgaand netwerkverkeer van, diverse typen Azure-resources kan worden toegestaan of geweigerd. Zie [Integratie van virtuele netwerken voor Azure-services](virtual-network-for-azure-services.md) voor meer informatie over de Azure-resources die in een virtueel netwerk kunnen worden geïmplementeerd en waaraan netwerkbeveiligingsgroepen kunnen worden gekoppeld. Voor elke regel kunt u de bron en het doel, de poort en het protocol opgeven.

In dit artikel worden de concepten van netwerkbeveiligingsgroepen uitgelegd, zodat u ze effectief kunt gebruiken. Als u nog nooit een netwerkbeveiligingsgroep hebt gemaakt, kunt u een snelle [zelfstudie](tutorial-filter-network-traffic.md) uitvoeren om enige ervaring met het maken van een netwerkbeveiligingsgroep op te doen. Zie [Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen](manage-network-security-group.md) als u bekend bent met het netwerkbeveiligingsgroepen en u ze wilt beheren. Zie [Een probleem met een netwerkverkeersfilter in een virtuele machine vaststellen](diagnose-network-traffic-filter-problem.md) als u communicatieproblemen hebt en problemen met netwerkbeveiligingsgroepen wilt oplossen. U kunt [logboekregistratie van stromen in netwerkbeveiligingsgroepen](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) inschakelen voor het analyseren van netwerkverkeer naar en van resources waaraan een netwerkbeveiligingsgroep is gekoppeld.

## <a name="security-rules"></a>Beveiligingsregels

Een netwerkbeveiligingsgroep bevat nul regels of zoveel regels als u wilt binnen de [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) van uw Azure-abonnement. Elke regel geeft de volgende eigenschappen aan:

|Eigenschap  |Uitleg  |
|---------|---------|
|Name|Een unieke naam binnen de netwerkbeveiligingsgroep.|
|Prioriteit | Een getal tussen 100 en 4096. Regels worden verwerkt in volgorde van prioriteit, waarbij lagere getallen worden verwerkt vóór hogere getallen omdat lagere getallen een hogere prioriteit hebben. Zodra het verkeer overeenkomt met een regel, wordt de verwerking beëindigd. Daardoor worden regels met een lagere prioriteit (een hoger getal) die dezelfde kenmerken hebben als regels met een hogere prioriteit, niet verwerkt.|
|Bron of doel| Een IP-adres, CIDR-blok (bijvoorbeeld 10.0.0.0/24), [servicetag](service-tags-overview.md) of [toepassingsbeveiligingsgroep](#application-security-groups). Als u een adres opgeeft voor een Azure-resource, geeft u het privé-IP-adres op dat aan de resource is toegewezen. Netwerkbeveiligingsgroepen worden verwerkt nadat Azure een openbaar IP-adres vertaalt naar een privé-IP-adres voor binnenkomend verkeer en voordat Azure een privé-IP-adres naar een openbaar IP-adres voor uitgaand verkeer vertaalt. Meer informatie over de [IP-adressen](virtual-network-ip-addresses-overview-arm.md) van Azure. Als u een bereik, servicetag of toepassingsbeveiligingsgroep opgeeft, hoeft u minder beveiligingsregels te maken. De mogelijkheid om meerdere afzonderlijke IP-adressen en bereiken op te geven (u niet meerdere servicetags of toepassingsgroepen opgeven) in een regel wordt [augmented security rules](#augmented-security-rules)genoemd. Uitgebreide beveiligingsregels kunnen alleen worden gemaakt in netwerkbeveiligingsgroepen die zijn gemaakt via het Resource Manager-implementatiemodel. U kunt niet meerdere IP-adressen en IP-adresbereiken opgeven in netwerkbeveiligingsgroepen die zijn gemaakt via het klassieke implementatiemodel. Meer informatie over [implementatiemodellen van Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protocol     | TCP, UDP, ICMP of Any.|
|Richting| Hiermee wordt aangegeven of de regel van toepassing is op binnenkomend of uitgaand verkeer.|
|Poortbereik     |U kunt één poort of een poortbereik opgeven. U kunt bijvoorbeeld 80 of 10000-10005 opgeven. Als u bereiken opgeeft, hoeft u minder beveiligingsregels te maken. Uitgebreide beveiligingsregels kunnen alleen worden gemaakt in netwerkbeveiligingsgroepen die zijn gemaakt via het Resource Manager-implementatiemodel. U kunt niet meerdere poorten of poortbereiken opgeven in dezelfde beveiligingsregel in netwerkbeveiligingsgroepen die zijn gemaakt via het klassieke implementatiemodel.   |
|Actie     | Toestaan of weigeren        |

Beveiligingsregels voor netwerkbeveiligingsgroepen worden op prioriteit geëvalueerd op basis van de 5 tuple-gegevens (bron, bronpoort, doel, doelpoort en protocol) voor het toestaan of weigeren van het verkeer. Voor bestaande verbindingen wordt een stroomrecord gemaakt. Communicatie wordt toegestaan of geweigerd op basis van de verbindingsstatus van de stroomrecord. Met de stroomrecord wordt een netwerkbeveiligingsgroep toegestaan stateful te zijn. Als u bijvoorbeeld een beveiligingsregel voor uitgaand verkeer opgeeft voor elk adres via poort 80, hoeft u geen beveiligingsregel voor binnenkomend verkeer op te geven voor de reacties op het uitgaande verkeer. U hoeft alleen een beveiligingsregel voor binnenkomend verkeer op te geven als de communicatie extern is gestart. Het omgekeerde geldt ook. Als binnenkomend verkeer via een poort is toegestaan, is het niet nodig om een beveiligingsregel voor uitgaand verkeer op te geven om te reageren op verkeer via die poort.
Bestaande verbindingen kunnen niet worden onderbroken wanneer u een beveiligingsregel verwijdert die de stroom mogelijk heeft gemaakt. Verkeersstromen worden onderbroken wanneer er verbindingen worden gestopt en er gedurende minstens een paar minuten in beide richtingen geen verkeer stroomt.

Er gelden beperkingen voor het aantal beveiligingsregels dat u in een netwerkbeveiligingsgroep kunt maken. Zie [Netwerkenlimieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.

### <a name="default-security-rules"></a>Standaardbeveiligingsregels

Azure maakt de volgende standaardregels in elke netwerkbeveiligingsgroep die u maakt:

#### <a name="inbound"></a>Inkomend

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioriteit|Bron|Bronpoorten|Doel|Doelpoorten|Protocol|Toegang|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Alle|Toestaan|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioriteit|Bron|Bronpoorten|Doel|Doelpoorten|Protocol|Toegang|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Alle|Toestaan|

##### <a name="denyallinbound"></a>DenyAllInbound

|Prioriteit|Bron|Bronpoorten|Doel|Doelpoorten|Protocol|Toegang|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Alle|Weigeren|

#### <a name="outbound"></a>Uitgaand

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Toegang |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Alle | Toestaan |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Toegang |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Alle | Toestaan |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Toegang |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Alle | Weigeren |

In de kolommen **Bron** en **Doel** zijn *VirtualNetwork*, *AzureLoadBalancer* en *Internet*[servicetags](service-tags-overview.md) in plaats van IP-adressen. In de protocolkolom omvat **Any** TCP, UDP en ICMP. Bij het maken van een regel u TCP, UDP, ICMP of Any opgeven. *0.0.0.0/0* in de kolommen **Bron** en **Doel** vertegenwoordigt alle adressen. Clients zoals Azure portal, Azure CLI of PowerShell kunnen * of een andere voor deze expressie gebruiken.
 
U kunt de standaardregels niet verwijderen, maar u kunt ze negeren door regels te maken met een hogere prioriteit.

### <a name="augmented-security-rules"></a>Uitgebreide beveiligingsregels

Uitgebreide beveiligingsregels vereenvoudigen de beveiligingsdefinitie voor virtuele netwerken, zodat u een uitgebreider en complexer netwerkbeveiligingsbeleid kunt definiëren met minder regels. U kunt meerdere poorten en meerdere expliciete IP-adressen en -bereiken combineren tot één gemakkelijk te begrijpen beveiligingsregel. U kunt uitgebreide regels gebruiken in de velden voor bron, doel en poort van een regel. Om het onderhoud van de definitie van uw beveiligingsregel te vereenvoudigen, kunt u uitgebreide beveiligingsregels combineren met [servicetags](service-tags-overview.md) of [toepassingsbeveiligingsgroepen](#application-security-groups). Er zijn beperkingen voor het aantal adressen, bereiken en poorten dat u in een regel opgeven. Zie [Netwerkenlimieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.

#### <a name="service-tags"></a>Servicetags

Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels van een bepaalde Azure-service. Het helpt om de complexiteit van frequente updates op de regels voor netwerkbeveiliging te minimaliseren.

Zie [Azure-servicetags](service-tags-overview.md)voor meer informatie . Zie [Netwerktoegang beperken tot PaaS-bronnen](tutorial-restrict-network-access-to-resources.md)voor een voorbeeld over het gebruik van de tag Opslagservice om de netwerktoegang te beperken.

#### <a name="application-security-groups"></a>Toepassingsbeveiligingsgroepen

Met behulp van toepassingsbeveiligingsgroepen kunt u netwerkbeveiliging configureren als een natuurlijk verlengstuk van de structuur van een toepassing, waarbij u virtuele machines kunt groeperen en netwerkbeveiligingsbeleid kunt definiëren op basis van die groepen. U kunt het beveiligingsbeleid op grote schaal opnieuw gebruiken zonder handmatig onderhoud van expliciete IP-adressen. Zie [Toepassingsbeveiligingsgroepen](application-security-groups.md)voor meer informatie.

## <a name="how-traffic-is-evaluated"></a>Verkeer evalueren

U kunt resources van diverse Azure-services in een virtueel Azure-netwerk implementeren. Zie [Services die kunnen worden geïmplementeerd in een virtueel netwerk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) voor de volledige lijst. U kunt nul of één netwerkbeveiligingsgroep aan elk [subnet](virtual-network-manage-subnet.md#change-subnet-settings) van een virtueel netwerk en [netwerkinterface](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) in een virtuele machine koppelen. Dezelfde netwerkbeveiligingsgroep kan aan zoveel subnetten en netwerkinterfaces worden gekoppeld als u wilt.

In de volgende afbeelding ziet u verschillende scenario's voor het implementeren van netwerkbeveiligingsgroepen om netwerkverkeer naar en van internet via TCP-poort 80 toe te staan:

![Verwerken van NSG's](./media/security-groups/nsg-interaction.png)

Raadpleeg de afbeelding hierboven in combinatie met de volgende tekst als u wilt weten hoe binnenkomende en uitgaande regels voor netwerkbeveiligingsgroepen worden verwerkt:

### <a name="inbound-traffic"></a>Binnenkomend verkeer

Voor binnenkomend verkeer worden eerst de regels verwerkt in een netwerkbeveiligingsgroep die aan een subnet is gekoppeld, indien aanwezig. Vervolgens worden de regels verwerkt die zijn gekoppeld aan een netwerkinterface, indien aanwezig.

- **VM1**: de beveiligingsregels in *NSG1* worden verwerkt, omdat deze is gekoppeld aan *Subnet1* en *VM1* zich in *Subnet1* bevindt. Tenzij u een regel hebt gemaakt waardoor poort 80 binnenkomend verkeer toestaat, wordt het verkeer geweigerd door de standaardbeveiligingsregel [DenyAllInbound](#denyallinbound) en nooit geëvalueerd door *NSG2*, omdat *NSG2* is gekoppeld aan de netwerkinterface. Als *NSG1* een beveiligingsregel bevat die poort 80 toestaat, wordt het verkeer door *NSG2* verwerkt. Als u poort 80 voor de virtuele machine wilt toestaan, moet zowel *NSG1* als *NSG2* een regel bevatten die verkeer vanaf internet via poort 80 toestaat.
- **VM2**: de regels in *NSG1* worden verwerkt omdat *VM2* zich ook in *Subnet1* bevindt. Omdat *VM2* geen netwerkinterface bevat die aan de bijbehorende netwerkinterface is gekoppeld, wordt hier alle verkeer ontvangen dat is toegestaan via *NSG1* of wordt alle verkeer geweigerd dat is geweigerd door *NSG1*. Verkeer wordt voor alle resources in hetzelfde subnet toegestaan of geweigerd als een netwerkbeveiligingsgroep aan een subnet is gekoppeld.
- **VM3**: omdat er geen netwerkbeveiligingsgroep is gekoppeld aan *Subnet2*, wordt verkeer naar het subnet toegestaan en verwerkt door *NSG2*, omdat *NSG2* is gekoppeld aan de netwerkinterface die met *VM3* is verbonden.
- **VM4**: verkeer naar *VM4* is toegestaan omdat er geen netwerkbeveiligingsgroep is gekoppeld aan *Subnet3* of de netwerkinterface in de virtuele machine. Alle netwerkverkeer wordt toegestaan via een subnet en netwerkinterface als er geen netwerkbeveiligingsgroep aan is gekoppeld.

### <a name="outbound-traffic"></a>Uitgaand verkeer

Voor uitgaand verkeer worden eerst de regels verwerkt in een netwerkbeveiligingsgroep die aan een netwerkinterface is gekoppeld, indien aanwezig. Vervolgens worden de regels in een netwerkbeveiligingsgroep verwerkt die zijn gekoppeld aan het subnet, indien aanwezig.

- **VM1**: de beveiligingsregels in *NSG2* worden verwerkt. Tenzij u een beveiligingsregel maakt die uitgaand verkeer naar internet via poort 80 weigert, wordt in zowel *NSG1* als *NSG2* verkeer toegestaan door de standaardbeveiligingsregel [AllowInternetOutbound](#allowinternetoutbound). Als *NSG2* een beveiligingsregel bevat die poort 80 weigert, wordt het verkeer geweigerd en nooit door *NSG1* geëvalueerd. Als u binnenkomend verkeer van de virtuele machine via poort 80 wilt weigeren, moet een van de netwerkbeveiligingsgroepen, of beide, een regel bevatten die uitgaand verkeer naar internet via poort 80 weigert.
- **VM2**: al het verkeer wordt via de netwerkinterface naar het subnet verzonden, omdat aan de netwerkinterface die met *VM2* is verbonden, geen netwerkbeveiligingsgroep is gekoppeld. De regels in *NSG1* worden verwerkt.
- **VM3**: als *NSG2* een beveiligingsregel bevat die poort 80 weigert, wordt het verkeer geweigerd. Als *NSG2* een beveiligingsregel bevat die poort 80 toestaat, wordt via poort 80 uitgaand verkeer naar internet toegestaan, omdat er geen netwerkbeveiligingsgroep aan *Subnet2* is gekoppeld.
- **VM4**: al het netwerkverkeer naar *VM4* is toegestaan omdat er geen netwerkbeveiligingsgroep is gekoppeld aan de netwerkinterface die met de virtuele machine is verbonden, of aan *Subnet3*.


### <a name="intra-subnet-traffic"></a>Intra-Subnetverkeer

Het is belangrijk op te merken dat beveiligingsregels in een NSG die zijn gekoppeld aan een subnet de connectiviteit tussen VM's erin kunnen beïnvloeden. Als er bijvoorbeeld een regel wordt toegevoegd aan *NSG1* die al het inkomende en uitgaande verkeer ontkent, kunnen *VM1* en *VM2* niet meer met elkaar communiceren. Er zou een andere regel moeten worden toegevoegd om dit mogelijk te maken. 



U kunt eenvoudig de cumulatieve regels bekijken die op een netwerkinterface worden toegepast door de [effectieve beveiligingsregels](virtual-network-network-interface.md#view-effective-security-rules) voor een netwerkinterface te bekijken. U kunt ook de functie [IP-stroom controleren](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) in Azure Network Watcher gebruiken om te bepalen of de communicatie van of naar een netwerkinterface is toegestaan. IP-stroom controleren geeft aan of communicatie is toegestaan en welke netwerkbeveiligingsregel verkeer toestaat of weigert.

> [!NOTE]
> Netwerkbeveiligingsgroepen zijn gekoppeld aan subnetten of aan virtuele machines en cloudservices die zijn geïmplementeerd in het klassieke implementatiemodel en aan subnetten of netwerkinterfaces in het implementatiemodel voor Resource Manager. Zie [Over Azure-implementatiemodellen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over Azure-implementatiemodellen.

> [!TIP]
> Het wordt aangeraden een netwerkbeveiligingsgroep aan hetzij een subnet, hetzij een netwerkinterface te koppelen, tenzij u een bepaalde reden hebt om ze aan beide te koppelen. Aangezien regels in een netwerkbeveiligingsgroep die aan een subnet is gekoppeld, kunnen conflicteren met regels in een netwerkbeveiligingsgroep die aan een netwerkinterface is gekoppeld, kunnen er onverwachte communicatieproblemen optreden waarvoor een probleemoplossing dient te worden uitgevoerd.

## <a name="azure-platform-considerations"></a>Overwegingen bij het Azure-platform

- **Virtueel IP van het hostknooppunt**: Basisinfrastructuurservices zoals DHCP, DNS, IMDS en statusbewaking worden geleverd via de gevirtualiseerde host-IP-adressen 168.63.129.16 en 169.254.169.254. Deze IP-adressen zijn van Microsoft en zijn de enige gevirtualiseerde IP-adressen die in alle regio's voor dit doel worden gebruikt. Effectieve beveiligingsregels en effectieve routes zullen deze platformregels niet bevatten. Als u deze basisinfrastructuurcommunicatie wilt overschrijven, u een beveiligingsregel maken om verkeer te weigeren met behulp van de volgende [servicetags](service-tags-overview.md) op de regels van uw netwerkbeveiligingsgroep: AzurePlatformDNS, AzurePlatformIMDS, AzurePlatformLKM. Meer informatie over het [diagnosticeren van netwerkverkeerfiltering](diagnose-network-traffic-filter-problem.md) en [het diagnosticeren van netwerkroutering.](diagnose-network-routing-problem.md)
- **Licentieverlening (Key Management Service):** voor alle Windows installatiekopieën die op virtuele machines worden uitgevoerd, is een licentie vereist. Hiervoor wordt een licentieaanvraag verstuurd naar de Key Management Service-hostservers waarop dergelijke query's worden afgehandeld. De uitgaande aanvraag wordt gedaan via poort. 1688. Voor implementaties die gebruikmaken van een configuratie met de [standaardroute 0.0.0.0/0](virtual-networks-udr-overview.md#default-route), wordt deze platformregel uitgeschakeld.
- **Virtuele machines in groepen met gelijke taakverdeling**: de bronpoort en het bronadresbereik die worden toegepast, zijn die van de oorspronkelijke computer, niet van de load balancer. De doelpoort en het doeladresbereik zijn die van de doelcomputer, niet van de load balancer.
- **Azure-service-exemplaren**: exemplaren van verschillende Azure-services, zoals HDInsight, toepassingsserviceomgevingen en virtuele-machineschaalsets, worden geïmplementeerd in virtuele netwerksubnetten. Zie [Virtueel netwerk voor Azure-services](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) voor een volledige lijst met services die u in virtuele netwerken kunt implementeren. Zorg ervoor dat u vertrouwd raakt met de poortvereisten voor elke service voordat u een netwerkbeveiligingsgroep toepast op het subnet waarin de resource is geïmplementeerd. Als u poorten weigert die vereist zijn voor de service, werkt de service niet naar behoren.
- **Uitgaande e-mail verzenden:** Microsoft raadt u aan geverifieerde SMTP-relayservices te gebruiken (meestal verbonden via TCP-poort 587, maar vaak ook anderen) om e-mail van Azure Virtual Machines te verzenden. SMTP-relayservices leggen zich toe op de reputatie van de afzender, om zo de kans dat e-mailproviders van derden berichten weigeren, tot het uiterste terug te dringen. Dergelijke SMTP-relayservices omvatten, maar beperken zich niet tot Exchange Online Protection en SendGrid. Het gebruik van de SMTP-relayservices wordt in Azure op geen enkele wijze beperkt, ongeacht welk type abonnement u hebt. 

  Als u uw Azure-abonnement vóór 15 november 2017 hebt gemaakt, kunt u naast de mogelijkheid om gebruik te maken van SMTP-relayservices ook rechtstreeks e-mail verzenden via TCP-poort 25. Als u uw abonnement na 15 november 2017 hebt gemaakt kunt u mogelijk geen e-mail rechtstreeks via poort 25 verzenden. Hoe uitgaande communicatie via poort 25 verloopt, hangt als volgt samen met het type abonnement dat u hebt:

     - **Enterprise Overeenkomst**: communicatie via poort 25 is toegestaan. U kunt uitgaande e-mail rechtstreeks vanaf virtuele machines naar externe e-mailproviders verzenden, zonder dat daar beperkingen voor gelden op grond van het Azure-platform. 
     - **Betalen naar gebruik:** communicatie via uitgaande poort 25 is voor alle resources geblokkeerd. Als u e-mail vanaf een virtuele machine rechtstreeks naar de externe e-mailproviders wilt verzenden (niet via een geverifieerde SMTP-relay), kunt u een aanvraag indienen om de beperking op te heffen. Het is aan Microsoft om te bepalen of aanvragen worden gecontroleerd en goedgekeurd, en aanvragen worden alleen toegekend nadat er controles ter voorkoming van fraude zijn uitgevoerd. Als u een aanvraag wilt indienen, opent u een ondersteuningsaanvraag met het probleemtype *Technisch*, *Virtuele netwerkverbinding*, *Kan geen e-mail verzenden (SMTP/poort 25)*. Vermeld in uw ondersteuningsaanvraag informatie zoals waarom uw abonnement e-mail rechtstreeks naar e-mailproviders moet kunnen verzenden in plaats van via een geverifieerde SMTP-relay. Als de aanvraag voor uw abonnement wordt toegekend, kunnen alleen de virtuele machines die na de toekenningsdatum zijn gemaakt poort 25 voor uitgaande communicatie gebruiken.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark en gratis proefabonnementen**: uitgaande communicatie via poort 25 voor alle resources geblokkeerd. Er kunnen geen aanvragen worden ingediend voor het opheffen van de beperking, omdat zulke aanvragen niet worden toegekend. Als u e-mail moet verzenden vanaf uw virtuele machine, dient u een SMTP-relayservice te gebruiken.
     - **Cloudserviceprovider**: klanten die Azure-resources via een cloudserviceprovider gebruiken, kunnen een ondersteuningsaanvraag maken bij hun cloudserviceprovider. Zij kunnen vervolgens aanvragen dat de provider namens hen een blokkering opheft, als er geen beveiligde SMTP-relay kan worden gebruikt.

  Als u in Azure e-mail kunt verzenden via poort 25, garandeert Microsoft niet dat e-mailproviders inkomende e-mail vanaf uw virtuele machine accepteren. Als een specifieke provider e-mail van uw virtuele machine weigert, dient u samen met de provider alle eventuele problemen met de aflevering van berichten of met de spamfilter op te lossen, of u moet een geverifieerde SMTP-relayservice gebruiken.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Een netwerkbeveiligingsgroep maken](tutorial-filter-network-traffic.md).

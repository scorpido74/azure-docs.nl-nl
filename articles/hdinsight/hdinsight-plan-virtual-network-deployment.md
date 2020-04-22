---
title: Een virtueel netwerk plannen voor Azure HDInsight
description: Meer informatie over het plannen van een Azure Virtual Network-implementatie om HDInsight te verbinden met andere cloudbronnen of resources in uw datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/21/2020
ms.openlocfilehash: 18774ae4a98b795846459251174ee47671aef39c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769887"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Een virtueel netwerk plannen voor Azure HDInsight

In dit artikel vindt u achtergrondinformatie over het gebruik van [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) (VNets) met Azure HDInsight. Het bespreekt ook ontwerp- en implementatiebeslissingen die moeten worden genomen voordat u een virtueel netwerk voor uw HDInsight-cluster implementeren. Zodra de planningsfase is voltooid, u doorgaan [met Virtuele netwerken maken voor Azure HDInsight-clusters.](hdinsight-create-virtual-network.md) Zie IP-adressen van [HDInsight-beheer](hdinsight-management-ip-addresses.md)voor meer informatie over IP-adressen van HDInsight-beheer die nodig zijn om netwerkbeveiligingsgroepen (NSG's) en door de gebruiker gedefinieerde routes correct te configureren.

Als u een Azure Virtual Network gebruikt, worden de volgende scenario's inde volgende scenario's mogelijk:

* Rechtstreeks verbinding maken met HDInsight vanuit een on-premises netwerk.
* HDInsight verbinden met gegevensopslag in een Azure Virtual-netwerk.
* Rechtstreeks toegang tot Apache Hadoop-services die niet openbaar beschikbaar zijn via het internet. Bijvoorbeeld Apache Kafka API's of de Apache HBase Java API.

> [!IMPORTANT]
> Als u een HDInsight-cluster maakt in een VNET, worden verschillende netwerkbronnen gemaakt, zoals NIC's en load balancers. Verwijder deze netwerkbronnen **niet,** omdat ze nodig zijn om uw cluster correct te laten functioneren met het VNET.
>
> Na 28 februari 2019 worden de netwerkbronnen (zoals NIC's, LB's, enz.) voor NIEUWE HDInsight-clusters die in een VNET zijn gemaakt, ondergebracht in dezelfde HDInsight-clusterbrongroep. Voorheen waren deze resources ingericht in de VNET-resourcegroep. Er is geen wijziging in de huidige lopende clusters en die clusters gemaakt zonder een VNET.

## <a name="planning"></a>Planning

Hieronder volgen de vragen die u moet beantwoorden bij het installeren van HDInsight in een virtueel netwerk:

* Moet u HDInsight installeren in een bestaand virtueel netwerk? Of bent u het creëren van een nieuw netwerk?

    Als u een bestaand virtueel netwerk gebruikt, moet u mogelijk de netwerkconfiguratie wijzigen voordat u HDInsight installeren. Zie voor meer informatie het [toevoegen van HDInsight aan een bestaande virtuele netwerksectie.](#existingvnet)

* Wilt u het virtuele netwerk met HDInsight verbinden met een ander virtueel netwerk of uw on-premises netwerk?

    Als u eenvoudig wilt werken met bronnen in verschillende netwerken, moet u mogelijk een aangepaste DNS maken en DNS-forwarding configureren. Zie de sectie [Meerdere netwerken verbinden](#multinet) voor meer informatie.

* Wilt u binnenkomend of uitgaand verkeer beperken/omleiden naar HDInsight?

    HDInsight moet onbeperkte communicatie hebben met specifieke IP-adressen in het Azure-datacenter. Er zijn ook verschillende poorten die moeten worden toegestaan via firewalls voor clientcommunicatie. Zie voor meer informatie de sectie [netwerkverkeer beheren.](#networktraffic)

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>HDInsight toevoegen aan een bestaand virtueel netwerk

Gebruik de stappen in deze sectie om te ontdekken hoe u een nieuw HDInsight toevoegt aan een bestaand Azure Virtual Network.

> [!NOTE]  
> U een bestaand HDInsight-cluster niet toevoegen aan een virtueel netwerk.

1. Gebruikt u een klassiek of Resource Manager implementatiemodel voor het virtuele netwerk?

    HDInsight 3.4 en meer vereist een virtueel netwerk van Resource Manager. Eerdere versies van HDInsight vereisten een klassiek virtueel netwerk.

    Als uw bestaande netwerk een klassiek virtueel netwerk is, moet u een virtueel netwerk van Resource Manager maken en vervolgens de twee verbinden. [Klassieke VNets verbinden met nieuwe VNets.](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)

    Nadat HDInsight is aangesloten in het Resource Manager-netwerk, kan het communiceren met bronnen in het klassieke netwerk.

2. Gebruikt u netwerkbeveiligingsgroepen, door de gebruiker gedefinieerde routes of virtuele netwerkapparaten om verkeer in of uit het virtuele netwerk te beperken?

    Als beheerde service vereist HDInsight onbeperkte toegang tot verschillende IP-adressen in het Azure-datacenter. Als u communicatie met deze IP-adressen wilt toestaan, werkt u bestaande netwerkbeveiligingsgroepen of door de gebruiker gedefinieerde routes bij.

    HDInsight biedt meerdere services, die verschillende poorten gebruiken. Blokkeer het verkeer naar deze poorten niet. Zie de sectie Beveiliging voor een lijst met poorten die u via firewalls voor virtuele apparaten toestaan.

    Als u uw bestaande beveiligingsconfiguratie wilt vinden, gebruikt u de volgende Azure PowerShell- of Azure CLI-opdrachten:

    * Netwerkbeveiligingsgroepen

        Vervang `RESOURCEGROUP` de naam van de resourcegroep die het virtuele netwerk bevat en voer de opdracht in:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Zie Het document [Netwerkbeveiligingsgroepen oplossen](../virtual-network/diagnose-network-traffic-filter-problem.md) voor meer informatie.

        > [!IMPORTANT]  
        > Netwerkbeveiligingsgroepregels worden in volgorde toegepast op basis van regelprioriteit. De eerste regel die overeenkomt met het verkeerspatroon wordt toegepast en er worden geen andere regels toegepast voor dat verkeer. Bestel regels van meest tolerant tot minst tolerant. Zie [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/security-overview.md) voor meer informatie.

    * Door de gebruiker gedefinieerde routes

        Vervang `RESOURCEGROUP` de naam van de resourcegroep die het virtuele netwerk bevat en voer de opdracht in:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Zie het document [Routes oplossen](../virtual-network/diagnose-network-routing-problem.md) voor meer informatie.

3. Maak een HDInsight-cluster en selecteer het Azure Virtual Network tijdens de configuratie. Gebruik de stappen in de volgende documenten om inzicht te krijgen in het proces voor het maken van het cluster:

    * [HDInsight maken met Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [HDInsight maken met Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [HDInsight maken met Azure Classic CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [HDInsight maken met een Azure Resource Manager-sjabloon](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Het toevoegen van HDInsight aan een virtueel netwerk is een optionele configuratiestap. Zorg ervoor dat u het virtuele netwerk selecteert bij het configureren van het cluster.

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>Meerdere netwerken verbinden

De grootste uitdaging met een multi-netwerk configuratie is naamresolutie tussen de netwerken.

Azure biedt naamsresolutie voor Azure-services die zijn geïnstalleerd in een virtueel netwerk. Met deze ingebouwde naamresolutie kan HDInsight verbinding maken met de volgende bronnen met behulp van een volledig gekwalificeerde domeinnaam (FQDN):

* Elke bron die beschikbaar is op het internet. Bijvoorbeeld microsoft.com, windowsupdate.com.

* Elke bron die zich in hetzelfde Azure Virtual Network bevindt, met behulp van de __interne DNS-naam__ van de bron. Wanneer u bijvoorbeeld de standaardnaamomzetting gebruikt, worden de volgende voorbeelden van interne DNS-namen die zijn toegewezen aan hdInsight-werknemersknooppunten:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Beide knooppunten kunnen rechtstreeks met elkaar communiceren en andere knooppunten in HDInsight, met behulp van interne DNS-namen.

Met de standaardnaamresolutie kan HDInsight de namen van bronnen in netwerken die zijn verbonden met het virtuele netwerk __niet__ oplossen. Het is bijvoorbeeld gebruikelijk om uw on-premises netwerk te aansluiten bij het virtuele netwerk. Met alleen de standaardnaamresolutie heeft HDInsight geen toegang tot bronnen in het on-premises netwerk op naam. Het tegenovergestelde is ook waar, resources in uw on-premises netwerk hebben geen toegang tot bronnen in het virtuele netwerk op naam.

> [!WARNING]  
> U moet de aangepaste DNS-server maken en het virtuele netwerk configureren om het te gebruiken voordat u het HDInsight-cluster maakt.

Als u naamoplossing tussen het virtuele netwerk en de bronnen in samengevoegde netwerken wilt inschakelen, moet u de volgende acties uitvoeren:

1. Maak een aangepaste DNS-server in het Azure Virtual Network waar u HDInsight wilt installeren.

2. Configureer het virtuele netwerk om de aangepaste DNS-server te gebruiken.

3. Zoek het met Azure toegewezen DNS-achtervoegsel voor uw virtuele netwerk. Deze waarde is `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`vergelijkbaar met . Zie de sectie [Voorbeeld: Aangepaste DNS](hdinsight-create-virtual-network.md#example-dns) voor informatie over het vinden van het DNS-achtervoegsel.

4. Doorsturen tussen de DNS-servers configureren. De configuratie is afhankelijk van het type extern netwerk.

   * Als het externe netwerk een on-premises netwerk is, configureert u DNS als volgt:

     * __Aangepaste DNS__ (in het virtuele netwerk):

         * Verzoeken voor het DNS-achtervoegsel van het virtuele netwerk doorsturen naar de azure recursieve resolver (168.63.129.16). Azure verwerkt aanvragen voor resources in het virtuele netwerk

         * Alle andere aanvragen doorsturen naar de on-premises DNS-server. De on-premises DNS verwerkt alle andere aanvragen voor naamomzetting, zelfs verzoeken om internetbronnen zoals Microsoft.com.

     * __On-premises DNS:__ Doorsturen van aanvragen voor het DNS-achtervoegsel van het virtuele netwerk naar de aangepaste DNS-server. De aangepaste DNS-server stuurt vervolgens door naar de Azure recursieve resolver.

       Met deze configuratie worden aanvragen voor volledig gekwalificeerde domeinnamen die het DNS-achtervoegsel van het virtuele netwerk bevatten, doorgestoend naar de aangepaste DNS-server. Alle andere verzoeken (zelfs voor openbare internetadressen) worden behandeld door de on-premises DNS-server.

   * Als het externe netwerk een ander Azure Virtual Network is, configureert u DNS als volgt:

     * __Aangepaste DNS__ (in elk virtueel netwerk):

         * Aanvragen voor het DNS-achtervoegsel van de virtuele netwerken worden doorgestuurd naar de aangepaste DNS-servers. De DNS in elk virtueel netwerk is verantwoordelijk voor het oplossen van bronnen binnen het netwerk.

         * Alle andere aanvragen doorsturen naar de Azure-recursieve resolver. De recursieve resolver is verantwoordelijk voor het oplossen van lokale en internetbronnen.

       De DNS-server voor elk netwerk stuurt aanvragen door naar het andere, op basis van DNS-achtervoegsel. Andere aanvragen worden opgelost met behulp van de Azure recursieve resolver.

     Zie de sectie [Voorbeeld: Aangepaste DNS](hdinsight-create-virtual-network.md#example-dns) voor een voorbeeld van elke configuratie.

Zie het document [Naamoplossing voor VM's en Rolinstanties voor](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) meer informatie.

## <a name="directly-connect-to-apache-hadoop-services"></a>Rechtstreeks verbinding maken met Apache Hadoop-services

U verbinding maken `https://CLUSTERNAME.azurehdinsight.net`met het cluster op. Dit adres maakt gebruik van een openbaar IP, dat mogelijk niet bereikbaar is als u NSG's hebt gebruikt om binnenkomend verkeer vanaf het internet te beperken. Bovendien u, wanneer u het cluster implementeert in een `https://CLUSTERNAME-int.azurehdinsight.net`VNet, toegang krijgen tot het cluster via het privéeindpunt. Dit eindpunt wordt opgelost naar een privé-IP in het VNet voor clustertoegang.

Als u verbinding wilt maken met Apache Ambari en andere webpagina's via het virtuele netwerk, gebruikt u de volgende stappen:

1. Als u de interne volledig gekwalificeerde domeinnamen (FQDN) van de HDInsight-clusterknooppunten wilt ontdekken, gebruikt u een van de volgende methoden:

    Vervang `RESOURCEGROUP` de naam van de resourcegroep die het virtuele netwerk bevat en voer de opdracht in:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Zoek in de lijst met geretourneerde knooppunten de FQDN voor de hoofdknooppunten en gebruik de FQDN's om verbinding te maken met Ambari en andere webservices. Bijvoorbeeld, gebruiken `http://<headnode-fqdn>:8080` om toegang te krijgen tot Ambari.

    > [!IMPORTANT]  
    > Sommige services die op de hoofdknooppunten worden gehost, zijn slechts op één knooppunt tegelijk actief. Als u probeert toegang te krijgen tot een service op het ene hoofdknooppunt en dit een fout van 404 retourneert, schakelt u over naar het andere hoofdknooppunt.

2. Zie de [poorten die worden gebruikt door Hadoop-services op hdinsight-document](./hdinsight-hadoop-port-settings-for-services.md) om te bepalen op welk knooppunt en de poort een service beschikbaar is.

## <a name="controlling-network-traffic"></a><a id="networktraffic"></a>Netwerkverkeer beheren

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>Technieken voor het regelen van inkomend en uitgaand verkeer naar HDInsight-clusters

Netwerkverkeer in een Azure Virtual Networks kan worden beheerd met de volgende methoden:

* **Met netwerkbeveiligingsgroepen** (NSG) u binnenkomend en uitgaand verkeer naar het netwerk filteren. Zie [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/security-overview.md) voor meer informatie.

* **Netwerk virtuele apparaten** (NVA) kan worden gebruikt met uitgaand verkeer alleen. NVA's repliceren de functionaliteit van apparaten zoals firewalls en routers. Zie het document [Netwerkapparaten](https://azure.microsoft.com/solutions/network-appliances) voor meer informatie.

Als managed service vereist HDInsight onbeperkte toegang tot de HDInsight health and management services, zowel voor inkomend als uitgaand verkeer van het VNET. Wanneer u NSG's gebruikt, moet u ervoor zorgen dat deze services nog steeds kunnen communiceren met het HDInsight-cluster.

![Diagram van HDInsight-entiteiten die zijn gemaakt in het aangepaste VNET voor Azure](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>HDInsight met netwerkbeveiligingsgroepen

Als u van plan bent **netwerkbeveiligingsgroepen** te gebruiken om het netwerkverkeer te beheren, voert u de volgende acties uit voordat u HDInsight installeert:

1. Identificeer het Azure-gebied dat u voor HDInsight wilt gebruiken.

2. Identificeer de servicetags die HDInsight voor uw regio vereist. Zie [NSG-servicetags (Network security group) voor Azure HDInsight voor](hdinsight-service-tags.md)meer informatie.

3. Maak of wijzig de netwerkbeveiligingsgroepen voor het subnet waarop u HDInsight wilt installeren.

    * __Netwerkbeveiligingsgroepen__: laat __binnenkomend__ verkeer op poort __443__ toe vanaf de IP-adressen. Dit zorgt ervoor dat HDInsight-beheerservices het cluster van buiten het virtuele netwerk kunnen bereiken.

Zie het [overzicht van netwerkbeveiligingsgroepen](../virtual-network/security-overview.md)voor meer informatie over netwerkbeveiligingsgroepen.

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Uitgaand verkeer van HDInsight-clusters beheren

Zie [Uitgaande netwerkverkeersbeperking configureren voor Azure HDInsight-clusters voor](hdinsight-restrict-outbound-traffic.md)meer informatie over het beheren van uitgaand verkeer vanuit HDInsight-clusters.

#### <a name="forced-tunneling-to-on-premises"></a>Gedwongen tunneling naar on-premises

Gedwongen tunneling is een door de gebruiker gedefinieerde routeringsconfiguratie waarbij al het verkeer van een subnet wordt gedwongen naar een specifiek netwerk of locatie, zoals uw on-premises netwerk. HDInsight ondersteunt __geen__ gedwongen tunneling van verkeer naar on-premises netwerken.

## <a name="required-ip-addresses"></a><a id="hdinsight-ip"></a>Vereiste IP-adressen

Zie [IP-adressen van HDInsight-beheer](hdinsight-management-ip-addresses.md)als u netwerkbeveiligingsgroepen of door de gebruiker gedefinieerde routes gebruikt om het verkeer te beheren.

## <a name="required-ports"></a><a id="hdinsight-ports"></a>Vereiste poorten

Als u van plan bent een **firewall** te gebruiken en toegang hebt tot het cluster van buitenaf op bepaalde poorten, moet u mogelijk verkeer toestaan op de poorten die nodig zijn voor uw scenario. Standaard is er geen speciale whitelisting van poorten nodig zolang het Azure-beheerverkeer dat in de vorige sectie is uitgelegd, het cluster op poort 443 mag bereiken.

Zie de poorten die [worden gebruikt door Apache Hadoop-services op HDInsight-document](hdinsight-hadoop-port-settings-for-services.md) voor een lijst met poorten voor specifieke services.

Zie het [scenariodocument voor virtuele toestellen](../virtual-network/virtual-network-scenario-udr-gw-nva.md) voor meer informatie over firewallregels voor virtuele apparaten.

## <a name="load-balancing"></a>Taakverdeling

Wanneer u een HDInsight-cluster maakt, wordt er ook een load balancer gemaakt. Het type van deze load balancer bevindt zich op het [basisSKU-niveau,](../load-balancer/concepts-limitations.md#skus)dat bepaalde beperkingen heeft. Een van deze beperkingen is dat als u twee virtuele netwerken in verschillende regio's hebt, u geen verbinding maken met basislastsbalansen. Zie [veelgestelde vragen over virtuele netwerken: beperkingen op wereldwijde vnet-peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers), voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Virtuele netwerken maken voor Azure HDInsight-clusters voor](hdinsight-create-virtual-network.md)codevoorbeelden en voorbeelden van het maken van Azure Virtual Networks.
* Zie HDInsight verbinden met [een on-premises netwerk](./connect-on-premises-network.md)voor een end-to-end voorbeeld van het configureren van HDInsight om verbinding te maken met een on-premises netwerk.
* Zie het overzicht van het [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)voor meer informatie over virtuele Azure-netwerken.
* Zie [Netwerkbeveiligingsgroepen](../virtual-network/security-overview.md)voor meer informatie over netwerkbeveiligingsgroepen.
* Zie [Door de gebruiker gedefinieerde routes en IP-forwarding](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie over door de gebruiker gedefinieerde routes.

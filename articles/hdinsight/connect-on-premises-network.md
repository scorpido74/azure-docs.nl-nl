---
title: Azure HDInsight verbinden met uw on-premises netwerk
description: Meer informatie over het maken van een HDInsight-cluster in een Azure Virtual Network en het vervolgens verbinden met uw on-premises netwerk. Meer informatie over het configureren van naamomzetting tussen HDInsight en uw on-premises netwerk met behulp van een aangepaste DNS-server.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 2ed7a5b9c81d1b50f80f379a88688b69c49ed382
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897927"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>HDInsight verbinden met uw on-premises netwerk

Ontdek hoe u HDInsight verbinden met uw on-premises netwerk met behulp van Azure Virtual Networks en een VPN-gateway. Dit document bevat planningsinformatie over:

* HDInsight gebruiken in een Azure Virtual Network dat verbinding maakt met uw on-premises netwerk.
* Dns-naamomzetting configureren tussen het virtuele netwerk en uw on-premises netwerk.
* Netwerkbeveiligingsgroepen configureren om de toegang tot HDInsight te beperken.
* Poorten geleverd door HDInsight op het virtuele netwerk.

## <a name="overview"></a>Overzicht

Als u HDInsight en resources in het samengevoegde netwerk op naam wilt laten communiceren, moet u de volgende acties uitvoeren:

1. Azure Virtual Network maken.
1. Maak een aangepaste DNS-server in het Azure Virtual Network.
1. Configureer het virtuele netwerk om de aangepaste DNS-server te gebruiken in plaats van de standaard Azure Recursieve Resolver.
1. Doorsturen configureren tussen de aangepaste DNS-server en uw on-premises DNS-server.

Met deze configuraties u het volgende gedrag vertonen:

* Aanvragen voor volledig gekwalificeerde domeinnamen met het DNS-achtervoegsel __voor het virtuele netwerk__ worden doorgestuurd naar de aangepaste DNS-server. De aangepaste DNS-server stuurt deze aanvragen vervolgens door naar de Azure Recursieve Resolver, die het IP-adres retourneert.
* Alle andere aanvragen worden doorgestuurd naar de on-premises DNS-server. Zelfs aanvragen voor openbare internetbronnen zoals microsoft.com worden doorgestuurd naar de on-premises DNS-server voor naamomzetting.

In het volgende diagram worden groene regels aanvragen voor resources die eindigen in het DNS-achtervoegsel van het virtuele netwerk. Blauwe lijnen zijn aanvragen voor resources in het on-premises netwerk of op het openbare internet.

![Diagram met de manier waarop DNS-verzoeken worden opgelost in de configuratie](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Vereisten

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Als u PowerShell gebruikt, hebt u de [AZ-module](https://docs.microsoft.com/powershell/azure/overview)nodig.
* Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)als u Azure CLI wilt gebruiken en u deze nog niet hebt geïnstalleerd.

## <a name="create-virtual-network-configuration"></a>Virtuele netwerkconfiguratie maken

Gebruik de volgende documenten voor meer informatie over het maken van een Azure Virtual Network dat is verbonden met uw on-premises netwerk:

* [Azure-portal gebruiken](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Azure PowerShell gebruiken](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Azure CLI gebruiken](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Aangepaste DNS-server maken

> [!IMPORTANT]  
> U moet de DNS-server maken en configureren voordat u HDInsight in het virtuele netwerk installeert.

Met deze stappen wordt de [Azure-portal](https://portal.azure.com) gebruikt om een Azure Virtual Machine te maken. Zie VM maken - Azure [CLI](../virtual-machines/linux/quick-create-cli.md) en VM [maken - Azure PowerShell voor](../virtual-machines/linux/quick-create-powershell.md)andere manieren om een virtuele machine te maken.  Als u een Linux-vm wilt maken die de [BIND](https://www.isc.org/downloads/bind/) DNS-software gebruikt, gebruikt u de volgende stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
  
1. Selecteer in het bovenste menu **+ Een resource maken.**

    ![Maak een Virtuele Ubuntu-machine](./media/connect-on-premises-network/azure-portal-create-resource.png)

1. Selecteer**Virtuele compute-machine** **Compute** > om naar de pagina **Een virtuele machine maken** te gaan.

1. Voer op het tabblad __Basisbeginselen__ de volgende gegevens in:  
  
    | Veld | Waarde |
    | --- | --- |
    |Abonnement |Selecteer het juiste abonnement.|
    |Resourcegroep |Selecteer de brongroep die het virtuele netwerk bevat dat eerder is gemaakt.|
    |Naam van de virtuele machine | Voer een vriendelijke naam in die deze virtuele machine identificeert. In dit voorbeeld wordt **DNSProxy gebruikt.**|
    |Regio | Selecteer hetzelfde gebied als het virtuele netwerk dat eerder is gemaakt.  Niet alle VM-formaten zijn beschikbaar in alle regio's.  |
    |Beschikbaarheidsopties |  Selecteer het gewenste beschikbaarheidsniveau.  Azure biedt een scala aan opties voor het beheren van beschikbaarheid en tolerantie voor uw toepassingen.  Ontwerp uw oplossing om gerepliceerde VM's te gebruiken in beschikbaarheidszones of beschikbaarheidssets om uw apps en gegevens te beschermen tegen uitval van datacenters en onderhoudsgebeurtenissen. In dit voorbeeld wordt **geen redundantie van de infrastructuur vereist.** |
    |Installatiekopie | Vertrek bij **Ubuntu Server 18.04 LTS**. |
    |Verificatietype | __Wachtwoord__ of __SSH-openbare sleutel__: de verificatiemethode voor het SSH-account. We raden u aan openbare sleutels te gebruiken, omdat ze veiliger zijn. In dit voorbeeld wordt **Wachtwoord**gebruikt .  Zie het document [SSH-toetsen maken en gebruiken voor Linux VM's voor](../virtual-machines/linux/mac-create-ssh-keys.md) meer informatie.|
    |Gebruikersnaam |Voer de gebruikersgebruikersnaam van de beheerder voor de VM in.  In dit voorbeeld wordt **sshuser gebruikt.**|
    |Wachtwoord of SSH-openbare sleutel | Het beschikbare veld wordt bepaald door uw keuze voor **verificatietype**.  Voer de juiste waarde in.|
    |Openbare poorten voor inkomend verkeer|Selecteer **Geselecteerde poorten toestaan**. Selecteer **vervolgens SSH (22)** in de vervolgkeuzelijst **Binnenkomende poorten selecteren.**|

    ![Basisconfiguratie virtuele machine](./media/connect-on-premises-network/virtual-machine-basics.png)

    Laat andere items achter op de standaardwaarden en selecteer vervolgens het tabblad **Netwerken.**

4. Voer op het tabblad **Netwerken** de volgende gegevens in:

    | Veld | Waarde |
    | --- | --- |
    |Virtueel netwerk | Selecteer het virtuele netwerk dat u eerder hebt gemaakt.|
    |Subnet | Selecteer het standaardsubnet voor het virtuele netwerk dat u eerder hebt gemaakt. Selecteer __niet__ het subnet dat door de VPN-gateway wordt gebruikt.|
    |Openbare IP | Gebruik de automatisch ingevulde waarde.  |

    ![HDInsight Virtual-netwerkinstellingen](./media/connect-on-premises-network/virtual-network-settings.png)

    Laat andere items achter op de standaardwaarden en selecteer vervolgens de **Beoordeling + maken**.

5. Selecteer op het tabblad **Controleren + maken** de optie **Maken** om de virtuele machine te maken.

### <a name="review-ip-addresses"></a>IP-adressen controleren

Zodra de virtuele machine is gemaakt, ontvangt u een **door implementatie geslaagde** melding met de knop **Ga naar bron.**  Selecteer **Ga naar resource om** naar uw nieuwe virtuele machine te gaan.  Voer in de standaardweergave voor uw nieuwe virtuele machine de volgende stappen uit om de bijbehorende IP-adressen te identificeren:

1. Selecteer **Eigenschappen**in **Instellingen**.

2. Let op de waarden voor **PUBLIC IP-ADRES/DNS-naamlabel** en **privé-IP-adres** voor later gebruik.

   ![Openbare en particuliere IP-adressen](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Binden (DNS-software) installeren en configureren

1. Gebruik SSH om verbinding te maken met het __openbare IP-adres__ van de virtuele machine. Vervang `sshuser` het SSH-gebruikersaccount dat u hebt opgegeven bij het maken van de VM. Het volgende voorbeeld maakt verbinding met een virtuele machine op 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Als u Binding wilt installeren, gebruikt u de volgende opdrachten uit de SSH-sessie:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Als u Binding wilt configureren om aanvragen voor naamomzetting door te sturen `/etc/bind/named.conf.options` naar uw dns-server op locatie, gebruikt u de volgende tekst als de inhoud van het bestand:

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]  
    > Vervang de waarden `goodclients` in de sectie door het IP-adresbereik van het virtuele netwerk en het on-premises netwerk. In deze sectie worden de adressen gedefinieerd waarvan deze DNS-server aanvragen accepteert.
    >
    > Vervang `192.168.0.1` de vermelding `forwarders` in de sectie door het IP-adres van uw on-premises DNS-server. Met deze vermelding worden DNS-verzoeken naar uw on-premises DNS-server verzonden voor oplossing.

    Als u dit bestand wilt bewerken, gebruikt u de volgende opdracht:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Als u het bestand wilt opslaan, gebruikt u __Ctrl+X__, __Y__en voert u __enter .__

4. Gebruik in de SSH-sessie de volgende opdracht:

    ```bash
    hostname -f
    ```

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst:

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    De `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` tekst is het __DNS-achtervoegsel__ voor dit virtuele netwerk. Sla deze waarde op, zoals deze later wordt gebruikt.

5. Als u Binding wilt configureren om DNS-namen op te lossen voor `/etc/bind/named.conf.local` bronnen in het virtuele netwerk, gebruikt u de volgende tekst als de inhoud van het bestand:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > U moet `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` het DNS-achtervoegsel vervangen dat u eerder hebt opgehaald.

    Als u dit bestand wilt bewerken, gebruikt u de volgende opdracht:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Als u het bestand wilt opslaan, gebruikt u __Ctrl+X__, __Y__en voert u __enter .__

6. Als u Binden wilt starten, gebruikt u de volgende opdracht:

    ```bash
    sudo service bind9 restart
    ```

7. Als u wilt controleren of binding de namen van resources in uw on-premises netwerk kan oplossen, gebruikt u de volgende opdrachten:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Vervang `dns.mynetwork.net` de volledig gekwalificeerde domeinnaam (FQDN) van een bron in uw on-premises netwerk.
    >
    > Vervang `10.0.0.4` door het __interne IP-adres__ van uw aangepaste DNS-server in het virtuele netwerk.

    Het antwoord lijkt op de volgende tekst:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Virtueel netwerk configureren om de aangepaste DNS-server te gebruiken

Als u het virtuele netwerk wilt configureren om de aangepaste DNS-server te gebruiken in plaats van de Azure-recursieve resolver, gebruikt u de volgende stappen van de [Azure-portal:](https://portal.azure.com)

1. Navigeer in het linkermenu naar Virtuele**Networking** > **netwerken**voor **alle services.** > 

2. Selecteer uw virtuele netwerk in de lijst, waarmee de standaardweergave voor uw virtuele netwerk wordt geopend.  

3. Selecteer **DNS-servers**in de standaardweergave onder **Instellingen**.  

4. Selecteer __Aangepast__en voer het **privé-IP-adres** van de aangepaste DNS-server in.

5. Selecteer __Opslaan__.  <br />  

    ![De aangepaste DNS-server voor het netwerk instellen](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>On-premises DNS-server configureren

In de vorige sectie hebt u de aangepaste DNS-server geconfigureerd om aanvragen door te sturen naar de on-premises DNS-server. Vervolgens moet u de on-premises DNS-server configureren om aanvragen door te sturen naar de aangepaste DNS-server.

Raadpleeg de documentatie voor uw DNS-server voor specifieke stappen over het configureren van uw DNS-server. Zoek naar de stappen voor het configureren van een __voorwaardelijke doorstuurserver__.

Een voorwaardelijke forward forward stuurt alleen aanvragen voor een specifiek DNS-achtervoegsel door. In dit geval moet u een doorstuurserver configureren voor het DNS-achtervoegsel van het virtuele netwerk. Aanvragen voor dit achtervoegsel moeten worden doorgestuurd naar het IP-adres van de aangepaste DNS-server. 

De volgende tekst is een voorbeeld van een configuratie van een voorwaardelijke expediteer voor de **BIND** DNS-software:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Zie de documentatie [Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) voor informatie over het gebruik van DNS op **Windows Server 2016...**

Zodra u de on-premises DNS-server hebt `nslookup` geconfigureerd, u vanaf het on-premises netwerk controleren of u namen in het virtuele netwerk oplossen. Het volgende voorbeeld 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

In dit voorbeeld wordt de on-premises DNS-server op 196.168.0.4 gebruikt om de naam van de aangepaste DNS-server op te lossen. Vervang het IP-adres door het IP-adres voor de on-premises DNS-server. Vervang `dnsproxy` het adres door de volledig gekwalificeerde domeinnaam van de aangepaste DNS-server.

## <a name="optional-control-network-traffic"></a>Optioneel: netwerkverkeer beheren

U netwerkbeveiligingsgroepen (NSG) of door de gebruiker gedefinieerde routes (UDR) gebruiken om het netwerkverkeer te beheren. Met NSG's u binnenkomend en uitgaand verkeer filteren en het verkeer toestaan of weigeren. Met UDR's u bepalen hoe verkeer stroomt tussen bronnen in het virtuele netwerk, het internet en het on-premises netwerk.

> [!WARNING]  
> HDInsight vereist inkomende toegang vanaf specifieke IP-adressen in de Azure-cloud en onbeperkte uitgaande toegang. Wanneer u NSG's of UDR's gebruikt om het verkeer te beheren, moet u de volgende stappen uitvoeren:

1. Zoek de IP-adressen voor de locatie die uw virtuele netwerk bevat. Zie [Vereiste IP-adressen](./hdinsight-management-ip-addresses.md)voor een lijst met vereiste IP-adressen op locatie.

2. Voor de IP-adressen die in stap 1 zijn geïdentificeerd, moet u binnenkomend verkeer van die IP-adressen toestaan.

   * Als u __NSG__gebruikt: __Inkomend__ verkeer toestaan op poort __443__ voor de IP-adressen.
   * Als u __UDR__gebruikt: stel het __volgende hoptype__ van de route naar __internet__ in voor de IP-adressen.

Zie het document [Extend HDInsight with Azure Virtual Networks](hdinsight-create-virtual-network.md#hdinsight-nsg) voor een voorbeeld van het gebruik van Azure PowerShell of azure CLI om NSG's te maken.

## <a name="create-the-hdinsight-cluster"></a>Het HDInsight-cluster maken

> [!WARNING]  
> U moet de aangepaste DNS-server configureren voordat u HDInsight in het virtuele netwerk installeert.

Gebruik de stappen in [het cluster Een HDInsight maken met het Azure-portaldocument](./hdinsight-hadoop-create-linux-clusters-portal.md) om een HDInsight-cluster te maken.

> [!WARNING]  
> * Tijdens het maken van het cluster moet u de locatie kiezen die uw virtuele netwerk bevat.
> * In het onderdeel __Geavanceerde instellingen__ van de configuratie moet u het virtuele netwerk en subnet selecteren dat u eerder hebt gemaakt.

## <a name="connecting-to-hdinsight"></a>Verbinding maken met HDInsight

De meeste documentatie over HDInsight gaat ervan uit dat u toegang hebt tot het cluster via het internet. Bijvoorbeeld, dat u verbinding met het cluster kunt maken op `https://CLUSTERNAME.azurehdinsight.net`. Dit adres maakt gebruik van de openbare gateway, die niet beschikbaar is als u NSG's of UDR's hebt gebruikt om de toegang vanaf het internet te beperken.

Sommige documentatie verwijst `headnodehost` ook naar een verbinding met het cluster vanuit een SSH-sessie. Dit adres is alleen beschikbaar vanaf knooppunten binnen een cluster en is niet bruikbaar op clients die via het virtuele netwerk zijn verbonden.

Als u rechtstreeks verbinding wilt maken met HDInsight via het virtuele netwerk, gebruikt u de volgende stappen:

1. Als u de interne volledig gekwalificeerde domeinnamen van de HDInsight-clusterknooppunten wilt ontdekken, gebruikt u een van de volgende methoden:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Zie de [poorten die worden gebruikt door Apache Hadoop-services op hdinsight-document](./hdinsight-hadoop-port-settings-for-services.md) om de poort te bepalen waarop een service beschikbaar is.

    > [!IMPORTANT]  
    > Sommige services die op de hoofdknooppunten worden gehost, zijn slechts op één knooppunt tegelijk actief. Als u probeert toegang te krijgen tot een service op het ene hoofdknooppunt en deze mislukt, schakelt u over naar het andere hoofdknooppunt.
    >
    > Apache Ambari is bijvoorbeeld slechts actief op één hoofdknooppunt tegelijk. Als u probeert toegang tot Ambari op een hoofd knooppunt en het geeft een 404 fout, dan is het draait op het andere hoofd knooppunt.

## <a name="next-steps"></a>Volgende stappen

* Zie [Een virtuele netwerkimplementatie plannen voor Azure HDInsight-clusters voor](./hdinsight-plan-virtual-network-deployment.md)meer informatie over het gebruik van HDInsight in een virtueel netwerk.

* Zie het overzicht van het [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)voor meer informatie over virtuele Azure-netwerken.

* Zie [Netwerkbeveiligingsgroepen](../virtual-network/security-overview.md)voor meer informatie over netwerkbeveiligingsgroepen.

* Zie [Door de gebruiker gedefinieerde routes en IP-forwarding](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie over door de gebruiker gedefinieerde routes.

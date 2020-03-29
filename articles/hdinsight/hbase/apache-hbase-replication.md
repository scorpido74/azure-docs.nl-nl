---
title: HBase-clusterreplicatie in virtuele netwerken - Azure HDInsight
description: Meer informatie over het instellen van HBase-replicatie van de ene HDInsight-versie naar de andere voor taakverdeling, hoge beschikbaarheid, migratie met nuldowntime en updates en herstel na noodgevallen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 1e6465584dd4e67f736b94d2939678c1a69163bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435670"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Apache HBase-clusterreplicatie instellen in virtuele Azure-netwerken

Meer informatie over het instellen van [Apache HBase-replicatie](https://hbase.apache.org/) binnen een virtueel netwerk of tussen twee virtuele netwerken in Azure.

Clusterreplicatie maakt gebruik van een bron-push-methodologie. Een HBase-cluster kan een bron of een bestemming zijn, of het kan beide rollen tegelijk vervullen. Replicatie is asynchroon. Het doel van replicatie is uiteindelijke consistentie. Wanneer de bron een bewerking ontvangt voor een kolomfamilie wanneer replicatie is ingeschakeld, wordt de bewerking doorgegeven aan alle doelclusters. Wanneer gegevens van het ene cluster naar het andere worden gerepliceerd, worden het broncluster en alle clusters die de gegevens al hebben verbruikt, bijgehouden om replicatielussen te voorkomen.

In dit artikel stelt u een brondoelreplicatie in. Zie de [naslaggids Apache HBase](https://hbase.apache.org/book.html#_cluster_replication)voor andere clustertopologieën.

Hieronder volgen HBase-replicatiegebruiksaanvragen voor één virtueel netwerk:

* Taakverdeling. U bijvoorbeeld scans of MapReduce-taken uitvoeren op het doelcluster en gegevens opnemen in het broncluster.
* Het toevoegen van hoge beschikbaarheid.
* Gegevens migreren van het ene HBase-cluster naar het andere.
* Een Azure HDInsight-cluster upgraden van de ene versie naar de andere.

Hieronder volgen HBase-replicatiegebruiksaanvragen voor twee virtuele netwerken:

* Het opzetten van noodherstel.
* Taakverdeling en partitionering van de toepassing.
* Het toevoegen van hoge beschikbaarheid.

U clusters repliceren met [scriptactiescripts](../hdinsight-hadoop-customize-cluster-linux.md) van [GitHub.](https://github.com/Azure/hbase-utils/tree/master/replication)

## <a name="prerequisites"></a>Vereisten
Voordat u met dit artikel begint, moet u een Azure-abonnement hebben. Zie [Een gratis proefversie van Azure bekijken.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

## <a name="set-up-the-environments"></a>De omgevingen instellen

Je hebt drie configuratieopties:

- Twee Apache HBase-clusters in één Virtueel Azure-netwerk.
- Twee Apache HBase-clusters in twee verschillende virtuele netwerken in dezelfde regio.
- Twee Apache HBase-clusters in twee verschillende virtuele netwerken in twee verschillende regio's (geo-replicatie).

Dit artikel behandelt het scenario voor georeplicatie.

Om u te helpen bij het instellen van de omgevingen, hebben we een aantal [Azure Resource Manager-sjablonen](../../azure-resource-manager/management/overview.md)gemaakt. Zie als u de omgevingen wilt instellen met behulp van andere methoden:

- [Apache Hadoop clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Apache HBase-clusters maken in Azure Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Twee virtuele netwerken instellen in twee verschillende regio's

Als u een sjabloon wilt gebruiken waarmee twee virtuele netwerken in twee verschillende regio's en de VPN-verbinding tussen de VNets worden gemaakt, selecteert u de volgende knop **Implementeren naar Azure.** De sjabloondefinitie wordt opgeslagen in een [openbare blobopslag.](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json)

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Enkele van de hardgecodeerde waarden in de sjabloon:

**VNet 1**

| Eigenschap | Waarde |
|----------|-------|
| Locatie | VS - west |
| VNet-naam | &lt;ClusterNamePrevix>-vnet1 |
| Adresruimtevoorvoegsel | 10.1.0.0/16 |
| Subnetnaam | subnet 1 |
| Subnetvoorvoegsel | 10.1.0.0/24 |
| Subnet (gateway) naam | GatewaySubnet (kan niet worden gewijzigd) |
| Subnet (gateway) voorvoegsel | 10.1.255.0/27 |
| Naam gateway | vnet1gw |
| Gatewaytype | VPN |
| Vpn-type gateway | RouteBased |
| Gateway SKU | Basic |
| Gateway-IP | vnet1gwip |

**VNet 2**

| Eigenschap | Waarde |
|----------|-------|
| Locatie | VS - oost |
| VNet-naam | &lt;ClusterNamePrevix>-vnet2 |
| Adresruimtevoorvoegsel | 10.2.0.0/16 |
| Subnetnaam | subnet 1 |
| Subnetvoorvoegsel | 10.2.0.0/24 |
| Subnet (gateway) naam | GatewaySubnet (kan niet worden gewijzigd) |
| Subnet (gateway) voorvoegsel | 10.2.255.0/27 |
| Naam gateway | vnet2gw |
| Gatewaytype | VPN |
| Vpn-type gateway | RouteBased |
| Gateway SKU | Basic |
| Gateway-IP | vnet1gwip |

## <a name="setup-dns"></a>DNS instellen

In het laatste gedeelte maakt de sjabloon een Virtuele Ubuntu-machine in elk van de twee virtuele netwerken.  In deze sectie installeert u Binding op de twee virtuele DNS-machines en configureert u vervolgens het DNS-doorsturen op de twee virtuele machines.

Om Bind te installeren, moet yon het openbare IP-adres van de twee virtuele DNS-machines vinden.

1. Open de [Azure Portal](https://portal.azure.com).
2. Open de virtuele DNS-machine door **resourcesgroepen > [naam van de resourcegroep] te selecteren > [vnet1DNS]**.  De naam van de resourcegroep is de naam die u in de laatste procedure maakt. De standaard DNS virtuele machine namen zijn *vnet1DNS* en *vnet2NDS*.
3. Selecteer **Eigenschappen** om de eigenschappenpagina van het virtuele netwerk te openen.
4. Noteer het **ip-adres van het publiek**en controleer ook het **privé-IP-adres**.  Het particuliere IP-adres is **10.1.0.4** voor vnet1DNS en **10.2.0.4** voor vnet2DNS.  
5. Wijzig de DNS-servers voor beide virtuele netwerken om standaard DNS-servers (Azure-Provided) te gebruiken om inkomende en uitgaande toegang te geven tot downloadpakketten om Bind in de volgende stappen te installeren.

Als u Binding wilt installeren, gebruikt u de volgende procedure:

1. Gebruik SSH om verbinding te maken met het __openbare IP-adres__ van de virtuele DNS-machine. Het volgende voorbeeld maakt verbinding met een virtuele machine op 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Vervang `sshuser` door het SSH-gebruikersaccount dat u hebt opgegeven bij het maken van de virtuele DNS-machine.

    > [!NOTE]  
    > Er zijn verschillende manieren om `ssh` het nut te verkrijgen. Op Linux, Unix en macOS wordt het geleverd als onderdeel van het besturingssysteem. Als u Windows gebruikt, u een van de volgende opties overwegen:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash op Ubuntu op Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Als u Binding wilt installeren, gebruikt u de volgende opdrachten uit de SSH-sessie:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Configureer Binding om aanvragen voor naamomzetting door te sturen naar uw on-premises DNS-server. Gebruik hiervoor de volgende tekst als de `/etc/bind/named.conf.options` inhoud van het bestand:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > Vervang de waarden `goodclients` in de sectie door het IP-adresbereik van de twee virtuele netwerken. In deze sectie worden de adressen gedefinieerd waarvan deze DNS-server aanvragen accepteert.

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

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    De `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` tekst is het __DNS-achtervoegsel__ voor dit virtuele netwerk. Sla deze waarde op, aangezien u die later nog nodig hebt.

    U moet ook het DNS-achtervoegsel van de andere DNS-server achterhalen. Je hebt het nodig in de volgende stap.

5. Als u Binding wilt configureren om DNS-namen op te lossen voor `/etc/bind/named.conf.local` bronnen in het virtuele netwerk, gebruikt u de volgende tekst als de inhoud van het bestand:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > U moet `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` het DNS-achtervoegsel van het andere virtuele netwerk vervangen. En de forwarder IP is het privé IP-adres van de DNS-server in het andere virtuele netwerk.

    Als u dit bestand wilt bewerken, gebruikt u de volgende opdracht:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Als u het bestand wilt opslaan, gebruikt u __Ctrl+X__, __Y__en voert u __enter .__

6. Als u Binden wilt starten, gebruikt u de volgende opdracht:

    ```bash
    sudo service bind9 restart
    ```

7. Als u wilt controleren of binding de namen van bronnen in het andere virtuele netwerk kan oplossen, gebruikt u de volgende opdrachten:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Vervang `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` door de volledig gekwalificeerde domeinnaam (FQDN) van de VIRTUELE DNS-machine in het andere netwerk.
    >
    > Vervang `10.2.0.4` door het __interne IP-adres__ van uw aangepaste DNS-server in het andere virtuele netwerk.

    Het antwoord lijkt op de volgende tekst:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Tot nu toe u het IP-adres van het andere netwerk niet opzoeken zonder opgegeven IP-adres van de DNS-server.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Het virtuele netwerk configureren om de aangepaste DNS-server te gebruiken

Als u het virtuele netwerk wilt configureren om de aangepaste DNS-server te gebruiken in plaats van de Azure-recursieve resolver, gebruikt u de volgende stappen:

1. Selecteer in de [Azure-portal](https://portal.azure.com)het virtuele netwerk en selecteer __DNS-servers__.

2. Selecteer __Aangepast__en voer het __interne IP-adres__ van de aangepaste DNS-server in. Selecteer tot slot __Opslaan__.

6. Open de virtuele dns-servermachine in vnet1 en klik op **Opnieuw starten**.  U moet alle virtuele machines in het virtuele netwerk opnieuw opstarten om de DNS-configuratie van kracht te maken.
7. Herhaalstappen configureren de aangepaste DNS-server voor vnet2.

Als u de DNS-configuratie wilt testen, u verbinding maken met de twee virtuele DNS-machines met SSH en de DNS-server van het andere virtuele netwerk pingen met behulp van de hostnaam. Als dit niet werkt, gebruikt u de volgende opdracht om de DNS-status te controleren:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Apache HBase-clusters maken

Maak een [Apache HBase-cluster](https://hbase.apache.org/) in elk van de twee virtuele netwerken met de volgende configuratie:

- **Naam van de brongroep:** gebruik dezelfde naam van de resourcegroep als u de virtuele netwerken hebt gemaakt.
- **Clustertype**: HBase
- **Versie**: HBase 1.1.2 (HDI 3.6)
- **Locatie:** Gebruik dezelfde locatie als het virtuele netwerk.  Standaard is vnet1 *West US*, en vnet2 is East *US*.
- **Opslag:** maak een nieuw opslagaccount voor het cluster.
- **Virtueel netwerk** (van Geavanceerde instellingen op de portal): Selecteer vnet1 die u in de laatste procedure hebt gemaakt.
- **Subnet**: De standaardnaam die in de sjabloon wordt gebruikt, is **subnet1**.

Om ervoor te zorgen dat de omgeving correct is geconfigureerd, moet u de FQDN van de headnode tussen de twee clusters kunnen pingen.

## <a name="load-test-data"></a>Testgegevens laden

Wanneer u een cluster nabootst, moet u de tabellen opgeven die u wilt repliceren. In deze sectie laadt u enkele gegevens in het broncluster. In de volgende sectie schakelt u replicatie tussen de twee clusters in.

Als u een **tabel Contactpersonen wilt** maken en wat gegevens in de tabel wilt invoegen, volgt u de instructies bij de Apache [HBase-zelfstudie: Aan de slag met Apache HBase in HDInsight](apache-hbase-tutorial-get-started-linux.md).

> [!NOTE]
> Als u tabellen wilt repliceren uit een aangepaste naamruimte, moet u ervoor zorgen dat ook de juiste aangepaste naamruimten op het doelcluster worden gedefinieerd.
>

## <a name="enable-replication"></a>Replicatie inschakelen

In de volgende stappen wordt beschreven hoe u het scriptactiescript aanroept vanuit de Azure-portal. Zie [HDInsight-clusters aanpassen met scriptactie](../hdinsight-hadoop-customize-cluster-linux.md)voor informatie over het uitvoeren van een scriptactie met Azure PowerShell en de Azure Classic CLI.

**HBase-replicatie inschakelen vanuit de Azure-portal**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Open het hbase-cluster van de bron.
3. Selecteer **Scriptacties**in het clustermenu .
4. Selecteer Boven aan de pagina **Nieuw verzenden**.
5. Selecteer of voer de volgende gegevens in:

   1. **Naam:** Voer **Replicatie inschakelen in**.
   2. **URL van script bash:** enter **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
   3. **Hoofd**: Zorg ervoor dat dit is geselecteerd. Maak de andere knooppunttypen vrij.
   4. **Parameters:** de volgende voorbeeldparameters maken replicatie voor alle bestaande tabellen mogelijk en kopiëren vervolgens alle gegevens van het broncluster naar het doelcluster:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
      > [!NOTE]
      > Gebruik hostname in plaats van FQDN voor zowel de DNS-naam van het bron- als het doelcluster.
      >
      > Deze walkthrough gaat uit van hn1 als actieve headnode. Controleer uw cluster om het actieve hoofdknooppunt te identificeren.

6. Selecteer **Maken**. Het script kan een tijdje duren om uit te voeren, vooral wanneer u het argument **-copydata** gebruikt.

Vereiste argumenten:

|Name|Beschrijving|
|----|-----------|
|-s, --src-cluster | Hiermee geeft u de DNS-naam op van het bron-HBase-cluster. Bijvoorbeeld: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Hiermee geeft u de DNS-naam op van het HBase-cluster (replica). Bijvoorbeeld: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Hiermee geeft u het beheerderswachtwoord voor Ambari op in het hbase-cluster van de bron. |
|-dp, --dst-ambari-password | Hiermee geeft u het beheerderswachtwoord voor Ambari op in het HBase-cluster van bestemming.|

Optionele argumenten:

|Name|Beschrijving|
|----|-----------|
|-su, --src-ambari-user | Hiermee geeft u de beheerdersgebruikersnaam voor Ambari op in het HBase-cluster van de bron. De standaardwaarde is **beheerder**. |
|-du, --dst-ambari-gebruiker | Hiermee geeft u de beheerdersgebruikersnaam voor Ambari op in het HBase-cluster van bestemming. De standaardwaarde is **beheerder**. |
|-t, --tabel-lijst | Hiermee geeft u de tabellen op die moeten worden gerepliceerd. Bijvoorbeeld: --tabel-list="tabel1;tabel2;tabel3". Als u geen tabellen opgeeft, worden alle bestaande HBase-tabellen gerepliceerd.|
|-m, --machine | Hiermee geeft u het hoofdknooppunt op waar de scriptactie wordt uitgevoerd. De waarde moet worden gekozen op basis waarvan het actieve hoofdknooppunt is. Gebruik deze optie wanneer u het script van $ 0 uitvoert als scriptactie vanuit de HDInsight-portal of Azure PowerShell.|
|-cp, -copydata | Hiermee u de migratie van bestaande gegevens inschakelen op de tabellen waarin replicatie is ingeschakeld. |
|-rpm, -repliceren-phoenix-meta | Hiermee u replicatie inschakelen op Phoenix-systeemtabellen. <br><br>*Gebruik deze optie met de nodige voorzichtigheid.* We raden u aan Phoenix-tabellen opnieuw te maken voor replicaclusters voordat u dit script gebruikt. |
|-h, --help | Geeft gebruiksinformatie weer. |

De `print_usage()` sectie van het [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) heeft een gedetailleerde uitleg van parameters.

Nadat de scriptactie is geïmplementeerd, u SSH gebruiken om verbinding te maken met het hbase-cluster van bestemming en vervolgens controleren of de gegevens zijn gerepliceerd.

### <a name="replication-scenarios"></a>Replicatiescenario's

In de volgende lijst ziet u enkele algemene gebruiksaanvragen en de parameterinstellingen:

- **Replicatie inschakelen op alle tabellen tussen de twee clusters**. Dit scenario vereist geen kopiëren of migreren van bestaande gegevens in de tabellen en maakt geen gebruik van Phoenix-tabellen. Gebruik de volgende parameters:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Replicatie inschakelen voor specifieke tabellen**. Als u replicatie in tabel1, tabel2 en tabel3 wilt inschakelen, gebruikt u de volgende parameters:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Schakel replicatie in specifieke tabellen in en kopieer de bestaande gegevens**. Als u replicatie in tabel1, tabel2 en tabel3 wilt inschakelen, gebruikt u de volgende parameters:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Schakel replicatie in op alle tabellen en repliceer Phoenix-metagegevens van bron naar bestemming**. Phoenix metadata replicatie is niet perfect. Gebruik het met de nodige voorzichtigheid. Gebruik de volgende parameters:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Gegevens kopiëren en migreren

Er zijn twee afzonderlijke scriptactiescripts beschikbaar voor het kopiëren of migreren van gegevens nadat replicatie is ingeschakeld:

- [Script voor kleine tabellen](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabellen met een paar gigabyte s groot en de algehele kopie zal naar verwachting in minder dan een uur worden voltooid)

- [Script voor grote tabellen](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabellen die naar verwachting langer dan een uur duren om te kopiëren)

U dezelfde procedure volgen die wordt beschreven in [Replicatie inschakelen](#enable-replication) om de scriptactie aan te roepen. Gebruik de volgende parameters:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

De `print_usage()` sectie van het [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) heeft een gedetailleerde beschrijving van parameters.

### <a name="scenarios"></a>Scenario's

- **Kopieer specifieke tabellen (test1, test2 en test3) voor alle rijen die tot nu toe zijn bewerkt (huidige tijdstempel):**

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Of:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Kopieer specifieke tabellen met een opgegeven tijdsbereik:**

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Replicatie uitschakelen

Als u replicatie wilt uitschakelen, gebruikt u een ander scriptactiescript van [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). U dezelfde procedure volgen die wordt beschreven in [Replicatie inschakelen](#enable-replication) om de scriptactie aan te roepen. Gebruik de volgende parameters:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

De `print_usage()` sectie van het [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) heeft een gedetailleerde uitleg van parameters.

### <a name="scenarios"></a>Scenario's

- **Replicatie uitschakelen op alle tabellen:**

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  of

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Replicatie uitschakelen op opgegeven tabellen (tabel1, tabel2 en tabel3):**

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

> [!NOTE]
> Als u het doelcluster wilt verwijderen, moet u het verwijderen uit de peerlist van het broncluster. Dit kan worden gedaan door de opdracht remove_peer '1' uit te voeren op de hbase shell op het broncluster. Als dit niet lukt, werkt het broncluster mogelijk niet goed.
>

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Apache HBase-replicatie in een virtueel netwerk of tussen twee virtuele netwerken instellen. Zie de volgende artikelen voor meer informatie over HDInsight en Apache HBase:

* [Aan de slag met Apache HBase in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight Apache HBase overzicht](./apache-hbase-overview.md)
* [Apache HBase-clusters maken in Azure Virtual Network](./apache-hbase-provision-vnet.md)


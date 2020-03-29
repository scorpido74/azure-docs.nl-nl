---
title: Fout ongeldige NetworkConfigurationErrorCode - Azure HDInsight
description: Verschillende redenen voor mislukte clustercreaties met Ongeldig NetworkConfigurationErrorCode in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720381"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Clustercreatie mislukt met InvalidNetworkConfigurationErrorCode in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

Als u foutcode `InvalidNetworkConfigurationErrorCode` ziet met de beschrijving 'Virtual Network configuration isn't compatible with HDInsight Requirement', duidt dit meestal op een probleem met de virtuele [netwerkconfiguratie](../hdinsight-plan-virtual-network-deployment.md) voor uw cluster. Volg de onderstaande secties op basis van de rest van de foutbeschrijving om het probleem op te lossen.

## <a name="hostname-resolution-failed"></a>'HostName-oplossing is mislukt'

### <a name="issue"></a>Probleem

Foutbeschrijving bevat "HostName Resolution failed".

### <a name="cause"></a>Oorzaak

Deze fout wijst op een probleem met de aangepaste DNS-configuratie. DNS-servers binnen een virtueel netwerk kunnen DNS-query's doorsturen naar de recursieve resolvers van Azure om hostnamen binnen dat virtuele netwerk op te lossen (zie [Naamomzetting in virtuele netwerken](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) voor meer informatie). Toegang tot de recursieve resolvers van Azure wordt geboden via het virtuele IP 168.63.129.16. Dit IP is alleen toegankelijk vanuit de Azure VM's. Het werkt dus niet als u een OnPrem DNS-server gebruikt of als uw DNS-server een Azure VM is, die geen deel uitmaakt van het virtuele netwerk van het cluster.

### <a name="resolution"></a>Oplossing

1. Ssh in de VM die deel uitmaakt van `hostname -f`het cluster, en voer de opdracht . Dit retourneert de volledig gekwalificeerde domeinnaam van `<host_fqdn>` de host (in de onderstaande instructies genoemd).

1. Voer vervolgens de `nslookup <host_fqdn>` opdracht uit `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`(bijvoorbeeld ). Als met deze opdracht de naam wordt opgelost naar een IP-adres, betekent dit dat uw DNS-server correct werkt. In dit geval u een ondersteuningsaanvraag instellen bij HDInsight en onderzoeken we uw probleem. Neem in uw ondersteuningscase de stappen voor het oplossen van problemen op die u hebt uitgevoerd. Dit zal ons helpen het probleem sneller op te lossen.

1. Als de bovenstaande opdracht geen IP-adres `nslookup <host_fqdn> 168.63.129.16` retourneert, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`wordt deze uitgevoerd (bijvoorbeeld ). Als deze opdracht het IP kan oplossen, betekent dit dat uw DNS-server de query niet doorstuurt naar de DNS van Azure of dat het geen VM is die deel uitmaakt van hetzelfde virtuele netwerk als het cluster.

1. Als u geen Azure VM hebt die als aangepaste DNS-server kan fungeren in het virtuele netwerk van het cluster, moet u deze eerst toevoegen. Maak een VM in het virtuele netwerk, die wordt geconfigureerd als DNS-expediteer.

1. Zodra u een VM in uw virtuele netwerk hebt geïmplementeerd, configureert u de DNS-doorstuurregels op deze vm. Alle iDNS-naamomzettingsaanvragen doorsturen naar 168.63.129.16 en de rest naar uw DNS-server. [Hier](../hdinsight-plan-virtual-network-deployment.md) volgt een voorbeeld van deze instelling voor een aangepaste DNS-server.

1. Voeg het IP-adres van deze VM toe als eerste DNS-vermelding voor de DNS-configuratie van het virtuele netwerk.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>'Kan geen verbinding maken met Azure Storage-account'

### <a name="issue"></a>Probleem

Foutbeschrijving bevat 'Geen verbinding maken met Azure Storage Account' of 'Geen verbinding maken met Azure SQL'.

### <a name="cause"></a>Oorzaak

Azure Storage en SQL hebben geen vaste IP-adressen, dus we moeten uitgaande verbindingen toestaan voor alle IP's om toegang tot deze services mogelijk te maken. De exacte oplossingsstappen zijn afhankelijk van de vraag of u een Network Security Group (NSG) of Door de gebruiker gedefinieerde regels (UDR) hebt ingesteld. Raadpleeg de sectie over [het beheren van netwerkverkeer met HDInsight met netwerkbeveiligingsgroepen en door de gebruiker gedefinieerde routes](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) voor meer informatie over deze configuraties.

### <a name="resolution"></a>Oplossing

* Als uw cluster een [Network Security Group (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md)gebruikt.

    Ga naar de Azure-portal en identificeer de NSG die is gekoppeld aan het subnet waar het cluster wordt geïmplementeerd. Geef in het gedeelte **Uitgaande beveiligingsregels** buitengebonden toegang tot internet zonder beperking (houd er rekening mee dat een kleiner **prioriteitsnummer** hier een hogere prioriteit betekent). Controleer in de sectie **subnetten** ook of deze NSG wordt toegepast op het clustersubnet.

* Als uw cluster een [Door de gebruiker gedefinieerde routes (UDR)](../../virtual-network/virtual-networks-udr-overview.md)gebruikt.

    Ga naar de Azure-portal en identificeer de routetabel die is gekoppeld aan het subnet waarin het cluster wordt geïmplementeerd. Zodra u de routetabel voor het subnet hebt gevonden, inspecteert u de **routessectie** erin.

    Als er routes zijn gedefinieerd, moet u ervoor zorgen dat er routes zijn voor IP-adressen voor het gebied waar het cluster is geïmplementeerd en dat het **NextHopType** voor elke route **internet**is. Er moet een route worden gedefinieerd voor elk vereist IP-adres dat in het voornoemde artikel is gedocumenteerd.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"Virtuele netwerkconfiguratie is niet compatibel met HDInsight-vereiste"

### <a name="issue"></a>Probleem

Foutbeschrijvingen bevatten als volgt soortgelijke berichten:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Oorzaak

Waarschijnlijk een probleem met de aangepaste DNS-instelling.

### <a name="resolution"></a>Oplossing

Valideren dat 168.63.129.16 zich in de aangepaste DNS-keten bevindt. DNS-servers binnen een virtueel netwerk kunnen DNS-query's doorsturen naar de recursieve resolvers van Azure om hostnamen binnen dat virtuele netwerk op te lossen. Zie [Naamomzetting in virtuele netwerken voor](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)meer informatie. Toegang tot de recursieve resolvers van Azure wordt geboden via het virtuele IP 168.63.129.16.

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Voer de volgende opdracht uit:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    U ziet ongeveer het volgende:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Op basis van het resultaat - kies een van de volgende stappen die u moet volgen:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 staat niet in deze lijst

**Optie 1**  
Voeg 168.63.129.16 toe als de eerste aangepaste DNS voor het virtuele netwerk met behulp van de stappen die zijn beschreven in [Een virtueel netwerk plannen voor Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Deze stappen zijn alleen van toepassing als uw aangepaste DNS-server op Linux wordt uitgevoerd.

**Optie 2**  
Een DNS-server-VM implementeren voor het virtuele netwerk. Dit omvat de volgende stappen:

* Maak een VM in het virtuele netwerk, die wordt geconfigureerd als DNS-expediteer (het kan een Linux- of windows-vm zijn).
* Dns-doorstuurregels configureren op deze VM (alle iDNS-naamomzettingsaanvragen doorsturen naar 168.63.129.16 en de rest naar uw DNS-server).
* Voeg het IP-adres van deze VM toe als eerste DNS-vermelding voor DNS-configuratie in virtuele netwerken.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 staat in de lijst

Maak in dit geval een ondersteuningscase met HDInsight en onderzoeken we uw probleem. Neem het resultaat van de onderstaande opdrachten op in uw ondersteuningsaanvraag. Dit zal ons helpen het probleem sneller te onderzoeken en op te lossen.

Vanuit een ssh-sessie op het hoofdknooppunt, bewerken en vervolgens het volgende uitvoeren:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).

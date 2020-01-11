---
title: InvalidNetworkConfigurationErrorCode-fout-Azure HDInsight
description: Verschillende redenen voor mislukte cluster creaties met InvalidNetworkConfigurationErrorCode in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: f857ee47f5dd8018d2e26aab47252533b0b17617
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887101"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Het maken van een cluster mislukt met InvalidNetworkConfigurationErrorCode in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

Als fout code `InvalidNetworkConfigurationErrorCode` wordt weer gegeven met de beschrijving ' Virtual Network configuratie is niet compatibel met HDInsight-vereiste ', duidt dit meestal op een probleem met de configuratie van het [virtuele netwerk](../hdinsight-plan-virtual-network-deployment.md) voor uw cluster. Op basis van de rest van de fout beschrijving, volgt u de onderstaande secties om het probleem op te lossen.

## <a name="hostname-resolution-failed"></a>' Omzetting van HostNamen mislukt '

### <a name="issue"></a>Probleem

Fout beschrijving bevat ' omzetting van hostnaam is mislukt '.

### <a name="cause"></a>Oorzaak

Deze fout wijst op een probleem met de aangepaste DNS-configuratie. DNS-servers binnen een virtueel netwerk kunnen DNS-query's door sturen naar recursieve resolvers van Azure om hostnamen in dat virtuele netwerk op te lossen (Zie [naam omzetting in virtuele netwerken](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) voor meer informatie). Toegang tot recursieve resolvers van Azure wordt geboden via de 168.63.129.16 van het virtuele IP-adres. Dit IP-adres is alleen toegankelijk vanaf de Azure-Vm's. Het werkt dus niet als u een premises DNS-server gebruikt of als uw DNS-server een Azure-VM is, die geen deel uitmaakt van het vNet van het cluster.

### <a name="resolution"></a>Resolutie

1. SSH naar de virtuele machine die deel uitmaakt van het cluster en voer de opdracht uit `hostname -f`. Hiermee wordt de Fully Qualified Domain Name van de host geretourneerd (aangeduid als `<host_fqdn>` in de onderstaande instructies).

1. Voer vervolgens de opdracht `nslookup <host_fqdn>` uit (bijvoorbeeld `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`). Als met deze opdracht de naam wordt omgezet in een IP-adres, betekent dit dat uw DNS-server goed werkt. In dit geval moet u een ondersteunings aanvraag met HDInsight genereren en uw probleem onderzoeken. Neem in uw ondersteunings aanvraag de stappen voor het oplossen van problemen op die u hebt uitgevoerd. Dit helpt ons het probleem sneller op te lossen.

1. Als met de bovenstaande opdracht geen IP-adres wordt geretourneerd, voert u `nslookup <host_fqdn> 168.63.129.16` uit (bijvoorbeeld `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`). Als met deze opdracht het IP-adres kan worden omgezet, betekent dit dat de DNS-server de query niet doorstuurt naar de DNS van Azure, of geen VM is die deel uitmaakt van hetzelfde vNet als het cluster.

1. Als u geen Azure-VM hebt die kan fungeren als aangepaste DNS-server in het vNet van het cluster, moet u dit eerst toevoegen. Maak een virtuele machine in het vNet, die wordt geconfigureerd als DNS-doorstuur server.

1. Wanneer u een virtuele machine in uw vNet hebt geïmplementeerd, configureert u de DNS-doorstuur regels op deze VM. Stuur alle Idn's-naam omzettings aanvragen door naar 168.63.129.16, en de rest naar uw DNS-server. [Hier](../hdinsight-plan-virtual-network-deployment.md) volgt een voor beeld van deze installatie voor een aangepaste DNS-server.

1. Voeg het IP-adres van deze virtuele machine toe als eerste DNS-vermelding voor de Virtual Network DNS-configuratie.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>' Kan geen verbinding maken met Azure Storage account '

### <a name="issue"></a>Probleem

Fout beschrijving bevat ' kan geen verbinding maken met Azure Storage account ' of ' kan geen verbinding maken met Azure SQL '.

### <a name="cause"></a>Oorzaak

Azure Storage en SQL hebben geen vaste IP-adressen, dus moeten uitgaande verbindingen met alle Ip's toestaan om toegang te krijgen tot deze services. De exacte stappen zijn afhankelijk van het feit of u een netwerk beveiligings groep (NSG) of door de gebruiker gedefinieerde regels (UDR) hebt ingesteld. Raadpleeg de sectie over het [beheren van netwerk verkeer met HDInsight met netwerk beveiligings groepen en door de gebruiker gedefinieerde routes](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) voor meer informatie over deze configuraties.

### <a name="resolution"></a>Resolutie

* Als uw cluster gebruikmaakt van een [netwerk beveiligings groep (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Ga naar de Azure Portal en Identificeer de NSG die is gekoppeld aan het subnet waar het cluster wordt geïmplementeerd. Geef in de sectie **uitgaande beveiligings regels** uitgaande toegang tot internet zonder enige beperking (Houd er rekening mee dat een kleiner **prioriteits** nummer een hogere prioriteit heeft). Controleer ook in het gedeelte **subnets** of deze NSG wordt toegepast op het subnet van het cluster.

* Als uw cluster gebruikmaakt van een door de [gebruiker gedefinieerde routes (UDR)](../../virtual-network/virtual-networks-udr-overview.md).

    Ga naar de Azure Portal en Identificeer de route tabel die is gekoppeld aan het subnet waar het cluster wordt geïmplementeerd. Wanneer u de route tabel voor het subnet hebt gevonden, inspecteert u de sectie **routes** hierin.

    Als er routes zijn gedefinieerd, moet u ervoor zorgen dat er routes zijn voor IP-adressen voor de regio waarin het cluster is geïmplementeerd en dat de **NextHopType** voor elke route **Internet**is. Er moet een route worden gedefinieerd voor elk vereiste IP-adres dat in het voor noemde artikel wordt beschreven.

---

### <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).

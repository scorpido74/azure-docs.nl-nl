---
title: Verbeter de beschik baarheid van uw toepassing met Azure Advisor
description: Gebruik Azure Advisor om de maximale Beschik baarheid van uw Azure-implementaties te verbeteren.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: bed092a51b5a4aba1dfa64c17f5ed3d6f72212da
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658479"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Verbeter de beschik baarheid van uw toepassing met Azure Advisor

Azure Advisor helpt u de continuïteit van uw bedrijfs kritieke toepassingen te garanderen en te verbeteren. U kunt aanbevelingen voor hoge Beschik baarheid van Advisor ontvangen via het tabblad **maximale Beschik baarheid** van het Advisor-dash board.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Fout tolerantie voor virtuele machines garanderen

U wordt aangeraden om twee of meer virtuele machines in een beschikbaarheidsset te groeperen, om uw toepassing van redundantie te voorzien. Advisor identificeert virtuele machines die geen deel uitmaken van een beschikbaarheidsset en raadt u aan om ze te verplaatsen naar een beschikbaarheidsset. Deze configuratie zorgt ervoor dat tijdens een geplande of niet-geplande onderhouds gebeurtenis ten minste één virtuele machine beschikbaar is en voldoet aan de SLA van de virtuele Azure-machine. U kunt ervoor kiezen een beschikbaarheidsset voor de virtuele machine te maken of de virtuele machine toe te voegen aan een bestaande beschikbaarheidsset.

> [!NOTE]
> Als u een beschikbaarheidsset maakt, moet u er ten minste één virtuele machine aan toevoegen. We raden u aan twee of meer virtuele machines in een beschikbaarheidsset te groeperen om ervoor te zorgen dat er ten minste één computer beschikbaar is tijdens een storing.

## <a name="ensure-availability-set-fault-tolerance"></a>Fout tolerantie voor beschikbaarheidsset garanderen

U wordt aangeraden om twee of meer virtuele machines in een beschikbaarheidsset te groeperen, om uw toepassing van redundantie te voorzien. Advisor identificeert beschikbaarheids sets die één virtuele machine bevatten en raadt u aan om een of meer virtuele machines toe te voegen.Deze configuratie zorgt ervoor dat tijdens een geplande of niet-geplande onderhouds gebeurtenis ten minste één virtuele machine beschikbaar is en voldoet aan de SLA van de virtuele Azure-machine.U kunt ervoor kiezen om een virtuele machine te maken of een bestaande virtuele machine toe te voegen aan de beschikbaarheidsset.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Managed Disks gebruiken voor verbeterde betrouwbaarheid van gegevens

Virtuele machines die deel uitmaken van een beschikbaarheidsset met schijven die opslag accounts of opslag schaal eenheden delen, zijn niet flexibel voor opslag met een enkele storing in de geheugen grootte tijdens uitval. Advisor identificeert deze beschikbaarheids sets en raadt u aan om te migreren naar Azure Managed Disks. Dit zorgt ervoor dat de schijven van de verschillende virtuele machines in de beschikbaarheidsset voldoende geïsoleerd zijn om een Single Point of Failure te voor komen. 

## <a name="known-issue-with-check-point-network-virtual-appliance-image-version"></a>Bekend probleem met de installatiekopieversie van Check Point Network Virtual Appliance

Met Advisor kan worden vastgesteld of op uw virtuele machine een versie van Check Point wordt uitgevoerd waarvan bekend is dat deze de netwerk verbinding verliest in het geval van een platform-onderhouds bewerking. Advisor-aanbeveling helpt u bij te werken naar een nieuwere versie van de installatie kopie waarmee dit probleem wordt opgelost. Dit zorgt voor bedrijfs continuïteit door middel van een betere netwerk verbinding.

## <a name="ensure-application-gateway-fault-tolerance"></a>Fout tolerantie van Application Gateway garanderen

Deze aanbeveling garandeert de bedrijfs continuïteit van essentiële toepassingen die worden aangedreven door toepassings gateways. Advisor identificeert Application Gateway-exemplaren die niet zijn geconfigureerd voor fout tolerantie en suggesties voor herstel acties die u kunt uitvoeren. Advisor identificeert gemiddeld of grote toepassings gateways voor één exemplaar en raadt aan om ten minste één instantie toe te voegen. Het identificeert ook kleine toepassings gateways met één of meerdere exemplaren en raadt u aan om te migreren naar normale of grote Sku's. Advisor raadt deze acties aan om ervoor te zorgen dat uw Application Gateway-exemplaren zijn geconfigureerd om te voldoen aan de huidige SLA-vereisten voor deze resources.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Beveilig de gegevens van uw virtuele machine tegen onbedoeld verwijderen

Bij het instellen van de back-up van de virtuele machine wordt de beschik baarheid van uw bedrijfs kritieke gegevens gegarandeerd en wordt bescherming geboden tegen onbedoeld verwijderen of beschadigd. Advisor identificeert virtuele machines waarop back-up niet is ingeschakeld en raadt aan om back-ups in te scha kelen. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Zorg ervoor dat u toegang hebt tot Azure-Cloud experts wanneer u deze nodig hebt

Wanneer u een kritieke werk belasting uitvoert, is het belang rijk dat u toegang hebt tot technische ondersteuning wanneer dat nodig is. Advisor identificeert potentiële bedrijfs kritieke abonnementen die geen technische ondersteuning hebben die zijn opgenomen in hun ondersteunings plan en waarmee wordt geadviseerd om een upgrade uit te voeren naar een optie met technische ondersteuning.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure Service Health waarschuwingen maken om een melding te ontvangen wanneer Azure-problemen van invloed zijn op u

We raden u aan Azure Service Health waarschuwingen in te stellen om te worden gewaarschuwd wanneer de Azure-Service problemen van invloed zijn op u. [Azure service Health](https://azure.microsoft.com/features/service-health/) is een gratis service die persoonlijke begeleiding en ondersteuning biedt wanneer u een Azure-service probleem ondervindt. Advisor identificeert abonnementen waarvoor geen waarschuwingen zijn geconfigureerd en raadt u aan om er een te maken.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Traffic Manager-eind punten voor tolerantie configureren

Traffic Manager profielen met meer dan één eindpunt ervaring hogere Beschik baarheid als een bepaald eind punt mislukt. Het plaatsen van eind punten in verschillende regio's verbetert de betrouw baarheid van de service. Advisor identificeert Traffic Manager-profielen waarbij er slechts één eind punt is en raadt aan ten minste één eind punt toe te voegen in een andere regio.

Als alle eind punten in een Traffic Manager profiel dat is geconfigureerd voor proximity-route ring zich in dezelfde regio bevinden, kunnen gebruikers uit andere regio's verbindings vertragingen ondervinden. Als u een eind punt toevoegt of verplaatst naar een andere regio, worden de algehele prestaties verbeterd en wordt er een betere Beschik baarheid geboden als alle eind punten in één regio mislukken. Advisor identificeert Traffic Manager profielen die zijn geconfigureerd voor proximity route ring waarbij alle eind punten zich in dezelfde regio bevinden. Het wordt aanbevolen een eind punt toe te voegen aan of te verplaatsen naar een andere Azure-regio.

Als een Traffic Manager profiel is geconfigureerd voor geografische route ring, wordt verkeer doorgestuurd naar eind punten op basis van gedefinieerde regio's. Als er een fout optreedt in een regio, is er geen vooraf gedefinieerde failover. Als u een eind punt hebt waarbij de regionale groepering is geconfigureerd op ' alle (wereld) ', voor komt u dat verkeer wordt verwijderd en de beschik baarheid van de service wordt verbeterd. Advisor identificeert Traffic Manager profielen die zijn geconfigureerd voor geografische route ring, waarbij er geen eind punt is geconfigureerd voor de regionale groepering als ' alle (wereld) '. Het wordt aanbevolen de configuratie te wijzigen om een eind punt ' alle (wereld) te maken.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Gebruik zacht verwijderen in uw Azure Storage-account om gegevens op te slaan en te herstellen na onbedoeld overschrijven of verwijderen

Schakel de optie [voorlopig verwijderen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) in uw opslag account in, zodat verwijderde blobs overstappen naar een voorlopig verwijderde status in plaats van permanent worden verwijderd. Als gegevens worden overschreven, wordt er een voorlopig verwijderde momentopname gegenereerd waarin de status van de overschreven gegevens wordt opgeslagen. Met de functie voor het gebruik van zacht verwijderen kunt u herstellen als er per ongeluk verwijderingen of overschrijvingen zijn. Advisor identificeert Azure Storage accounts waarvoor geen tijdelijke verwijdering is ingeschakeld en stelt u in staat om deze in te scha kelen.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Configureer uw VPN-gateway naar actief-actief voor verbindings tolerantie

In de configuratie actief-actief maken beide exemplaren van een VPN-gateway S2S VPN-tunnels naar uw on-premises VPN-apparaat. Wanneer een geplande onderhouds gebeurtenis of een niet-geplande gebeurtenis plaatsvindt in één gateway-exemplaar, wordt het verkeer automatisch overgeschakeld naar de andere actieve IPsec-tunnel. Azure Advisor worden VPN-gateways geïdentificeerd die niet zijn geconfigureerd als actief-actief en suggesties stellen dat u ze configureert voor hoge Beschik baarheid.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Productie-VPN-gateways gebruiken voor het uitvoeren van uw productie-workloads

Azure Advisor controleert op alle VPN-gateways die een basis-SKU zijn en raadt u aan om in plaats daarvan een productie-SKU te gebruiken. De basis-SKU is ontworpen voor ontwikkelings-en test doeleinden. Productie-Sku's bieden een hoger aantal tunnels, BGP-ondersteuning, actieve configuratie opties, aangepast IPSec/IKE-beleid, en hogere stabiliteit en beschik baarheid.

## <a name="repair-invalid-log-alert-rules"></a>Ongeldige regels voor logboek waarschuwingen herstellen

Met Azure Advisor worden waarschuwings regels gedetecteerd waarvoor ongeldige query's zijn opgegeven in de voor waarde-sectie. De logboekwaarschuwingsregels worden gemaakt in Azure Monitor en worden gebruikt om analytische query's met bepaalde intervallen uit te voeren. De resultaten van de query bepalen of er een waarschuwing moet worden gegeven. Analytics-query’s kunnen ongeldig worden als gevolg van wijzigingen in bronnen, tabellen of opdrachten waarnaar wordt verwezen. Advisor wordt aangeraden de query in de waarschuwings regel te corrigeren om te voor komen dat deze automatisch wordt uitgeschakeld en de bewakings dekking van uw resources in azure te controleren. [Meer informatie over het oplossen van waarschuwings regels](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Consistente indexerings modus configureren voor uw Cosmos DB verzameling

Azure Cosmos DB containers die zijn geconfigureerd met de vertraagde indexerings modus kunnen van invloed zijn op de versheid van query resultaten. Advisor detecteert containers die op deze manier zijn geconfigureerd en raden u aan om over te scha kelen naar de consistente modus. [Meer informatie over het indexeren van beleid in Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Uw Azure Cosmos DB-containers met een partitiesleutel configureren

Met Azure Advisor worden Azure Cosmos DB niet-gepartitioneerde verzamelingen geïdentificeerd die de ingerichte opslag quota nader benaderen. U wordt aangeraden deze verzamelingen te migreren naar nieuwe verzamelingen met een partitie sleutel definitie, zodat deze automatisch kan worden uitgebreid door de service. [Meer informatie over het kiezen van een partitie sleutel](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Uw Azure Cosmos DB .NET SDK bijwerken naar de nieuwste versie van Nuget

Met Azure Advisor worden Azure Cosmos DB-accounts geïdentificeerd die gebruikmaken van oude versies van de .NET SDK en wordt aanbevolen een upgrade uit te voeren naar de meest recente versie van Nuget voor de nieuwste oplossingen, prestatie verbeteringen en nieuwe functies. [Meer informatie over Cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Uw Azure Cosmos DB Java SDK bijwerken naar de nieuwste versie van Maven

Azure Advisor identificeert Azure Cosmos DB accounts die gebruikmaken van oude versies van de Java-SDK en raadt u aan om een upgrade uit te voeren naar de meest recente versie van Maven voor de nieuwste oplossingen, prestatie verbeteringen en nieuwe functies. [Meer informatie over Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-async-java)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Uw Azure Cosmos DB Spark-connector bijwerken naar de nieuwste versie van Maven

Met Azure Advisor worden Azure Cosmos DB accounts geïdentificeerd die gebruikmaken van oude versies van de Cosmos DB Spark-connector en wordt aanbevolen om een upgrade uit te voeren naar de nieuwste versie van Maven voor de nieuwste oplossingen, prestatie verbeteringen en nieuwe functies. [Meer informatie over Cosmos DB Spark-connector](https://aka.ms/cosmosdb/spark-connector)

## <a name="upgrade-recommendation-for-deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Upgrade aanbeveling voor afschaffing van Kafka 1,1 in HDInsight 4,0 Kafka-cluster

Vanaf 1 juli 2020 kunnen klanten geen nieuwe Kafka-clusters maken met Kafka 1.1 op HDInsight 4.0. Bestaande clusters worden zonder ondersteuning van Microsoft uitgevoerd zoals ze zijn. Overweeg om de overstap naar Kafka 2.1 op HDInsight 4.0 voor 30 juni 2020 te maken om potentiële systeem- en ondersteuningsonderbrekingen te voorkomen.

## <a name="upgrade-recommendation-for-deprecation-of-older-spark-versions-in-hdinsight-spark-cluster"></a>Upgrade aanbeveling voor oudere Spark-versies in HDInsight Spark-cluster

Vanaf 1 juli 2020 kunnen klanten geen nieuwe Spark-clusters maken met Apache Spark 2.1 en 2.2 op HDInsight 3.6 en Spark 2.3 op HDInsight 4.0. Bestaande clusters worden zonder ondersteuning van Microsoft uitgevoerd zoals ze zijn. ",

## <a name="enable-virtual-machine-replication"></a>Replicatie van virtuele machines inschakelen
Virtuele machines waarvoor geen replicatie is ingeschakeld voor een andere regio, zijn niet flexibel voor regionale storingen. Het repliceren van virtuele machines vermindert de impact van het bedrijf tijdens de tijd van een onderbreking van de Azure-regio. Advisor detecteert Vm's waarvoor geen replicatie is ingeschakeld en raadt u aan om replicatie in te scha kelen, zodat u in het geval van een storing snel uw virtuele machines in een externe Azure-regio kunt weer geven. [Meer informatie over replicatie van virtuele machines](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Toegang tot aanbevelingen voor hoge Beschik baarheid in Advisor

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open vervolgens [Advisor](https://aka.ms/azureadvisordashboard).

2.  Klik op het Advisor-dash board op het tabblad **maximale Beschik baarheid** .

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:
* [Inleiding tot Advisor](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor Advisor-kosten](advisor-cost-recommendations.md)
* [Aanbevelingen voor Advisor-prestaties](advisor-performance-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
* [Aanbevelingen voor operationele uitmuntendheid van Advisor](advisor-operational-excellence-recommendations.md)

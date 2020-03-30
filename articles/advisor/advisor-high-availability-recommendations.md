---
title: De beschikbaarheid van uw toepassing verbeteren met Azure Advisor
description: Gebruik Azure Advisor om de hoge beschikbaarheid van uw Azure-implementaties te verbeteren.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 997681ed62fa9985e3122ece22565dbae0e65b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443114"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>De beschikbaarheid van uw toepassing verbeteren met Azure Advisor

Azure Advisor helpt u de continuïteit van uw bedrijfskritieke toepassingen te waarborgen en te verbeteren. U aanbevelingen met hoge beschikbaarheid krijgen van Advisor via het tabblad **Hoge beschikbaarheid** van het Dashboard Adviseur.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Zorg voor fouttolerantie voor virtuele machines

U wordt aangeraden om twee of meer virtuele machines in een beschikbaarheidsset te groeperen, om uw toepassing van redundantie te voorzien. Advisor identificeert virtuele machines die geen deel uitmaken van een beschikbaarheidsset en raadt aan deze te verplaatsen naar een beschikbaarheidsset. Deze configuratie zorgt ervoor dat tijdens een geplande of ongeplande onderhoudsgebeurtenis ten minste één virtuele machine beschikbaar is en voldoet aan de SLA van de Azure-virtuele machine. U ervoor kiezen om een beschikbaarheidsset voor de virtuele machine te maken of om de virtuele machine toe te voegen aan een bestaande beschikbaarheidsset.

> [!NOTE]
> Als u ervoor kiest een beschikbaarheidsset te maken, moet u er nog minstens één virtuele machine aan toevoegen. We raden u aan twee of meer virtuele machines te groeperen in een beschikbaarheidsset om ervoor te zorgen dat ten minste één machine beschikbaar is tijdens een storing.

## <a name="ensure-availability-set-fault-tolerance"></a>Zorg voor fouttolerantie voor beschikbaarheidsset

U wordt aangeraden om twee of meer virtuele machines in een beschikbaarheidsset te groeperen, om uw toepassing van redundantie te voorzien. Advisor identificeert beschikbaarheidssets die één virtuele machine bevatten en raadt aan er een of meer virtuele machines aan toe te voegen.Deze configuratie zorgt ervoor dat tijdens een geplande of ongeplande onderhoudsgebeurtenis ten minste één virtuele machine beschikbaar is en voldoet aan de SLA van de Azure-virtuele machine.U ervoor kiezen om een virtuele machine te maken of om een bestaande virtuele machine toe te voegen aan de beschikbaarheidsset.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Managed Disks gebruiken voor verbeterde betrouwbaarheid van gegevens

Virtuele machines die zich in een beschikbaarheidsset bevinden met schijven die opslagaccounts of opslagschaaleenheden delen, zijn niet bestand tegen storingen in één opslagschaaltijdens uitval. Advisor identificeert deze beschikbaarheidssets en raadt u aan te migreren naar Azure Managed Disks. Dit zal ervoor zorgen dat de schijven van de verschillende virtuele machines in de beschikbaarheidset voldoende geïsoleerd zijn om een enkel storingspunt te voorkomen. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Fouttolerantie voor toepassingsgateways garanderen

Deze aanbeveling garandeert de bedrijfscontinuïteit van bedrijfskritieke toepassingen die worden aangedreven door toepassingsgateways. Advisor identificeert toepassingsgateway-exemplaren die niet zijn geconfigureerd voor fouttolerantie en stelt voor dat u herstelacties uitvoeren. Advisor identificeert middelgrote of grote toepassingsgateways met één instantie en raadt aan om ten minste één exemplaar toe te voegen. Het identificeert ook single- of multi-instance small application gateways en raadt het migreren naar middelgrote of grote SKU's aan. Advisor raadt deze acties aan om ervoor te zorgen dat uw toepassingsgateway-exemplaren zijn geconfigureerd om te voldoen aan de huidige SLA-vereisten voor deze resources.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Bescherm uw virtuele machinegegevens tegen onbedoelde verwijdering

Het instellen van een back-up van virtuele machines zorgt voor de beschikbaarheid van uw bedrijfskritieke gegevens en biedt bescherming tegen onbedoelde verwijdering of beschadiging. Advisor identificeert virtuele machines waar back-up niet is ingeschakeld en raadt aan back-up in te schakelen. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Zorg ervoor dat u toegang hebt tot Azure-cloudexperts wanneer u dat nodig hebt

Bij het uitvoeren van een bedrijfskritieke workload is het belangrijk om toegang te hebben tot technische ondersteuning wanneer dat nodig is. Advisor identificeert potentiële bedrijfskritieke abonnementen die geen technische ondersteuning in hun ondersteuningsplan bevatten en beveelt een upgrade aan naar een optie die technische ondersteuning bevat.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure Service Health-waarschuwingen maken om te worden gewaarschuwd wanneer Azure-problemen u beïnvloeden

We raden u aan Azure Service Health-waarschuwingen in te stellen om op de hoogte te worden gesteld wanneer azure-serviceproblemen van invloed zijn op u. [Azure Service Health](https://azure.microsoft.com/features/service-health/) is een gratis service die persoonlijke richtlijnen en ondersteuning biedt wanneer u wordt beïnvloed door een Azure-serviceprobleem. Advisor identificeert abonnementen die geen waarschuwingen hebben geconfigureerd en raadt aan er een te maken.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Eindpunten voor traffic manager configureren voor tolerantie

Traffic Manager-profielen met meer dan één eindpunt ervaren een hogere beschikbaarheid als een bepaald eindpunt mislukt. Het plaatsen van eindpunten in verschillende regio's verbetert de betrouwbaarheid van de service verder. Advisor identificeert Traffic Manger profielen waar er slechts één eindpunt is en raadt aan om ten minste één eindpunt in een andere regio toe te voegen.

Als alle eindpunten in een Traffic Manager-profiel dat is geconfigureerd voor nabijheidsroutering zich in dezelfde regio bevinden, kunnen gebruikers uit andere regio's verbindingsvertragingen ondervinden. Als u een eindpunt toevoegt of verplaatst naar een andere regio, worden de algehele prestaties verbeterd en wordt de beschikbaarheid verbeterd als alle eindpunten in één regio mislukken. Advisor identificeert Traffic Manager-profielen die zijn geconfigureerd voor nabijheidsroutering waarbij alle eindpunten zich in dezelfde regio bevinden. Het beveelt aan om een eindpunt toe te voegen of te verplaatsen naar een andere Azure-regio.

Als een Traffic Manager-profiel is geconfigureerd voor geografische routering, wordt het verkeer doorgestuurd naar eindpunten op basis van gedefinieerde regio's. Als een regio faalt, is er geen vooraf gedefinieerde failover. Als u een eindpunt hebt waarbij de regionale groepering is geconfigureerd voor 'Alle (wereld)' wordt voorkomen dat het verkeer wordt verwijderd en de beschikbaarheid van de service wordt verbeterd. Advisor identificeert Traffic Manager-profielen die zijn geconfigureerd voor geografische routering wanneer er geen eindpunt is geconfigureerd om de regionale groepering te hebben als "Alle (wereld)". Het beveelt aan de configuratie te wijzigen om een eindpunt "All (World) te maken.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Gebruik soft delete op uw Azure Storage Account om gegevens op te slaan en te herstellen na onbedoeld overschrijven of verwijderen

Schakel [soft delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) in uw opslagaccount in, zodat verwijderde blobs overgaan naar een zachte verwijderde status in plaats van permanent te worden verwijderd. Als gegevens worden overschreven, wordt er een voorlopig verwijderde momentopname gegenereerd waarin de status van de overschreven gegevens wordt opgeslagen. Met behulp van soft delete u herstellen als er per ongeluk verwijderingen of overschrijft. Advisor identificeert Azure Storage-accounts die geen soft delete hebben ingeschakeld en stelt voor dat u deze inschakelt.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Uw VPN-gateway configureren naar actief-actief voor verbindingstolerantie

In actieve configuratie zullen beide exemplaren van een VPN-gateway S2S VPN-tunnels instellen naar uw on-premises VPN-apparaat. Wanneer een geplande onderhoudsgebeurtenis of ongeplande gebeurtenis gebeurt met een gateway-instantie, wordt het verkeer automatisch overgeschakeld naar de andere actieve IPsec-tunnel. Azure Advisor identificeert VPN-gateways die niet zijn geconfigureerd als actief actief en stelt voor dat u ze configureert voor hoge beschikbaarheid.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Gebruik vpn-gateways voor productie om uw productieworkloads uit te voeren

Azure Advisor controleert op VPN-gateways die een Basic SKU zijn en raadt u aan in plaats daarvan een productie-SKU te gebruiken. De Basic SKU is ontworpen voor ontwikkelings- en testdoeleinden. Productie SKU's bieden een hoger aantal tunnels, BGP-ondersteuning, actieve configuratieopties, aangepast Ipsec/IKE-beleid en een hogere stabiliteit en beschikbaarheid.

## <a name="repair-invalid-log-alert-rules"></a>Ongeldige logboekwaarschuwingsregels herstellen

Azure Advisor detecteert waarschuwingsregels met ongeldige query's die zijn opgegeven in de sectie voorwaarde. De logboekwaarschuwingsregels worden gemaakt in Azure Monitor en worden gebruikt om analytische query's met bepaalde intervallen uit te voeren. De resultaten van de query bepalen of er een waarschuwing moet worden gegeven. Analytics-query’s kunnen ongeldig worden als gevolg van wijzigingen in bronnen, tabellen of opdrachten waarnaar wordt verwezen. Advisor raadt u aan de query in de waarschuwingsregel te corrigeren om te voorkomen dat deze automatisch wordt uitgeschakeld en ervoor te zorgen dat de dekking van uw resources in Azure wordt bewaakt. [Meer informatie over het oplossen van probleemoplossingsregels](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Consistente indexeringsmodus configureren in uw Cosmos DB-verzameling

Azure Cosmos DB-containers die zijn geconfigureerd met de lazy-indexeringsmodus, kunnen van invloed zijn op de versheid van queryresultaten. Advisor detecteert containers die op deze manier zijn geconfigureerd en raadt aan om over te schakelen naar de consistente modus. [Meer informatie over indexeringsbeleid in Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Uw Azure Cosmos DB-containers met een partitiesleutel configureren

Azure Advisor identificeert azure cosmos DB-niet-partitieverzamelingen die hun ingerichte opslagquotum naderen. Het zal aanbevelen om deze verzamelingen te migreren naar nieuwe verzamelingen met een definitie van partitiesleutel, zodat ze automatisch door de service kunnen worden uitgeschaald. [Meer informatie over het kiezen van een partitiesleutel](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Uw Azure Cosmos DB .NET SDK bijwerken naar de nieuwste versie van Nuget

Azure Advisor identificeert Azure Cosmos DB-accounts die oude versies van de .NET SDK gebruiken en raadt aan om te upgraden naar de nieuwste versie van Nuget voor de nieuwste oplossingen, prestatieverbeteringen en nieuwe functiemogelijkheden. [Meer informatie over Cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Uw Azure Cosmos DB Java SDK bijwerken naar de nieuwste versie van Maven

Azure Advisor identificeert Azure Cosmos DB-accounts die oude versies van de Java SDK gebruiken en raadt aan om te upgraden naar de nieuwste versie van Maven voor de nieuwste oplossingen, prestatieverbeteringen en nieuwe functiemogelijkheden. [Meer informatie over Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Uw Azure Cosmos DB Spark-connector bijwerken naar de nieuwste versie van Maven

Azure Advisor identificeert Azure Cosmos DB-accounts die oude versies van de Cosmos DB Spark-connector gebruiken en raadt aan om te upgraden naar de nieuwste versie van Maven voor de nieuwste oplossingen, prestatieverbeteringen en nieuwe functiemogelijkheden. [Meer informatie over cosmos DB Spark-connector](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Replicatie van virtuele machines inschakelen
Virtuele machines die geen replicatie hebben ingeschakeld voor een andere regio, zijn niet bestand tegen regionale uitval. Het repliceren van virtuele machines vermindert de nadelige gevolgen voor het bedrijf tijdens de periode van een uitval van de Azure-regio. Advisor detecteert VM's die geen replicatie hebben ingeschakeld en raadt aan replicatie in te schakelen, zodat u in het geval van een storing uw virtuele machines snel weergeven in een extern Azure-gebied. [Meer informatie over replicatie van virtuele machines](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Toegang tot aanbevelingen voor hoge beschikbaarheid in Adviseur

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open [Vervolgens Advisor](https://aka.ms/azureadvisordashboard).

2.  Klik op het dashboard Van Advisor op het tabblad **Hoge beschikbaarheid.**

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over aanbevelingen van Advisor:
* [Inleiding tot Azure Advisor](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor kosten van adviseur](advisor-cost-recommendations.md)
* [Aanbevelingen voor prestaties van adviseur](advisor-performance-recommendations.md)
* [Aanbevelingen voor beveiliging van adviseur](advisor-security-recommendations.md)
* [Aanbevelingen adviseur Operational Excellence](advisor-operational-excellence-recommendations.md)

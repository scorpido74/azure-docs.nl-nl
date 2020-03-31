---
title: Geo-replicatie instellen voor Azure-cache voor Redis | Microsoft Documenten
description: Meer informatie over het repliceren van uw Azure-cache voor Redis-exemplaren in geografische regio's.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129428"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Geo-replicatie instellen voor Azure Cache voor Redis

Geo-replicatie biedt een mechanisme voor het koppelen van twee Azure-cache in de Premium-laag voor Redis-exemplaren. De ene cache wordt gekozen als de primaire gekoppelde cache en de andere als de secundaire gekoppelde cache. De secundaire gekoppelde cache wordt alleen-lezen en gegevens die naar de primaire cache worden geschreven, worden gerepliceerd naar de secundaire gekoppelde cache. Deze functionaliteit kan worden gebruikt om een cache te repliceren in Azure-regio's. In dit artikel vindt u een handleiding voor het configureren van georeplicatie voor uw Azure-cache in de Premium-laag voor Redis-exemplaren.

## <a name="geo-replication-prerequisites"></a>Vereisten voor georeplicatie

Als u georeplicatie tussen twee caches wilt configureren, moet aan de volgende vereisten worden voldaan:

- Beide caches zijn [Premium tier](cache-premium-tier-intro.md) caches.
- Beide caches bevinden zich in hetzelfde Azure-abonnement.
- De secundaire gekoppelde cache heeft dezelfde cachegrootte of een grotere cachegrootte dan de primaire gekoppelde cache.
- Beide caches worden gemaakt en in een lopende status.

Sommige functies worden niet ondersteund met georeplicatie:

- Persistentie wordt niet ondersteund met geo-replicatie.
- Clustering wordt ondersteund als beide caches zijn ingeschakeld voor clustering en hetzelfde aantal shards hebben.
- Caches in dezelfde VNET worden ondersteund.
- Caches in verschillende VNETs worden ondersteund met kanttekeningen. Zie [Kan ik geo-replicatie gebruiken met mijn caches in een VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

Nadat georeplicatie is geconfigureerd, gelden de volgende beperkingen voor het gekoppelde cachepaar:

- De secundaire gekoppelde cache is alleen-lezen; je ervan lezen, maar je er geen gegevens naar schrijven. 
- Alle gegevens die zich in de secundaire gekoppelde cache bevinden voordat de koppeling werd toegevoegd, worden verwijderd. Als de georeplicatie later echter wordt verwijderd, blijven de gerepliceerde gegevens in de secundaire gekoppelde cache.
- U beide caches niet [schalen](cache-how-to-scale.md) terwijl de caches zijn gekoppeld.
- U [het aantal shards](cache-how-to-premium-clustering.md) niet wijzigen als de cache clustering heeft ingeschakeld.
- U geen persistentie inschakelen in beide cache.
- U [exporteren](cache-how-to-import-export-data.md#export) vanuit beide cache.
- U niet [importeren](cache-how-to-import-export-data.md#import) in de secundaire gekoppelde cache.
- U geen gekoppelde cache of de brongroep die deze bevat verwijderen totdat u de koppelingen ontkoppelt. Zie [Waarom is de bewerking mislukt toen ik mijn gekoppelde cache probeerde te verwijderen?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Als de caches zich in verschillende regio's bevinden, zijn netwerkverwijderingskosten van toepassing op de gegevens die door verschillende regio's worden verplaatst. Zie [Hoeveel kost het repliceren van mijn gegevens in Azure-regio's voor](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions) meer informatie?
- Automatische failover vindt niet plaats tussen de primaire en secundaire gekoppelde cache. Zie [Hoe werkt het niet overwerken naar de secundaire gekoppelde cache voor](#how-does-failing-over-to-the-secondary-linked-cache-work) meer informatie en informatie over hoe u een clienttoepassing failoveren?

## <a name="add-a-geo-replication-link"></a>Een koppeling met georeplicatie toevoegen

1. Als u twee caches aan elkaar wilt koppelen voor georeplicatie, klikt u met de vuist op **Geo-replicatie** in het resourcemenu van de cache waarvan u van plan bent de primaire gekoppelde cache te zijn. Klik vervolgens op **Cachereplicatiekoppeling toevoegen** vanuit het **georeplicatieblad.**

    ![Koppeling toevoegen](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klik op de naam van de beoogde secundaire cache in de lijst **Compatibele caches.** Als uw secundaire cache niet in de lijst wordt weergegeven, controleert u of aan de vereisten voor [georeplicatie](#geo-replication-prerequisites) voor de secundaire cache is voldaan. Als u de caches per regio wilt filteren, klikt u op het gebied in de kaart om alleen die caches weer te geven in de lijst **Compatibele caches.**

    ![Geo-replicatiecompatibele caches](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    U het koppelingsproces ook starten of details over de secundaire cache weergeven met behulp van het contextmenu.

    ![Contextmenu geo-replicatie](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klik **op Koppeling** om de twee caches aan elkaar te koppelen en het replicatieproces te starten.

    ![Caches koppelen](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. U de voortgang van het replicatieproces bekijken op het **georeplicatieblad.**

    ![Status koppelen](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    U ook de koppelingsstatus op het **overzichtsblad** voor zowel de primaire als de secundaire caches bekijken.

    ![Cachestatus](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Zodra het replicatieproces is voltooid, wordt de **status Koppeling** gewijzigd **in Geslaagd**.

    ![Cachestatus](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    De primaire gekoppelde cache blijft beschikbaar voor gebruik tijdens het koppelingsproces. De secundaire gekoppelde cache is pas beschikbaar als het koppelingsproces is voltooid.

## <a name="remove-a-geo-replication-link"></a>Een koppeling met georeplicatie verwijderen

1. Als u de koppeling tussen twee caches wilt verwijderen en georeplicatie wilt stoppen, klikt u op **Cache verwijderen** uit het **georeplicatieblad.**
    
    ![Caches ontkoppelen](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Wanneer het ontkoppelingsproces is voltooid, is de secundaire cache beschikbaar voor zowel lezen als schrijven.

>[!NOTE]
>Wanneer de georeplicatiekoppeling wordt verwijderd, blijven de gerepliceerde gegevens uit de primaire gekoppelde cache in de secundaire cache.
>
>

## <a name="geo-replication-faq"></a>Veelgestelde vragen over georeplicatie

- [Kan ik geo-replicatie gebruiken met een standaard- of basic-laagcache?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Is mijn cache beschikbaar voor gebruik tijdens het koppelings- of ontkoppelingsproces?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Kan ik meer dan twee caches aan elkaar koppelen?](#can-i-link-more-than-two-caches-together)
- [Kan ik twee caches van verschillende Azure-abonnementen koppelen?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Kan ik twee caches met verschillende formaten koppelen?](#can-i-link-two-caches-with-different-sizes)
- [Kan ik geo-replicatie gebruiken waarbij clustering is ingeschakeld?](#can-i-use-geo-replication-with-clustering-enabled)
- [Kan ik geo-replicatie gebruiken met mijn caches in een VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Wat is het replicatieschema voor georeplicatie van Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Hoe lang duurt georeplicatie?](#how-long-does-geo-replication-replication-take)
- [Is het replicatieherstelpunt gegarandeerd?](#is-the-replication-recovery-point-guaranteed)
- [Kan ik PowerShell of Azure CLI gebruiken om georeplicatie te beheren?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Hoeveel kost het repliceren van mijn gegevens in Azure-regio's?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Waarom is de bewerking mislukt toen ik mijn gekoppelde cache probeerde te verwijderen?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Welke regio moet ik gebruiken voor mijn secundaire gekoppelde cache?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hoe werkt het niet overwerken naar de secundaire gekoppelde cache?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Kan ik geo-replicatie gebruiken met een standaard- of basic-laagcache?

Nee, georeplicatie is alleen beschikbaar voor Premium-lagencaches.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Is mijn cache beschikbaar voor gebruik tijdens het koppelings- of ontkoppelingsproces?

- Bij het koppelen blijft de primaire gekoppelde cache beschikbaar terwijl het koppelingsproces is voltooid.
- Bij het koppelen is de secundaire gekoppelde cache pas beschikbaar als het koppelingsproces is voltooid.
- Bij het ontkoppelen blijven beide caches beschikbaar terwijl het ontkoppelingsproces is voltooid.

### <a name="can-i-link-more-than-two-caches-together"></a>Kan ik meer dan twee caches aan elkaar koppelen?

Nee, u slechts twee caches aan elkaar koppelen.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Kan ik twee caches van verschillende Azure-abonnementen koppelen?

Nee, beide caches moeten in hetzelfde Azure-abonnement zitten.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Kan ik twee caches met verschillende formaten koppelen?

Ja, zolang de secundaire gekoppelde cache groter is dan de primaire gekoppelde cache.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Kan ik geo-replicatie gebruiken waarbij clustering is ingeschakeld?

Ja, zolang beide caches hetzelfde aantal scherven hebben.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Kan ik geo-replicatie gebruiken met mijn caches in een VNET?

Ja, geo-replicatie van caches in VNETs wordt ondersteund met kanttekeningen:

- Geo-replicatie tussen caches in dezelfde VNET wordt ondersteund.
- Geo-replicatie tussen caches in verschillende VNET's wordt ook ondersteund.
  - Als de VNET's zich in dezelfde regio bevinden, u ze verbinden via [VNET-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) of een [VPN-gateway VNET-to-VNET-verbinding.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)
  - Als de VNET's zich in verschillende regio's bevinden, wordt georeplicatie met VNET-peering niet ondersteund vanwege een beperking met interne basislastsbalansen. Zie [Virtual Network - Peering - Vereisten en beperkingen](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)voor meer informatie over VNET-peeringbeperkingen. De aanbevolen oplossing is het gebruik van een VPN Gateway VNET-to-VNET-verbinding.

Met [deze Azure-sjabloon](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)u snel twee geo-gerepliceerde caches implementeren in een VNET dat is verbonden met een VPN-gateway VNET-to-VNET-verbinding.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Wat is het replicatieschema voor georeplicatie van Redis?

Replicatie is continu en asynchroon en gebeurt niet volgens een specifiek schema. Alle schrijfbewerkingen die aan de primaire worden gedaan, worden onmiddellijk en asynchroon gerepliceerd op de secundaire.

### <a name="how-long-does-geo-replication-replication-take"></a>Hoe lang duurt georeplicatie?

Replicatie is incrementeel, asynchroon en continu en de tijd die nodig is, verschilt niet veel van de latentie tussen regio's. Onder bepaalde omstandigheden kan de secundaire cache nodig zijn om de gegevens van de primaire volledige synchronisatie uit te vullen. De replicatietijd is in dit geval afhankelijk van het aantal factoren, zoals: belasting op de primaire cache, beschikbare netwerkbandbreedte en interregiolatentie. We hebben gevonden replicatie tijd voor een volledige 53-GB geo-gerepliceerd paar kan ergens tussen de 5 tot 10 minuten.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Is het replicatieherstelpunt gegarandeerd?

Voor caches in een geo-gerepliceerde modus is persistentie uitgeschakeld. Als een geo-gerepliceerd paar niet is gekoppeld, zoals een door de klant geïnitieerde failover, behoudt de secundaire gekoppelde cache de gesynchroniseerde gegevens tot dat moment. Geen herstelpunt is gegarandeerd in dergelijke situaties.

Als u een herstelpunt wilt verkrijgen, [exporteert u uit](cache-how-to-import-export-data.md#export) beide cache. U later [importeren](cache-how-to-import-export-data.md#import) in de primaire gekoppelde cache.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Kan ik PowerShell of Azure CLI gebruiken om georeplicatie te beheren?

Ja, georeplicatie kan worden beheerd met de Azure-portal, PowerShell of Azure CLI. Zie de [PowerShell-documenten](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) of [Azure CLI-documenten](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest)voor meer informatie.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Hoeveel kost het repliceren van mijn gegevens in Azure-regio's?

Bij het gebruik van geo-replicatie worden gegevens uit de primaire gekoppelde cache gerepliceerd naar de secundaire gekoppelde cache. Er zijn geen kosten verbonden voor de gegevensoverdracht als de twee gekoppelde caches zich in dezelfde regio bevinden. Als de twee gekoppelde caches zich in verschillende regio's bevinden, zijn de kosten voor gegevensoverdracht de netwerkkosten van gegevens die in beide regio's worden verplaatst. Zie [Details bandbreedteprijzen voor](https://azure.microsoft.com/pricing/details/bandwidth/)meer informatie .

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Waarom is de bewerking mislukt toen ik mijn gekoppelde cache probeerde te verwijderen?

Geo-gerepliceerde caches en hun brongroepen kunnen niet worden verwijderd terwijl ze zijn gekoppeld totdat u de koppeling met georeplicatie verwijdert. Als u probeert de resourcegroep te verwijderen die een of beide gekoppelde caches bevat, worden de andere `deleting` bronnen in de resourcegroep verwijderd, `running` maar blijft de brongroep in de status en blijven alle gekoppelde caches in de brongroep in de status behouden. Als u de brongroep en de gekoppelde caches erin volledig wilt verwijderen, ontkoppelt u de koppelingen van de caches zoals beschreven in [Een koppeling met georeplicatie verwijderen.](#remove-a-geo-replication-link)

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Welke regio moet ik gebruiken voor mijn secundaire gekoppelde cache?

In het algemeen wordt aanbevolen dat uw cache bestaat in hetzelfde Azure-gebied als de toepassing die toegang heeft tot de cache. Voor toepassingen met afzonderlijke primaire en fallback-regio's wordt aanbevolen dat uw primaire en secundaire caches in dezelfde regio's bestaan. Zie [Aanbevolen procedures – Azure Paired-regio's](../best-practices-availability-paired-regions.md)voor meer informatie over gekoppelde regio's .

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hoe werkt het niet overwerken naar de secundaire gekoppelde cache?

Automatische failover in Azure-regio's wordt niet ondersteund voor geo-gerepliceerde caches. In een scenario voor noodherstel moeten klanten de volledige toepassingsstack op een gecoördineerde manier in hun back-upregio naar boven halen. Afzonderlijke toepassingsonderdelen zelf laten beslissen wanneer ze zelf naar hun back-ups overschakelen, kan een negatieve invloed hebben op de prestaties. Een van de belangrijkste voordelen van Redis is dat het een zeer lage latentie winkel. Als de hoofdtoepassing van de klant zich in een andere regio bevindt dan de cache, zou de toegevoegde retourtijd een merkbare invloed hebben op de prestaties. Om deze reden voorkomen we dat er automatisch niet meer is mislukt vanwege tijdelijke beschikbaarheidsproblemen.

Als u een door de klant geïnitieerde failover wilt starten, ontkoppelt u eerst de koppelingen van de caches. Wijzig vervolgens uw Redis-client om het verbindingseindpunt van de (voorheen gekoppelde) secundaire cache te gebruiken. Wanneer de twee caches niet zijn gekoppeld, wordt de secundaire cache opnieuw een gewone lees-schrijfcache en accepteert het aanvragen rechtstreeks van Redis-clients.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [azure-cache voor Redis Premium-laag](cache-premium-tier-intro.md).

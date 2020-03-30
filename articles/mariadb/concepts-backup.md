---
title: Back-up maken en herstellen - Azure-database voor MariaDB
description: Meer informatie over automatische back-ups en het herstellen van uw Azure Database voor MariaDB-server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: c4d5a9ca85237bde1277904a478a0b8828fc2b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369240"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Back-upmaken en herstellen in Azure Database voor MariaDB

Azure Database voor MariaDB maakt automatisch serverback-ups en slaat deze op in lokaal redundante of georedundante opslag van de gebruiker. Back-ups kunnen worden gebruikt om de status van de server naar een bepaald tijdstip te herstellen. Back-ups en herstel zijn een essentieel onderdeel van elke bedrijfscontinuïteitsstrategie omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering.

## <a name="backups"></a>Back-ups

Azure Database voor MariaDB maakt volledige, differentiële en transactielogboekbacks. Met deze back-ups u een server herstellen naar elk point-in-time binnen uw geconfigureerde back-upbewaarperiode. De standaardbewaarperiode voor back-ups is zeven dagen. U het optioneel tot 35 dagen configureren. Alle back-ups worden versleuteld met AES 256-bits versleuteling.

Deze back-upbestanden worden niet door de gebruiker belicht en kunnen niet worden geëxporteerd. Deze back-ups kunnen alleen worden gebruikt voor herstelbewerkingen in Azure Database voor MariaDB. U [mysqldump](howto-migrate-dump-restore.md) gebruiken om een database te kopiëren.

### <a name="backup-frequency"></a>Back-upfrequentie

Over het algemeen worden volledige back-ups wekelijks uitgevoerd, worden differentiële back-ups twee keer per dag uitgevoerd en worden back-ups van transactielogboeken om de vijf minuten uitgevoerd. De eerste volledige back-up wordt direct na het maken van een server gepland. De eerste back-up kan langer duren op een grote herstelde server. Het vroegste punt in de tijd dat een nieuwe server kan worden hersteld is het moment waarop de eerste volledige back-up is voltooid.

### <a name="backup-redundancy-options"></a>Redundantieopties voor back-ups

Azure Database voor MariaDB biedt de flexibiliteit om te kiezen tussen lokaal redundante of georedundante back-upopslag in de lagen Algemeen Doel en Geheugengeoptimaliseerd. Wanneer de back-ups worden opgeslagen in georedundante back-upopslag, worden ze niet alleen opgeslagen in het gebied waar uw server wordt gehost, maar worden ze ook gerepliceerd naar een [gekoppeld datacenter.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) Dit biedt een betere bescherming en de mogelijkheid om uw server te herstellen in een andere regio in het geval van een ramp. De klasse Basic biedt alleen lokaal redundante back-upopslag.

> [!IMPORTANT]
> Het configureren van lokaal redundante of georedundante opslag voor back-up is alleen toegestaan tijdens het maken van de server. Zodra de server is ingericht, u de redundantieoptie voor back-upopslag niet meer wijzigen.

### <a name="backup-storage-cost"></a>Kosten voor back-upopslag

Azure Database voor MariaDB biedt tot 100% van uw ingerichte serveropslag als back-upopslag zonder extra kosten. Meestal is dit geschikt voor een back-up retentie van zeven dagen. Er wordt extra back-upopslag in rekening gebracht in GB-maanden.

Als u bijvoorbeeld een server met 250 GB hebt ingericht, beschikt u zonder extra kosten over 250 GB back-upopslag. Opslag van meer dan 250 GB wordt in rekening gebracht.

Ga voor meer informatie over de kosten van back-upopslag naar de [prijspagina van MariaDB.](https://azure.microsoft.com/pricing/details/mariadb/)

## <a name="restore"></a>Herstellen

In Azure Database voor MariaDB maakt het uitvoeren van een herstel een nieuwe server uit de back-ups van de oorspronkelijke server en worden alle databases in de server hersteld.

Er zijn twee soorten herstel beschikbaar:

- **Point-in-time restore** is beschikbaar met een back-up redundantieoptie en maakt een nieuwe server in dezelfde regio als uw oorspronkelijke server met behulp van de combinatie van volledige en transactielogboekback-ups.
- **Geo-restore** is alleen beschikbaar als u uw server hebt geconfigureerd voor georedundante opslag en u uw server herstellen naar een andere regio met behulp van de meest recente back-up.

De geschatte hersteltijd is afhankelijk van verschillende factoren, waaronder de databasegrootte, de grootte van het transactielogboek, de netwerkbandbreedte en het totale aantal databases dat tegelijkertijd in dezelfde regio herstelt. De hersteltijd is meestal minder dan 12 uur.

> [!IMPORTANT]
> Verwijderde servers **kunnen niet** worden hersteld. Als u de server verwijdert, worden alle databases die tot de server behoren ook verwijderd en kunnen ze niet worden hersteld. Om serverbronnen, na implementatie, te beschermen tegen onbedoelde verwijdering of onverwachte wijzigingen, kunnen beheerders [beheervergrendelingen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)gebruiken.

### <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

Onafhankelijk van uw back-upredundantieoptie, u een herstel uitvoeren naar elk moment binnen uw back-upbewaarperiode. Er wordt een nieuwe server gemaakt in dezelfde Azure-regio als de oorspronkelijke server. Het is gemaakt met de configuratie van de oorspronkelijke server voor de prijscategorie, compute generatie, aantal vCores, opslaggrootte, back-up bewaarperiode en back-up redundantie optie.

Point-in-time restore is handig in meerdere scenario's. Wanneer een gebruiker bijvoorbeeld per ongeluk gegevens verwijdert, een belangrijke tabel of database laat vallen of als een toepassing per ongeluk goede gegevens overschrijft met slechte gegevens als gevolg van een toepassingsfout.

Mogelijk moet u wachten tot de volgende back-up van het transactielogboek moet worden gemaakt voordat u in de laatste vijf minuten herstellen naar een punt in de tijd.

### <a name="geo-restore"></a>Geo-herstel

U een server herstellen naar een andere Azure-regio waar de service beschikbaar is als u uw server hebt geconfigureerd voor georedundante back-ups. Geo-restore is de standaardhersteloptie wanneer uw server niet beschikbaar is vanwege een incident in het gebied waar de server wordt gehost. Als een grootschalig incident in een regio leidt tot onbeschikbaarheid van uw databasetoepassing, u een server herstellen van de georedundante back-ups naar een server in een andere regio. Geo-restore maakt gebruik van de meest recente back-up van de server. Er is een vertraging tussen het moment waarop een back-up wordt genomen en wanneer deze wordt gerepliceerd naar een andere regio. Deze vertraging kan oplopen tot een uur, dus als zich een ramp voordoet, kan er tot een uur gegevensverlies zijn.

Tijdens geo-restore zijn de serverconfiguraties die kunnen worden gewijzigd compute generation, vCore, back-upbewaarperiode en redundantieopties voor back-ups. Het wijzigen van de prijslaag (Basic, General Purpose of Memory Optimized) of de opslaggrootte tijdens geo-restore wordt niet ondersteund.

### <a name="perform-post-restore-tasks"></a>Taken na het herstellen uitvoeren

Na een herstel van een herstelmechanisme moet u de volgende taken uitvoeren om uw gebruikers en toepassingen weer aan de praat te krijgen:

- Als de nieuwe server bedoeld is om de oorspronkelijke server te vervangen, leidt u clients en clienttoepassingen om naar de nieuwe server
- Zorg ervoor dat er geschikte VNet-regels zijn voor gebruikers om verbinding te maken. Deze regels worden niet overgekopieerd van de oorspronkelijke server.
- Zorg ervoor dat de juiste aanmeldingen en machtigingen op databaseniveau zijn ingevoerd
- Waarschuwingen configureren, indien van toepassing

## <a name="next-steps"></a>Volgende stappen

- Zie het [overzicht bedrijfscontinuïteit](concepts-business-continuity.md)voor meer informatie over bedrijfscontinuïteit.
- Zie [Server herstellen naar een point-in-time met de Azure-portal met de Azure-portal.](howto-restore-server-portal.md)
- Zie Server herstellen naar een point-in-time met CLI als u wilt herstellen naar een [point-in-time met behulp van CLI.](howto-restore-server-cli.md)

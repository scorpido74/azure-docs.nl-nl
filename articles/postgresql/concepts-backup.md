---
title: Backup en Restore-Azure Database for PostgreSQL-één server
description: Meer informatie over automatische back-ups en het herstellen van uw Azure Database for PostgreSQL server-één server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3e6dfd5882e49ad903e8cff6f0ec7f3d6bd4a8b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77619628"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Back-ups maken en herstellen in Azure Database for PostgreSQL-één server

Azure Database for PostgreSQL maakt automatisch server back-ups en slaat ze op in een door de gebruiker geconfigureerde lokaal redundante of geografisch redundante opslag. Back-ups kunnen worden gebruikt om de status van de server naar een bepaald tijdstip te herstellen. Backup en Restore zijn een essentieel onderdeel van een strategie voor bedrijfs continuïteit omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering.

## <a name="backups"></a>Back-ups

Azure Database for PostgreSQL maakt back-ups van de gegevens bestanden en het transactie logboek. Afhankelijk van de ondersteunde maximale opslag grootte, nemen we volledige en differentiële back-ups (4 TB Maxi maal opslag servers) of momentopname back-ups (Maxi maal 16 TB aan opslag servers). Met deze back-ups kunt u een server herstellen naar elk gewenst moment binnen de geconfigureerde back-upperiode. De standaard retentie periode voor back-ups is zeven dagen. U kunt deze optioneel configureren tot 35 dagen. Alle back-ups worden versleuteld met AES 256-bits versleuteling.

Deze back-upbestanden kunnen niet worden geëxporteerd. De back-ups kunnen alleen worden gebruikt voor herstel bewerkingen in Azure Database for PostgreSQL. U kunt [pg_dump](howto-migrate-using-dump-and-restore.md) gebruiken om een Data Base te kopiëren.

### <a name="backup-frequency"></a>Back-upfrequentie

Over het algemeen worden volledige back-ups wekelijks uitgevoerd, differentiële back-ups twee keer per dag voor servers met een Maxi maal ondersteunde opslag van 4 TB. Back-ups van momentopnamen worden minstens één keer per dag uitgevoerd voor servers die ondersteuning bieden voor maximaal 16 TB opslag. Back-ups van transactielogboeken worden voor beide gevallen elke vijf minuten uitgevoerd. De eerste moment opname van een volledige back-up wordt onmiddellijk gepland nadat een server is gemaakt. De eerste volledige back-up kan langer duren op een grote herstelde server. Het vroegste tijdstip waarop een nieuwe server kan worden hersteld, is het tijdstip waarop de eerste volledige back-up is voltooid. Omdat moment opnamen direct worden ondersteund, kunnen servers met ondersteuning tot wel 16 TB aan opslag ruimte worden hersteld.

### <a name="backup-redundancy-options"></a>Opties voor back-upredundantie

Azure Database for PostgreSQL biedt de flexibiliteit om te kiezen tussen lokaal redundante of geografisch redundante back-upopslag in de lagen Algemeen en geoptimaliseerd voor geheugen. Wanneer de back-ups worden opgeslagen in geografisch redundante back-upopslag, worden ze niet alleen opgeslagen in de regio waarin uw server wordt gehost, maar worden ook gerepliceerd naar een [gekoppeld Data Center](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Dit biedt betere beveiliging en de mogelijkheid om uw server in een andere regio te herstellen in het geval van een ramp. De laag basis biedt alleen lokaal redundante back-upopslag.

> [!IMPORTANT]
> Het configureren van lokaal redundante of geografisch redundante opslag voor back-up is alleen toegestaan tijdens het maken van de server. Zodra de server is ingericht, kunt u de optie voor opslag redundantie van back-ups niet meer wijzigen.

### <a name="backup-storage-cost"></a>Kosten voor back-upopslag

Azure Database for PostgreSQL biedt Maxi maal 100% van uw ingerichte Server opslag als back-upopslag zonder extra kosten. Dit is normaal gesp roken geschikt voor het bewaren van een back-up van zeven dagen. Alle extra back-upopslag wordt in GB per maand in rekening gebracht.

Als u bijvoorbeeld een server hebt ingericht met 250 GB, hebt u 250 GB aan back-upopslag zonder extra kosten. Voor opslag van meer dan 250 GB worden kosten in rekening gebracht.

## <a name="restore"></a>Herstellen

In Azure Database for PostgreSQL wordt met het uitvoeren van een herstel bewerking een nieuwe server gemaakt van de back-ups van de oorspronkelijke server.

Er zijn twee soorten herstel beschikbaar:

- **Herstel naar** een bepaald tijdstip is beschikbaar met de optie redundantie van back-ups en maakt een nieuwe server in dezelfde regio als de oorspronkelijke server.
- **Geo-Restore** is alleen beschikbaar als u uw server hebt geconfigureerd voor geo-redundante opslag en u de server kunt herstellen naar een andere regio.

De geschatte duur van de herstel bewerking is afhankelijk van verschillende factoren, zoals de grootte van de data base, het transactie logboek, de netwerk bandbreedte en het totale aantal data bases dat op hetzelfde moment in dezelfde regio wordt hersteld. De herstel tijd is doorgaans minder dan 12 uur.

> [!IMPORTANT]
> Verwijderde servers **kunnen niet** worden hersteld. Als u de server verwijdert, worden ook alle data bases die deel uitmaken van de server, verwijderd en kunnen deze niet worden hersteld. Beheerders kunnen gebruikmaken van [beheer vergrendelingen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)om Server bronnen te beveiligen, na implementatie van onopzettelijk verwijderen of onverwachte wijzigingen.

### <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

Onafhankelijk van de optie voor de redundantie van de back-up kunt u een herstel bewerking uitvoeren op elk gewenst moment binnen de retentie periode van de back-up. Er wordt een nieuwe server gemaakt in dezelfde Azure-regio als de oorspronkelijke server. Het wordt gemaakt met de oorspronkelijke server configuratie voor de prijs categorie, het berekenen van de berekening, het aantal vCores, de opslag grootte, de Bewaar periode voor back-ups en de optie voor de redundantie van back-ups.

Herstel naar een bepaald tijdstip is handig in meerdere scenario's. Wanneer een gebruiker bijvoorbeeld per ongeluk gegevens verwijdert, een belang rijke tabel of data base weglaat of als een toepassing per ongeluk goede gegevens overschrijft door een toepassings defect.

Mogelijk moet u wachten totdat de back-up van het volgende transactie logboek wordt gemaakt voordat u de laatste vijf minuten kunt herstellen naar een bepaald tijdstip.

### <a name="geo-restore"></a>Geo-herstel

U kunt een server herstellen naar een andere Azure-regio waar de service beschikbaar is als u uw server hebt geconfigureerd voor geografisch redundante back-ups. Servers die ondersteuning bieden voor Maxi maal 4 TB aan opslag kunnen worden hersteld naar de geografische paar regio, of op elke regio die ondersteuning biedt voor Maxi maal 16 TB aan opslag ruimte. Voor servers die Maxi maal 16 TB aan opslag ondersteunen, kunnen geo-back-ups worden hersteld in elke regio die ook ondersteuning biedt voor 16 TB-servers. Bekijk de [prijs categorieën van Azure data base for PostgeSQL](concepts-pricing-tiers.md) voor de lijst met ondersteunde regio's.

Geo-Restore is de standaard herstel optie als uw server niet beschikbaar is vanwege een incident in de regio waarin de server wordt gehost. Als een grootschalig incident in een regio resulteert in niet-beschik baarheid van uw database toepassing, kunt u een server herstellen van de geo-redundante back-ups naar een server in een andere regio. Er is een vertraging tussen het moment waarop een back-up wordt gemaakt en wanneer deze wordt gerepliceerd naar een andere regio. Deze vertraging kan tot een uur duren, dus als er sprake is van een nood geval, kan er Maxi maal één uur gegevens verlies zijn.

Tijdens de geo-herstel bewerking kunnen de volgende server configuraties worden gewijzigd: Compute Generation, vCore, Bewaar periode voor back-up en opties voor back-redundantie. Het wijzigen van de prijs categorie (Basic, Algemeen of Optimized memory) of opslag grootte wordt niet ondersteund.

### <a name="perform-post-restore-tasks"></a>Taken na herstel uitvoeren

Na een herstel na een van beide herstel mechanismen moet u de volgende taken uitvoeren om uw gebruikers en toepassingen back-ups te maken en uit te voeren:

- Als de nieuwe server de oorspronkelijke server moet vervangen, kunt u clients en client toepassingen omleiden naar de nieuwe server
- Zorg ervoor dat de juiste firewall-en VNet-regels op server niveau aanwezig zijn voor gebruikers om verbinding te maken. Deze regels worden niet van de oorspronkelijke server gekopieerd.
- Zorg ervoor dat de juiste aanmeldingen en machtigingen op database niveau aanwezig zijn
- Waarschuwingen configureren, indien van toepassing

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het herstellen met behulp van [de Azure Portal](howto-restore-server-portal.md).
- Meer informatie over het herstellen met behulp van [de Azure cli](howto-restore-server-cli.md).
- Meer informatie over bedrijfs continuïteit vindt u in het [overzicht van bedrijfs continuïteit](concepts-business-continuity.md).

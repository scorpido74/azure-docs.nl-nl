---
title: Back-up en herstellen-Azure Database for MySQL
description: Meer informatie over automatische back-ups en het herstellen van uw Azure Database for MySQL-server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 3f24e3538f05ca3b6a27907e0b794705402fce7c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285438"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Back-ups maken en herstellen in Azure Database for MySQL

Azure Database for MySQL maakt automatisch server back-ups en slaat ze op in een door de gebruiker geconfigureerde lokaal redundante of geografisch redundante opslag. Back-ups kunnen worden gebruikt om de status van de server naar een bepaald tijdstip te herstellen. Backup en Restore zijn een essentieel onderdeel van een strategie voor bedrijfs continuïteit omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering.

## <a name="backups"></a>Back-ups

Azure Database for MySQL maakt back-ups van de gegevens bestanden en het transactie logboek. Afhankelijk van de ondersteunde maximale opslag grootte, nemen we volledige en differentiële back-ups (Maxi maal 4 TB opslag servers) of momentopname back-ups (Maxi maal 16 TB aan opslag servers). Met deze back-ups kunt u een server herstellen naar elk gewenst moment binnen de geconfigureerde back-upperiode. De standaard retentie periode voor back-ups is zeven dagen. U kunt [deze optioneel configureren](howto-restore-server-portal.md#set-backup-configuration) tot 35 dagen. Alle back-ups worden versleuteld met AES 256-bits versleuteling.

Deze back-upbestanden zijn niet beschikbaar voor gebruikers en kunnen niet worden geëxporteerd. Deze back-ups kunnen alleen worden gebruikt voor herstel bewerkingen in Azure Database for MySQL. U kunt [mysqldump](concepts-migrate-dump-restore.md) gebruiken om een Data Base te kopiëren.

### <a name="backup-frequency"></a>Back-upfrequentie

#### <a name="servers-with-up-to-4-tb-storage"></a>Servers met Maxi maal 4 TB opslag

Voor servers die Maxi maal 4 TB aan maximale opslag ondersteunen, worden er één keer per week volledige back-ups uitgevoerd. Differentiële back-ups worden twee keer per dag uitgevoerd. Back-ups van transactie logboeken worden om de vijf minuten uitgevoerd.

#### <a name="servers-with-up-to-16-tb-storage"></a>Servers met Maxi maal 16 TB opslag
In een subset van [Azure-regio's](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage)kan alle nieuw ingerichte servers Maxi maal 16 TB opslag ondersteunen. Back-ups op deze grote opslag servers zijn gebaseerd op moment opnamen. De eerste volledige back-up van de moment opname wordt onmiddellijk gepland nadat een server is gemaakt. De eerste volledige back-up van de moment opname wordt bewaard als basis back-up van de server. Volgende back-ups voor moment opnamen zijn alleen differentiële back-ups. 

Back-ups van differentiële moment opnamen worden minstens één keer per dag uitgevoerd. Back-ups van differentiële moment opnamen worden niet uitgevoerd volgens een vast schema. Back-ups van differentiële moment opnamen worden elke 24 uur uitgevoerd, tenzij het transactie logboek (binlog in MySQL) 50-GB overschrijdt sinds de laatste differentiële back-up. In een dag zijn Maxi maal zes differentiële moment opnamen toegestaan. 

Back-ups van transactie logboeken worden om de vijf minuten uitgevoerd. 

### <a name="backup-retention"></a>Retentie van back-ups

Back-ups worden bewaard op basis van de instelling voor de Bewaar periode voor back-ups op de server. U kunt een Bewaar periode van 7 tot 35 dagen selecteren. De standaard Bewaar periode is 7 dagen. U kunt de Bewaar periode instellen tijdens het maken van de server of later door de back-upconfiguratie bij te werken met [Azure Portal](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal#set-backup-configuration) of [Azure cli](https://docs.microsoft.com/azure/mysql/howto-restore-server-cli#set-backup-configuration). 

De Bewaar periode voor back-ups bepaalt hoe ver terug in de tijd een herstel naar een bepaald tijdstip kan worden opgehaald, omdat het is gebaseerd op back-ups die beschikbaar zijn. De Bewaar periode voor back-ups kan ook worden behandeld als een herstel venster van een Restore-perspectief. Alle back-ups die zijn vereist voor het uitvoeren van een herstel naar een bepaald tijdstip binnen de Bewaar periode voor back-ups, worden bewaard in back-upopslag. Als de Bewaar periode voor back-ups bijvoorbeeld is ingesteld op 7 dagen, wordt het herstel venster als laatste 7 dagen beschouwd. In dit scenario blijven alle back-ups die nodig zijn om de server in de afgelopen 7 dagen te herstellen, behouden. Met een Bewaar periode voor back-ups van zeven dagen:
- Servers met Maxi maal 4 TB opslag behouden Maxi maal twee volledige back-ups van de data base, alle differentiële back-ups en back-ups van transactie logboeken die zijn uitgevoerd sinds de eerste volledige back-up van de data base.
-   Servers met Maxi maal 16 TB opslag behouden de volledige database momentopname, alle differentiële moment opnamen en back-ups van transactie Logboeken in de afgelopen 8 dagen.

### <a name="backup-redundancy-options"></a>Opties voor back-upredundantie

Azure Database for MySQL biedt de flexibiliteit om te kiezen tussen lokaal redundante of geografisch redundante back-upopslag in de lagen Algemeen en geoptimaliseerd voor geheugen. Wanneer de back-ups worden opgeslagen in geografisch redundante back-upopslag, worden ze niet alleen opgeslagen in de regio waarin uw server wordt gehost, maar worden ook gerepliceerd naar een [gekoppeld Data Center](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Dit biedt betere beveiliging en de mogelijkheid om uw server in een andere regio te herstellen in het geval van een ramp. De laag basis biedt alleen lokaal redundante back-upopslag.

> [!IMPORTANT]
> Het configureren van lokaal redundante of geografisch redundante opslag voor back-up is alleen toegestaan tijdens het maken van de server. Zodra de server is ingericht, kunt u de optie voor opslag redundantie van back-ups niet meer wijzigen.

### <a name="backup-storage-cost"></a>Kosten voor back-upopslag

Azure Database for MySQL biedt Maxi maal 100% van uw ingerichte Server opslag als back-upopslag zonder extra kosten. Alle extra back-upopslag wordt in GB per maand in rekening gebracht. Als u bijvoorbeeld een server hebt ingericht met 250 GB opslag, hebt u 250 GB aan extra opslag ruimte beschikbaar voor Server back-ups zonder extra kosten. Opslag die voor back-250 ups wordt gebruikt, wordt in rekening gebracht volgens het [prijs model](https://azure.microsoft.com/pricing/details/mysql/). 

U kunt de metrische [back-upopslag](concepts-monitoring.md) gebruiken in azure monitor beschikbaar via de Azure Portal om de back-upopslag te bewaken die door een server wordt gebruikt. De metrische back-upopslagwaarde vertegenwoordigt de som van de opslag die wordt gebruikt door alle back-ups van de volledige data base, differentiële back-ups en logboek back-ups die worden bewaard op basis van de Bewaar periode voor back-ups die is ingesteld voor de server. De frequentie van de back-ups is service beheerd en wordt eerder uitgelegd. Zware transactionele activiteit op de server kan ertoe leiden dat het gebruik van back-ups wordt verg root onafhankelijk van de totale database grootte. Voor geo-redundante opslag is het gebruik van back-upopslag twee keer zo dat van de lokaal redundante opslag. 

De belangrijkste manier om de opslag kosten voor back-ups te beheren, is door de juiste Bewaar periode voor back-ups in te stellen en de opties voor de juiste back-upredundantie te kiezen om te voldoen aan de gewenste herstel doelen. U kunt een Bewaar periode van 7 tot 35 dagen selecteren. Algemeen-servers en geoptimaliseerd voor geheugen kunnen ervoor kiezen om geografisch redundante opslag te hebben voor back-ups.

## <a name="restore"></a>Herstellen

In Azure Database for MySQL wordt met het uitvoeren van een herstel bewerking een nieuwe server gemaakt van de back-ups van de oorspronkelijke server en worden alle data bases die zich op de server bevinden, hersteld.

Er zijn twee soorten herstel beschikbaar:

- **Herstel naar** een bepaald tijdstip is beschikbaar met de optie voor redundantie van back-ups en maakt een nieuwe server in dezelfde regio als de oorspronkelijke server die gebruikmaakt van de combi natie van volledige en transactie logboek back-ups.
- **Geo-Restore** is alleen beschikbaar als u uw server hebt geconfigureerd voor geo-redundante opslag en u uw server naar een andere regio kunt herstellen met de meest recente back-up die u hebt gemaakt.

De geschatte duur van de herstel bewerking is afhankelijk van verschillende factoren, zoals de grootte van de data base, het transactie logboek, de netwerk bandbreedte en het totale aantal data bases dat op hetzelfde moment in dezelfde regio wordt hersteld. De herstel tijd is doorgaans minder dan 12 uur.

> [!IMPORTANT]
> Verwijderde servers **kunnen niet** worden hersteld. Als u de server verwijdert, worden ook alle data bases die deel uitmaken van de server, verwijderd en kunnen deze niet worden hersteld. Beheerders kunnen gebruikmaken van [beheer vergrendelingen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)om Server bronnen te beveiligen, na implementatie van onopzettelijk verwijderen of onverwachte wijzigingen.

### <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

Onafhankelijk van de optie voor de redundantie van de back-up kunt u een herstel bewerking uitvoeren op elk gewenst moment binnen de retentie periode van de back-up. Er wordt een nieuwe server gemaakt in dezelfde Azure-regio als de oorspronkelijke server. Het wordt gemaakt met de oorspronkelijke server configuratie voor de prijs categorie, het berekenen van de berekening, het aantal vCores, de opslag grootte, de Bewaar periode voor back-ups en de optie voor de redundantie van back-ups.

Herstel naar een bepaald tijdstip is handig in meerdere scenario's. Wanneer een gebruiker bijvoorbeeld per ongeluk gegevens verwijdert, een belang rijke tabel of data base weglaat of als een toepassing per ongeluk goede gegevens overschrijft door een toepassings defect.

Mogelijk moet u wachten totdat de back-up van het volgende transactie logboek wordt gemaakt voordat u de laatste vijf minuten kunt herstellen naar een bepaald tijdstip.

### <a name="geo-restore"></a>Geo-herstel

U kunt een server herstellen naar een andere Azure-regio waar de service beschikbaar is als u uw server hebt geconfigureerd voor geografisch redundante back-ups. Servers die ondersteuning bieden voor Maxi maal 4 TB aan opslag kunnen worden hersteld naar de geografische paar regio, of op elke regio die ondersteuning biedt voor Maxi maal 16 TB aan opslag ruimte. Voor servers die Maxi maal 16 TB aan opslag ondersteunen, kunnen geo-back-ups worden hersteld in elke regio die ook ondersteuning biedt voor 16 TB-servers. Bekijk [Azure database for MySQL prijs categorieën](concepts-pricing-tiers.md) voor de lijst met ondersteunde regio's.

Geo-Restore is de standaard herstel optie als uw server niet beschikbaar is vanwege een incident in de regio waarin de server wordt gehost. Als een grootschalig incident in een regio resulteert in niet-beschik baarheid van uw database toepassing, kunt u een server herstellen van de geo-redundante back-ups naar een server in een andere regio. Geo-Restore maakt gebruik van de meest recente back-up van de server. Er is een vertraging tussen het moment waarop een back-up wordt gemaakt en wanneer deze wordt gerepliceerd naar een andere regio. Deze vertraging kan tot een uur duren, dus als er sprake is van een nood geval, kan er Maxi maal één uur gegevens verlies zijn.

Tijdens de geo-herstel bewerking kunnen de volgende server configuraties worden gewijzigd: Compute Generation, vCore, Bewaar periode voor back-up en opties voor back-redundantie. Het wijzigen van de prijs categorie (Basic, Algemeen of Optimized memory) of opslag grootte tijdens geo-Restore wordt niet ondersteund.

### <a name="perform-post-restore-tasks"></a>Taken na herstel uitvoeren

Na een herstel na een van beide herstel mechanismen moet u de volgende taken uitvoeren om uw gebruikers en toepassingen back-ups te maken en uit te voeren:

- Als de nieuwe server de oorspronkelijke server moet vervangen, kunt u clients en client toepassingen omleiden naar de nieuwe server
- Zorg ervoor dat de juiste VNet-regels aanwezig zijn voor gebruikers om verbinding te maken. Deze regels worden niet van de oorspronkelijke server gekopieerd.
- Zorg ervoor dat de juiste aanmeldingen en machtigingen op database niveau aanwezig zijn
- Waarschuwingen configureren, indien van toepassing

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over bedrijfs continuïteit vindt u in het [overzicht van bedrijfs continuïteit](concepts-business-continuity.md).
- Als u wilt herstellen naar een bepaald tijdstip met behulp van de Azure Portal, raadpleegt u [server herstellen naar een bepaald tijdstip met behulp van de Azure Portal](howto-restore-server-portal.md).
- Als u wilt herstellen naar een bepaald tijdstip met behulp van Azure CLI, raadpleegt u [server herstellen naar een bepaald tijdstip met behulp van CLI](howto-restore-server-cli.md).

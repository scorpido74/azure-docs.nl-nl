---
title: Bedrijfscontinuïteit - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt de bedrijfscontinuïteit beschreven (point-in-time restore, datacenteroutage, geo-restore) bij het gebruik van Azure Database voor PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: afa03399933bdc8bd8ff869125955cfd9e0abecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981917"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Overzicht van bedrijfscontinuïteit met Azure Database voor PostgreSQL - Single Server

In dit overzicht worden de mogelijkheden beschreven die Azure Database voor PostgreSQL biedt voor bedrijfscontinuïteit en noodherstel. Meer informatie over opties voor het herstellen van storende gebeurtenissen die kunnen leiden tot gegevensverlies of waardoor uw database en toepassing niet meer beschikbaar zijn. Lees wat u moet doen wanneer een gebruikers- of toepassingsfout van invloed is op de gegevensintegriteit, een Azure-regio een storing heeft of uw toepassing onderhoud vereist.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Functies die u gebruiken om bedrijfscontinuïteit te bieden

Azure Database voor PostgreSQL biedt functies voor bedrijfscontinuïteit, waaronder geautomatiseerde back-ups en de mogelijkheid voor gebruikers om geo-restore te starten. Elk heeft verschillende kenmerken voor Geschatte Hersteltijd (ERT) en potentieel gegevensverlies. Zodra u deze opties begrijpt, u kiezen tussen hen, en gebruik ze samen voor verschillende scenario's. Bij het ontwikkelen van uw bedrijfscontinuïteitsplan moet u de maximaal aanvaardbare tijd begrijpen voordat de toepassing volledig herstelt na de verstorende gebeurtenis - dit is uw Recovery Time Objective (RTO). U moet ook de maximale hoeveelheid recente gegevensupdates (tijdsinterval) begrijpen die de toepassing kan verdragen verliezen bij het herstellen na de storende gebeurtenis - dit is uw Herstelpuntdoelstelling (RPO).

In de volgende tabel worden de ERT en De RPO vergeleken voor de beschikbare functies:

| **Mogelijkheid** | **Basic** | **Algemeen doel** | **Geoptimaliseerd geheugen** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Herstel naar een bepaald tijdstip vanuit back-up | Elk herstelpunt binnen de bewaartermijn | Elk herstelpunt binnen de bewaartermijn | Elk herstelpunt binnen de bewaartermijn |
| Geo-herstel van geo-gerepliceerde back-ups | Niet ondersteund | ERT < 12 uur<br/>RPO < 1 uur | ERT < 12 uur<br/>RPO < 1 uur |

> [!IMPORTANT]
> Verwijderde servers **kunnen niet** worden hersteld. Als u de server verwijdert, worden alle databases die tot de server behoren ook verwijderd en kunnen ze niet worden hersteld. Gebruik [Azure-bronvergrendeling](../azure-resource-manager/management/lock-resources.md) om te voorkomen dat uw server per ongeluk wordt verwijderd.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Een server herstellen na een gebruikers- of toepassingsfout

U de back-ups van de service gebruiken om een server te herstellen van verschillende storende gebeurtenissen. Een gebruiker kan per ongeluk bepaalde gegevens verwijderen, per ongeluk een belangrijke tabel laten vallen of zelfs een hele database laten vallen. Een toepassing kan per ongeluk goede gegevens overschrijven met slechte gegevens als gevolg van een toepassingsfout, enzovoort.

U **een point-in-time-herstel** uitvoeren om een kopie van uw server te maken op een bekend goed moment. Dit punt moet binnen de back-upbewaarperiode zijn die u voor uw server hebt geconfigureerd. Nadat de gegevens zijn hersteld naar de nieuwe server, u de oorspronkelijke server vervangen door de nieuw herstelde server of de benodigde gegevens van de herstelde server naar de oorspronkelijke server kopiëren.

## <a name="recover-from-an-azure-data-center-outage"></a>Herstellen van een azure-datacenterstoring

Hoewel zeldzaam, kan er een storing optreden in een Azure-datacenter. Wanneer er een storing optreedt, veroorzaakt dit een bedrijfsonderbreking die slechts enkele minuten kan duren, maar uren kan duren.

Een optie is om te wachten tot uw server weer online komt wanneer de datacenterstoring voorbij is. Dit werkt voor toepassingen die het zich kunnen veroorloven om de server een bepaalde periode offline te hebben, bijvoorbeeld een ontwikkelomgeving. Wanneer een datacenter een storing heeft, weet u niet hoe lang de storing kan duren, dus deze optie werkt alleen als u uw server een tijdje niet nodig hebt.

## <a name="geo-restore"></a>Geo-herstel

De functie geo-herstel herstelt de server met behulp van georedundante back-ups. De back-ups worden gehost in het [gekoppelde gebied](../best-practices-availability-paired-regions.md)van uw server. U deze back-ups herstellen naar een andere regio. Met de geo-restore wordt een nieuwe server gemaakt met de gegevens van de back-ups. Meer informatie over geo-herstel van het [artikel Back-upen en concepten herstellen](concepts-backup.md).

> [!IMPORTANT]
> Geo-restore is alleen mogelijk als u de server hebt voorzien van georedundante back-upopslag. Als u wilt overschakelen van lokaal redundante naar georedundante back-ups voor een bestaande server, moet u een dump nemen met behulp van pg_dump van uw bestaande server en deze herstellen naar een nieuw gemaakte server die is geconfigureerd met georedundante back-ups.

## <a name="cross-region-read-replicas"></a>Lees-replica's in verschillende regio's
U cross-region read replica's gebruiken om uw bedrijfscontinuïteit en planning voor noodherstel te verbeteren. Leesreplica's worden asynchroon bijgewerkt met behulp van de fysieke replicatietechnologie van PostgreSQL. Meer informatie over leesreplica's, beschikbare regio's en hoe u falen in het [artikel Leesreplica's concepten](concepts-read-replicas.md). 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [geautomatiseerde back-ups in Azure Database voor PostgreSQL](concepts-backup.md). 
- Meer informatie over het herstellen met behulp van [de Azure-portal](howto-restore-server-portal.md) of [de Azure CLI](howto-restore-server-cli.md).
- Meer informatie over [gelezen replica's in Azure Database voor PostgreSQL](concepts-read-replicas.md).
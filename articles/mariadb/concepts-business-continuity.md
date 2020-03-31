---
title: Bedrijfscontinuïteit - Azure Database voor MariaDB
description: Meer informatie over bedrijfscontinuïteit (point-in-time restore, datacenter outage, geo-restore) bij het gebruik van Azure Database voor MariaDB service.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c01e0df1f420c8489ca3445d9fa025b251a870f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532388"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Inzicht in de bedrijfscontinuïteit in Azure Database voor MariaDB

In dit artikel worden de mogelijkheden beschreven die Azure Database voor MariaDB biedt voor bedrijfscontinuïteit en herstel na noodgevallen. Meer informatie over opties voor het herstellen van storende gebeurtenissen die kunnen leiden tot gegevensverlies of waardoor uw database en toepassing niet meer beschikbaar zijn. Lees wat u moet doen wanneer een gebruikers- of toepassingsfout van invloed is op de gegevensintegriteit, een Azure-regio een storing heeft of uw toepassing onderhoud vereist.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Functies die u gebruiken om bedrijfscontinuïteit te bieden

Azure Database voor MariaDB biedt functies voor bedrijfscontinuïteit, waaronder geautomatiseerde back-ups en de mogelijkheid voor gebruikers om geo-herstel te starten. Elk heeft verschillende kenmerken voor Geschatte Hersteltijd (ERT) en potentieel gegevensverlies. Zodra u deze opties begrijpt, u kiezen tussen hen, en gebruik ze samen voor verschillende scenario's. Bij het ontwikkelen van uw bedrijfscontinuïteitsplan moet u de maximaal aanvaardbare tijd begrijpen voordat de toepassing volledig herstelt na de verstorende gebeurtenis - dit is uw Recovery Time Objective (RTO). U moet ook de maximale hoeveelheid recente gegevensupdates (tijdsinterval) begrijpen die de toepassing kan verdragen verliezen bij het herstellen na de storende gebeurtenis - dit is uw Herstelpuntdoelstelling (RPO).

In de volgende tabel worden de ERT en De RPO vergeleken voor de beschikbare functies:

| **Mogelijkheid** | **Basic** | **Algemeen doel** | **Geoptimaliseerd geheugen** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Herstel naar een bepaald tijdstip vanuit back-up | Elk herstelpunt binnen de bewaartermijn | Elk herstelpunt binnen de bewaartermijn | Elk herstelpunt binnen de bewaartermijn |
| Geo-herstel van geo-gerepliceerde back-ups | Niet ondersteund | ERT < 12 uur<br/>RPO < 1 uur | ERT < 12 uur<br/>RPO < 1 uur |

> [!IMPORTANT]
> Als u de server verwijdert, worden alle databases in de server ook verwijderd en kunnen ze niet worden hersteld. U een verwijderde server niet herstellen.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Een server herstellen na een gebruikers- of toepassingsfout

U de back-ups van de service gebruiken om een server te herstellen van verschillende storende gebeurtenissen. Een gebruiker kan per ongeluk bepaalde gegevens verwijderen, per ongeluk een belangrijke tabel laten vallen of zelfs een hele database laten vallen. Een toepassing kan per ongeluk goede gegevens overschrijven met slechte gegevens als gevolg van een toepassingsfout, enzovoort.

U een point-in-time-herstel uitvoeren om een kopie van uw server te maken op een bekend goed moment. Dit punt moet binnen de back-upbewaarperiode zijn die u voor uw server hebt geconfigureerd. Nadat de gegevens zijn hersteld naar de nieuwe server, u de oorspronkelijke server vervangen door de nieuw herstelde server of de benodigde gegevens van de herstelde server naar de oorspronkelijke server kopiëren.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Herstellen van een azure-storing in het regionale datacenter

Hoewel zeldzaam, kan er een storing optreden in een Azure-datacenter. Wanneer er een storing optreedt, veroorzaakt dit een bedrijfsonderbreking die slechts enkele minuten kan duren, maar uren kan duren.

Een optie is om te wachten tot uw server weer online komt wanneer de datacenterstoring voorbij is. Dit werkt voor toepassingen die het zich kunnen veroorloven om de server een bepaalde periode offline te hebben, bijvoorbeeld een ontwikkelomgeving. Wanneer het datacenter een storing heeft, weet u niet hoe lang de storing kan duren, dus deze optie werkt alleen als u uw server een tijdje niet nodig hebt.

De andere optie is om de Azure Database te gebruiken voor de geo-restore-functie van MariaDB die de server herstelt met behulp van georedundante back-ups. Deze back-ups zijn toegankelijk, zelfs wanneer de regio waarin uw server wordt gehost offline is. U deze back-ups herstellen naar een andere regio en uw server weer online zetten.

> [!IMPORTANT]
> Geo-restore is alleen mogelijk als u de server hebt voorzien van georedundante back-upopslag.

## <a name="next-steps"></a>Volgende stappen

- Zie [Back-ups in Azure Database voor MariaDB voor](concepts-backup.md)meer informatie over de geautomatiseerde back-ups.
- Zie [Database herstellen naar een tijdstip met de Azure-portal met behulp van de Azure-portal.](howto-restore-server-portal.md)

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->

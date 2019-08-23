---
title: Overzicht van bedrijfs continuïteit met Azure Database for PostgreSQL-één server
description: Overzicht van bedrijfs continuïteit met Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: c346360c125d9316aed81ceeedbe265fd09465c1
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907507"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Overzicht van bedrijfs continuïteit met Azure Database for PostgreSQL-één server

In dit overzicht worden de mogelijkheden beschreven die Azure Database for PostgreSQL biedt voor bedrijfs continuïteit en herstel na nood gevallen. Meer informatie over opties voor het herstellen van verstorende gebeurtenissen die gegevens verlies kunnen veroorzaken of ervoor zorgen dat uw data base en toepassing niet meer beschikbaar zijn. Meer informatie over wat u moet doen wanneer een gebruiker of toepassings fout van invloed is op de gegevens integriteit, een Azure-regio een storing heeft of als uw toepassing onderhoud vereist.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Functies die u kunt gebruiken om bedrijfs continuïteit te bieden

Azure Database for PostgreSQL biedt functies voor bedrijfs continuïteit, waaronder geautomatiseerde back-ups en de mogelijkheid voor gebruikers om geo-herstel te initiëren. Elk heeft verschillende kenmerken voor de geschatte herstel tijd (ERT) en het mogelijke gegevens verlies. Wanneer u deze opties kent, kunt u er een kiezen en ze samen gebruiken voor verschillende scenario's. Wanneer u uw bedrijfs continuïteits plan ontwikkelt, moet u weten wat de Maxi maal toegestane tijd is voordat de toepassing volledig wordt hersteld nadat de gebeurtenis is verstoord. Dit is de beoogde herstel tijd (RTO). U moet ook inzicht krijgen in de maximale hoeveelheid recente gegevens updates (tijds interval) die de toepassing kan afnemen bij het herstellen na het verstorings proces. Dit is het beoogde herstel punt (RPO).

De volgende tabel vergelijkt de ERT en RPO voor de beschik bare functies:

| **Voorzieningen** | **Basic** | **Algemeen** | **Geoptimaliseerd geheugen** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Herstel naar een bepaald tijdstip vanuit back-up | Elk herstel punt binnen de Bewaar periode | Elk herstel punt binnen de Bewaar periode | Elk herstel punt binnen de Bewaar periode |
| Geo-herstel van geo-gerepliceerde back-ups | Niet ondersteund | ERT < 12 uur<br/>RPO < 1 uur | ERT < 12 uur<br/>RPO < 1 uur |

> [!IMPORTANT]
> Verwijderde servers **kunnen niet** worden hersteld. Als u de server verwijdert, worden ook alle data bases die deel uitmaken van de server, verwijderd en kunnen deze niet worden hersteld. Gebruik [Azure resource Lock](../azure-resource-manager/resource-group-lock-resources.md) om onbedoelde verwijdering van uw server te voor komen.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Een server herstellen na een gebruikers-of toepassings fout

U kunt de back-ups van de service gebruiken om een server te herstellen van verschillende verstorende gebeurtenissen. Een gebruiker kan per ongeluk enkele gegevens verwijderen, onbedoeld een belang rijke tabel neerzetten of zelfs een volledige data base neerzetten. Een toepassing kan per ongeluk goede gegevens overschrijven met onjuiste gegevens als gevolg van een defect van de toepassing, enzovoort.

U kunt een **herstel punt op tijd** uitvoeren om een kopie van uw server te maken naar een bekend, goed moment. Dit tijdstip moet binnen de retentie periode voor back-ups vallen die u voor uw server hebt geconfigureerd. Nadat de gegevens zijn teruggezet naar de nieuwe server, kunt u de oorspronkelijke server vervangen door de zojuist herstelde server of de benodigde gegevens van de herstelde server naar de oorspronkelijke server kopiëren.

## <a name="recover-from-an-azure-data-center-outage"></a>Herstellen van een Azure Data Center-storing

Hoewel zeldzaam, kan er een storing optreden in een Azure-datacenter. Wanneer er zich een storing voordoet, wordt er een probleem ondervinden dat een bedrijf niet langer dan een paar minuten kan duren.

Een optie is om te wachten tot de server weer online is wanneer de storing van het Data Center wordt overschreden. Dit werkt voor toepassingen die de server gedurende een bepaalde periode offline kunnen hebben, bijvoorbeeld een ontwikkel omgeving. Wanneer een Data Center een storing heeft, weet u niet hoe lang de onderbreking kan duren. deze optie werkt dus alleen als u de server enige tijd niet nodig hebt.

## <a name="geo-restore"></a>Geo-herstel

De functie voor geo-Restore herstelt de server met behulp van geo-redundante back-ups. De back-ups worden gehost in de gekoppelde [regio](../best-practices-availability-paired-regions.md)van uw server. U kunt terugzetten van deze back-ups naar een andere regio. Met geo-Restore wordt een nieuwe server gemaakt met de gegevens van de back-ups. Meer informatie over geo-Restore vindt u in het [artikel back-ups maken en herstellen](concepts-backup.md).

> [!IMPORTANT]
> Geo-herstel is alleen mogelijk als u de server hebt ingericht met geografisch redundante back-upopslag. Als u wilt overschakelen van lokaal redundant naar geo-redundante back-ups voor een bestaande server, moet u een dump maken met behulp van pg_dump van uw bestaande server en deze herstellen op een nieuwe server die is geconfigureerd met geografisch redundante back-ups.

## <a name="cross-region-read-replicas"></a>Meerdere regio's replica's lezen
U kunt Kruis regio's gebruiken om replica's te verg Roten om uw bedrijfs continuïteit en herstel na nood gevallen te verbeteren. Lees replica's worden asynchroon bijgewerkt met de fysieke replicatie technologie van PostgreSQL. Meer informatie over het lezen van replica's, beschik bare regio's en hoe u een failover kunt uitvoeren vanuit het [artikel concepten van replica's lezen](concepts-read-replicas.md). 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [automatische back-ups in azure database for PostgreSQL](concepts-backup.md). 
- Meer informatie over het herstellen met behulp van [de Azure Portal](howto-restore-server-portal.md) of [de Azure cli](howto-restore-server-cli.md).
- Meer informatie over het [lezen van replica's in azure database for PostgreSQL](concepts-read-replicas.md).
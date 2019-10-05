---
title: Inzicht in de bedrijfs continuïteit in Azure Database for MariaDB
description: Meer informatie over bedrijfs continuïteit (herstel naar een bepaald tijdstip, de onderbreking van data centers, geo-Restore) bij het gebruik van Azure Database for MariaDB service.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d364c3a5fafaca514baade25cd3f5b4c0f10dc26
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973599"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Inzicht in de bedrijfs continuïteit in Azure Database for MariaDB

In dit artikel worden de mogelijkheden beschreven die Azure Database for MariaDB biedt voor bedrijfs continuïteit en herstel na nood gevallen. Meer informatie over opties voor het herstellen van verstorende gebeurtenissen die gegevens verlies kunnen veroorzaken of ervoor zorgen dat uw data base en toepassing niet meer beschikbaar zijn. Meer informatie over wat u moet doen wanneer een gebruiker of toepassings fout van invloed is op de gegevens integriteit, een Azure-regio een storing heeft of als uw toepassing onderhoud vereist.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Functies die u kunt gebruiken om bedrijfs continuïteit te bieden

Azure Database for MariaDB biedt functies voor bedrijfs continuïteit, waaronder geautomatiseerde back-ups en de mogelijkheid voor gebruikers om geo-herstel te initiëren. Elk heeft verschillende kenmerken voor de geschatte herstel tijd (ERT) en het mogelijke gegevens verlies. Wanneer u deze opties kent, kunt u er een kiezen en ze samen gebruiken voor verschillende scenario's. Wanneer u uw bedrijfs continuïteits plan ontwikkelt, moet u weten wat de Maxi maal toegestane tijd is voordat de toepassing volledig wordt hersteld nadat de gebeurtenis is verstoord. Dit is de beoogde herstel tijd (RTO). U moet ook inzicht krijgen in de maximale hoeveelheid recente gegevens updates (tijds interval) die de toepassing kan afnemen bij het herstellen na het verstorings proces. Dit is het beoogde herstel punt (RPO).

De volgende tabel vergelijkt de ERT en RPO voor de beschik bare functies:

| **Voorzieningen** | **Basic** | **Algemeen** | **Geoptimaliseerd geheugen** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Herstel naar een bepaald tijdstip vanuit back-up | Elk herstel punt binnen de Bewaar periode | Elk herstel punt binnen de Bewaar periode | Elk herstel punt binnen de Bewaar periode |
| Geo-herstel van geo-gerepliceerde back-ups | Niet ondersteund | ERT < 12 uur<br/>RPO < 1 uur | ERT < 12 uur<br/>RPO < 1 uur |

> [!IMPORTANT]
> Als u de server verwijdert, worden ook alle data bases op de server verwijderd en kunnen deze niet worden hersteld. U kunt een verwijderde server niet herstellen.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Een server herstellen na een gebruikers-of toepassings fout

U kunt de back-ups van de service gebruiken om een server te herstellen van verschillende verstorende gebeurtenissen. Een gebruiker kan per ongeluk enkele gegevens verwijderen, onbedoeld een belang rijke tabel neerzetten of zelfs een volledige data base neerzetten. Een toepassing kan per ongeluk goede gegevens overschrijven met onjuiste gegevens als gevolg van een defect van de toepassing, enzovoort.

U kunt een herstel punt op tijd uitvoeren om een kopie van uw server te maken naar een bekend, goed moment. Dit tijdstip moet binnen de retentie periode voor back-ups vallen die u voor uw server hebt geconfigureerd. Nadat de gegevens zijn teruggezet naar de nieuwe server, kunt u de oorspronkelijke server vervangen door de zojuist herstelde server of de benodigde gegevens van de herstelde server naar de oorspronkelijke server kopiëren.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Herstellen van een regionale Data Center-storing in azure

Hoewel zeldzaam, kan er een storing optreden in een Azure-datacenter. Wanneer er zich een storing voordoet, wordt er een probleem ondervinden dat een bedrijf niet langer dan een paar minuten kan duren.

Een optie is om te wachten tot de server weer online is wanneer de storing van het Data Center wordt overschreden. Dit werkt voor toepassingen die de server gedurende een bepaalde periode offline kunnen hebben, bijvoorbeeld een ontwikkel omgeving. Wanneer Data Center een storing heeft, weet u niet hoe lang de onderbreking kan duren, dus deze optie werkt alleen als u de server enige tijd niet nodig hebt.

De andere optie is het gebruik van de functie geo-Restore van de Azure Database for MariaDB waarmee de server wordt hersteld met behulp van geo-redundante back-ups. Deze back-ups zijn toegankelijk, zelfs wanneer de regio waarin uw server wordt gehost, offline is. U kunt van deze back-ups naar een andere regio herstellen en uw server weer online brengen.

> [!IMPORTANT]
> Geo-herstel is alleen mogelijk als u de server hebt ingericht met geografisch redundante back-upopslag.

## <a name="next-steps"></a>Volgende stappen

- Zie [back-ups in azure database for MariaDB](concepts-backup.md)voor meer informatie over de automatische back-ups.
- Als u wilt herstellen naar een bepaald tijdstip met behulp van de Azure Portal, raadpleegt u [Data Base herstellen naar een bepaald tijdstip met behulp van de Azure Portal](howto-restore-server-portal.md).

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->

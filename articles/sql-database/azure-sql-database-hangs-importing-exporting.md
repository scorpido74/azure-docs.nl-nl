---
title: Import/Export service duurt lang
description: Het importeren/exporteren van Azure SQL Database-service duurt lang om een database te importeren of te exporteren
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
manager: dcscontentpm
ms.openlocfilehash: cf2d9b218fe63414af2446b8562d3ba187b2d395
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535762"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Het importeren/exporteren van Azure SQL Database-service duurt lang om een database te importeren of te exporteren

Wanneer u de Azure SQL Database Import/Export-service gebruikt, kan het proces langer duren dan verwacht. In dit artikel worden de mogelijke oorzaken voor deze vertraging en alternatieve tijdelijke oplossingsmethoden beschreven.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL-database import/exportservice

De Azure SQL Database Import/Export-service is een op RUST gebaseerde webservice die in elk Azure-datacenter wordt uitgevoerd. Deze service wordt aangeroepen wanneer u de [optie Database importeren](sql-database-import.md#using-azure-portal) of [Exporteren](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) gebruikt om uw SQL-database in de Azure-portal te verplaatsen. De service biedt gratis wachtrijen voor aanvragen en computeservices om import en export uit te voeren tussen een Azure SQL-database en Azure Blob-opslag.

De import- en exportbewerkingen vertegenwoordigen geen traditionele fysieke databaseback-up, maar een logische back-up van de database die een speciale BACPAC-indeling gebruikt. Met de BACPAC-indeling hoeft u te voorkomen dat u een fysieke indeling hoeft te gebruiken die kan variëren tussen versies van Microsoft SQL Server en Azure SQL Database. Daarom u deze gebruiken om de database veilig te herstellen naar een SQL Server-database en naar een SQL-database.

## <a name="what-causes-delays-in-the-process"></a>Wat zijn de oorzaken vertragingen in het proces?

De Azure SQL Database Import/Export-service biedt een beperkt aantal compute virtual machines (VM's) per regio om import- en exportbewerkingen te verwerken. De compute VM's worden gehost per regio om ervoor te zorgen dat de import of export vertragingen en kosten voor komover de bandbreedte voorkomt. Als er in dezelfde regio te veel aanvragen tegelijkertijd worden gedaan, kunnen er aanzienlijke vertragingen optreden bij de verwerking van de bewerkingen. De tijd die nodig is om aanvragen te voltooien, kan variëren van enkele seconden tot vele uren.

> [!NOTE]
> Als een aanvraag niet binnen vier dagen wordt verwerkt, annuleert de service de aanvraag automatisch.

## <a name="recommended-solutions"></a>Aanbevolen oplossingen

Als uw database-export alleen wordt gebruikt voor herstel van het per ongeluk verwijderen van gegevens, bieden alle Azure SQL Database-edities selfserviceherstelmogelijkheden van door het systeem gegenereerde back-ups. Maar als u deze export om andere redenen nodig hebt en als u consistent snellere of meer voorspelbare import-/exportprestaties nodig hebt, overweeg dan de volgende opties:

* [Exporteer naar een BACPAC-bestand met behulp van het SQLPackage-hulpprogramma.](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [Exporteer naar een BACPAC-bestand met SQL Server Management Studio (SSMS).](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Voer de BACPAC-import of export rechtstreeks uit in uw code met behulp van de Microsoft SQL Server Data-Tier Application Framework (DacFx) API. Zie voor meer informatie:
  * [Een toepassing op gegevenslagen exporteren](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Naamruimte van Microsoft.SqlServer.Dac](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [DACFx downloaden](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Dingen waar u rekening mee moet houden wanneer u een Azure SQL-database exporteert of importeert

* Alle methoden die in dit artikel worden besproken, gebruiken het DTU-quotum (Database Transaction Unit), dat wordt geblokkeerd door de Azure SQL Database-service. U [de DTU-statistieken voor de database bekijken op de Azure-portal.](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring) Als de database de resourcelimieten heeft bereikt, [kunt u de servicelaag upgraden](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) om meer resources toe te voegen.
* Idealiter moet u clienttoepassingen (zoals het sqlpackage utility of uw aangepaste DAC-toepassing) uitvoeren vanuit een VM in dezelfde regio als uw SQL-database. Anders u prestatieproblemen ondervinden in verband met netwerklatentie.
* Het exporteren van grote tabellen zonder geclusterde indexen kan erg traag zijn of zelfs fouten veroorzaken. Dit gedrag treedt op omdat de tabel niet parallel kan worden opgesplitst en geëxporteerd. In plaats daarvan moet het worden geëxporteerd in een enkele transactie, en dat veroorzaakt trage prestaties en mogelijke mislukking tijdens de export, met name voor grote tabellen.


## <a name="related-documents"></a>Gerelateerde documenten

[Overwegingen bij het exporteren van een Azure SQL-database](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)

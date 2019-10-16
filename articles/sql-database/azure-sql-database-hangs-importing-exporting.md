---
title: Het duurt lang voordat een Data Base wordt geïmporteerd of geëxporteerd met Azure SQL Database import/export-service | Microsoft Docs
description: Het duurt lang voordat een Data Base wordt geïmporteerd of geëxporteerd met Azure SQL Database import/export-service
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 8d6cd5e925e17130e9ddee8074294275558d3cc2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331881"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Het duurt lang voordat een Data Base wordt geïmporteerd of geëxporteerd met Azure SQL Database import/export-service

Wanneer u de Azure SQL Database import/export-service gebruikt, kan het proces langer duren dan verwacht. In dit artikel worden de mogelijke oorzaken voor deze vertraging en alternatieve methoden voor tijdelijke oplossingen beschreven.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database import/export-service

De Azure SQL Database import/export-service is een op REST gebaseerde webservice die in elk Azure-Data Center wordt uitgevoerd. Deze service wordt aangeroepen wanneer u de optie [Data Base importeren](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) of [exporteren](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) gebruikt om uw SQL database in de Azure portal te verplaatsen. De service biedt gratis aanvraag wachtrijen en Compute-Services voor het importeren en exporteren tussen een Azure SQL database en Azure Blob-opslag.

De import-en export bewerkingen vertegenwoordigen geen traditionele back-up van de fysieke data base, maar in plaats daarvan een logische back-up van de data base die gebruikmaakt van een speciale BACPAC-indeling. Met de BACPAC-indeling kunt u voor komen dat u een fysieke indeling moet gebruiken die kan verschillen tussen de versies van Microsoft SQL Server en Azure SQL Database. Daarom kunt u het gebruiken om de data base veilig te herstellen naar een SQL Server-Data Base en naar een SQL database.

## <a name="what-causes-delays-in-the-process"></a>Wat veroorzaken vertragingen in het proces?

De Azure SQL Database import/export-service biedt een beperkt aantal Compute-Vm's (virtuele machines) per regio voor het verwerken van import-en export bewerkingen. De bewerkings-Vm's worden gehost per regio om ervoor te zorgen dat de import-of export bandbreedte vertragingen en kosten voor meerdere regio's vermijdt. Als er te veel aanvragen tegelijkertijd in dezelfde regio worden gemaakt, kunnen er aanzienlijke vertragingen optreden bij het verwerken van de bewerkingen. De benodigde tijd voor het volt ooien van aanvragen kan variëren van een paar seconden tot vele uren.

> [!NOTE]
> Als een aanvraag niet binnen vier dagen wordt verwerkt, wordt de aanvraag automatisch geannuleerd door de service.

## <a name="recommended-solutions"></a>Aanbevolen oplossingen

Als uw data base-exports alleen worden gebruikt voor herstel na onbedoeld verwijderen van gegevens, bieden alle Azure SQL Database-edities selfservice herstel mogelijkheid voor door het systeem gegenereerde back-ups. Maar als u deze exports om andere redenen nodig hebt, en als u consistent snellere of meer voorspel bare prestaties voor importeren/exporteren nodig hebt, moet u rekening houden met de volgende opties:

* [Exporteren naar een BACPAC-bestand met behulp van het SQLPackage-hulp programma](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Exporteren naar een BACPAC-bestand met behulp van SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Voer de BACPAC-import-of export bewerking rechtstreeks uit in uw code met behulp van de API van Microsoft SQL Server Data-Tier Application Framework (DacFx). Zie voor meer informatie:
  * [Een gegevenslaag toepassing exporteren](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Micro soft. SqlServer. DAC-naam ruimte](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [DACFx downloaden](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Overwegingen bij het exporteren of importeren van een Azure-SQL database

* Alle methoden die in dit artikel worden besproken, maken gebruik van het DTU-quotum (data base Trans Action Unit) dat de Azure SQL Database-Service beperkt. U kunt [de DTU-statistieken voor de Data Base weer geven op de Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Als de bron limiet van de data base is bereikt, moet u [een upgrade uitvoeren voor de](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) servicelaag om meer resources toe te voegen.
* In het ideale geval moet u client toepassingen (zoals het sqlpackage-hulp programma of uw aangepaste DAC-toepassing) uitvoeren vanaf een virtuele machine in dezelfde regio als uw SQL database. Anders kunt u prestatie problemen ondervinden met betrekking tot netwerk latentie.
* Het exporteren van grote tabellen zonder geclusterde indexen kan erg traag of zelfs een fout veroorzaken. Dit probleem treedt op omdat de tabel niet parallel kan worden opgesplitst en geëxporteerd. In plaats daarvan moet het worden geëxporteerd in één trans actie en dat trage prestaties en mogelijke fouten tijdens het exporteren veroorzaakt, met name voor grote tabellen.


## <a name="related-documents"></a>Gerelateerde documenten

[Overwegingen bij het exporteren van een Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)

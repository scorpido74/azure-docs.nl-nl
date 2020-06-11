---
title: Het importeren en exporteren van een Data Base neemt veel tijd in beslag
description: Het importeren of exporteren van een Data Base duurt lang Azure SQL Database en Azure SQL Managed instance import/export-service
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 15b27990842da8837e6cd8fc8d29f361961ee044
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669542"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Het importeren of exporteren van een Data Base duurt lang Azure SQL Database en Managed instance import/export-service
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Wanneer u de import/export-service gebruikt, kan het proces langer duren dan verwacht. In dit artikel worden de mogelijke oorzaken voor deze vertraging en alternatieve methoden voor tijdelijke oplossingen beschreven.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database import/export-service

De Azure SQL Database import/export-service is een op REST gebaseerde webservice die in elk Azure-Data Center wordt uitgevoerd. Deze service wordt aangeroepen wanneer u de optie [Data Base importeren](database-import.md#using-azure-portal) of [exporteren](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) gebruikt om uw SQL database in de Azure portal te verplaatsen. De service biedt gratis aanvraag wachtrijen en Compute-Services voor het importeren en exporteren tussen Azure SQL Database en Azure Blob-opslag.

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

* Alle methoden die in dit artikel worden besproken, maken gebruik van het DTU-quotum (data base Trans Action Unit) dat de Azure SQL Database-Service beperkt. U kunt [de DTU-statistieken voor de Data Base weer geven op de Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring). Als de bron limiet van de data base is bereikt, moet u [een upgrade uitvoeren voor de](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) servicelaag om meer resources toe te voegen.
* In het ideale geval moet u client toepassingen (zoals het sqlpackage-hulp programma of uw aangepaste DAC-toepassing) uitvoeren vanaf een virtuele machine in dezelfde regio als uw SQL database. Anders kunt u prestatie problemen ondervinden met betrekking tot netwerk latentie.
* Het exporteren van grote tabellen zonder geclusterde indexen kan erg traag of zelfs een fout veroorzaken. Dit probleem treedt op omdat de tabel niet parallel kan worden opgesplitst en geëxporteerd. In plaats daarvan moet het worden geëxporteerd in één trans actie en dat trage prestaties en mogelijke fouten tijdens het exporteren veroorzaakt, met name voor grote tabellen.


## <a name="related-documents"></a>Gerelateerde documenten

[Overwegingen bij het exporteren van een Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)

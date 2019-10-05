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
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974459"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Het duurt lang voordat een Data Base wordt geïmporteerd of geëxporteerd met Azure SQL Database import/export-service

Wanneer u Azure SQL Database import/export-service gebruikt, ziet u mogelijk dat het proces veel tijd in beslag kan nemen. In dit artikel vindt u meer informatie over de mogelijke oorzaken van thee-vertragingen en alternatieve methoden die u kunt gebruiken om deze problemen op te lossen.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database import/export-service

De Azure SQL Database import/export-service is een op REST gebaseerde webservice die in elk Microsoft Azure Data Center wordt uitgevoerd. Dit is de service die wordt aangeroepen wanneer u de [Data Base import](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) of [export](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) opties gebruikt om uw SQL database te verplaatsen in de Microsoft Azure-Portal. De service voorziet in een gratis aanvraag van een Queuing-service en een gratis compute-service voor het uitvoeren van import-en export bewerkingen vanuit een Microsoft Azure SQL database naar Microsoft Azure BLOB-opslag (binary large object).

De import-en export bewerkingen zijn geen traditionele fysieke back-up van de data base, maar een logische back-up van de data base die gebruikmaakt van een speciale BACPAC-indeling. Met deze logische BACPAC-indeling kunt u voor komen dat u een fysieke indeling moet gebruiken die kan verschillen tussen de versies van SQL Server en SQL Database. Daarom kunt u het gebruiken om de data base veilig te herstellen naar een SQL database en naar een SQL Server-Data Base.

## <a name="what-causes-the-process-to-take-a-long-time"></a>Waardoor het proces lange tijd in beslag neemt

De Azure SQL Database import/export-service biedt een beperkt aantal Compute-Vm's (virtuele machines) per regio om de import-en export bewerkingen te verwerken. De compute-VM wordt gehost per regio om ervoor te zorgen dat het importeren of exporteren de bandbreedte vertraging en-kosten voor meerdere regio's vermijdt. Dus als er te veel aanvragen tegelijk in dezelfde regio worden gemaakt, treden er aanzienlijke vertragingen op bij het verwerken van de bewerkingen. De tijd die nodig is voor het volt ooien van aanvragen kan variëren van een paar seconden tot vele uren.

> [!NOTE]
> Als een aanvraag niet binnen vier dagen wordt verwerkt, wordt de aanvraag automatisch geannuleerd door de service.

## <a name="recommended-solutions"></a>Aanbevolen oplossingen

Als uw data base-exports alleen worden gebruikt voor herstel na onbedoeld verwijderen van gegevens, bieden alle Azure SQL Server Data Base-edities een selfservice herstel mogelijkheid voor door het systeem gegenereerde back-ups. Maar als u deze uitvoer om andere redenen nodig hebt, en als u consistente of meer voorspel bare import-of export prestaties nodig hebt, moet u rekening houden met de volgende opties:

* [Exporteren naar een BACPAC-bestand met het SQLPackage-hulp programma](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [Exporteren naar een BACPAC-bestand met behulp van SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Voer de BACPAC-import-of export bewerking rechtstreeks uit in uw code met behulp van de micro soft® SQL Server® data-tier Application Framework (DacFx) API. Raadpleeg voor meer informatie
  * [Een gegevenslaag toepassing exporteren](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Micro soft. SqlServer. DAC-naam ruimte](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [DACFx downloaden](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>Overwegingen bij het exporteren of importeren van een Azure-SQL database

* Alle methoden die in dit artikel worden besproken, maken gebruik van het DTU-quotum, wat leidt tot een beperking door de Azure SQLDB-service. U kunt [de DTU-statistieken voor de Data Base weer geven op de Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Als de data base limieten voor resources is, [werkt u de](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) servicelaag bij om meer resources toe te voegen.
* Client toepassingen (zoals sqlpackage Utility of uw aangepaste DAC-toepassing) moeten idea liter worden uitgevoerd vanaf een virtuele machine (VM) in dezelfde regio als uw SQL database of anders kunt u prestatie problemen ondervinden vanwege netwerk latentie.
* Het exporteren van grote tabellen zonder geclusterde indexen kan erg traag of zelfs als gevolg van fouten optreden. Dit komt doordat de tabel niet parallel kan worden opgesplitst en geëxporteerd en moet worden geëxporteerd in één trans actie, waardoor de vertraging en mogelijke fouten tijdens het exporteren worden veroorzaakt, met name voor grote tabellen. 


## <a name="related-documents"></a>Gerelateerde documenten

[Overwegingen bij het exporteren van een Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)

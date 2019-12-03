---
title: Continue integratie en implementatie
description: Data base DevOps-ervaring op ondernemings niveau voor SQL Data Warehouse met ingebouwde ondersteuning voor continue integratie en implementatie met behulp van Azure-pijp lijnen.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/28/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e8d7e7764a01dbd0169efae093bac4d984982108
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708656"
---
# <a name="continuous-integration-and-deployment-for-azure-sql-data-warehouse"></a>Continue integratie en implementatie voor Azure SQL Data Warehouse

In deze eenvoudige zelf studie wordt uitgelegd hoe u het SSDT-data base project (SQL Server Data Tools) integreert met Azure DevOps en Azure-pijp lijnen kunt gebruiken om doorlopende integratie en implementatie in te stellen. Deze zelf studie is de tweede stap bij het bouwen van uw continue integratie-en implementatie pijplijn met SQL Data Warehouse. 

## <a name="before-you-begin"></a>Voordat u begint

- Door loop de [zelf studie over de integratie van bron beheer](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Azure DevOps instellen en er verbinding mee maken


## <a name="continuous-integration-with-visual-studio-build"></a>Continue integratie met Visual Studio build

1. Navigeer naar Azure-pijp lijnen en maak een nieuwe build-pijp lijn

      ![Nieuwe pijp lijn](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nieuwe pijplijn")

2. Selecteer uw bron code opslagplaats (Azure opslag plaatsen Git) en selecteer de sjabloon .NET desktop-app.

      ![Pijplijn instellen](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Pijplijn instellen") 

3. Bewerk uw YAML-bestand om de juiste pool van uw agent te gebruiken. Uw YAML-bestand moet er ongeveer als volgt uitzien:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Op dit moment hebt u een eenvoudige omgeving waar elke check-in voor de hoofd vertakking van de opslag plaats van de bron beheer automatisch een succes volle Visual Studio-build van uw database project moet activeren. Controleer of de automatisering aan het einde van het project wordt uitgevoerd door een wijziging aan te brengen in uw lokale data base en de wijzigingen in uw hoofd vertakking in te scha kelen.


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Doorlopende implementatie met de implementatie taak van Azure SQL Data Warehouse (of data base)

1. Voeg een nieuwe taak toe met behulp van de [implementatie taak Azure SQL database](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) en vul de vereiste velden in om verbinding te maken met uw doel-Data Warehouse. Wanneer deze taak wordt uitgevoerd, wordt de DACPAC die is gegenereerd op basis van het vorige bouw proces, geïmplementeerd naar het doel Data Warehouse. U kunt ook de [implementatie taak van Azure SQL Data Warehouse](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment) gebruiken 

      ![Implementatie taak](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Implementatie taak")

2. Als u een zelf-hostende agent gebruikt, moet u ervoor zorgen dat u de omgevings variabele hebt ingesteld om de juiste SqlPackage. exe te gebruiken voor SQL Data Warehouse. Het pad moet er ongeveer als volgt uitzien:

      ![Omgevings variabele](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Omgevings variabele")

   C:\Program Files (x86) \Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Voer de pijp lijn uit en valideer deze. U kunt lokaal wijzigingen aanbrengen en wijzigingen in uw broncode beheer inchecken, waardoor een automatische build en implementatie moet worden gegenereerd.

## <a name="next-steps"></a>Volgende stappen

- [Azure SQL Data Warehouse architectuur](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture) verkennen
- Snel [een SQL Data Warehouse maken][create a SQL Data Warehouse]
- [Voorbeeld gegevens laden][load sample data].
- [Video's](/azure/sql-data-warehouse/sql-data-warehouse-videos) verkennen



<!--Image references-->

[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Feature requests]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN forum]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/

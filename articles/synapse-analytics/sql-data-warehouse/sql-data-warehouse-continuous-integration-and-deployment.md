---
title: Continue integratie en implementatie
description: Database DevOps op enterprise-klasse ervaring voor gegevensopslag met ingebouwde ondersteuning voor continue integratie en implementatie met Azure Pipelines.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c5a326c07c8c2b0e5482361060c25c06d25643c1
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874140"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>Continue integratie en implementatie voor data warehousing

Deze eenvoudige zelfstudie beschrijft hoe u uw SQL Server Data Tools (SSDT)-databaseproject integreren met Azure DevOps en Azure Pipelines gebruiken om continue integratie en implementatie in te stellen. Deze zelfstudie is de tweede stap in het bouwen van uw continue integratie- en implementatiepijplijn voor gegevensopslag.

## <a name="before-you-begin"></a>Voordat u begint

- Ga door de [zelfstudie voor integratie van bronbeheer](sql-data-warehouse-source-control-integration.md)

- Azure DevOps instellen en verbinding maken met Azure DevOps

## <a name="continuous-integration-with-visual-studio-build"></a>Continue integratie met Visual Studio build

1. Navigeer naar Azure Pipelines en maak een nieuwe buildpijplijn.

      ![Nieuwe pijplijn](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nieuwe pijplijn")

2. Selecteer uw broncoderepository (Azure Repos Git) en selecteer de sjabloon .NET Desktop-app.

      ![Pijplijninstellingen](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Pijplijninstellingen")

3. Bewerk uw YAML-bestand om de juiste pool van uw agent te gebruiken. Uw YAML-bestand moet er ongeveer zo uitzien:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Op dit punt hebt u een eenvoudige omgeving waar elke check-in bij uw source control repository master branch automatisch een succesvolle Visual Studio build van uw databaseproject moet activeren. Valideren van de automatisering werkt end-to-end door een wijziging aan te brengen in uw lokale databaseproject en die wijziging in te checken in uw masterbranch.

## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Continue implementatie met de implementatietaak Azure SQL Data Warehouse (of Database)

1. Voeg een nieuwe taak toe met de [azure SQL Database-implementatietaak](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-sqldb?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) en vul de vereiste velden in om verbinding te maken met uw doelgegevensmagazijn. Wanneer deze taak wordt uitgevoerd, wordt de DACPAC die is gegenereerd uit het vorige buildproces geïmplementeerd in het doelgegevensmagazijn. U ook de [implementatietaak azure SQL Data Warehouse](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)gebruiken.

      ![Implementatietaak](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Implementatietaak")

2. Als u een zelfgehoste agent gebruikt, moet u ervoor zorgen dat u uw omgevingsvariabele instelt om de juiste SqlPackage.exe voor SQL Data Warehouse te gebruiken. Het pad moet er ongeveer zo uitzien:

      ![Omgevingsvariabele](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Omgevingsvariabele")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Voer uw pijplijn uit en valideer deze. U lokaal wijzigingen aanbrengen en wijzigingen in uw bronbeheer inchecken die een automatische build en implementatie moeten genereren.

## <a name="next-steps"></a>Volgende stappen

- [Ontdek de Synapse SQL-pool MPP-architectuur](massively-parallel-processing-mpp-architecture.md)
- Snel [een SQL-groep maken](create-data-warehouse-portal.md)
- [Voorbeeldgegevens laden](load-data-from-azure-blob-storage-using-polybase.md)
- [Video's verkennen](sql-data-warehouse-videos.md)

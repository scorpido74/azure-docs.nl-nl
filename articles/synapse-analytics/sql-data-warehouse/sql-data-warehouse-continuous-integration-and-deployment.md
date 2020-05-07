---
title: Continue integratie en implementatie
description: Data Warehouse DevOps-ervaring op ondernemings niveau voor gegevens opslag met ingebouwde ondersteuning voor continue integratie en implementatie met behulp van Azure-pijp lijnen.
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
ms.openlocfilehash: 14c3dde4a86e36a4015a319e608ab8543302932f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791286"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>Continue integratie en implementatie voor gegevens opslag

In deze eenvoudige zelf studie wordt uitgelegd hoe u het SSDT-data base project (SQL Server Data Tools) integreert met Azure DevOps en Azure-pijp lijnen kunt gebruiken om doorlopende integratie en implementatie in te stellen. Deze zelf studie is de tweede stap bij het bouwen van uw continue integratie-en implementatie pijplijn voor gegevens opslag.

## <a name="before-you-begin"></a>Voordat u begint

- Door loop de [zelf studie over de integratie van bron beheer](sql-data-warehouse-source-control-integration.md)

- Azure DevOps instellen en er verbinding mee maken

## <a name="continuous-integration-with-visual-studio-build"></a>Continue integratie met Visual Studio build

1. Navigeer naar Azure-pijp lijnen en maak een nieuwe build-pijp lijn.

      ![Nieuwe pijp lijn](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nieuwe pijplijn")

2. Selecteer uw bron code opslagplaats (Azure opslag plaatsen Git) en selecteer de sjabloon .NET desktop-app.

      ![Pijplijn instellen](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Pijplijn instellen")

3. Bewerk uw YAML-bestand om de juiste pool van uw agent te gebruiken. Uw YAML-bestand moet er ongeveer als volgt uitzien:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Op dit moment hebt u een eenvoudige omgeving waar elke check-in voor de hoofd vertakking van de opslag plaats van de bron beheer automatisch een succes volle Visual Studio-build van uw database project moet activeren. Controleer of de automatisering aan het einde van het project wordt uitgevoerd door een wijziging aan te brengen in uw lokale data base en de wijzigingen in uw hoofd vertakking in te scha kelen.

## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Doorlopende implementatie met de implementatie taak van Azure SQL Data Warehouse (of data base)

1. Voeg een nieuwe taak toe met behulp van de [implementatie taak Azure SQL database](/azure/devops/pipelines/targets/azure-sqldb) en vul de vereiste velden in om verbinding te maken met uw doel-Data Warehouse. Wanneer deze taak wordt uitgevoerd, wordt de DACPAC die is gegenereerd op basis van het vorige bouw proces, geïmplementeerd naar het doel Data Warehouse. U kunt ook de [Azure SQL Data Warehouse implementatie taak](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)gebruiken.

      ![Implementatie taak](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Implementatie taak")

2. Als u een zelf-hostende agent gebruikt, moet u ervoor zorgen dat u de omgevings variabele hebt ingesteld om de juiste SqlPackage. exe te gebruiken voor SQL Data Warehouse. Het pad moet er ongeveer als volgt uitzien:

      ![Omgevings variabele](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Omgevings variabele")

   C:\Program Files (x86) \Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Voer de pijp lijn uit en valideer deze. U kunt lokaal wijzigingen aanbrengen en wijzigingen in uw broncode beheer inchecken, waardoor een automatische build en implementatie moet worden gegenereerd.

## <a name="next-steps"></a>Volgende stappen

- [Synapse SQL pool MPP-architectuur](massively-parallel-processing-mpp-architecture.md) verkennen
- Snel [een SQL-groep maken](create-data-warehouse-portal.md)
- [Voorbeeld gegevens laden](load-data-from-azure-blob-storage-using-polybase.md)
- [Video's](sql-data-warehouse-videos.md) verkennen

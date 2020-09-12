---
title: Continue integratie en implementatie
description: Data Warehouse DevOps-ervaring op ondernemings niveau voor gegevens opslag met ingebouwde ondersteuning voor continue integratie en implementatie met behulp van Azure-pijp lijnen.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 131811ffd268f001a047a7031170f0723770d24c
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462323"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>Continue integratie en implementatie voor gegevens opslag

In deze eenvoudige zelf studie wordt uitgelegd hoe u het SSDT-data base project (SQL Server Data Tools) integreert met Azure DevOps en Azure-pijp lijnen kunt gebruiken om doorlopende integratie en implementatie in te stellen. Deze zelf studie is de tweede stap bij het bouwen van uw continue integratie-en implementatie pijplijn voor gegevens opslag.

## <a name="before-you-begin"></a>Voordat u begint

- Door loop de [zelf studie over de integratie van bron beheer](sql-data-warehouse-source-control-integration.md)

- Instellen van en verbinding maken met Azure DevOps

## <a name="continuous-integration-with-visual-studio-build"></a>Continue integratie met Visual Studio build

1. Navigeer naar Azure-pijp lijnen en maak een nieuwe build-pijp lijn.

      ![Nieuwe pijplijn](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nieuwe pijplijn")

2. Selecteer uw bron code opslagplaats (Azure opslag plaatsen Git) en selecteer de sjabloon .NET desktop-app.

      ![Pijplijn instellen](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Pijplijn instellen")

3. Bewerk uw YAML-bestand om de juiste pool van uw agent te gebruiken. Uw YAML-bestand moet er ongeveer als volgt uitzien:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Op dit moment hebt u een eenvoudige omgeving waar elke check-in voor de hoofd vertakking van de opslag plaats van de bron beheer automatisch een succes volle Visual Studio-build van uw database project moet activeren. Controleer of de automatisering aan het einde van het project wordt uitgevoerd door een wijziging aan te brengen in uw lokale data base en de wijzigingen in uw hoofd vertakking in te scha kelen.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Continue implementatie met de implementatie taak van Azure Synapse Analytics (of data base)

1. Voeg een nieuwe taak toe met behulp van de [implementatie taak Azure SQL database](/azure/devops/pipelines/targets/azure-sqldb) en vul de vereiste velden in om verbinding te maken met uw doel-Data Warehouse. Wanneer deze taak wordt uitgevoerd, wordt de DACPAC die is gegenereerd op basis van het vorige bouw proces, geïmplementeerd naar het doel Data Warehouse. U kunt ook de [Azure Synapse Analytics-implementatie taak](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)gebruiken.

      ![Implementatie taak](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Implementatie taak")

2. Als u een zelf-hostende agent gebruikt, moet u ervoor zorgen dat u de omgevings variabele hebt ingesteld voor het gebruik van de juiste SqlPackage.exe voor Azure Synapse Analytics. Het pad moet er ongeveer als volgt uitzien:

      ![Omgevings variabele](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Omgevings variabele")

   C:\Program Files (x86) \Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Voer de pijp lijn uit en valideer deze. U kunt lokaal wijzigingen aanbrengen en wijzigingen in uw broncode beheer inchecken, waardoor een automatische build en implementatie moet worden gegenereerd.

## <a name="next-steps"></a>Volgende stappen

- [Synapse SQL pool MPP-architectuur](massively-parallel-processing-mpp-architecture.md) verkennen
- Snel [een SQL-groep maken](create-data-warehouse-portal.md)
- [Voorbeeldgegevens laden](load-data-from-azure-blob-storage-using-polybase.md)
- [Video's verkennen](sql-data-warehouse-videos.md)

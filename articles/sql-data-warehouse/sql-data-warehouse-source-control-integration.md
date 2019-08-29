---
title: Integratie van broncode beheer | Microsoft Docs
description: Data base DevOps-ervaring op ondernemings niveau voor SQL Data Warehouse met systeem eigen integratie van broncode beheer met behulp van Azure opslag plaatsen (Git en GitHub).
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 10a10882efe05ef9e6bb86e54fcfcf8c5d73d225
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098747"
---
# <a name="source-control-integration-for-azure-sql-data-warehouse"></a>Integratie van broncode beheer voor Azure SQL Data Warehouse

In deze zelf studie wordt beschreven hoe u het SSDT-database project (SQL Server Data Tools) integreert met broncode beheer.  Integratie van broncode beheer is de eerste stap bij het bouwen van uw continue integratie-en implementatie pijplijn met SQL Data Warehouse. 

## <a name="before-you-begin"></a>Voordat u begint

- Meld u aan voor een [Azure DevOps-organisatie](https://azure.microsoft.com/services/devops/)
- Ga door met de zelf studie [maken en verbinden](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)
-  [Installeer Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/) 

## <a name="set-up-and-connect-to-azure-devops"></a>Azure DevOps instellen en er verbinding mee maken

1. Maak in uw Azure DevOps-organisatie een project dat uw SSDT-database project host via een Azure opslag plaats-opslag plaats

   ![Project maken](media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Project maken")

2. Open Visual Studio en maak verbinding met uw Azure DevOps-organisatie en project vanuit stap 1 door verbindingen beheren te selecteren.

   ![Verbindingen beheren](media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Verbindingen beheren")

   ![Verbinding maken](media/sql-data-warehouse-source-control-integration/3-connect.png "Verbinding maken")

3. Uw Azure opslag plaats-opslag plaats vanuit uw project naar uw lokale machine klonen

   ![Opslag plaats klonen](media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Opslag plaats klonen")

## <a name="create-and-connect-your-project"></a>Uw project maken en verbinden

1. Maak in Visual Studio een nieuw SQL Server Data Base-project met zowel een map als een lokale Git-opslag plaats in uw **lokale gekloonde opslag plaats**

   ![Nieuw project maken](media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Nieuw project maken")  

2. Klik met de rechter muisknop op uw lege sqlproject en Importeer uw data warehouse in het database project

   ![Project importeren](media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Project importeren")  

3. Voer in team Explorer in Visual Studio uw alle wijzigingen door naar uw lokale Git-opslag plaats 

   ![Door voeren](media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Door voeren")  

4. Nu u de wijzigingen lokaal hebt doorgevoerd in de gekloonde opslag plaats, synchroniseert en pusht u uw wijzigingen in uw Azure opslag plaats-opslag plaats in uw Azure DevOps-project.

   ![Synchronisatie en push-staging](media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchronisatie en push-staging")

   ![Synchroniseren en pushen](media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchroniseren en pushen")  

## <a name="validation"></a>Validatie

1. Controleer of de wijzigingen zijn gepusht naar uw Azure-opslag plaats door een tabel kolom in uw database project bij te werken vanuit Visual Studio SQL Server Data Tools (SSDT)

   ![Update kolom valideren](media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Update kolom valideren")

2. De wijziging door voeren en pushen van uw lokale opslag plaats naar uw Azure-opslag plaats

   ![Push wijzigingen](media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Push wijzigingen")

3. Controleer of de wijziging is gepusht in uw Azure opslag plaats-opslag plaats

   ![Verifiëren](media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Wijzigingen controleren")

4. (**Optioneel**) Gebruik schema Compare en werk de wijzigingen in uw doel-Data Warehouse bij met SSDT om ervoor te zorgen dat de object definities in uw Azure opslag plaats-opslag plaats en lokale opslag locatie overeenkomen met uw data warehouse

## <a name="next-steps"></a>Volgende stappen

- [Ontwikkelen voor Azure SQL Data Warehouse](sql-data-warehouse-overview-develop.md)

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->


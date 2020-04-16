---
title: Integratie van bronbeheer
description: Enterprise-class Database DevOps-ervaring voor SQL-pool met native source control-integratie met Azure Repos (Git en GitHub).
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 3ec52c5274891619cf7976e99b5241bfc67a4076
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415085"
---
# <a name="source-control-integration-for-sql-pool"></a>Source Control Integration for SQL pool

In deze zelfstudie wordt beschreven hoe u uw SQL Server Data Tools (SSDT)-databaseproject integreren met bronbeheer.  Source control integration is de eerste stap in het bouwen van uw pijplijn voor continue integratie en implementatie met de SQL-poolbron in Azure Synapse Analytics.

## <a name="before-you-begin"></a>Voordat u begint

- Aanmelden voor een [Azure DevOps-organisatie](https://azure.microsoft.com/services/devops/)
- Ga door de [zelfstudie Maken en Verbinden](create-data-warehouse-portal.md)
- [Visual Studio 2019 installeren](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Azure DevOps instellen en verbinding maken met Azure DevOps

1. Maak in uw Azure DevOps-organisatie een project dat uw SSDT-databaseproject host via een Azure Repo-repository

   ![Project maken](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Project maken")

2. Open Visual Studio en maak verbinding met uw Azure DevOps-organisatie en project vanaf stap 1 door 'Verbindingen beheren' te selecteren

   ![Verbindingen beheren](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Verbindingen beheren")

   ![Verbinden](./media/sql-data-warehouse-source-control-integration/3-connect.png "Verbinding maken")

3. Uw Azure Repo-repository klonen van uw project naar uw lokale machine

   ![Kloon repo](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Kloon repo")

## <a name="create-and-connect-your-project"></a>Uw project maken en verbinden

1. Maak in Visual Studio een nieuw SQL Server Database Project met zowel een directory als een lokale Git-repository in uw **lokale gekloonde repository**

   ![Nieuw project maken](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Nieuw project maken")  

2. Klik met de rechtermuisknop op uw lege sqlproject en importeer uw datawarehouse in het databaseproject

   ![Project importeren](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Project importeren")  

3. In teamexplorer in Visual Studio verbind je je alle wijzigingen in je lokale Git-repository

   ![Doorvoeren](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Doorvoeren")  

4. Nu u de wijzigingen lokaal hebt vastgelegd in de gekloonde opslagplaats, synchroniseert en pusht u uw wijzigingen in uw Azure Repo-repository in uw Azure DevOps-project.

   ![Synchroniseren en pushen - fasering](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchroniseren en pushen - fasering")

   ![Synchroniseren en pushen](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchroniseren en pushen")  

## <a name="validation"></a>Validatie

1. Wijzigingen controleren zijn doorgevoerd in uw Azure Repo door een tabelkolom in uw databaseproject bij te werken vanuit Visual Studio SQL Server Data Tools (SSDT)

   ![Kolom bijwerken valideren](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Kolom bijwerken valideren")

2. De wijziging van uw lokale opslagplaats naar uw Azure Repo vastleggen en pushen

   ![Wijzigingen pushen](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Wijzigingen pushen")

3. Controleer of de wijziging is gepusht in uw Azure Repo-repository

   ![Controleren](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Wijzigingen verifiëren")

4. (**Optioneel**) Schema vergelijken en bijwerken van de wijzigingen in uw doelgegevensmagazijn met SSDT om ervoor te zorgen dat de objectdefinities in uw Azure Repo-opslagplaats en lokale opslagplaats uw gegevensmagazijn weergeven

## <a name="next-steps"></a>Volgende stappen

- [Ontwikkelen voor ontwikkeling voor SQL-pool](sql-data-warehouse-overview-develop.md)

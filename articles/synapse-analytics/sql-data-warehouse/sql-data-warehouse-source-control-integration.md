---
title: Integratie van bronbeheer
description: Data base DevOps-ervaring op ondernemings niveau voor SQL-pool met native broncode beheer integratie met behulp van Azure opslag plaatsen (Git en GitHub).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81415085"
---
# <a name="source-control-integration-for-sql-pool"></a>Integratie van broncode beheer voor SQL-groep

In deze zelf studie wordt beschreven hoe u het SSDT-database project (SQL Server Data Tools) integreert met broncode beheer.  Integratie van broncode beheer is de eerste stap bij het bouwen van een continue integratie-en implementatie pijplijn met de resource van de SQL-groep in azure Synapse Analytics.

## <a name="before-you-begin"></a>Voordat u begint

- Meld u aan voor een [Azure DevOps-organisatie](https://azure.microsoft.com/services/devops/)
- Ga door met de zelf studie [maken en verbinden](create-data-warehouse-portal.md)
- [Installeer Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Azure DevOps instellen en er verbinding mee maken

1. Maak in uw Azure DevOps-organisatie een project dat uw SSDT-database project host via een Azure opslag plaats-opslag plaats

   ![Project maken](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Project maken")

2. Open Visual Studio en maak verbinding met uw Azure DevOps-organisatie en project vanuit stap 1 door verbindingen beheren te selecteren.

   ![Verbindingen beheren](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Verbindingen beheren")

   ![Verbinden](./media/sql-data-warehouse-source-control-integration/3-connect.png "Verbinding maken")

3. Uw Azure opslag plaats-opslag plaats vanuit uw project naar uw lokale machine klonen

   ![Opslag plaats klonen](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Opslag plaats klonen")

## <a name="create-and-connect-your-project"></a>Uw project maken en verbinden

1. Maak in Visual Studio een nieuw SQL Server Data Base-project met zowel een map als een lokale Git-opslag plaats in uw **lokale gekloonde opslag plaats**

   ![Nieuw project maken](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Nieuw project maken")  

2. Klik met de rechter muisknop op uw lege sqlproject en Importeer uw data warehouse in het database project

   ![Project importeren](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Project importeren")  

3. Voer in team Explorer in Visual Studio uw alle wijzigingen door naar uw lokale Git-opslag plaats

   ![Doorvoeren](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Doorvoeren")  

4. Nu u de wijzigingen lokaal hebt doorgevoerd in de gekloonde opslag plaats, synchroniseert en pusht u uw wijzigingen in uw Azure opslag plaats-opslag plaats in uw Azure DevOps-project.

   ![Synchronisatie en push-staging](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchronisatie en push-staging")

   ![Synchroniseren en pushen](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchroniseren en pushen")  

## <a name="validation"></a>Validatie

1. Controleer of de wijzigingen zijn gepusht naar uw Azure-opslag plaats door een tabel kolom in uw database project bij te werken vanuit Visual Studio SQL Server Data Tools (SSDT)

   ![Update kolom valideren](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Update kolom valideren")

2. De wijziging door voeren en pushen van uw lokale opslag plaats naar uw Azure-opslag plaats

   ![Wijzigingen pushen](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Wijzigingen pushen")

3. Controleer of de wijziging is gepusht in uw Azure opslag plaats-opslag plaats

   ![Controleren](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Wijzigingen controleren")

4. (**Optioneel**) Gebruik schema Compare en werk de wijzigingen in uw doel-Data Warehouse bij met SSDT om ervoor te zorgen dat de object definities in uw Azure opslag plaats-opslag plaats en lokale opslag locatie overeenkomen met uw data warehouse

## <a name="next-steps"></a>Volgende stappen

- [Ontwikkelen voor het ontwikkelen van een SQL-groep](sql-data-warehouse-overview-develop.md)

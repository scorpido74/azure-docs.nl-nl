---
title: Integratie van bronbeheer
description: Database DevOps-ervaring van bedrijfsklasse voor SQL-pool met systeemeigen integratie van broncodebeheer met Azure-opslagplaatsen (Git en GitHub).
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: afb1108bacadd16007e1f53186107ea8458d96e9
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85205115"
---
# <a name="source-control-integration-for-sql-pool"></a>Integratie van broncodebeheer voor SQL-pool

In deze zelfstudie leert u hoe u uw SQL Server Data Tools (SSDT)-databaseproject integreert met broncodebeheer.  Integratie met broncodebeheer is de eerste stap voor het bouwen van een doorlopende integratie- en implementatiepijplijn met de SQL-poolresource in Azure Synapse Analytics.

## <a name="before-you-begin"></a>Voordat u begint

- Meld u aan voor een [Azure DevOps-organisatie](https://azure.microsoft.com/services/devops/)
- Voltooi de zelfstudie [Maken en koppelen](create-data-warehouse-portal.md)
- [Installeer Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Instellen van en verbinding maken met Azure DevOps

1. Maak in uw Azure DevOps-organisatie een project voor het hosten van uw SSDT-databaseproject via een Azure-opslagplaats

   ![Project maken](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Project maken")

2. Open Visual Studio en selecteer Verbindingen beheren om verbinding te maken met uw Azure DevOps-organisatie en project uit stap 1

   ![Verbindingen beheren](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Verbindingen beheren")

   ![Verbinding maken](./media/sql-data-warehouse-source-control-integration/3-connect.png "Verbinding maken")

3. Uw Azure-opslagplaats klonen vanuit uw project naar uw lokale machine

   ![Opslagplaats klonen](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Opslagplaats klonen")

## <a name="create-and-connect-your-project"></a>Uw project maken en ermee verbinding maken

1. Maak in Visual Studio een nieuw SQL Server-databaseproject met zowel een map als een lokale Git-opslag plaats in uw **lokaal gekloonde opslagplaats**

   ![Nieuw project maken](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Nieuw project maken")  

2. Klik met de rechtermuisknop op uw lege sqlproject en importeer uw datawarehouse in het databaseproject

   ![Project importeren](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Project importeren")  

3. Voer in Team Explorer in Visual Studio alle wijzigingen in uw lokale Git-opslagplaats door

   ![Doorvoeren](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Doorvoeren")  

4. Nu u de wijzigingen lokaal hebt doorgevoerd in de gekloonde opslagplaats, synchroniseert u uw wijzigingen met en pusht u ze naar uw Azure-opslagplaats in uw Azure DevOps-project.

   ![Synchronisatie en push - fasering](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchronisatie en push - fasering")

   ![Synchronisatie en push](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchronisatie en push")  

## <a name="validation"></a>Validatie

1. Controleer of de wijzigingen zijn gepusht naar uw Azure -opslagplaats door een tabelkolom in uw databaseproject bij te werken vanuit Visual Studio SQL Server Data Tools (SSDT)

   ![Bijwerken kolom valideren](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Bijwerken kolom valideren")

2. Voer de wijziging door en push die van uw lokale opslagplaats naar uw Azure-opslagplaats

   ![Wijzigingen pushen](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Wijzigingen pushen")

3. Controleer of de wijziging naar uw Azure-opslagplaats is gepusht

   ![Verifiëren](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Wijzigingen controleren")

4. (**Optioneel**) Gebruik schemavergelijking en werk de wijzigingen in uw doel-datawarehouse bij met SSDT om ervoor te zorgen dat de objectdefinities in uw Azure-opslagplaats en lokale opslagplaats overeenkomen met uw datawarehouse

## <a name="next-steps"></a>Volgende stappen

- [Ontwikkelen voor SQL-pool](sql-data-warehouse-overview-develop.md)

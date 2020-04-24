---
title: Synapse Studio gebruiken (preview-versie)
description: In deze Quick start ziet u hoe eenvoudig het is om te zoeken in verschillende typen bestanden met behulp van Synapse Studio.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a29fd66cf264b09cc5e0db7ac1a329be3f297bb8
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096330"
---
# <a name="quickstart-using-synapse-studio-preview"></a>Snelstartgids: Synapse Studio gebruiken (preview-versie)

In deze Quick Start leert u hoe u bestanden kunt zoeken met behulp van Synapse Studio.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Vereisten

[Maak een Azure Synapse-werk ruimte en een gekoppeld opslag account](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>Synapse Studio starten

Klik in de Azure Synapse-werk ruimte in de Azure Portal op **Start Synapse Studio**.

![Synapse Studio starten](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

U kunt ook Synapse Studio starten door te klikken op [Azure Synapse Analytics](https://web.azuresynapse.net) en de juiste Tenant-, abonnements-en werkruimte waarden op te geven.

## <a name="browse-storage-accounts"></a>Bladeren in opslag accounts

Nadat u Synapse Studio hebt geopend, bladert u naar **gegevens** en vouwt u **opslag accounts** uit om het opslag account in de werk ruimte weer te geven.

![Door bestanden in opslag bladeren](./media/quickstart-synapse-studio/browse-files-on-storage.png)

U kunt nieuwe mappen maken en bestanden uploaden via de koppelingen op de werk balk om uw bestanden te organiseren.

## <a name="query-files-on-storage-account"></a>Query's uitvoeren op bestanden in het opslag account

> [!IMPORTANT]
> U moet lid zijn van de `Storage Blob Reader` rol op de onderliggende opslag om een query voor de bestanden te kunnen uitvoeren. Meer informatie over het [toewijzen van **opslag-BLOB-gegevens lezer** of **gegevens Inzender voor Storage BLOB** RBAC-machtigingen voor Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role).

1. Upload een `PARQUET` aantal bestanden.
2. Selecteer een of meer bestanden en maak vervolgens een nieuw SQL-script of een Spark-notebook om de inhoud van de bestanden weer te geven. Als u een notitie blok wilt maken, moet u [Apache Spark groep maken in de werk ruimte](quickstart-create-apache-spark-pool.md).

   ![Bestanden op opslag zoeken](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Voer de gegenereerde query of notitie blok uit om de inhoud van het bestand te bekijken:

   ![De inhoud van het bestand weer geven](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. U kunt de query wijzigen om de resultaten te filteren en te sorteren. Zoek de taal functies die beschikbaar zijn in SQL on-demand in het [overzicht van SQL-functies](sql/overview-features.md).

## <a name="next-steps"></a>Volgende stappen

- Azure AD-gebruikers in staat stellen om bestanden te zoeken aan de hand van de [ **opslag-BLOB gegevens lezer** of de **gegevensinzender voor opslag BLOB** RBAC-machtigingen voor Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)
- [Query's uitvoeren op Azure Storage met behulp van SQL on-demand](sql/on-demand-workspace-overview.md)
- [Apache Spark pool maken](quickstart-create-apache-spark-pool.md)
- [Power BI-rapport maken voor bestanden die zijn opgeslagen op Azure Storage](sql/tutorial-connect-power-bi-desktop.md)

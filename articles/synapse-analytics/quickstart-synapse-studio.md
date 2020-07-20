---
title: 'Quickstart: Synapse Studio gebruiken'
description: In deze quickstart ziet u en leert u hoe u eenvoudig een query kunt uitvoeren op verschillende bestandstypen met Synapse Studio (preview).
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 271fd77e519ce9d78e2fa4e4837004a77cf73522
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276053"
---
# <a name="quickstart-use-synapse-studio-preview"></a>Quickstart: Synapse Studio (preview) gebruiken

In deze quickstart leert u hoe u een query kunt uitvoeren op bestanden met behulp van Synapse Studio.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Vereisten

[Een Azure Synapse-werkruimte en een gekoppeld opslagaccount maken](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>Synapse Studio starten

Klik in uw Azure Synapse-werkruimte in de Azure-portal op **Synapse Studio starten**.

![Synapse Studio starten](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

U kunt Synapse Studio ook starten door op [Azure Synapse Analytics](https://web.azuresynapse.net) te klikken en de juiste waarden op de gegeven voor tenant, abonnement en werkruimte.

## <a name="browse-storage-accounts"></a>Door opslagaccounts bladeren

Nadat u Synapse Studio hebt geopend, bladert u naar **Gegevens** en vouwt u vervolgens **Opslagaccounts** uit om het opslagaccount in de werkruimte weer te geven.

![Door bestanden in opslag bladeren](./media/quickstart-synapse-studio/browse-files-on-storage.png)

U kunt nieuwe mappen maken en bestanden uploaden via de koppelingen op de werkbalk om uw bestanden te organiseren.

## <a name="query-files-on-storage-account"></a>Query’s uitvoeren op bestanden in een opslagaccount

> [!IMPORTANT]
> U moet lid zijn van de rol `Storage Blob Reader` voor de onderliggende opslag om query's te kunnen uitvoeren op de bestanden. Leer hoe u [RBAC-machtigingen voor **Gegevenslezer voor opslagblob** of **Gegevensbijdrager voor opslagblob** toewijst in Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role).

1. Upload enkele `PARQUET`-bestanden.
2. Selecteer een of meer bestanden en maak vervolgens een nieuw SQL-script of een Spark-notebook om de inhoud van de bestanden te bekijken. Als u een notebook wilt maken, moet u een [Apache Spark-pool in Synapse-werkruimten](quickstart-create-apache-spark-pool-studio.md) maken.

   ![Query’s uitvoeren op bestanden in opslag](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Voer de gegenereerde query of notebook uit om de inhoud van het bestand te bekijken.

   ![De inhoud van het bestand bekijken](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. U kunt de query wijzigen om de resultaten te filteren en te sorteren. Zoek in [overzicht van SQL-functies](sql/overview-features.md) taalfuncties die beschikbaar zijn in SQL on-demand.

## <a name="next-steps"></a>Volgende stappen

- Stel Azure AD-gebruikers in staat om query’s uit te voeren op bestanden [door RBAC-machtigingen voor **Gegevenslezer voor opslagblob** of **Gegevensbijdrager voor opslagblob** toe te wijzen in Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)
- [Query's uitvoeren op Azure Storage met behulp van SQL on-demand](sql/on-demand-workspace-overview.md)
- [Create Apache Spark pool using Azure portal](quickstart-create-apache-spark-pool-portal.md) (Een Apache Spark-pool maken met behulp van de Azure-portal)
- [Een Power BI-rapport maken voor bestanden die zijn opgeslagen in Azure Storage](sql/tutorial-connect-power-bi-desktop.md)

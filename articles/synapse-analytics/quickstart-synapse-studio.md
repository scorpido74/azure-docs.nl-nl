---
title: Synapse Studio gebruiken (voorbeeld)
description: In deze quickstart ziet en leert u hoe eenvoudig het is om verschillende soorten bestanden op te vragen met Behulp van Synapse Studio.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 294f53fe929343708bdbb9564b23c46865cf02ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423864"
---
# <a name="quickstart-using-synapse-studio-preview"></a>Snelstart: Synapse Studio gebruiken (preview)

In deze quickstart leert u hoe u bestanden opvragen met Synapse Studio.

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Vereisten

[Maak een Azure Synapse-werkruimte en bijbehorend opslagaccount](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>Start Synapse Studio

Klik in uw Azure Synapse-werkruimte in de Azure-portal op **Synapse Studio starten**.

![Start Synapse Studio](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

U Synapse Studio ook starten door op [Azure Synapse Analytics](https://web.azuresynapse.net) te klikken en de juiste tenant-, abonnements- en werkruimtewaarden op te geven.

## <a name="browse-storage-accounts"></a>Bladeren door opslagaccounts

Zodra u Synapse Studio opent, bladert u naar **Gegevens** en vouwt u **Opslagaccounts** uit om het opslagaccount in de werkruimte weer te geven.

![Bladeren door bestanden op opslag](./media/quickstart-synapse-studio/browse-files-on-storage.png)

U nieuwe mappen maken en bestanden uploaden met behulp van de werkbalk koppelingen om uw bestanden te ordenen.

## <a name="query-files-on-storage-account"></a>Querybestanden op opslagaccount

> [!IMPORTANT]
> U moet lid zijn `Storage Blob Reader` van de rol op de onderliggende opslag om de bestanden te kunnen opvragen. Meer informatie over het [toewijzen van **Storage Blob-gegevenslezer** of **RBAC-machtigingen voor opslagblobgegevens inzender** rbac op Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role).

1. Upload `PARQUET` sommige bestanden.
2. Selecteer een of meer bestanden en maak vervolgens een nieuw SQL-script of een Spark-notitieblok om de inhoud van de bestanden te bekijken. Als u een notitieblok wilt maken, moet u [de Apache Spark-pool in de werkruimte](spark/apache-spark-notebook-create-spark-use-sql.md)maken.

   ![Querybestanden in opslag](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Voer de gegenereerde query of het notitieblok uit om de inhoud van het bestand te bekijken:

   ![De inhoud van het bestand bekijken](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. U de query wijzigen om resultaten te filteren en te sorteren. Zoek taalfuncties die beschikbaar zijn in SQL on-demand in [SQL-functiesoverzicht.](sql/overview-features.md)

## <a name="next-steps"></a>Volgende stappen

- Azure AD-gebruikers in staat stellen bestanden op te vragen [door RBAC-machtigingen voor **Opslagblob-gegevenslezer** of **Opslagblobgegevensinzender** ](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role) toe te staan in Azure Storage
- [Querybestanden op Azure Storage met SQL On-Demand](sql/on-demand-workspace-overview.md)
- [Groep Apache Spark maken](spark/apache-spark-notebook-create-spark-use-sql.md)
- [Power BI-rapport maken over bestanden die zijn opgeslagen in Azure Storage](sql/tutorial-connect-power-bi-desktop.md)

---
title: 'Snelstartgids: scale Compute-Azure Portal '
description: Schaal berekenen in SQL-groep in de Azure Portal. Vergroot de schaal van Compute voor betere prestaties, of verklein de schaal juist om kosten te besparen.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7463849223fdf81466237c7d0c912763988e80e6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200334"
---
# <a name="quickstart-scale-compute-in-azure-synapse-analytics-sql-pool-in-the-azure-portal"></a>Snelstartgids: Compute scale in azure Synapse Analytics SQL-groep in de Azure Portal

Schaal berekenen in SQL-groep in de Azure Portal. [Vergroot de schaal van Compute](sql-data-warehouse-manage-compute-overview.md) voor betere prestaties, of verklein de schaal juist om kosten te besparen. 

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Voordat u begint

U kunt een SQL-groep die u al hebt, schalen of [Quick Start: Create and Connect-Portal](create-data-warehouse-portal.md) gebruiken om een SQL-groep met de naam **mySampleDataWarehouse**te maken.  Met deze snelstart wordt **mySampleDataWarehouse** geschaald.

>[!Note]
>Uw SQL-groep moet online zijn om te kunnen worden geschaald. 

## <a name="scale-compute"></a>De schaal van Compute aanpassen

Reken resources van SQL-pool kunnen worden geschaald door de Data Warehouse-eenheden te verg Roten of te verkleinen. Met behulp van de quickstart [maken en verbinden - portal] (create-data-warehouse-portal.md) is **mySampleDataWarehouse** gemaakt en vervolgens gestart met 400 DWU's. In de volgende stappen wordt het aantal DWU’s voor **mySampleDataWarehouse** aangepast.

DWU’s wijzigen:

1. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linker pagina van de Azure Portal.
2. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** . De SQL-groep wordt geopend.
3. Klik op **Schalen**.

    ![Op Schalen klikken](media/quickstart-scale-compute-portal/click-scale.png)

2. Verplaats in het paneel Schalen de schuifregelaar naar links of rechts om de DWU-instelling te wijzigen. Selecteer vervolgens schalen.

    ![Schuifregelaar verplaatsen](media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Volgende stappen
U hebt nu geleerd hoe u de reken kracht voor de SQL-groep kunt schalen. Voor meer informatie over SQL-pool gaat u verder met de zelf studie voor het laden van gegevens.

> [!div class="nextstepaction"]
>[Gegevens laden in SQL-groep](load-data-from-azure-blob-storage-using-polybase.md)

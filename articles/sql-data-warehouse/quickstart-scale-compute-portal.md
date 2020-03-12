---
title: Schaal berekenen voor Synapse SQL-pool (Azure Portal)
description: U kunt Compute voor Synapse SQL pool (Data Warehouse) schalen met behulp van de Azure Portal.
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
ms.openlocfilehash: df0b21e98812faf99b6e67f262cec6e9c29db2f1
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130255"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Snelstartgids: Compute Scale for Synapse SQL pool with the Azure Portal

U kunt Compute voor Synapse SQL pool (Data Warehouse) schalen met behulp van de Azure Portal. [Schaal rekenkracht uit](sql-data-warehouse-manage-compute-overview.md) voor betere prestaties of verklein de schaal om kosten te besparen. 

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Voordat u begint

U kunt een SQL-groep die u al hebt, schalen of [Quick Start: Create and Connect-Portal](create-data-warehouse-portal.md) gebruiken om een SQL-groep met de naam **mySampleDataWarehouse**te maken. Met deze snelstart wordt **mySampleDataWarehouse** geschaald.

>[!IMPORTANT] 
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
Ga naar de zelf studie over het [laden van gegevens in een SQL-groep](load-data-from-azure-blob-storage-using-polybase.md) voor meer informatie over SQL-pool. 

---
title: Schaalgegevens voor Synapse SQL-pool (Azure-portal)
description: U compute schalen naar Synapse SQL-pool (datawarehouse) met behulp van de Azure-portal.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f92152658b9db83740ffc2de2dc6956003849e06
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350820"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Snelstart: Compute schalen voor Synapse SQL-pool met de Azure-portal

U compute schalen naar Synapse SQL-pool (datawarehouse) met behulp van de Azure-portal. [Vergroot de schaal van Compute](sql-data-warehouse-manage-compute-overview.md) voor betere prestaties of verklein de schaal juist om kosten te besparen. 

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Voordat u begint

U een SQL-groep schalen die u al hebt of [Quickstart gebruiken: maken en verbinden - portal](create-data-warehouse-portal.md) om een SQL-groep met de naam **mySampleDataWarehouse**te maken. Met deze snelstart wordt **mySampleDataWarehouse** geschaald.

>[!IMPORTANT] 
>Uw SQL-pool moet online zijn om te schalen. 

## <a name="scale-compute"></a>De schaal van Compute aanpassen

SQL-groepcomputeresources kunnen worden geschaald door gegevensmagazijnen te verhogen of te verkleinen. Met behulp van de quickstart [maken en verbinden - portal] (create-data-warehouse-portal.md) is **mySampleDataWarehouse** gemaakt en vervolgens gestart met 400 DWU's. In de volgende stappen wordt het aantal DWU's voor **mySampleDataWarehouse** aangepast.

DWU’s wijzigen:

1. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linkerpagina van de Azure-portal.
2. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW).** De SQL-pool wordt geopend.
3. Klik op **Schalen**.

    ![Op Schalen klikken](./media/quickstart-scale-compute-portal/click-scale.png)

2. Verplaats in het paneel Schalen de schuifregelaar naar links of rechts om de DWU-instelling te wijzigen. Selecteer vervolgens schaal.

    ![Schuifregelaar verplaatsen](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Volgende stappen
Ga voor meer informatie over SQL-groep verder met de zelfstudie [gegevens laden in SQL-groep.](load-data-from-azure-blob-storage-using-polybase.md) 

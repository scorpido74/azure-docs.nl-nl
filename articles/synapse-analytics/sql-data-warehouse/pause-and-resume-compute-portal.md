---
title: Gegevens over de berekening in Synapse SQL-groep onderbreken en hervatten via de Azure-portal
description: Gebruik de Azure-portal om compute voor SQL-groep te pauzeren om kosten te besparen. Hervat de gegevenswanneer u klaar bent om het gegevensmagazijn te gebruiken.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 55e3d5bf4fb63c35d484e4a764c7eeb2e2484fcf
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350958"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Snelstart: gegevens opnemen en hervatten in synapse SQL-pool via de Azure-portal

U de Azure-portal gebruiken om de compute resources voor de Synapse SQL-groep (datawarehouse) te pauzeren en te hervatten. Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Voordat u begint

Gebruik [Create and Connect - portal](create-data-warehouse-portal.md) om een SQL-groep te maken met de naam **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Compute onderbreken

Om de kosten te verlagen, u de on-demand compute resources onderbreken en hervatten. Als u de database bijvoorbeeld 's nachts en in het weekend niet gebruikt, u deze tijdens die tijden pauzeren en overdag hervatten. 
>[!NOTE]
>Er worden geen kosten in rekening gebracht voor rekenbronnen terwijl de database is onderbroken. U blijft echter in rekening worden gebracht voor opslag. 

Volg de volgende stappen om een SQL-groep te pauzeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linkernavigatiepagina van de Azure-portal.
2. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** om de SQL-groep te openen. 
3. Op de **mySampleDataWarehouse-pagina** wordt de **status** **online weergegeven.**

    ![Online berekenen](././media/pause-and-resume-compute-portal/compute-online.png)

4. Als u sql-pool wilt onderbreken, klikt u op de knop **Onderbreken.** 
5. Er verschijnt een bevestigingsvraag met de vraag of u door wilt gaan. Klik **op Ja**.
6. Wacht even en let op dat de **status** **wordt onderbroken.**

    ![Onderbreken](./media/pause-and-resume-compute-portal/pausing.png)

7. Wanneer de pauzebewerking is voltooid, wordt de status **onderbroken** en wordt de optieknop **Hervat.**
8. De compute resources voor de SQL-groep zijn nu offline. Er worden geen kosten in rekening gebracht voor compute totdat u de service hervat.

    ![Offline berekenen](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Compute hervatten

Volg deze stappen om de SQL-groep te hervatten.

1. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linkerpagina van de Azure-portal.
2. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** om de SQL-poolpagina te openen. 
3. Op de pagina **mySampleDataWarehouse** wordt de **status** **onderbroken**.

    ![Offline berekenen](././media/pause-and-resume-compute-portal/compute-offline.png)

4. Als u de SQL-groep wilt hervatten, klikt u op **Hervatten**. 
5. Er verschijnt een bevestigingsvraag met de vraag of u wilt beginnen. Klik **op Ja**.
6. Let op: **Status** wordt **hervat**.

    ![Hervatten](./media/pause-and-resume-compute-portal/resuming.png)

7. Wanneer de SQL-groep weer online is, is de status **Online** en is de optieknop **Onderbreken.**
8. De compute resources voor SQL-groep zijn nu online en u de service gebruiken. De kosten voor compute zijn hervat.

    ![Online berekenen](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Resources opschonen

Er worden kosten in rekening gebracht voor gegevensmagazijnen en de gegevens die zijn opgeslagen in uw SQL-groep. Deze compute- en opslagresources worden apart in rekening gebracht. 

- Als u de gegevens in opslag wilt bewaren, pauzeert u de gegevens.
- Als u toekomstige kosten wilt verwijderen, u de SQL-groep verwijderen. 

Volg deze stappen om de resources op te schonen zoals gewenst.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en klik op uw SQL-groep.

    ![Resources opschonen](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Als u het berekenen wilt onderbreken, klikt u op de knop **Onderbreken**. 

2. Als u de SQL-groep wilt verwijderen zodat er geen kosten in rekening worden gebracht voor compute of opslag, klikt u op **Verwijderen**.

3. Als u de SQL-server wilt verwijderen die u hebt gemaakt, klikt u op **sqlpoolservername.database.windows.net**en klikt u vervolgens op **Verwijderen**.  

   > [!CAUTION]
   > Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de resourcegroep wilt verwijderen, klikt u op **myResourceGroup**. Klik vervolgens op **Resourcegroep verwijderen**.


## <a name="next-steps"></a>Volgende stappen

U hebt nu de compute voor uw SQL-pool onderbroken en hervat. Ga verder naar het volgende artikel voor meer informatie over het [laden van gegevens in SQL-groep.](load-data-from-azure-blob-storage-using-polybase.md) Zie het artikel [Compute overview beheren](sql-data-warehouse-manage-compute-overview.md) voor meer informatie over het beheren van rekenmogelijkheden. 


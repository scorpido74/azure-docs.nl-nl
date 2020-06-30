---
title: Berekening onderbreken en hervatten in Synapse SQL-pool via de Azure Portal
description: Gebruik de Azure Portal om de berekening voor de SQL-groep te onderbreken om kosten te besparen. Hervat de rekenbewerking wanneer u klaar bent om het datawarehouse te gebruiken.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 25f12d038b2a83688c12d95adefef3d4fb39be43
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85214465"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Quickstart: Berekening onderbreken en hervatten in Synapse SQL-pool via de Azure Portal

U kunt de Azure Portal gebruiken om de rekenresources van Synapse SQL-pool (datawarehouse) te onderbreken en weer te hervatten. Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Voordat u begint

Gebruik [Maken en koppelen - portal](create-data-warehouse-portal.md) om een SQL-pool met de naam **mySampleDataWarehouse** te maken. 

## <a name="pause-compute"></a>Berekening onderbreken

Als u kosten wilt besparen, kunt u de rekenresources op aanvraag onderbreken en hervatten. Als u de database bijvoorbeeld ‘s nachts en in het weekend niet gebruikt, kunt u deze dan onderbreken en gedurende de dag hervatten. 
>[!NOTE]
>Er worden geen kosten in rekening gebracht voor rekenresources terwijl de database is onderbroken. Er worden echter nog wel steeds kosten in rekening gebracht voor opslag. 

Volg deze stappen om een SQL-pool te onderbreken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linkernavigatiepagina van de Azure Portal.
2. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** om de SQL-pool te openen. 
3. U ziet op de pagina **mySampleDataWarehouse** dat de **Status** **Online** is.

    ![Online berekenen](././media/pause-and-resume-compute-portal/compute-online.png)

4. Klik op de knop **Onderbreken** als u het berekenen wilt onderbreken. 
5. Er wordt een bevestigingsvraag weergegeven waarin u wordt gevraagd of u wilt doorgaan. Klik op **Ja**.
6. Wacht even en u ziet dat de **Status** verandert in **Wordt onderbroken**.

    ![Wordt onderbroken](./media/pause-and-resume-compute-portal/pausing.png)

7. Wanneer de onderbreking is voltooid, verandert de status in **Onderbroken** en verandert de optieknop in **Hervatten**.
8. De rekenresources voor de SQL-pool zijn nu offline. Er worden geen kosten in rekening gebracht voor de rekenkracht totdat u de service hervat.

    ![Offline berekenen](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Berekening hervatten

Volg deze stappen om een SQL-pool te hervatten.

1. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linkerpagina van de Azure Portal.
2. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** om de pagina van de SQL-pool te openen. 
3. U ziet op de pagina **mySampleDataWarehouse** dat de **Status** **Onderbroken** is.

    ![Offline berekenen](././media/pause-and-resume-compute-portal/compute-offline.png)

4. Klik op **Hervatten**om de SQL-pool te hervatten. 
5. Er wordt een bevestigingsvraag weergegeven waarin u wordt gevraagd of u wilt starten. Klik op **Ja**.
6. U ziet dat de **Status** **Hervatten** weergeeft.

    ![Hervatten](./media/pause-and-resume-compute-portal/resuming.png)

7. Wanneer de SQL-pool weer online is, is de status **Online** en verandert de keuzeknop in **Onderbreken**.
8. De rekenresources voor de SQL-pool zijn nu online en u kunt de service gebruiken. De kosten voor de rekenkracht zijn hervat.

    ![Online berekenen](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Resources opschonen

Er worden kosten in rekening gebracht voor datawarehouse-eenheden en de gegevens die zijn opgeslagen in uw SQL-pool. Deze compute- en opslagresources worden apart in rekening gebracht. 

- Als u de gegevens in de opslag wilt houden, moet u het berekenen onderbreken.
- Als u in de toekomst geen kosten meer wilt hebben, kunt u de SQL-pool verwijderen. 

Volg deze stappen om de resources op te schonen zoals gewenst.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op uw SQL-pool.

    ![Resources opschonen](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Als u het berekenen wilt onderbreken, klikt u op de knop **Onderbreken**. 

2. Als u de SQL-pool wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, klikt u op **Verwijderen**.

3. Als u de door u gemaakte SQL-server wilt verwijderen, klikt u op **sqlpoolservername.database.windows.net** en vervolgens op **Verwijderen**.  

   > [!CAUTION]
   > Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de resourcegroep wilt verwijderen, klikt u op **myResourceGroup**. Klik vervolgens op **Resourcegroep verwijderen**.


## <a name="next-steps"></a>Volgende stappen

U hebt nu de berekening voor uw SQL-pool gepauzeerd en hervat. Ga naar het volgende artikel voor meer informatie over [Gegevens in een SQL-pool laden](load-data-from-azure-blob-storage-using-polybase.md). Zie het artikel [Overzicht rekencapaciteit beheren](sql-data-warehouse-manage-compute-overview.md) voor meer informatie over het beheren van rekencapaciteit. 


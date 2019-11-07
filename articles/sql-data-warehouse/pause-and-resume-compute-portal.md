---
title: 'Quick Start: Compute & Resume-Azure Portal '
description: Gebruik de Azure Portal om Compute in Azure SQL Data Warehouse te onderbreken om kosten op te slaan. Hervat de compute wanneer u klaar bent om het Data Warehouse te gebruiken.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 14f66f71948f75a723c9fdbed7490d54c2c3e2b2
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692996"
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Quick Start: de berekening onderbreken en hervatten voor een Azure SQL Data Warehouse in het Azure Portal

Gebruik de Azure Portal om Compute in Azure SQL Data Warehouse te onderbreken om kosten op te slaan. [Hervat de compute](sql-data-warehouse-manage-compute-overview.md) wanneer u klaar bent om het Data Warehouse te gebruiken.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Voordat u begint

Gebruik [Create en Connect-Portal](create-data-warehouse-portal.md) om een Data Warehouse te maken met de naam **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Compute onderbreken

Als u kosten wilt besparen, kunt u de reken resources op aanvraag onderbreken en hervatten. Als u bijvoorbeeld de data base niet meer gebruikt in de nacht en in het weekend, kunt u deze onderbreken tijdens deze tijden en deze op de dag hervatten. Er worden geen kosten in rekening gebracht voor reken resources wanneer de data base wordt onderbroken. Er worden echter nog steeds kosten in rekening gebracht voor opslag. 

Volg deze stappen om een SQL Data Warehouse te onderbreken.

1. Klik op de linkerpagina in Azure Portal op **SQL-databases**.
2. Selecteer **mySampleDataWarehouse** op de pagina **SQL-databases**. De datawarehouse wordt geopend. 
3. Op de pagina **mySampleDataWarehouse** is de **status** van de melding **online**.

    ![Online berekenen](media/pause-and-resume-compute-portal/compute-online.png)

4. Klik op de knop **pause** om het Data Warehouse te onderbreken. 
5. Er wordt een bevestigings vraag weer gegeven waarin u wordt gevraagd of u wilt door gaan. Klik op **Ja**.
6. Wacht even en u ziet dan dat de **status** is **onderbroken**.

    ![Onderbreken](media/pause-and-resume-compute-portal/pausing.png)

7. Wanneer de pauze bewerking is voltooid, wordt de status **onderbroken** en wordt het keuze rondje **gestart**.
8. De reken resources voor het Data Warehouse zijn nu offline. Er worden geen kosten in rekening gebracht voor de reken kracht tot u de service hervat.

    ![Offline berekenen](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Compute hervatten

Volg deze stappen om een SQL Data Warehouse te hervatten.

1. Klik op de linkerpagina in Azure Portal op **SQL-databases**.
2. Selecteer **mySampleDataWarehouse** op de pagina **SQL-databases**. De datawarehouse wordt geopend. 
3. Op de pagina **mySampleDataWarehouse** wordt de **status** van de melding **onderbroken**.

    ![Offline berekenen](media/pause-and-resume-compute-portal/compute-offline.png)

4. Klik op **Start**om het Data Warehouse te hervatten. 
5. Er wordt een bevestigings vraag weer gegeven waarin u wordt gevraagd of u wilt beginnen. Klik op **Ja**.
6. U ziet dat de **status** wordt **hervat**.

    ![Hervatten](media/pause-and-resume-compute-portal/resuming.png)

7. Wanneer het Data Warehouse weer online is, is de status **online** en is het keuze rondje **pauzeren**.
8. De reken resources voor het Data Warehouse zijn nu online en u kunt de service gebruiken. De kosten voor de reken kracht zijn hervat.

    ![Online berekenen](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Resources opschonen

Er worden kosten in rekening gebracht voor Data Warehouse-eenheden en de gegevens die zijn opgeslagen in uw data warehouse. Deze compute- en opslagresources worden apart in rekening gebracht. 

- Als u de gegevens in de opslag ruimte wilt bewaren, moet u de compute onderbreken.
- Als u in de toekomst geen kosten meer wilt hebben, kunt u de datawarehouse verwijderen. 

Volg deze stappen om de resources op te schonen zoals gewenst.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en klik op uw data warehouse.

    ![Resources opschonen](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Als u het berekenen wilt onderbreken, klikt u op de knop **Onderbreken**. Als het datawarehouse is onderbroken, ziet u een knop **Start**.  Als u het berekenen wilt hervatten, klikt u op **Start**.

2. Als u de datawarehouse wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, klikt u op **Verwijderen**.

3. Als u de door u gemaakte SQL-Server wilt verwijderen, klikt u op **mynewserver-20171113.database.Windows.net**en vervolgens op **verwijderen**.  Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

4. Als u de resourcegroep wilt verwijderen, klikt u op **myResourceGroup**. Klik vervolgens op **Resourcegroep verwijderen**.


## <a name="next-steps"></a>Volgende stappen

U hebt nu de reken kracht voor uw data warehouse onderbroken en hervat. Voor meer informatie over Azure SQL Data Warehouse gaat u verder met de zelfstudie voor het laden van gegevens.

> [!div class="nextstepaction"]
> [Gegevens in een SQL Data Warehouse laden](load-data-from-azure-blob-storage-using-polybase.md)

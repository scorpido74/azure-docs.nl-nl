---
title: De compute in Synapse SQL pool onderbreken en hervatten via de Azure Portal
description: Gebruik de Azure Portal voor het onderbreken van de reken kracht voor de SQL-groep om kosten op te slaan. Hervat de compute wanneer u klaar bent om het Data Warehouse te gebruiken.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3a131c1ebbf2a69f1c738cbc3421635406500d3c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130360"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Snelstartgids: de compute en resume in de Synapse SQL-groep via de Azure Portal onderbreken en hervatten

U kunt de Azure Portal gebruiken om de reken resources van de Synapse SQL-pool (Data Warehouse) te onderbreken en te hervatten. Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Voordat u begint

Gebruik [Create en Connect-Portal](create-data-warehouse-portal.md) om een SQL-groep met de naam **mySampleDataWarehouse**te maken. 

## <a name="pause-compute"></a>Compute onderbreken

Als u de kosten wilt verlagen, kunt u de reken resources op aanvraag onderbreken en hervatten. Als u bijvoorbeeld de data base niet meer gebruikt in de nacht en in het weekend, kunt u deze onderbreken tijdens deze tijden en deze op de dag hervatten. 
>[!NOTE]
>Er worden geen kosten in rekening gebracht voor reken resources wanneer de data base wordt onderbroken. Er worden echter nog steeds kosten in rekening gebracht voor opslag. 

Volg deze stappen om een SQL-groep te onderbreken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linker navigatie pagina van het Azure Portal.
2. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** om de SQL-groep te openen. 
3. Op de pagina **mySampleDataWarehouse** is de **status** van de melding **online**.

    ![Online berekenen](media/pause-and-resume-compute-portal/compute-online.png)

4. Klik op de knop **pause** om de SQL-groep te onderbreken. 
5. Er wordt een bevestigings vraag weer gegeven waarin u wordt gevraagd of u wilt door gaan. Klik op **Ja**.
6. Wacht even en u ziet dan dat de **status** is **onderbroken**.

    ![Onderbreken](media/pause-and-resume-compute-portal/pausing.png)

7. Wanneer de pauze bewerking is voltooid, wordt de status **onderbroken** en wordt het keuze rondje **hervat**.
8. De reken resources voor de SQL-groep zijn nu offline. Er worden geen kosten in rekening gebracht voor de reken kracht tot u de service hervat.

    ![Offline berekenen](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Compute hervatten

Volg deze stappen om de SQL-groep te hervatten.

1. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linker pagina van de Azure Portal.
2. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** om de pagina SQL-groep te openen. 
3. Op de pagina **mySampleDataWarehouse** wordt de **status** van de melding **onderbroken**.

    ![Offline berekenen](media/pause-and-resume-compute-portal/compute-offline.png)

4. Klik op **hervatten**om de SQL-groep te hervatten. 
5. Er wordt een bevestigings vraag weer gegeven waarin u wordt gevraagd of u wilt beginnen. Klik op **Ja**.
6. U ziet dat de **status** wordt **hervat**.

    ![Hervatten](media/pause-and-resume-compute-portal/resuming.png)

7. Wanneer de SQL-groep weer online is, is de status **online** en is het keuze rondje **pauzeren**.
8. De reken resources voor de SQL-groep zijn nu online en u kunt de service gebruiken. De kosten voor de reken kracht zijn hervat.

    ![Online berekenen](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Resources opschonen

Er worden kosten in rekening gebracht voor Data Warehouse-eenheden en de gegevens die zijn opgeslagen in de SQL-groep. Deze compute- en opslagresources worden apart in rekening gebracht. 

- Als u de gegevens in de opslag ruimte wilt bewaren, moet u de compute onderbreken.
- Als u toekomstige kosten wilt verwijderen, kunt u de SQL-groep verwijderen. 

Volg deze stappen om de resources op te schonen zoals gewenst.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en klik op uw SQL-groep.

    ![Resources opschonen](media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Als u het berekenen wilt onderbreken, klikt u op de knop **Onderbreken**. 

2. Als u de SQL-groep wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, klikt u op **verwijderen**.

3. Als u de door u gemaakte SQL-Server wilt verwijderen, klikt u op **sqlpoolservername.database.Windows.net**en vervolgens op **verwijderen**.  

   > [!CAUTION]
   > Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de resourcegroep wilt verwijderen, klikt u op **myResourceGroup**. Klik vervolgens op **Resourcegroep verwijderen**.


## <a name="next-steps"></a>Volgende stappen

U hebt nu de reken kracht voor uw SQL-groep gepauzeerd en hervat. Ga door naar het volgende artikel voor meer informatie over het [laden van gegevens in een SQL-groep](load-data-from-azure-blob-storage-using-polybase.md). Zie het artikel [Compute-overzicht beheren](sql-data-warehouse-manage-compute-overview.md) voor meer informatie over het beheren van reken mogelijkheden. 


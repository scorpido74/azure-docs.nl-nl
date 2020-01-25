---
title: Een ondersteunings ticket maken
description: Maak een ondersteuningsticket in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717834"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Een ondersteuningsticket maken voor SQL Data Warehouse
Als u problemen ondervindt met uw SQL Data Warehouse, kunt u een ondersteunings ticket maken zodat het technische ondersteunings team u kan helpen.

## <a name="create-a-support-ticket"></a>Een ondersteuningsticket maken
1. Open de [Azure Portal](https://portal.azure.com/).
2. Klik in het beginscherm op het tabblad **Help en ondersteuning**.
   
    ![Help en ondersteuning](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. Klik op de blade Help en ondersteuning op **Nieuw ondersteuningsverzoek** en vul de blade **Basisbeginselen** in.

   Selecteer uw [Azure-ondersteuningsplan](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * Ondersteuning voor het **beheren van facturering, quota en abonnementen** is op alle ondersteuningsniveaus beschikbaar.
   * Ondersteuning voor **het oplossen** van problemen wordt geboden via [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional direct](https://azure.microsoft.com/support/plans/prodirect/)of [premier](https://azure.microsoft.com/support/plans/premier/) support. Problemen die verband houden met schadevergoeding, zijn problemen waar klanten op stuiten tijdens het gebruik van Azure, waarbij er redelijkerwijs van kan worden uitgegaan dat het probleem door Microsoft is veroorzaakt.
   * **Begeleiding van ontwikkelaars** en **adviesdiensten** zijn beschikbaar op de ondersteuningsniveaus [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) en [Premier](https://azure.microsoft.com/support/plans/premier/). 
     
     Als u een Premier-ondersteuningsplan hebt, kunt u ook problemen met betrekking tot SQL Data Warehouse rapporteren in de [Microsoft Premier Online-portal](https://premier.microsoft.com/). Raadpleeg [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) voor meer informatie over de verschillende ondersteunings plannen, waaronder bereik, reactie tijden, prijzen enzovoort.  Zie Veelgestelde vragen over [ondersteuning](https://azure.microsoft.com/support/faq/)van Azure voor meer informatie over ondersteuning voor Azure.  
        
     ![Blade basisbeginselen](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![BladeBasisbeginselen1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Vul de blade **Probleem** in.

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Elke SQL-server (bijvoorbeeld myserver.database.windows.net) heeft standaard een **DTU-quotum** van 45.000. Dit quotum is gewoon een veiligheidsbeperking. U kunt uw quotum verhogen door een ondersteuningsticket te maken en *Quotum* als het aanvraagtype te selecteren. Als u uw DTU-behoeften wilt berekenen, vermenigvuldigt u 7,5 met het totale aantal [DWU](sql-data-warehouse-overview-what-is.md) dat nodig is. Als u bijvoorbeeld twee DW6000’s op één SQL server wilt hosten, moet u een DTU-quotum van 90.000 aanvragen.  U kunt uw huidige DTU-verbruik zien in de blade voor de SQL-server in de portal. Zowel onderbroken als niet-onderbroken databases tellen mee voor het DTU-quotum. 
   > 
   > 
   
5. Voer uw **contactgegevens** in.

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Klik op **Maken** om het ondersteuningsverzoek in te dienen.

## <a name="monitor-a-support-ticket"></a>Een ondersteuningsticket bewaken
Nadat u het ondersteunings verzoek hebt ingediend, neemt het ondersteunings team van Azure contact met u op. Als u de aanvraagstatus en -details wilt controleren, klikt u op het dashboard op **Alle ondersteuningsaanvragen**.

![Status controleren](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Overige bronnen
U kunt ook verbinding maken met de SQL Data Warehouse Community op [stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) of via het [MSDN-forum Azure SQL Data Warehouse](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 

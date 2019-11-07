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
ms.openlocfilehash: 12a977e26f458fa8ee8a58c44985d9d1ae47d5c3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692734"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Een ondersteuningsticket maken voor SQL Data Warehouse
Als u problemen ondervindt met SQL Data Warehouse, kunt u een ondersteuningsticket maken zodat ons technisch ondersteuningsteam u kan helpen.

## <a name="create-a-support-ticket"></a>Een ondersteuningsticket maken
1. Open de [Azure Portal][Azure portal].
2. Klik in het beginscherm op het tabblad **Help en ondersteuning**.
   
    ![Help en ondersteuning](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. Klik op de blade Help en ondersteuning op **Nieuw ondersteuningsverzoek** en vul de blade **Basisbeginselen** in.

   Selecteer uw [Azure-ondersteuningsplan][Azure support plan].
   
   * Ondersteuning voor het **beheren van facturering, quota en abonnementen** is op alle ondersteuningsniveaus beschikbaar.
   * Ondersteuning voor **het oplossen** van problemen wordt geboden via [Developer][Developer], [Standard][Standard], [Professional direct][Professional Direct]of [premier][Premier] support. Het gaat hierbij om problemen die klanten ondervinden tijdens het gebruik van Azure waarbij er een redelijke verwachting is dat het probleem is veroorzaakt door Microsoft.
   * **Begeleiding van ontwikkelaars** en **adviesdiensten** zijn beschikbaar op de ondersteuningsniveaus [Professional Direct][Professional Direct] en [Premier][Premier]. 
     
     Als u een Premier-ondersteuningsplan hebt, kunt u ook problemen met betrekking tot SQL Data Warehouse rapporteren in de [Microsoft Premier Online-portal][Microsoft Premier online portal].  Raadpleeg [ondersteunings abonnementen voor Azure][Azure support plan] voor meer informatie over de verschillende ondersteunings plannen, waaronder bereik, reactie tijden, prijzen enzovoort.  Zie Veelgestelde vragen over [ondersteuning][Azure support FAQs]van Azure voor meer informatie over ondersteuning voor Azure.  
        
     ![Blade basisbeginselen](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![BladeBasisbeginselen1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Vul de blade **Probleem** in.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Elke SQL-server (bijvoorbeeld myserver.database.windows.net) heeft standaard een **DTU-quotum** van 45.000. Dit quotum is gewoon een veiligheidsbeperking. U kunt uw quotum verhogen door een ondersteuningsticket te maken en *Quotum* als het aanvraagtype te selecteren. Als u uw DTU-behoeften wilt berekenen, vermenigvuldigt u 7,5 met het totale aantal [DWU][DWU] dat nodig is. Als u bijvoorbeeld twee DW6000’s op één SQL server wilt hosten, moet u een DTU-quotum van 90.000 aanvragen.  U kunt uw huidige DTU-verbruik zien in de blade voor de SQL-server in de portal. Zowel onderbroken als niet-onderbroken databases tellen mee voor het DTU-quotum. 
   > 
   > 
   
5. Voer uw **contactgegevens** in.
   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Klik op **Maken** om het ondersteuningsverzoek in te dienen.

## <a name="monitor-a-support-ticket"></a>Een ondersteuningsticket bewaken
Nadat u de ondersteuningsaanvraag hebt ingediend, zal het ondersteuningsteam van Azure contact met u opnemen. Als u de aanvraagstatus en -details wilt controleren, klikt u op het dashboard op **Alle ondersteuningsaanvragen**.

![Status controleren](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Meer informatie
Daarnaast kunt u verbinding maken met de SQL Data Warehouse-community op [Stack Overflow][Stack Overflow] of op het [MSDN-forum Azure SQL Data Warehouse][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/


---
title: Een ondersteunings ticket maken
description: Een ondersteunings ticket maken in azure Synapse Analytics.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195668"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>Een ondersteunings ticket maken voor Azure Synapse Analytics
Als u problemen ondervindt met Azure Synapse Analytics, maakt u een ondersteunings ticket zodat het technische ondersteunings team u kan helpen.

## <a name="create-a-support-ticket"></a>Een ondersteuningsticket maken
1. Open de [Azure-portal](https://portal.azure.com/).
1. Klik in het beginscherm op het tabblad **Help en ondersteuning**.
   
    ![Help en ondersteuning](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. Controleer uw [ondersteunings abonnement voor Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * Ondersteuning voor het **beheren van facturering, quota en abonnementen** is op alle ondersteuningsniveaus beschikbaar.
   * Ondersteuning voor **het oplossen** van problemen wordt geboden via [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional direct](https://azure.microsoft.com/support/plans/prodirect/)of [premier](https://azure.microsoft.com/support/plans/premier/) support. Het gaat hierbij om problemen die klanten ondervinden tijdens het gebruik van Azure waarbij er een redelijke verwachting is dat het probleem is veroorzaakt door Microsoft.
   * **Begeleiding van ontwikkelaars** en **adviesdiensten** zijn beschikbaar op de ondersteuningsniveaus [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) en [Premier](https://azure.microsoft.com/support/plans/premier/). 
     
     Als u een Premier-ondersteuningsplan hebt, kunt u ook problemen met betrekking tot SQL Data Warehouse rapporteren in de [Microsoft Premier Online-portal](https://premier.microsoft.com/). Raadpleeg [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) voor meer informatie over de verschillende ondersteunings plannen, waaronder bereik, reactie tijden, prijzen enzovoort.  Zie Veelgestelde vragen over [ondersteuning](https://azure.microsoft.com/support/faq/)van Azure voor meer informatie over ondersteuning voor Azure.
1. Selecteer op de pagina **Help en ondersteuning** de optie **nieuwe ondersteunings aanvraag**. Selecteer een probleem type in de vervolg keuzelijst. Vul vervolgens de gegevens in op het tabblad **basis beginselen** . Voer een **samen vatting** van het probleem in en selecteer vervolgens een **probleem type** in het menu en selecteer Opslaan.

    ![Help en ondersteuning](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > Elke SQL-server (bijvoorbeeld myserver.database.windows.net) heeft standaard een **DTU-quotum** van 45.000. Dit quotum is gewoon een veiligheidsbeperking. U kunt uw quotum verhogen door een ondersteuningsticket te maken en *Quotum* als het aanvraagtype te selecteren. Als u uw DTU-behoeften wilt berekenen, vermenigvuldigt u 7,5 met het totale aantal [DWU](sql-data-warehouse-overview-what-is.md) dat nodig is. Als u bijvoorbeeld twee DW6000’s op één SQL server wilt hosten, moet u een DTU-quotum van 90.000 aanvragen.  U kunt uw huidige DTU-verbruik zien in de blade voor de SQL-server in de portal. Zowel onderbroken als niet-onderbroken databases tellen mee voor het DTU-quotum. 
   > 

1. Mogelijk ziet u oplossingen om het probleem op te lossen. Als het probleem niet wordt opgelost met de oplossingen die worden weer gegeven, selecteert u **volgende: Details**. Verzend de details van uw probleem en uw contact gegevens. Selecteer **volgende:
bekijken +** Details ![maken](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. Controleer uw gegevens en selecteer **maken** aan de onderkant van het formulier om de ondersteunings aanvraag in te dienen.

## <a name="monitor-a-support-ticket"></a>Een ondersteuningsticket bewaken
Nadat u het ondersteunings verzoek hebt ingediend, neemt het ondersteunings team van Azure contact met u op. Als u de aanvraagstatus en -details wilt controleren, klikt u op het dashboard op **Alle ondersteuningsaanvragen**.

![Status controleren](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Meer informatie
U kunt ook verbinding maken met de SQL Data Warehouse Community op [stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) of via het [MSDN-forum Azure SQL Data Warehouse](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 

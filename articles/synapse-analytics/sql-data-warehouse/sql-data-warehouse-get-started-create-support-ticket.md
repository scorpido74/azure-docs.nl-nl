---
title: Quotaverhogingen aanvragen en ondersteuning krijgen
description: Een ondersteuningsaanvraag maken in de Azure-portal voor Azure Synapse Analytics. Het aanvragen van quota verhogingen of krijg ondersteuning voor probleemoplossing.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: eaa6ceded9893b278b28d517cbddb303f8469e10
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350901"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Het aanvragen van quota wordt verhoogd en ondersteuning voor Azure Synapse Analytics

In dit artikel wordt beschreven hoe u een ondersteuningsticket indient in de Azure-portal voor Azure Synapse Analytics. Met dit proces u een quotumverhoging aanvragen of een technische ondersteuningsaanvraag indienen voor het technische ondersteuningsteam.

## <a name="create-a-support-ticket"></a>Een ondersteuningsticket maken

Gebruik de volgende stappen om een nieuwe ondersteuningsaanvraag te maken vanuit de Azure-portal voor Azure Synapse Analytics.

1. Selecteer **help + ondersteuning**in het menu Azure [portal](https://portal.azure.com) .

   ![De koppeling Help + ondersteuning](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. Selecteer in **Help + ondersteuning**de optie Nieuw **ondersteuningsverzoek**.

    ![Een nieuw ondersteuningsverzoek maken](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Bekijk uw [Azure-ondersteuningsplan](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * Ondersteuning voor het **beheren van facturering, quota en abonnementen** is op alle ondersteuningsniveaus beschikbaar.
   * **Break-fix-ondersteuning** wordt geleverd via [ondersteuning voor ontwikkelaars,](https://azure.microsoft.com/support/plans/developer/) [standaard,](https://azure.microsoft.com/support/plans/standard/) [professional direct](https://azure.microsoft.com/support/plans/prodirect/)of [Premier.](https://azure.microsoft.com/support/plans/premier/) Het gaat hierbij om problemen die klanten ondervinden tijdens het gebruik van Azure waarbij er een redelijke verwachting is dat het probleem is veroorzaakt door Microsoft.
   * **Begeleiding van ontwikkelaars** en **adviesdiensten** zijn beschikbaar op de ondersteuningsniveaus [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) en [Premier](https://azure.microsoft.com/support/plans/premier/).

   Als u een Premier-ondersteuningsplan hebt, u azure Synapse Analytics-problemen ook melden op de [onlineportal van Microsoft Premier.](https://premier.microsoft.com/) Zie [Azure-ondersteuningsplannen](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) voor meer informatie over de verschillende ondersteuningsplannen, waaronder scope, responstijden, prijzen, enz.  Zie [veelgestelde](https://azure.microsoft.com/support/faq/)vragen over Azure-ondersteuning voor veelgestelde vragen over Azure-ondersteuning.

1. Selecteer **bij Probleemtype**het juiste probleemtype. Selecteer Technische optie **voor**problemen met het oplossen van een break-fix . Selecteer **Service- en abonnementslimieten (quota)** voor aanvragen voor quotaverhoging.

   ![Een probleemtype selecteren](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > In dit artikel wordt de nadruk gevestigd op aanvragen voor het verhogen van quota. Maar u hier ook **Techniek** selecteren voor ondersteuningsaanvragen voor probleemoplossing. Als u **Techniek**selecteert, wordt u gevraagd een overzicht op te geven en vervolgens een probleemtype te identificeren door **Probleemtype selecteren**te selecteren . Mogelijk ziet u oplossingen om uw probleem op te lossen. Als de gepresenteerde oplossingen het probleem niet oplossen, selecteert u **Volgende:Details** en vult u het formulier in om het ondersteuningsticket in te dienen.

1. Selecteer **Azure Synapse Analytics** voor het **type Quotum**voor aanvragen voor het verhogen van quota. Selecteer **vervolgens Volgende: Oplossingen >>**.

   ![Een quotumtype selecteren](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Selecteer **in** het venster Details de optie **Details opgeven** om aanvullende informatie in te voeren.

   ![De link 'Details geven'](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Quotaaanvraagtypen

Als u op **Details opgeven klikt,** wordt in het venster **Quotadetails** weergegeven waarmee u aanvullende informatie toevoegen. In de volgende secties worden de verschillende quotaaanvragen beschreven die beschikbaar zijn voor Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Data Warehouse Units (DOM's) per server

Gebruik de volgende stappen om een verhoging van de DTO's per server aan te vragen.

1. Selecteer de **DTU's (Data Warehouse Units) per** serverquotumtype.

1. Selecteer **in** de lijst Resource de resource die u wilt targeten.

1. Voer in het veld **Quota aanvragen** de nieuwe DWU-limiet in die u aanvraagt.

   ![DWU-quotumgegevens](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Servers per abonnement

Gebruik de volgende stappen om een toename van het aantal servers per abonnement aan te vragen.

1. Selecteer het **type servers per abonnementsquotum.**

1. Selecteer **in** de lijst Locatie het Azure-gebied dat u wilt gebruiken. Het quotum is per abonnement in elke regio.

1. Voer in het veld **Nieuw quotum** uw aanvraag in voor het maximum aantal servers in die regio.

   ![Gegevens over serversquota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Abonnementstoegang tot een regio inschakelen

Sommige aanbiedingstypen zijn niet in elke regio beschikbaar. Mogelijk ziet u een fout als volgt:

`This location is not available for subscription`

Als uw abonnement toegang nodig heeft in een bepaalde regio, gebruikt u de optie **Andere quotaaanvraag** om toegang aan te vragen. Geef in uw aanvraag de aanbiedings- en SKU-gegevens op die u voor de regio wilt inschakelen. Zie [Azure Synapse Analytics-prijzen](https://azure.microsoft.com/pricing/details/synapse-analytics/)voor het verkennen van de opties voor het aanbieden en sku.

![Overige contingentgegevens](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Uw aanvraag indienen

De laatste stap is het invullen van de resterende details van uw SQL Database-ondersteuningsaanvraag. Selecteer **Vervolgens Volgende: Controleren +>>maken **en klik na het bekijken van de aanvraaggegevens op **Maken** om de aanvraag in te dienen.

## <a name="monitor-a-support-ticket"></a>Een ondersteuningsticket bewaken

Nadat u het ondersteuningsverzoek hebt ingediend, neemt het Azure-ondersteuningsteam contact met u op. Als u de aanvraagstatus en -details wilt controleren, klikt u op het dashboard op **Alle ondersteuningsaanvragen**.

![Status controleren](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Meer informatie

U ook verbinding maken met de Azure Synapse Analytics-community op [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) of via het Azure SQL Data [Warehouse MSDN-forum.](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)


---
title: Verbinding maken met SQL Server of Azure SQL Database-Azure Logic Apps | Microsoft Docs
description: Toegang tot en beheer van SQL-data bases op locatie of in de Cloud door werk stromen te automatiseren met Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 804a913d17c3151d07a1ecf229e2db148dc45558
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050767"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Verbinding maken met SQL Server of Azure SQL Database vanuit Azure Logic Apps

In dit artikel wordt beschreven hoe u toegang kunt krijgen tot gegevens in uw SQL database vanuit een logische app met de SQL Server-connector. Op die manier kunt u taken, processen en werk stromen automatiseren die uw SQL-gegevens en-resources beheren door logische apps te maken. De connector werkt zowel voor [SQL Server on](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) -premises als voor [Azure SQL database in de Cloud](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

U kunt logische apps maken die worden uitgevoerd wanneer deze worden geactiveerd door gebeurtenissen in uw SQL database of in andere systemen, zoals Dynamics CRM Online. Uw Logic apps kunnen ook gegevens ophalen, invoegen en verwijderen, samen met het uitvoeren van SQL-query's en opgeslagen procedures. U kunt bijvoorbeeld een logische app maken waarmee automatisch wordt gecontroleerd op nieuwe records in Dynamics CRM Online, items toevoegt aan uw SQL database voor nieuwe records en vervolgens e-mail waarschuwingen worden verzonden.

Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Quick Start: Maak uw eerste logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Zie de naslag informatie over de [SQL Server-connector](https://docs.microsoft.com/connectors/sql/)voor connector-specifieke technische gegevens.

## <a name="prerequisites"></a>Vereisten

* De logische app waar u toegang tot uw SQL database nodig hebt. Als u uw logische app wilt starten met een SQL-trigger, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig. 

* Een [Azure SQL database](../sql-database/sql-database-get-started-portal.md) -of [SQL Server-Data Base](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Uw tabellen moeten gegevens bevatten zodat uw logische app resultaten kan retour neren wanneer bewerkingen worden aangeroepen. Als u een Azure SQL Database maakt, kunt u voorbeeld databases gebruiken die zijn opgenomen. 

* De naam van uw SQL-Server, de database naam, uw gebruikers naam en uw wacht woord. U hebt deze referenties nodig zodat u uw logica kunt machtigen om toegang te krijgen tot uw SQL Server. 

  * Voor Azure SQL Database kunt u deze details vinden in de connection string of in de Azure Portal onder de SQL Database eigenschappen:

    "Server = TCP: <*yourServerName*>. data base. Windows. net, 1433; Initial Catalog = <*yourDatabaseName*>; Beveiligings gegevens persistent maken = False; Gebruikers-ID = <*yourUserName*>; Wacht woord = <*yourPassword*>; MultipleActiveResultSets = False; Versleutelen = True; TrustServerCertificate = False; Verbindingstime-out = 30; "

  * Voor SQL Server kunt u deze informatie vinden in het connection string: 

    "Server = <*yourServerAddress*>;D atabase = <*yourDatabaseName*>; Gebruikers-id = <*yourUserName*>; Wacht woord = <*yourPassword*>; "

* Voordat u logische apps kunt verbinden met on-premises systemen, zoals SQL Server, moet u [een on-premises gegevens gateway instellen](../logic-apps/logic-apps-gateway-install.md). Op die manier kunt u de gateway selecteren wanneer u de SQL-verbinding voor uw logische app maakt.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>SQL-trigger toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een bepaalde gebeurtenis plaatsvindt of wanneer aan een bepaalde voor waarde wordt voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-Engine een exemplaar van een logische app en begint de werk stroom van uw app uit te voeren.

1. Maak in de Azure Portal of Visual Studio een lege logische app, waarmee Logic Apps Designer wordt geopend. In dit voor beeld wordt de Azure Portal gebruikt.

2. Voer in het zoekvak ' SQL Server ' in als uw filter. Selecteer in de lijst triggers de gewenste SQL-trigger. 

   Voor dit voor beeld selecteert u deze trigger: **SQL Server: wanneer een item wordt gemaakt**

   ![Selecteer de trigger SQL Server wanneer een item is gemaakt](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Als u wordt gevraagd om de verbindings gegevens, [maakt u nu uw SQL-verbinding](#create-connection). 
   Als uw verbinding al bestaat, selecteert u de naam van de **tabel** die u wilt in de lijst.

   ![Tabel selecteren](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Stel de eigenschappen **interval** en **frequentie** in, waarmee wordt aangegeven hoe vaak uw logische app de tabel controleert.

   In dit voor beeld wordt alleen de geselecteerde tabel gecontroleerd, niets anders. 
   Als u iets interessanter wilt maken, voegt u acties toe waarmee de gewenste taken worden uitgevoerd. 
   
   Als u bijvoorbeeld het nieuwe item in de tabel wilt weer geven, kunt u andere acties toevoegen, zoals het maken van een bestand met velden uit de tabel en e-mail waarschuwingen verzenden. 
   Zie [Logic apps](../connectors/apis-list.md)-connectors voor meer informatie over andere acties voor deze connector of andere connectors.

5. Wanneer u klaar bent, selecteert u op de werk balk ontwerpen de optie **Opslaan**. 

   Met deze stap wordt uw logische app Live in azure automatisch ingeschakeld en gepubliceerd. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>SQL-actie toevoegen

In Azure Logic Apps is een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) een stap in uw werk stroom die volgt op een trigger of een andere actie. In dit voor beeld wordt de logische app gestart met de [terugkeer patroon](../connectors/connectors-native-recurrence.md)en wordt een actie aangeroepen waarmee een rij uit een SQL database wordt opgehaald.

1. Open in de Azure Portal of Visual Studio uw logische app in Logic Apps Designer. In dit voor beeld wordt de Azure Portal gebruikt.

2. In de Logic app Designer, onder de trigger of actie, selecteert u **nieuwe stap** > **een actie toevoegen**.

   ![Selecteer ' nieuwe stap ', ' een actie toevoegen '](./media/connectors-create-api-sqlazure/add-action.png)
   
   Als u een actie wilt toevoegen tussen de bestaande stappen, plaatst u de muis aanwijzer op de verbindings pijl. 
   Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

2. Voer in het zoekvak ' SQL Server ' in als uw filter. Selecteer in de lijst acties de gewenste SQL-actie. 

   Voor dit voor beeld selecteert u deze actie. Hiermee wordt één record opgehaald: **SQL Server-rij ophalen**

   ![Voer SQL Server in, selecteer SQL Server-rij ophalen](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Als u wordt gevraagd om de verbindings gegevens, [maakt u nu uw SQL-verbinding](#create-connection). 
   Als uw verbinding bestaat, selecteert u een **tabel naam**en voert u de **rij-id** voor de gewenste record in.

   ![De tabel naam en de rij-ID invoeren](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   In dit voor beeld wordt slechts één rij uit de geselecteerde tabel geretourneerd, niets anders. 
   Als u de gegevens in deze rij wilt weer geven, kunt u andere acties toevoegen waarmee een bestand wordt gemaakt met velden uit de rij voor latere controle en dat bestand opslaan in een account voor Cloud opslag. Zie [Logic apps](../connectors/apis-list.md)-connectors voor meer informatie over andere acties in deze connector of andere connectors.

4. Wanneer u klaar bent, selecteert u op de werk balk ontwerpen de optie **Opslaan**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Verbinding maken met uw database

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Bulk gegevens verwerken

Soms moet u met de resultaten sets zo groot zijn dat de connector niet alle resultaten tegelijk retourneert, of u wilt de controle over de grootte en de structuur voor uw resultaten sets beter te bepalen. Hier volgt een aantal manieren waarop u dergelijke grote resultaten sets kunt verwerken:

* Schakel *paginering*in om de resultaten te beheren als kleinere sets. Zie voor meer informatie [bulk gegevens, records en items ophalen](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)met behulp van paginering.

* Maak een opgeslagen procedure waarmee de resultaten op de gewenste manier worden georganiseerd.

  Wanneer u meerdere rijen haalt of invoegt, kan de logische app deze rijen door lopen met behulp van een [*until-lus*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) binnen deze [grenzen](../logic-apps/logic-apps-limits-and-config.md). 
  Als uw logische app echter moet werken met record sets zo groot is, bijvoorbeeld duizenden of miljoenen rijen, die u de kosten wilt beperken die voortvloeien uit aanroepen naar de data base.

  Als u de resultaten op de gewenste manier wilt indelen, kunt u een [*opgeslagen procedure*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) maken die wordt uitgevoerd in uw SQL-exemplaar en gebruikmaakt van de **Select-order by-** instructie. 
  Met deze oplossing hebt u meer controle over de grootte en de structuur van uw resultaten. 
  Uw logische app roept de opgeslagen procedure aan met behulp van de actie voor het uitvoeren van een **opgeslagen procedure** van de SQL Server-connector.

  Raadpleeg de volgende artikelen voor meer informatie over de oplossing:

  * [SQL-paginering voor bulk overdracht van gegevens met Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT-ORDER BY-component](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Connector-specifieke Details

Zie de [referentie Details van de connector](/connectors/sql/)voor technische informatie over de triggers, acties en limieten van deze connector. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors


---
title: bestand opnemen
description: bestand opnemen
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 05/15/2018
ms.openlocfilehash: d60d7727e0674298fa6da7e7330221318da23efd
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161661"
---
* Als u Azure SQL Database gebruikt, volgt u de stappen onder [verbinding maken met Azure SQL database](#connect-azure-sql-db).

* Als u SQL Server gebruikt, volgt u de stappen onder [verbinding maken met SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Verbinding maken met Azure SQL Database

Wanneer de SQL-trigger of actie u vraagt om verbindings gegevens, voert u de volgende stappen uit om zowel triggers als acties te gebruiken.

1. Maak voor de **verbindings naam**een naam voor de verbinding.

1. Selecteer onder **SQL Server naam**uw Azure SQL-Server. Wanneer de lijst **SQL database naam** wordt weer gegeven, selecteert u uw data base. Geef de gebruikers naam en het wacht woord op voor uw Azure SQL-Server.

   U kunt deze informatie ook vinden in de Azure Portal onder uw SQL database eigenschappen of in uw connection string:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Verbinding maken met Azure SQL Database](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Als u gereed bent, selecteert u **Maken**.

1. Nadat u de verbinding hebt gemaakt, gaat u door met [het toevoegen van een SQL-trigger](#add-sql-trigger) of [het toevoegen van een SQL-actie](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Verbinding maken met SQL Server

Wanneer de SQL-trigger of actie u vraagt om verbindings gegevens, voert u de volgende stappen uit om zowel triggers als acties te gebruiken. Voordat u begint, moet u er echter voor zorgen dat u [uw on-premises gegevens gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)al hebt ingesteld. Anders wordt uw gateway niet weer gegeven in de lijst met gateways wanneer u de verbinding maakt.

1. Maak voor de **verbindings naam**een naam voor de verbinding.

1. Selecteer in de trigger of actie **verbinding maken via een on-premises gegevens gateway** zodat de opties van SQL Server worden weer gegeven.

1. Voor de naam van de **SQL-Server** en de **SQL database**, geeft u het adres voor uw SQL-Server en de naam van uw Data Base op. Geef de gebruikers naam en het **wacht woord voor**de **server op.**

   U kunt deze informatie ook vinden in uw connection string:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Verbinding maken met SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Als uw SQL server gebruikmaakt van Windows of basis verificatie, selecteert u het **verificatie type**.

1. Onder **gateways**selecteert u het Azure-abonnement dat is gekoppeld aan uw eerder gemaakte on-premises gegevens gateway en selecteert u de naam voor uw on-premises gegevens gateway.

   Als uw gateway niet in de lijst wordt weer gegeven, controleert u of u [de gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)correct hebt ingesteld.

   ![SQL Server verbinding maken is voltooid](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Als u gereed bent, selecteert u **Maken**.

1. Nadat u de verbinding hebt gemaakt, gaat u door met [SQL-trigger toevoegen](#add-sql-trigger) of [SQL-actie toevoegen](#add-sql-action).

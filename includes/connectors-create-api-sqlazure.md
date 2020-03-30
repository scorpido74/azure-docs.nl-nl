---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789190"
---
* Als u Azure SQL Database gebruikt, voert u de stappen uit onder [Verbinding maken met Azure SQL Database](#connect-azure-sql-db).

* Als u SQL Server gebruikt, voert u de stappen uit onder [Verbinding maken met SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Verbinding maken met Azure SQL Database

Wanneer de SQL-trigger of actie u om verbindingsgegevens vraagt, voert u deze stappen uit, die werken voor zowel triggers als acties.

1. Maak voor **Verbindingsnaam**een naam voor uw verbinding.

1. Selecteer **onder SQL Server-naam**uw Azure SQL-server. Wanneer de lijst **MET SQL-databasenaam** wordt weergegeven, selecteert u uw database. Geef de gebruikersnaam en het wachtwoord op voor uw Azure SQL-server.

   U deze informatie ook vinden in de Azure-portal onder de eigenschappen van uw SQL-database of in uw verbindingstekenreeks:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Verbinding maken met Azure SQL Database](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Als u gereed bent, selecteert u **Maken**.

1. Nadat u uw verbinding hebt gemaakt, gaat u verder met [Een SQL-trigger toevoegen](#add-sql-trigger) of [EEN SQL-actie toevoegen.](#add-sql-action)

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Verbinding maken met SQL Server

Wanneer de SQL-trigger of actie u om verbindingsgegevens vraagt, voert u deze stappen uit, die werken voor zowel triggers als acties. Voor scenario's waarvoor u de [on-premises gegevensgateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) op een lokale computer moet installeren en [de Azure-gegevensgatewaybron moet maken,](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)moet u ervoor zorgen dat u deze vereisten eerst voltooit. Anders wordt uw gatewaybron niet weergegeven in de gatewayslijst wanneer u uw verbinding maakt.

Als u Windows-verificatie wilt gebruiken met de SQL Server-connector in een [integratieserviceomgeving (ISE),](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)gebruikt u ook de niet-ISE-versie van de connector en de on-premises gegevensgateway. De versie met ISE-label biedt geen ondersteuning voor Windows-verificatie.

1. Maak voor **Verbindingsnaam**een naam voor uw verbinding.

1. Selecteer verbinding **maken via on-premises gegevensgateway** in de trigger of actie, zodat de SQL-serveropties worden weergegeven.

1. Geef voor **de naam van sql-servernaam** en **SQL-database**het adres op voor uw SQL-server en de naam voor uw database. Geef **bij gebruikersnaam** en **wachtwoord**de gebruikersnaam en het wachtwoord voor uw server op.

   U deze informatie ook vinden in uw verbindingstekenreeks:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Verbinding maken met SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Als uw SQL-server Windows- of Basisverificatie gebruikt, selecteert u **het verificatietype**.

1. Selecteer **onder Gateways**het Azure-abonnement dat is gekoppeld aan uw eerder gemaakte on-premises gegevensgateway en selecteer de naam voor uw on-premises gegevensgateway.

   Als uw gateway niet in de lijst wordt weergegeven, controleert u of u de gateway correct [hebt ingesteld.](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)

   ![SQL Server-verbinding voltooid maken](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Als u gereed bent, selecteert u **Maken**.

1. Nadat u uw verbinding hebt gemaakt, gaat u verder met [SQL-trigger toevoegen](#add-sql-trigger) of [SQL-actie toevoegen.](#add-sql-action)

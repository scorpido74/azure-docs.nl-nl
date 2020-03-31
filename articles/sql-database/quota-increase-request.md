---
title: Een quotumverhoging aanvragen
description: Op deze pagina wordt beschreven hoe u een ondersteuningsaanvraag maakt om de quota voor afzonderlijke databases, servers en beheerde exemplaren van Azure SQL Database te verhogen.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586152"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Verhoging van het aanvraagquotum voor Azure SQL-database

In dit artikel wordt uitgelegd hoe u een quotumverhoging voor Azure SQL Database aanvragen voor afzonderlijke databases, servers en beheerde exemplaren. Het legt ook uit hoe u abonnementstoegang tot een regio inschakelen.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>Een nieuw ondersteuningsverzoek maken

Gebruik de volgende stappen om een nieuwe ondersteuningsaanvraag te maken vanuit de Azure-portal voor SQL Database.

1. Selecteer **help + ondersteuning**in het menu Azure [portal](https://portal.azure.com) .

   ![De koppeling Help + ondersteuning](./media/quota-increase-request/help-plus-support.png)

1. Selecteer in **Help + ondersteuning**de optie Nieuw **ondersteuningsverzoek**.

    ![Een nieuw ondersteuningsverzoek maken](./media/quota-increase-request/new-support-request.png)

1. Selecteer **Service- en abonnementslimieten (quota)** **voor issuetype.**

   ![Een probleemtype selecteren](./media/quota-increase-request/select-quota-issue-type.png)

1. Selecteer **bij Abonnement**het abonnement waarvan u het quotum wilt verhogen.

   ![Een abonnement selecteren voor een verhoogd quotum](./media/quota-increase-request/select-subscription-support-request.png)

1. Selecteer **voor quotumtype**een van de volgende quotatypen:

   - **SQL-database** voor afzonderlijke database- en elastische poolquota.
   - **SQL Database Managed Instance** voor beheerde exemplaren.

   Selecteer **vervolgens Volgende: Oplossingen >>**.

   ![Een quotumtype selecteren](./media/quota-increase-request/select-quota-type.png)

1. Selecteer **in** het venster Details de optie **Details opgeven** om aanvullende informatie in te voeren.

   ![De link 'Details geven'](./media/quota-increase-request/provide-details-link.png)

Als u op **Details opgeven klikt,** wordt in het venster **Quotadetails** weergegeven waarmee u aanvullende informatie toevoegen. In de volgende secties worden de verschillende opties voor **SQL Database-** en **SQL Database Managed Instance-quotatypen** beschreven.

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>SQL-databasequotatypen

In de volgende secties worden drie quotaverhogende opties voor de **SQL Database-quotatypen** beschreven:

- DTU's (Databasetransactie-eenheden) per server
- Servers per abonnement
- Abonnementstoegang tot een regio inschakelen

### <a name="database-transaction-units-dtus-per-server"></a>DTU's (Databasetransactie-eenheden) per server

Gebruik de volgende stappen om een verhoging van de DTU's per server aan te vragen.

1. Selecteer de **DTU's (Databasetransactieeenheden) per** serverquotumtype.

1. Selecteer **in** de lijst Resource de resource die u wilt targeten.

1. Voer in het veld **Nieuw quotum** de nieuwe DTU-limiet in die u aanvraagt.

   ![DTU-quotumgegevens](./media/quota-increase-request/quota-details-dtus.png)

Zie [Resourcelimieten voor afzonderlijke databases met behulp van het DTU-inkoopmodel](sql-database-dtu-resource-limits-single-databases.md) en [resources-limieten voor elastische pools met behulp van het DTU-inkoopmodel](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servers per abonnement

Gebruik de volgende stappen om een toename van het aantal servers per abonnement aan te vragen.

1. Selecteer het **type servers per abonnementsquotum.**

1. Selecteer **in** de lijst Locatie het Azure-gebied dat u wilt gebruiken. Het quotum is per abonnement in elke regio.

1. Voer in het veld **Nieuw quotum** uw aanvraag in voor het maximum aantal servers in die regio.

   ![Gegevens over serversquota](./media/quota-increase-request/quota-details-servers.png)

Zie [SQL Database resource limits en resource governance](sql-database-resource-limits-database-server.md)voor meer informatie.

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>Abonnementstoegang tot een regio inschakelen

Sommige aanbiedingstypen zijn niet in elke regio beschikbaar. Mogelijk ziet u een fout als volgt:

`This location is not available for subscription`

Als uw abonnement toegang nodig heeft in een bepaalde regio, gebruikt u de optie **Andere quotaaanvraag** om toegang aan te vragen. Geef in uw aanvraag de aanbiedings- en SKU-gegevens op die u voor de regio wilt inschakelen. Zie [Azure SQL Database-prijzen](https://azure.microsoft.com/pricing/details/sql-database/single/)voor het verkennen van de opties voor het aanbieden en sku.

![Overige contingentgegevens](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>Beheerd instantiequotumtype

Gebruik de volgende stappen voor het quotumtype **SQL Server Managed Instance:**

1. Selecteer **in** de lijst Regio het Azure-gebied dat u wilt targeten.

1. Voer de nieuwe limieten in die u aanvraagt voor **Subnet** en **vCore.**

   ![Details van beheerde instantiequota](./media/quota-increase-request/quota-details-managed-instance.png)

Zie [Overzicht Azure SQL Database managed instance resource limits](sql-database-managed-instance-resource-limits.md)voor meer informatie.

## <a name="submit-your-request"></a>Uw aanvraag indienen

De laatste stap is het invullen van de resterende details van uw SQL Database-quotumaanvraag. Selecteer **Vervolgens Volgende: Controleren +>>maken **en klik na het bekijken van de aanvraaggegevens op **Maken** om de aanvraag in te dienen.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw aanvraag hebt ingediend, wordt het beoordeeld. U wordt gecontacteerd met een antwoord op basis van de informatie die u in het formulier.

Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over andere Azure-limieten.

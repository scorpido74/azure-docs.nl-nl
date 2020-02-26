---
title: Een quotum verhoging aanvragen
description: Op deze pagina wordt beschreven hoe u een ondersteunings aanvraag maakt om de quota's voor Azure SQL Database afzonderlijke data bases, servers en beheerde exemplaren te verhogen.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586152"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Toename van quotum aanvragen voor Azure SQL Database

In dit artikel wordt uitgelegd hoe u een quotum verhoging kunt aanvragen voor Azure SQL Database voor afzonderlijke data bases, servers en beheerde exemplaren. Ook wordt uitgelegd hoe u de toegang tot een regio kunt inschakelen voor abonnementen.

## <a id="newquota"></a>Een nieuwe ondersteunings aanvraag maken

Gebruik de volgende stappen om een nieuwe ondersteunings aanvraag te maken op basis van de Azure Portal voor SQL Database.

1. Selecteer **Help en ondersteuning**in het menu [Azure Portal](https://portal.azure.com) .

   ![De koppeling Help en ondersteuning](./media/quota-increase-request/help-plus-support.png)

1. Selecteer **nieuwe ondersteunings aanvraag**in **Help en ondersteuning**.

    ![Een nieuwe ondersteunings aanvraag maken](./media/quota-increase-request/new-support-request.png)

1. Selecteer voor **probleem type** **service-en abonnements limieten (quota's)** .

   ![Selecteer een probleem type](./media/quota-increase-request/select-quota-issue-type.png)

1. Selecteer bij **abonnement**het abonnement waarvan u het quotum wilt verhogen.

   ![Selecteer een abonnement voor een verhoogd quotum](./media/quota-increase-request/select-subscription-support-request.png)

1. Selecteer bij **quotum type**een van de volgende quotum typen:

   - **SQL database** voor de quota van één data base en elastische groep.
   - **SQL database beheerde instantie** voor beheerde exemplaren.

   Selecteer vervolgens **volgende: oplossingen > >** .

   ![Een quotum type selecteren](./media/quota-increase-request/select-quota-type.png)

1. Selecteer in het **detail** venster **Details opgeven** om aanvullende informatie in te voeren.

   ![De koppeling Details opgeven](./media/quota-increase-request/provide-details-link.png)

Als u op **Details geven** klikt, wordt het venster **quotum Details** weer gegeven waarin u aanvullende informatie kunt toevoegen. In de volgende secties vindt u een beschrijving van de verschillende opties voor **SQL database** en SQL database het quotum type **Managed instance** .

## <a id="sqldbquota"></a>SQL Database quotum typen

In de volgende secties worden drie quota verhogings opties beschreven voor de **SQL database** quotum typen:

- Data base Trans Action units (Dtu's) per server
- Servers per abonnement
- Abonnements toegang tot een regio inschakelen

### <a name="database-transaction-units-dtus-per-server"></a>Data base Trans Action units (Dtu's) per server

Gebruik de volgende stappen om een verhoging van de Dtu's per server aan te vragen.

1. Selecteer het **dtu's (data base Trans Action units) per server-** quotum type.

1. Selecteer in de lijst **resource** de resource die u wilt instellen.

1. Voer in het veld **nieuw quotum** de nieuwe DTU-limiet in die u aanvraagt.

   ![DTU-quotum Details](./media/quota-increase-request/quota-details-dtus.png)

Zie voor meer informatie [resource limieten voor afzonderlijke data bases met behulp van het DTU-aankoop model](sql-database-dtu-resource-limits-single-databases.md) en de [resource limieten voor elastische Pools met behulp van het DTU-aankoop model](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servers per abonnement

Gebruik de volgende stappen om een verhoging van het aantal servers per abonnement aan te vragen.

1. Selecteer het quotum type voor **servers per abonnement** .

1. Selecteer in de lijst **locatie** de Azure-regio die u wilt gebruiken. Het quotum is per abonnement in elke regio.

1. In het veld **nieuw quotum** voert u uw aanvraag in voor het maximum aantal servers in die regio.

   ![Details van servers-quota](./media/quota-increase-request/quota-details-servers.png)

Zie [SQL database resource limieten en resource governance](sql-database-resource-limits-database-server.md)voor meer informatie.

### <a id="other"></a>Abonnements toegang tot een regio inschakelen

Sommige aanbiedings typen zijn niet beschikbaar in elke regio. Er wordt mogelijk een fout bericht weer geven, zoals het volgende:

`This location is not available for subscription`

Als uw abonnement toegang moet hebben tot een bepaalde regio, gebruikt u de optie **andere quotum aanvraag** om toegang aan te vragen. Geef in uw aanvraag de aanbieding-en SKU-Details op die u wilt inschakelen voor de regio. Zie [Azure SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/single/)voor meer informatie over de aanbiedings-en SKU-opties.

![Andere quotum Details](./media/quota-increase-request/quota-details-whitelisting.png)

## <a id="sqlmiquota"></a>Quota type voor beheerd exemplaar

Gebruik de volgende stappen voor het quota type **SQL Server Managed instance** :

1. Selecteer in de lijst **regio** de Azure-regio die u wilt instellen.

1. Voer de nieuwe limieten in die u aanvraagt voor het **subnet** en de **vCore**.

   ![Details van beheerd exemplaar quotum](./media/quota-increase-request/quota-details-managed-instance.png)

Zie [overzicht Azure SQL database beheerde exemplaar resource limieten](sql-database-managed-instance-resource-limits.md)voor meer informatie.

## <a name="submit-your-request"></a>Uw aanvraag verzenden

De laatste stap bestaat uit het invullen van de resterende Details van uw SQL Database quotum aanvraag. Selecteer **volgende: controleren + > > maken**en klik na het controleren van de aanvraag gegevens op **maken** om de aanvraag in te dienen.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw aanvraag hebt ingediend, wordt deze gecontroleerd. U neemt contact met een antwoord op op basis van de informatie die u in het formulier hebt verstrekt.

Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over andere Azure-limieten.

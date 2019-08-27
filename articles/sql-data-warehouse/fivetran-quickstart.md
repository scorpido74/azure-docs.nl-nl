---
title: Snelstartgids voor Azure SQL Data Warehouse Fivetran | Microsoft Docs
description: Snel aan de slag met Fivetran en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d829ee67d516892283fa31d9180336d768170ac1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65857002"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Snel aan de slag met Fivetran en SQL Data Warehouse

In deze Quick Start wordt beschreven hoe u een nieuwe Fivetran-gebruiker instellen voor gebruik met Azure SQL Data Warehouse. Het artikel wordt ervan uitgegaan dat u een bestaand exemplaar van SQL Data Warehouse hebt.

## <a name="set-up-a-connection"></a>Een verbinding instellen

1. De volledig gekwalificeerde servernaam en databasenaam die u gebruikt om verbinding met SQL Data Warehouse te vinden.
    
    Als u hulp bij het vinden van deze informatie nodig hebt, raadpleegt u [verbinding maken met Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md).

2. Kies in de wizard setup of u wilt verbinding maken met uw database rechtstreeks of via een SSH-tunnel.

   Als u rechtstreeks verbinding maken met uw database, moet u een firewallregel om toegang te verlenen. Dit is de eenvoudigste en veiligste methode.

   Als u verbinding maken wilt met behulp van een SSH-tunnel, wordt Fivetran verbonden met een afzonderlijke server in uw netwerk. De server biedt een SSH-tunnel met uw database. U moet deze methode gebruiken als uw database zich in een niet-toegankelijke subnet in een virtueel netwerk.

3. Het IP-adres toevoegen **52.0.2.4** aan de firewall op serverniveau om binnenkomende verbindingen met uw SQL Data Warehouse-exemplaar van Fivetran.

   Zie [Een serverfirewallregel maken](create-data-warehouse-portal.md#create-a-server-level-firewall-rule) voor meer informatie.

## <a name="set-up-user-credentials"></a>Instellen van de referenties van gebruiker

1. Verbinding maken met uw Azure SQL Data Warehouse met behulp van SQL Server Management Studio of het hulpprogramma dat u de voorkeur geeft. Meld u aan als een beheerder van de server. Voer de volgende SQL-opdrachten voor het maken van een gebruiker voor Fivetran:
    - In de database master: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - In SQL Data Warehouse-database:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Verleen de gebruiker Fivetran de volgende machtigingen voor uw datawarehouse:

    ```
    GRANT CONTROL to fivetran;
    ```

    Bevoegdheid is vereist voor het maken van database-scoped referenties die worden gebruikt wanneer een gebruiker bestanden uit Azure Blob storage geladen met behulp van PolyBase.

3. Een resourceklasse geschikt toevoegen aan de gebruiker Fivetran. De resourceklasse die u gebruikt, is afhankelijk van het geheugen dat is vereist voor het maken van een columnstore-index. Bijvoorbeeld, integratie met producten zoals Marketo en Salesforce een hogere resourceklasse vanwege het grote aantal kolommen en de grotere hoeveelheid gegevens vereist de producten gebruiken. Een hogere resourceklasse vereist meer geheugen voor het maken van columnstore-indexen.

    U wordt aangeraden dat u statische resourceklassen. U kunt beginnen met de `staticrc20` resourceklasse. De `staticrc20` resourceklasse toegewezen 200 MB voor elke gebruiker, ongeacht het prestatieniveau dat u gebruikt. Als op het niveau van de eerste resource klasse columnstore-indexering mislukt, verhoogt u de resourceklasse.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Lees voor meer informatie over [limieten voor geheugen en gelijktijdigheid](memory-and-concurrency-limits.md) en [resourceklassen](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Aanmelden bij Fivetran

Als u wilt aanmelden bij Fivetran, voert u de referenties die u gebruikt voor toegang tot SQL Data Warehouse: 

* Host (de naam van uw server).
* Poort.
* Database.
* Gebruiker (de naam van de gebruiker moet **fivetran\@_servernaam_** waar *servernaam* maakt deel uit van uw Azure-host URI: ***servernaam*.database.Windows.NET**).
* Het wachtwoord.

---
title: Fivetran Quick Start
description: Ga snel aan de slag met Fivetran en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c9b4a15cd6cbae80d80407ba929bfbfa1402eeb5
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689224"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Snel aan de slag met Fivetran en SQL Data Warehouse

In deze Quick Start wordt beschreven hoe u een nieuwe Fivetran-gebruiker instelt om met Azure SQL Data Warehouse te werken. In dit artikel wordt ervan uitgegaan dat u een bestaand exemplaar van SQL Data Warehouse hebt.

## <a name="set-up-a-connection"></a>Een verbinding instellen

1. Zoek de volledig gekwalificeerde server naam en database naam die u gebruikt om verbinding te maken met SQL Data Warehouse.
    
    Zie [verbinding maken met Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md)als u hulp nodig hebt bij het vinden van deze informatie.

2. Kies in de installatie wizard of u rechtstreeks verbinding wilt maken met uw data base of met behulp van een SSH-tunnel.

   Als u ervoor kiest om rechtstreeks verbinding te maken met uw data base, moet u een firewall regel maken om toegang toe te staan. Deze methode is de eenvoudigste en veiligste methode.

   Als u ervoor kiest om verbinding te maken met behulp van een SSH-tunnel, maakt Fivetran verbinding met een afzonderlijke server in uw netwerk. De server biedt een SSH-tunnel voor uw data base. U moet deze methode gebruiken als uw data base zich in een niet-toegankelijk subnet in een virtueel netwerk bevindt.

3. Voeg het IP-adres **52.0.2.4** toe aan de firewall op server niveau om binnenkomende verbindingen met uw SQL Data Warehouse-exemplaar toe te staan vanaf Fivetran.

   Zie [Een serverfirewallregel maken](create-data-warehouse-portal.md#create-a-server-level-firewall-rule) voor meer informatie.

## <a name="set-up-user-credentials"></a>Gebruikers referenties instellen

1. Maak verbinding met uw Azure SQL Data Warehouse met behulp van SQL Server Management Studio of het hulp programma dat u wilt gebruiken. Meld u aan als een server beheerder gebruiker. Voer vervolgens de volgende SQL-opdrachten uit om een gebruiker te maken voor Fivetran:
    - In de hoofd database: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - In SQL Data Warehouse Data Base:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Ken de Fivetran-gebruiker de volgende machtigingen toe aan uw magazijn:

    ```
    GRANT CONTROL to fivetran;
    ```

    De machtiging beheren is vereist voor het maken van data base-Scope-referenties die worden gebruikt wanneer een gebruiker bestanden uit Azure Blob-opslag laadt met poly base.

3. Voeg een geschikte resource klasse toe aan de Fivetran-gebruiker. De resource klasse die u gebruikt, is afhankelijk van het geheugen dat vereist is voor het maken van een column store-index. Integraties met producten als Marketo en Sales Force vereisen bijvoorbeeld een hogere resource klasse vanwege het grote aantal kolommen en de grotere hoeveelheid gegevens die de producten gebruiken. Een hogere resource klasse vereist meer geheugen voor het maken van Column Store-indexen.

    U wordt aangeraden statische resource klassen te gebruiken. U kunt beginnen met de resource klasse `staticrc20`. De resource klasse `staticrc20` wijst 200 MB toe voor elke gebruiker, ongeacht het prestatie niveau dat u gebruikt. Als column Store-indexering mislukt op het eerste niveau van de resource klasse, verhoogt u de resource klasse.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Lees voor meer informatie over [geheugen en gelijktijdigheids limieten](memory-concurrency-limits.md) en [resource klassen](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Aanmelden bij Fivetran

Als u zich wilt aanmelden bij Fivetran, voert u de referenties in die u gebruikt voor toegang tot SQL Data Warehouse: 

* Host (uw server naam).
* Importeer.
* Database.
* Gebruiker (de gebruikers naam moet **fivetran\@_server_name_**  waarbij *server_name* deel uitmaakt van de URI van uw Azure-host:  **_Server\_name_. database.Windows.net**).
* Wacht woord.

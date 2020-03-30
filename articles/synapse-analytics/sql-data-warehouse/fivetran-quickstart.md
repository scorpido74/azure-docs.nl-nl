---
title: 'Quickstart: Fivetran en data warehouse'
description: Ga aan de slag met Fivetran en een Azure Synapse Analytics datawarehouse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: b4742f48ee9ad0db60e21dd53c5c0f447c1ded67
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348923"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Quickstart: Fivetran met datawarehouse 

In deze quickstart wordt beschreven hoe u een nieuwe Fivetran-gebruiker instelt voor gebruik met een Azure Synapse Analytics-gegevensmagazijn dat is uitgerust met een SQL-groep. Het artikel gaat ervan uit dat u een bestaand gegevensmagazijn hebt.

## <a name="set-up-a-connection"></a>Een verbinding instellen

1. Zoek de volledig gekwalificeerde servernaam en databasenaam die u gebruikt om verbinding te maken met uw gegevensmagazijn.
    
    Zie [Verbinding maken met uw gegevensmagazijn](sql-data-warehouse-connect-overview.md)als u hulp nodig hebt bij het vinden van deze informatie.

2. Kies in de wizard Setup of u uw database rechtstreeks wilt verbinden of met behulp van een SSH-tunnel.

   Als u ervoor kiest om rechtstreeks verbinding te maken met uw database, moet u een firewallregel maken om toegang toe te staan. Deze methode is de eenvoudigste en veiligste methode.

   Als u ervoor kiest om verbinding te maken via een SSH-tunnel, maakt Fivetran verbinding met een aparte server in uw netwerk. De server biedt een SSH-tunnel naar uw database. U moet deze methode gebruiken als uw database zich in een ontoegankelijk subnet op een virtueel netwerk bevindt.

3. Voeg het IP-adres **52.0.2.4** toe aan uw firewall op serverniveau om inkomende verbindingen met uw gegevensmagazijnexemplaar van Fivetran toe te staan.

   Zie [Een serverfirewallregel maken](create-data-warehouse-portal.md#create-a-server-level-firewall-rule) voor meer informatie.

## <a name="set-up-user-credentials"></a>Gebruikersreferenties instellen

1. Maak verbinding met uw datawarehouse met SQL Server Management Studio (SSMS) of de tool die u verkiest. Meld u aan als serverbeheerder. Voer vervolgens de volgende SQL-opdrachten uit om een gebruiker voor Fivetran te maken:

    - In de hoofddatabase: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - In de database van het gegevensmagazijn:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Geef de Gebruiker van Fivetran de volgende machtigingen voor uw gegevensmagazijn:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    Er is een machtiging nodig om referenties met een databasebereik te maken die worden gebruikt wanneer een gebruiker bestanden vanuit Azure Blob-opslag laadt met PolyBase.

3. Voeg een geschikte resourceklasse toe aan de Fivetran-gebruiker. De resourceklasse die u gebruikt, is afhankelijk van het geheugen dat nodig is om een kolomarchiefindex te maken. Integraties met producten zoals Marketo en Salesforce vereisen bijvoorbeeld een hogere resourceklasse vanwege het grote aantal kolommen en het grotere volume aan gegevens dat de producten gebruiken. Voor een hogere resourceklasse is meer geheugen nodig om kolomarchiefindexen te maken.

    We raden u aan statische resourceklassen te gebruiken. U beginnen `staticrc20` met de resourceklasse. De `staticrc20` resourceklasse kent 200 MB toe voor elke gebruiker, ongeacht het prestatieniveau dat u gebruikt. Als de indexering van kolomopslag mislukt op het niveau van de initiële resourceklasse, verhoogt u de resourceklasse.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Lees voor meer informatie over [geheugen- en gelijktijdigheidslimieten](memory-concurrency-limits.md) en [resourceklassen](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Log hier in

Als u zich wilt aanmelden bij Fivetran, voert u de referenties in die u gebruikt om toegang te krijgen tot uw gegevensmagazijn: 

* Host (uw servernaam).
* Poort.
* Database.
* Gebruiker (de gebruikersnaam moet **\@fivetran** zijn server_name waarbij *server_name* deel uitmaakt van uw Azure-host URI: ** _servernaam\__.database.windows.net**).
* Wachtwoord.

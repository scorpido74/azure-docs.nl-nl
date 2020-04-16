---
title: Toegang tot werkruimten, gegevens en pijplijnen beheren
description: Meer informatie over het beheren van toegangsbeheer voor werkruimten, gegevens en pijplijnen in een Azure Synapse Analytics-werkruimte (voorbeeld).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424767"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Toegang tot werkruimten, gegevens en pijplijnen beheren

Meer informatie over het beheren van toegangsbeheer voor werkruimten, gegevens en pijplijnen in een Azure Synapse Analytics-werkruimte (voorbeeld).

> [!NOTE]
> Voor GA zal RBAC meer ontwikkeld worden door de introductie van Synapse-specifieke Azure RBAC-rollen

## <a name="access-control-for-workspace"></a>Toegangsbeheer voor werkruimte

Voor een productie-implementatie in een Azure Synapse-werkruimte raden we u aan uw omgeving te organiseren om het eenvoudig te maken om gebruikers en beheerders in te richten.

> [!NOTE]
> De aanpak die hier wordt gevolgd, is om verschillende beveiligingsgroepen te maken en vervolgens de werkruimte te configureren om ze consistent te gebruiken. Nadat de groepen zijn ingesteld, hoeft een beheerder alleen het lidmaatschap binnen de beveiligingsgroepen te beheren.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Stap 1: Beveiligingsgroepen instellen met namen die dit patroon volgen

1. Beveiligingsgroep maken met de aangeroepen beveiligingsgroep`Synapse_WORKSPACENAME_Users`
2. Beveiligingsgroep maken met de aangeroepen beveiligingsgroep`Synapse_WORKSPACENAME_Admins`
3. `Synapse_WORKSPACENAME_Admins` is toegevoegd aan `ProjectSynapse_WORKSPACENAME_Users`

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Stap 2: Het standaard ADLS Gen2-account voorbereiden

Toen u uw werkruimte ingerichte, moest u een ADLSGEN2-account en een container kiezen voor het bestandssysteem dat de werkruimte kan gebruiken.

1. De [Azure-portal openen](https://portal.azure.com)
2. Navigeren naar het ADLSGEN2-account
3. Navigeren naar container (bestandssysteem) die u hebt gekozen voor de Azure Synapse-werkruimte
4. Klik **op Toegangsbeheer (IAM)**
5. Wijs de volgende rollen toe:
   1. De rol **van de lezer:**`Synapse_WORKSPACENAME_Users`
   2. **Rol eigenaar van opslagblobgegevens:**`Synapse_WORKSPACENAME_Admins`
   3. **Rol opslagblobgegevensbijdrager:**`Synapse_WORKSPACENAME_Users`
   4. **Rol eigenaar van opslagblobgegevens:**`WORKSPACENAME`
  
### <a name="step-3-configure-the-workspace-admin-list"></a>Stap 3: De lijst met werkruimtebeheerders configureren

1. Ga naar de [ **Web-gebruikersinterface van Azure Synapse**](https://web.azuresynapse.net)
2. Ga naar  > **Security** > **Beveiligingstoegangsbeheer** **beheren**
3. Klik **op Beheerder toevoegen**en selecteer`Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Stap 4: SQL-beheertoegang configureren voor de werkruimte

1. Ga naar [Azure Portal](https://portal.azure.com)
2. Navigeren naar uw werkruimte
3. Ga naar **Instellingen** > **Active Directory-beheerder**
4. Klik **op Beheerder instellen**
5. Selecteer `Synapse_WORKSPACENAME_Admins`
6. klik **op Selecteren**
7. klik **op Opslaan**

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Stap 5: Gebruikers en beheerders toevoegen en verwijderen aan beveiligingsgroepen

1. Gebruikers toevoegen waar toe beheerderstoegang nodig hebben`Synapse_WORKSPACENAME_Admins`
2. Alle andere gebruikers toevoegen aan`Synapse_WORKSPACENAME_Users`

## <a name="access-control-to-data"></a>Toegangsbeheer tot gegevens

Toegangsbeheer tot de onderliggende gegevens is opgesplitst in drie delen:

- Toegang tot het opslagaccount (hierboven al geconfigureerd in stap 2)
- Toegang tot gegevensvlak tot de SQL-databases (voor zowel SQL-pools als SQL on-demand)
- Een referentie maken voor SQL on-demand databases via het opslagaccount

## <a name="access-control-to-sql-databases"></a>Toegangsbeheer tot SQL-databases

> [!TIP]
> De onderstaande stappen moeten worden uitgevoerd voor **elke** SQL-database om gebruikers toegang te verlenen tot alle SQL-databases.

### <a name="sql-on-demand"></a>SQL on-demand

Als u toegang wilt verlenen aan een gebruiker tot **één** SQL on-demand-database, voert u de stappen in dit voorbeeld uit:

1. AANMELDING maken

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Gebruiker maken

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. Gebruiker toevoegen aan leden van de opgegeven rol

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
    ```

### <a name="sql-pools"></a>SQL-groepen

Voer de volgende stappen uit om een gebruiker toegang te verlenen tot **één** SQL-database:

1. Maak de gebruiker in de database door de volgende opdracht uit te voeren die de gewenste database target in de contextkiezer (vervolgkeuzelijst om databases te selecteren):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Geef de gebruiker een rol om toegang te krijgen tot de database:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* en *db_datawriter* kunnen werken voor lees-/schrijfmachtigingen als het verlenen van *db_owner* toestemming ongewenst is.
> Als een Spark-gebruiker rechtstreeks vanuit Spark kan lezen en schrijven in/vanuit een SQL-groep, is *db_owner* toestemming vereist.

Nadat u de gebruikers hebt gemaakt, bevestigt u dat SQL on-demand het opslagaccount kan opvragen:

- Voer de volgende opdracht uit die is gericht op de **hoofddatabase** van SQL on-demand:

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

## <a name="access-control-to-workspace-pipeline-runs"></a>Toegangsbeheer tot pijplijnvoor werkruimtewordt uitgevoerd

### <a name="workspace-managed-identity"></a>Door werkruimtebeheerbeheerde identiteit

> [!IMPORTANT]
> Als u pijplijnen met gegevenssets of activiteiten die verwijzen naar een SQL-groep, succesvol wilt uitvoeren, moet de identiteit van de werkruimte rechtstreeks toegang krijgen tot de SQL-groep.

Voer de volgende opdrachten uit voor elke SQL-groep om de door de werkruimte beheerde identiteit in staat te stellen pijplijnen uit te voeren in de SQL-groepdatabase:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Deze machtiging kan worden verwijderd door het volgende script op dezelfde SQL-groep uit te voeren:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Volgende stappen

Zie [Synapse SQL access control](../sql/access-control.md)voor een overzicht van toegang en controle in Synapse SQL. Zie Principals voor meer informatie over [databaseprincipals.](https://msdn.microsoft.com/library/ms181127.aspx) Aanvullende informatie over databaserollen vindt u in het artikel [Databaserollen.](https://msdn.microsoft.com/library/ms189121.aspx)

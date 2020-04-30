---
title: Toegang tot werk ruimten, gegevens en pijp lijnen beheren
description: Meer informatie over het beheren van toegangs beheer voor werk ruimten, gegevens en pijp lijnen in een Azure Synapse Analytics-werk ruimte (preview).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424767"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Toegang tot werk ruimten, gegevens en pijp lijnen beheren

Meer informatie over het beheren van toegangs beheer voor werk ruimten, gegevens en pijp lijnen in een Azure Synapse Analytics-werk ruimte (preview).

> [!NOTE]
> Voor GA is RBAC beter ontwikkeld door de introductie van Synapse Azure RBAC-rollen

## <a name="access-control-for-workspace"></a>Access Control voor werk ruimte

Voor een productie-implementatie in een Azure Synapse-werk ruimte raden we u aan om uw omgeving te organiseren, zodat u gebruikers en beheerders eenvoudig kunt inrichten.

> [!NOTE]
> De aanpak die u hier kunt volgen, is door verschillende beveiligings groepen te maken en vervolgens de werk ruimte te configureren om ze consistent te gebruiken. Nadat de groepen zijn ingesteld, hoeft een beheerder alleen lidmaatschap van de beveiligings groepen te beheren.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Stap 1: Stel beveiligings groepen in met een naam die volgt op dit patroon

1. Beveiligings groep met de naam maken`Synapse_WORKSPACENAME_Users`
2. Beveiligings groep met de naam maken`Synapse_WORKSPACENAME_Admins`
3. `Synapse_WORKSPACENAME_Admins` is toegevoegd aan `ProjectSynapse_WORKSPACENAME_Users`

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Stap 2: de standaard ADLS Gen2-account voorbereiden

Wanneer u uw werk ruimte hebt ingericht, moest u een ADLSGEN2-account en een container voor het bestands systeem voor de werk ruimte kiezen.

1. Open de [Azure Portal](https://portal.azure.com)
2. Ga naar het ADLSGEN2-account
3. Ga naar de container (bestands systeem) die u hebt gekozen voor de Azure Synapse-werk ruimte
4. Klik op **Access Control (IAM)**
5. Wijs de volgende rollen toe:
   1. Rol van **lezer** :`Synapse_WORKSPACENAME_Users`
   2. Rol van **gegevens eigenaar van opslag-BLOB** :`Synapse_WORKSPACENAME_Admins`
   3. Rol van gegevensbeheerderrol voor **opslag BLOB** :`Synapse_WORKSPACENAME_Users`
   4. Rol van **gegevens eigenaar van opslag-BLOB** :`WORKSPACENAME`
  
### <a name="step-3-configure-the-workspace-admin-list"></a>Stap 3: de beheer lijst voor de werk ruimte configureren

1. Ga naar de [ **Web-UI van Azure Synapse**](https://web.azuresynapse.net)
2. Ga naar **Manage**  > **beveiligings** > **toegangs beheer** beheren
3. Klik op **beheerder toevoegen**en selecteer`Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Stap 4: SQL-beheerders toegang voor de werk ruimte configureren

1. Ga naar [Azure Portal](https://portal.azure.com)
2. Navigeer naar uw werk ruimte
3. Ga naar **instellingen** > **Active Directory beheerder**
4. Klik op **beheerder instellen**
5. Selecteer `Synapse_WORKSPACENAME_Admins`
6. Klik op **selecteren**
7. Klik op **Opslaan**

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Stap 5: gebruikers en beheerders toevoegen en verwijderen voor beveiligings groepen

1. Gebruikers toevoegen die beheerders toegang nodig hebben tot`Synapse_WORKSPACENAME_Admins`
2. Alle andere gebruikers toevoegen aan`Synapse_WORKSPACENAME_Users`

## <a name="access-control-to-data"></a>Access Control naar gegevens

Toegangs beheer voor de onderliggende gegevens wordt in drie delen gesplitst:

- Toegang tot het gegevens vlak van het opslag account (al eerder geconfigureerd in stap 2)
- Data-vlak toegang tot de SQL-data bases (voor zowel SQL-groepen als SQL op aanvraag)
- Een referentie maken voor SQL on-demand-data bases via het opslag account

## <a name="access-control-to-sql-databases"></a>Toegangs beheer voor SQL-data bases

> [!TIP]
> De onderstaande stappen moeten worden uitgevoerd voor **elke** SQL database om gebruikers toegang te verlenen tot alle SQL-data bases.

### <a name="sql-on-demand"></a>SQL on-demand

Volg de stappen in dit voor beeld om toegang te verlenen aan een gebruiker aan **één** SQL on-demand-Data Base:

1. AANMELDING maken

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. GEBRUIKER maken

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. GEBRUIKER toevoegen aan leden van de opgegeven rol

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
    ```

### <a name="sql-pools"></a>SQL-groepen

Ga als volgt te werk om een gebruiker toegang te verlenen tot een **enkele** SQL database:

1. Maak de gebruiker in de-data base door de volgende opdracht uit te voeren die gericht is op de gewenste data base in de context kiezer (vervolg keuzelijst om data bases te selecteren):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. De gebruiker een rol geven om toegang te krijgen tot de Data Base:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* en *db_datawriter* kunnen worden gebruikt voor lees-en schrijf machtigingen als het verlenen van *db_owner* machtiging niet gewenst is.
> *Db_owner* machtiging is vereist voor een Spark-gebruiker om rechtstreeks vanuit Spark naar/vanuit een SQL-groep te lezen en te schrijven.

Nadat de gebruikers zijn gemaakt, controleert u of SQL on-demand een query kan uitvoeren op het opslag account:

- Voer de volgende opdracht uit om de **Master** -data base van SQL on-demand te richten:

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

## <a name="access-control-to-workspace-pipeline-runs"></a>Toegangs beheer voor werk ruimte pijplijn uitvoeringen

### <a name="workspace-managed-identity"></a>Door een werk ruimte beheerde identiteit

> [!IMPORTANT]
> Als u pijp lijnen wilt uitvoeren die gegevens sets of activiteiten bevatten die verwijzen naar een SQL-groep, moet de werk ruimte-id rechtstreeks toegang krijgen tot de SQL-groep.

Voer de volgende opdrachten uit op elke SQL-groep om de beheerde identiteit van de werk ruimte toe te staan om pijp lijnen uit te voeren op de SQL-groeps database:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Deze machtiging kan worden verwijderd door het volgende script uit te voeren in dezelfde SQL-groep:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Volgende stappen

Zie voor een overzicht van toegang en beheer in Synapse SQL [Synapse SQL Access Control](../sql/access-control.md). Zie [principals](https://msdn.microsoft.com/library/ms181127.aspx)voor meer informatie over data base-principals. Meer informatie over database rollen vindt u in het artikel [database rollen](https://msdn.microsoft.com/library/ms189121.aspx) .

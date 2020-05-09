---
title: Verificatie mechanismen met de instructie COPY
description: Geeft een overzicht van de verificatie mechanismen voor het bulksgewijs laden van gegevens
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: c7d6a0d289953376c6535f2401c9c77edb3205df
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994854"
---
# <a name="securely-load-data-using-synapse-sql"></a>Gegevens veilig laden met Synapse SQL

In dit artikel vindt u voor beelden over de veilige verificatie mechanismen voor de [instructie Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). De instructie COPY is de meest flexibele en veilige manier om gegevens bulksgewijs te laden in Synapse SQL.
## <a name="supported-authentication-mechanisms"></a>Ondersteunde verificatie mechanismen

De volgende matrix beschrijft de ondersteunde verificatie methoden voor elk bestands type en opslag account. Dit geldt voor de bron opslag locatie en de locatie van het fout bestand.

|                      |                CSV                |              Parquet              |                ORC                |
| :------------------: | :-------------------------------: | :-------------------------------: | :-------------------------------: |
|  Azure Blob Storage  | SAS/MSI/SERVICE-PRINCIPAL/SLEUTEL/AAD |              SAS/SLEUTEL              |              SAS/SLEUTEL              |
| Azure Data Lake Gen2 | SAS/MSI/SERVICE-PRINCIPAL/SLEUTEL/AAD | SAS/MSI/SERVICE-PRINCIPAL/SLEUTEL/AAD | SAS/MSI/SERVICE-PRINCIPAL/SLEUTEL/AAD |

## <a name="a-storage-account-key-with-lf-as-the-row-terminator"></a>A. De sleutel van het opslag account met LF als het rij-eind teken


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - Gebruik de hexadecimale waarde (0x0A) om op te geven van het teken voor de regel invoer/nieuwe regel. Houd er rekening mee dat de instructie COPY de teken reeks ' \n ' interpreteert als ' \r\n ' (regel terugloop retour regel).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator"></a>B. Shared Access signatures (SAS) met CRLF als het rij-eind punt
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - Geef de ROWTERMINATOR niet op als ' \r\n ' die wordt geïnterpreteerd als ' \r\r\n ' en kan leiden tot het parseren van problemen

## <a name="c-managed-identity"></a>C. Beheerde identiteit

Beheerde identiteits verificatie is vereist wanneer uw opslag account is gekoppeld aan een VNet. 

### <a name="prerequisites"></a>Vereisten

1. Installeer Azure PowerShell met behulp van deze [hand leiding](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Als u een v1-of Blob-opslag account voor algemeen gebruik hebt, moet u eerst een upgrade uitvoeren naar de v2 voor algemeen gebruik met behulp van deze [hand leiding](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
3. U moet **vertrouwde micro soft-Services toegang geven tot dit opslag account** ingeschakeld onder Azure Storage account **firewalls en instellingen voor virtuele netwerken** . Raadpleeg deze [hand leiding](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions) voor meer informatie.
#### <a name="steps"></a>Stappen

1. **Registreer uw SQL Server** bij Azure Active Directory (Aad) in Power shell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```

2. Maak met behulp van deze [hand leiding](../../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)een **v2-opslag account voor algemeen** gebruik.

   > [!NOTE]
   > Als u een v1-of Blob-opslag account voor algemeen gebruik hebt, moet u **eerst een upgrade uitvoeren naar v2** met behulp van deze [hand leiding](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

3. Navigeer onder uw opslag account naar **Access Control (IAM)** en selecteer **roltoewijzing toevoegen**. Wijs de rol van **Storage BLOB data-eigenaar, Inzender of lezer** RBAC toe aan uw SQL-Server.

   > [!NOTE]
   > Alleen leden met de bevoegdheid eigenaar kunnen deze stap uitvoeren. Raadpleeg deze [hand leiding](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor verschillende ingebouwde rollen voor Azure-resources.
   
4. U kunt nu de instructie COPY uitvoeren om een beheerde identiteit op te geven:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

> [!IMPORTANT]
>
> - Geef de rol van de **BLOB-gegevens** eigenaar, bijdrager of lezer van het **opslag** programma op. Deze rollen verschillen van de ingebouwde rollen van Azure van eigenaar, Inzender en lezer. 

## <a name="d-azure-active-directory-authentication-aad"></a>D. Azure Active Directory authenticatie (AAD)
#### <a name="steps"></a>Stappen

1. Navigeer onder uw opslag account naar **Access Control (IAM)** en selecteer **roltoewijzing toevoegen**. Wijs de rol van **Storage BLOB data-eigenaar, Inzender of lezer** RBAC toe aan uw Aad-gebruiker. 

2. Configureer Azure AD-verificatie door de volgende [documentatie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#create-an-azure-ad-administrator-for-azure-sql-server)te volgen. 

3. Maak verbinding met uw SQL-groep met Active Directory waarbij u nu de instructie COPY kunt uitvoeren zonder referenties op te geven:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```

> [!IMPORTANT]
>
> - Geef de rol van de **BLOB-gegevens** eigenaar, bijdrager of lezer van het **opslag** programma op. Deze rollen verschillen van de ingebouwde rollen van Azure van eigenaar, Inzender en lezer. 

## <a name="e-service-principal-authentication"></a>E. Service-Principal-verificatie
#### <a name="steps"></a>Stappen

1. [Een Azure Active Directory-toepassing (AAD) maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)
2. [Toepassings-ID ophalen](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)
3. [De verificatie sleutel ophalen](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret)
4. [Het v1 OAuth 2,0-token-eind punt ophalen](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Lees-, schrijf-en uitvoerings machtigingen toewijzen aan uw Aad-toepassing](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) in uw opslag account
6. U kunt nu de instructie COPY uitvoeren:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - De **v1** -versie van het OAuth 2,0-token-eind punt gebruiken

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg het artikel over de [document instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) voor de gedetailleerde syntaxis
- Raadpleeg het artikel overzicht van het [laden van gegevens](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt) voor het laden van aanbevolen procedures

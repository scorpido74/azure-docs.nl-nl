---
title: Verificatiemechanismen met de COPY-instructie
description: Geeft een overzicht van de verificatiemechanismen voor het bulksgewijs laden van gegevens
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: f5f6c6970ad8bb697ceb118b6725b37e93ca80b5
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213054"
---
# <a name="securely-load-data-using-synapse-sql"></a>Gegevens veilig laden met Synapse SQL

In dit artikel vindt u voorbeelden over de veilige verificatiemechanismen voor de [COPY-instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). De instructie COPY is de meest flexibele en veilige manier om gegevens bulksgewijs te laden in Synapse SQL.
## <a name="supported-authentication-mechanisms"></a>Ondersteunde verificatiemechanismen

De volgende matrix beschrijft de ondersteunde verificatiemethoden voor elk bestandstype en opslagaccount. Dit geldt voor de bronopslaglocatie en de locatie van het foutbestand.

|                      |                CSV                |              Parquet              |                ORC                |
| :------------------: | :-------------------------------: | :-------------------------------: | :-------------------------------: |
|  Azure Blob Storage  | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |              SAS/KEY              |              SAS/KEY              |
| Azure Data Lake Gen2 | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. Opslagaccountsleutel met LF als het rij-eindpunt (nieuwe regel voor UNIX-stijl)


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
> - Gebruik de hexadecimale waarde (0x0A) om de lijnfeed/het teken voor nieuwe regel op te geven. Houd er rekening mee dat de instructie COPY de tekenreeks '\n' interpreteert als '\r\n' (regelterugloop nieuwe regel).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Shared Access Signatures (SAS) met CRLF als het rij-eindpunt (nieuwe regel Windows-stijl)
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
> - Geef de ROWTERMINATOR niet op als '\r\n'. Die wordt geïnterpreteerd als '\r\r\n' en kan leiden tot het parseerproblemen

## <a name="c-managed-identity"></a>C. Beheerde identiteit

Beheerde identiteitsverificatie is vereist wanneer uw opslagaccount is gekoppeld aan een VNet. 

### <a name="prerequisites"></a>Vereisten

1. Installeer Azure PowerShell met behulp van deze [gids](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Als u een v1-of blob-opslagaccount voor algemeen gebruik hebt, moet u eerst een upgrade uitvoeren naar de v2 voor algemeen gebruik met behulp van deze [gids](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
3. U moet **vertrouwde Microsoft-services toegang geven tot dit opslagaccount** ingeschakeld onder het instellingenmenu van uw Azure Storage-account **Firewalls en virtuele netwerken**. Raadpleeg deze [gids](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions) voor meer informatie.
#### <a name="steps"></a>Stappen

1. **Registreer uw SQL Server-** in PowerShell bij Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```

2. **Een opslagaccount voor algemeen gebruik v2 maken** met deze [gids](../../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

   > [!NOTE]
   > Als u een v1-of blob-opslagaccount voor algemeen gebruik hebt, moet u **eerst een upgrade uitvoeren naar de v2** voor algemeen gebruik met behulp van deze [gids](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

3. Navigeer onder uw opslagaccount naar **Access Control (IAM)** en selecteer **Roltoewijzing toevoegen**. Wijs de RBAC-rol van **Eigenaar van opslagblobgegevens, Inzender of Lezer** op uw SQL Server toe.

   > [!NOTE]
   > Alleen leden met de bevoegdheid Eigenaar kunnen deze stap uitvoeren. Raadpleeg deze [gids](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) voor verschillende ingebouwde rollen voor Azure-resources.
   
4. U kunt nu de instructie COPY uitvoeren om een "Beheerde identiteit" op te geven:

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
> - Geef de RBAC-rol van Eigenaar van **opslag** **blobgegevens**, Inzender of Lezer op. Deze rollen verschillen van de ingebouwde Azure-rollen Eigenaar, Inzender en Lezer. 

## <a name="d-azure-active-directory-authentication-aad"></a>D. Azure Active Directory-verificatie (AAD)
#### <a name="steps"></a>Stappen

1. Navigeer onder uw opslagaccount naar **Access Control (IAM)** en selecteer **Roltoewijzing toevoegen**. Wijs de RBAC-rol van **Eigenaar van opslagblobgegevens, Inzender of Lezer** aan uw AAD-gebruiker toe. 

2. Configureer Azure AD-verificatie door middel van de volgende [documentatie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#create-an-azure-ad-administrator-for-azure-sql-server). 

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
> - Geef de RBAC-rol van Eigenaar van **opslag** **blobgegevens**, Inzender of Lezer op. Deze rollen verschillen van de ingebouwde Azure-rollen Eigenaar, Inzender en Lezer. 

## <a name="e-service-principal-authentication"></a>E. Verificatie van service-principal
#### <a name="steps"></a>Stappen

1. [Een Azure Active Directory (AAD)-toepassing maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)
2. [Toepassings-id ophalen](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)
3. [De verificatiesleutel ophalen](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret)
4. [Het token-eindpunt V1 OAuth 2.0 ophalen](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Lees-, schrijf- en uitvoeringsmachtigingen toewijzen aan uw AAD-toepassing](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) in uw opslagaccount
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
> - Gebruik de **v1-** versie van het OAuth 2.0-token-eindpunt

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg het [artikel over COPY-instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) voor de gedetailleerde syntaxis
- Controleer het overzichtsartikel [gegevens laden](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt) voor het laden van aanbevolen procedures

---
title: Acl's recursief instellen voor Azure Data Lake Storage Gen2 | Microsoft Docs
description: U kunt toegangs beheer lijsten recursief toevoegen, bijwerken en verwijderen voor bestaande onderliggende items van een bovenliggende map.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: cbf5d8286d6f181c69cd090df6cf595934cd547e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88942141"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Acl's (toegangs beheer lijsten) recursief instellen voor Azure Data Lake Storage Gen2

ACL-overname is al beschikbaar voor nieuwe onderliggende items die zijn gemaakt onder een bovenliggende map. U kunt nu ook recursief toevoegen, bijwerken en verwijderen van de Acl's voor bestaande onderliggende items van een bovenliggende map zonder dat u deze wijzigingen afzonderlijk voor elk onderliggend item hoeft aan te brengen.

> [!NOTE]
> De mogelijkheid om toegangs lijsten recursief in te stellen, is in open bare preview en is beschikbaar in alle regio's.  

[Bibliotheken](#libraries)  |  Voor [beelden](#code-samples)  |  [Aanbevolen procedures](#best-practice-guidelines)  |  [Feedback geven](#provide-feedback)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.

- De juiste machtigingen om het recursieve ACL-proces uit te voeren. De juiste machtiging bevat een van de volgende opties: 

  - Een [beveiligings-principal](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) ingericht Azure Active Directory (AD) waaraan de rol [Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) is toegewezen in het bereik van de doel container, de bovenliggende resource groep of het abonnement.   

  - De gebruiker die eigenaar is van de doel container of-map waarop u het recursieve ACL-proces wilt Toep assen. Dit omvat alle onderliggende items in de doel container of-map. 

- Uitleg over hoe Acl's worden toegepast op directory's en bestanden. Zie [toegangs beheer in azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Zie de sectie **uw project instellen** in dit artikel voor informatie over de installatie richtlijnen voor Power shell, .NET SDK en python SDK.

## <a name="set-up-your-project"></a>Uw project instellen

Installeer de benodigde bibliotheken.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Zorg ervoor dat .NET Framework is geïnstalleerd. Zie [.NET Framework downloaden](https://dotnet.microsoft.com/download/dotnet-framework).
 
2. Controleer of de versie van Power shell die is geïnstalleerd, `5.1` of hoger is met behulp van de volgende opdracht.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Zie [bestaande Windows Power shell upgraden](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) voor informatie over het bijwerken van uw versie van Power shell
    
3. Installeer de nieuwste versie van de PowershellGet-module.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. Sluit de Power shell-console en open deze opnieuw.

5. Installeer **AZ. Storage** preview-module.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) voor meer informatie over het installeren van Power shell-modules.

### <a name="net"></a>[.NET](#tab/dotnet)

1. Open een opdracht venster (bijvoorbeeld: Windows Power shell).

2. Installeer in de projectmap het bestand Azure. storage. file. DataLake Preview met behulp van de `dotnet add package` opdracht.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.3.0-dev.20200811.1 -s https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json
   ```

3. Voeg deze instructies toe aan de bovenkant van het code bestand.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="python"></a>[Python](#tab/python)

1. Down load de [Azure data Lake Storage-client bibliotheek voor python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0).

2. Installeer de bibliotheek die u hebt gedownload met behulp van [PIP](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

Voeg deze import instructies toe aan de bovenkant van het code bestand.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

---

## <a name="connect-to-your-account"></a>Verbinding maken met uw account

U kunt verbinding maken met behulp van Azure Active Directory (AD) of met behulp van een account sleutel. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Open een Windows Power shell-opdracht venster en meld u vervolgens aan bij uw Azure-abonnement met de `Connect-AzAccount` opdracht en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account waarin u directory's wilt maken en beheren. In dit voor beeld vervangt `<subscription-id>` u de waarde van de tijdelijke aanduiding door de id van uw abonnement.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Kies vervolgens hoe u wilt dat uw opdrachten autorisatie aanvragen voor het opslag account. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Optie 1: autorisatie verkrijgen met behulp van Azure Active Directory (AD)

Met deze methode zorgt het systeem ervoor dat uw gebruikers account de juiste RBAC-toewijzingen (op rollen gebaseerd toegangs beheer) en ACL-machtigingen heeft. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

In de volgende tabel ziet u alle ondersteunde rollen en de instellingen voor de ACL-instelling.

|Rol|Mogelijkheid van ACL-instelling|
|--|--|
|[Eigenaar van gegevens van opslag-BLOB](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle mappen en bestanden in het account.|
|[Inzender voor Storage Blob-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Alleen mappen en bestanden die eigendom zijn van de beveiligingsprincipal.|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Optie 2: autorisatie verkrijgen met behulp van de sleutel van het opslag account

Met deze methode controleert het systeem geen RBAC-of ACL-machtigingen.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="net"></a>[.NET](#tab/dotnet)

Als u de fragmenten in dit artikel wilt gebruiken, moet u een [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -exemplaar maken dat het opslag account vertegenwoordigt.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met behulp van Azure Active Directory (AD)

U kunt de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) gebruiken om uw toepassing te verifiëren met Azure AD.

Nadat u het pakket hebt geïnstalleerd, voegt u deze met de instructie toe aan de bovenkant van het code bestand.

```csharp
using Azure.Identity;
```

Haal een client-ID, een client geheim en een Tenant-ID op. Zie voor dit doen [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md). Als onderdeel van dit proces moet u een van de volgende [RBAC-rollen (op rollen gebaseerd toegangs beheer)](../../role-based-access-control/overview.md) toewijzen aan uw beveiligingsprincipal. 

|Rol|Mogelijkheid van ACL-instelling|
|--|--|
|[Eigenaar van gegevens van opslag-BLOB](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle mappen en bestanden in het account.|
|[Inzender voor Storage Blob-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Alleen mappen en bestanden die eigendom zijn van de beveiligingsprincipal.|

In dit voor beeld wordt een [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

Deze methode is de eenvoudigste manier om verbinding te maken met een account. 

In dit voor beeld wordt een [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) -exemplaar gemaakt met behulp van een account sleutel.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> Zie de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) -documentatie voor meer voor beelden.

### <a name="python"></a>[Python](#tab/python)

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **DataLakeServiceClient** -exemplaar maken dat het opslag account vertegenwoordigt. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met behulp van Azure Active Directory (AD)

U kunt de [Azure Identity client-bibliotheek voor python](https://pypi.org/project/azure-identity/) gebruiken om uw toepassing te verifiëren met Azure AD.

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  Zie [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md)om deze waarden op te halen. Als onderdeel van dit proces moet u een van de volgende [RBAC-rollen (op rollen gebaseerd toegangs beheer)](../../role-based-access-control/overview.md) toewijzen aan uw beveiligingsprincipal. 

|Rol|Mogelijkheid van ACL-instelling|
|--|--|
|[Eigenaar van gegevens van opslag-BLOB](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle mappen en bestanden in het account.|
|[Inzender voor Storage Blob-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Alleen mappen en bestanden die eigendom zijn van de beveiligingsprincipal.|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Zie de documentatie [van de Azure Identity client-bibliotheek voor python](https://pypi.org/project/azure-identity/) voor meer voor beelden.

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

Dit is de eenvoudigste manier om verbinding te maken met een account. 

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een account sleutel.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Vervang de waarde van de tijdelijke plaatsaanduiding `storage_account_name` door de naam van uw opslagaccount.

- Vervang de `storage_account_key` waarde van de tijdelijke aanduiding door de toegangs sleutel van uw opslag account.

---

## <a name="set-an-acl-recursively"></a>Recursief instellen van een ACL

U kunt de Acl's recursief instellen.  

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Stel een recursief-ACL in met behulp van de- `Set-AzDataLakeGen2AclRecursive` cmdlet.

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze vermeldingen geven de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgen alle andere geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

Stel een recursief-ACL in door de methode **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** aan te roepen. Geef deze methode een [lijst](/dotnet/api/system.collections.generic.list-1) van [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Elk [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definieert een ACL-vermelding.

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze vermeldingen geven de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgen alle andere geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="python"></a>[Python](#tab/python)

Stel een recursief-ACL in door de methode **DataLakeDirectoryClient. set_access_control_recursive** aan te roepen.

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze vermeldingen geven de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgen alle andere geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

```python
def set_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>Recursief bijwerken van een ACL

U kunt een bestaande ACL recursief bijwerken.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Een ACL recursief bijwerken met behulp van de cmdlet  **Update-AzDataLakeGen2AclRecursive** . 

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

Werk een ACL recursief bij door de methode **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** aan te roepen. 

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt. 

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Werk een ACL recursief bij door de methode **DataLakeDirectoryClient. update_access_control_recursive** aan te roepen. 

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt. 

```python
def update_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>ACL-vermeldingen recursief verwijderen

U kunt een of meer ACL-vermeldingen recursief verwijderen.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwijder ACL-vermeldingen met behulp van de cmdlet **Remove-AzDataLakeGen2AclRecursive** . 

In dit voor beeld wordt een ACL-vermelding verwijderd uit de hoofd directory van de container.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

### <a name="net"></a>[.NET](#tab/dotnet)

Verwijder ACL-vermeldingen door de methode **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** aan te roepen. 

In dit voor beeld wordt een ACL-vermelding verwijderd uit de toegangs beheer lijst van de map met de naam `my-parent-directory` . 

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Verwijder ACL-vermeldingen door de methode **DataLakeDirectoryClient. remove_access_control_recursive** aan te roepen. 

In dit voor beeld wordt een ACL-vermelding verwijderd uit de toegangs beheer lijst van de map met de naam `my-parent-directory` . 

```python
def remove_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:4a9028cf-f779-4032-b09d-970ebe3db258'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>Herstellen van fouten

Er kunnen runtime-of machtigings fouten optreden. Start voor runtime-fouten het proces vanaf het begin. Machtigings fouten kunnen optreden als de beveiligingsprincipal niet voldoende machtigingen heeft om de toegangs beheer lijst van een map of bestand te wijzigen dat zich in de Directory-hiërarchie bevindt die wordt gewijzigd. Adresseer het probleem met de machtiging en kies vervolgens het proces hervatten vanaf het moment van de fout met behulp van een vervolg token of start het proces opnieuw vanaf het begin. U hoeft het vervolg token niet te gebruiken als u de voor keur hebt om vanaf het begin opnieuw op te starten. U kunt ACL-vermeldingen zonder negatieve gevolgen opnieuw Toep assen.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Resultaten naar de variabele retour neren. Niet-ingedeelde gegevens pipes naar een opgemaakte tabel.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Op basis van de uitvoer van de tabel kunt u eventuele machtigings fouten oplossen en de uitvoering hervatten met behulp van het vervolg token.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

## <a name="net"></a>[.NET](#tab/dotnet)

In dit voor beeld wordt een vervolg token geretourneerd in het geval van een fout. De toepassing kan deze voorbeeld methode opnieuw aanroepen nadat de fout is opgelost en door gegeven in het vervolg token. Als deze voorbeeld methode voor de eerste keer wordt aangeroepen, kan de toepassing de waarde ``null`` voor de vervolg token parameter door geven. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, null, continuationToken: continuationToken);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="python"></a>[Python](#tab/python)

In dit voor beeld wordt een vervolg token geretourneerd in het geval van een fout. De toepassing kan deze voorbeeld methode opnieuw aanroepen nadat de fout is opgelost en door gegeven in het vervolg token. Als deze voorbeeld methode voor de eerste keer wordt aangeroepen, kan de toepassing de waarde ``None`` voor de vervolg token parameter door geven. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

## <a name="resources"></a>Resources

Deze sectie bevat koppelingen naar bibliotheken en code voorbeelden.

#### <a name="libraries"></a>Bibliotheken

- [PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [.NET](https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>Codevoorbeelden

- Power shell: [Leesmij](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)-voor  |  [beeld](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- NET: [Leesmij](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)-voor  |  [beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [Leesmij](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)-voor  |  [beeld](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D)

## <a name="best-practice-guidelines"></a>Richt lijnen voor best practices

In deze sectie vindt u enkele best practice richt lijnen voor het recursief instellen van Acl's. 

#### <a name="handling-runtime-errors"></a>Runtime-fouten afhandelen

Een runtime-fout kan verschillende oorzaken hebben (bijvoorbeeld: een storing of een probleem met de client verbinding). Als er een runtime-fout optreedt, start u het recursieve ACL-proces opnieuw. Acl's kunnen opnieuw worden toegepast op items zonder dat dit een negatieve invloed heeft veroorzaakt. 

#### <a name="handling-permission-errors-403"></a>Afhandeling van machtigings fouten (403)

Als er een uitzonde ring voor Access Control optreedt tijdens het uitvoeren van een recursieve ACL-proces, heeft uw AD- [beveiligings-principal](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) mogelijk onvoldoende machtigingen om een ACL toe te passen op een of meer onderliggende items in de Directory-hiërarchie. Als er een machtigings fout optreedt, wordt het proces gestopt en wordt er een vervolg token gegeven. Los het probleem met de machtiging op en gebruik vervolgens de vervolg token om de resterende gegevensset te verwerken. De mappen en bestanden die al zijn verwerkt, hoeven niet opnieuw te worden verwerkt. U kunt er ook voor kiezen om het recursieve ACL-proces opnieuw te starten. Acl's kunnen opnieuw worden toegepast op items zonder dat dit een negatieve invloed heeft veroorzaakt. 

#### <a name="credentials"></a>Referenties 

We raden u aan een Azure AD-beveiligings-principal in te richten waaraan de rol van [BLOB-gegevens eigenaar voor opslag](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) is toegewezen in het bereik van het doel opslag account of de container. 

#### <a name="performance"></a>Prestaties 

Als u de latentie wilt beperken, raden we u aan het recursieve ACL-proces uit te voeren op een virtuele Azure-machine (VM) die zich in dezelfde regio bevindt als uw opslag account. 

#### <a name="acl-limits"></a>ACL-limieten

Het maximum aantal Acl's dat u kunt Toep assen op een map of bestand is 32 toegangs-Acl's en standaard-32-Acl's. Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) voor meer informatie.

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>Feedback geven of problemen melden

U kunt uw feedback geven of een probleem melden met behulp van een van deze pagina's: [Power shell](https://github.com/Azure/azure-powershell/issues/new?assignees=&labels=triage&template=az-module-bug-report.md&title=), [.net](https://github.com/Azure/azure-sdk-for-net/issues/new?assignees=&labels=&template=bug_report.md&title=), [python](https://github.com/Azure/azure-sdk-for-python/issues/new?assignees=&labels=&template=bug_report.md&title=)

## <a name="see-also"></a>Zie ook

- [Toegangsbeheer in Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [Bekende problemen](data-lake-storage-known-issues.md)



---
title: Toegang verlenen tot blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources-Azure Storage
description: Azure Blob-en Queue Storage ondersteunen het verlenen van toegang tot resources met Azure Active Directory en beheerde identiteiten voor Azure-resources. U kunt beheerde identiteiten voor Azure-resources gebruiken om toegang te verlenen tot blobs en wacht rijen van toepassingen die worden uitgevoerd op virtuele machines van Azure, functie-apps, schaal sets voor virtuele machines en andere.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bed661873b195694c2fd9b30b1d98a3ecf1fc8a4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671107"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Toegang verlenen tot blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources

Azure Blob-en Queue Storage ondersteunen Azure Active Directory-verificatie (Azure AD) met [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md). Beheerde identiteiten voor Azure-resources kunnen toegang verlenen tot Blob-en wachtrij gegevens met behulp van Azure AD-referenties van toepassingen die worden uitgevoerd in azure virtual machines (Vm's), functie-apps, schaal sets voor virtuele machines en andere services. Door beheerde identiteiten voor Azure-resources te gebruiken in combi natie met Azure AD-verificatie kunt u voor komen dat referenties worden opgeslagen in uw toepassingen die in de cloud worden uitgevoerd.  

In dit artikel wordt beschreven hoe u toegang kunt verlenen tot BLOB-of wachtrij gegevens met een beheerde identiteit van een Azure-VM.

## <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten op een virtuele machine inschakelen

Voordat u beheerde identiteiten voor Azure-resources kunt gebruiken om toegang te verlenen tot blobs en wacht rijen van uw VM, moet u eerst beheerde identiteiten voor Azure-resources inschakelen op de VM. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Client bibliotheken Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Machtigingen verlenen aan een door Azure AD beheerde identiteit

Als u een aanvraag voor de BLOB of Queue-service van een beheerde identiteit in uw Azure Storage toepassing wilt machtigen, moet u eerst instellingen voor op rollen gebaseerde toegangs beheer (RBAC) configureren voor die beheerde identiteit. Azure Storage worden RBAC-rollen gedefinieerd die machtigingen voor Blob-en wachtrij gegevens omvatten. Wanneer de RBAC-rol is toegewezen aan een beheerde identiteit, wordt aan de beheerde identiteit die machtigingen verleend voor BLOB-of wachtrij gegevens in het juiste bereik.

Zie een van de volgende artikelen voor meer informatie over het toewijzen van RBAC-rollen:

- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC in de Azure-portal](storage-auth-aad-rbac-portal.md)
- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC met behulp van Azure CLI](storage-auth-aad-rbac-cli.md)
- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC met behulp van PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>Resource-ID Azure Storage

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Voor beeld van .NET-code: Een blok-Blob maken

Het code voorbeeld laat zien hoe u een OAuth 2,0-token ophaalt uit Azure AD en dit kunt gebruiken om een aanvraag voor het maken van een blok-BLOB te autoriseren. Om dit voor beeld te laten werken, moet u eerst de stappen volgen die in de voor gaande secties worden beschreven.

[!INCLUDE [storage-app-auth-lib-include](../../../includes/storage-app-auth-lib-include.md)]

### <a name="add-the-callback-method"></a>De call back methode toevoegen

Met de methode call back wordt de verloop tijd van het token gecontroleerd en zo nodig vernieuwd:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

### <a name="get-a-token-and-create-a-block-blob"></a>Een Token ophalen en een blok-Blob maken

De app-verificatie bibliotheek biedt de **AzureServiceTokenProvider** -klasse. Een exemplaar van deze klasse kan worden door gegeven aan een call back waarmee een token wordt opgehaald en het token wordt vernieuwd voordat het verloopt.

In het volgende voor beeld wordt een token opgehaald en gebruikt om een nieuwe BLOB te maken. vervolgens wordt hetzelfde token gebruikt voor het lezen van de blob.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = await TokenRenewerAsync(azureServiceTokenProvider,CancellationToken.None);

// Create storage credentials using the initial token, and connect the callback function
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token,
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider,
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName),
                                            storageCredentials);

// Upload text to the blob.
await blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage.
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}",
                        DateTimeOffset.UtcNow,
                        await blob.DownloadTextAsync());

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Zie [service-to-service-verificatie voor Azure Key Vault met .net](../../key-vault/service-to-service-authentication.md)voor meer informatie over de app-verificatie bibliotheek.

Voor meer informatie over het verkrijgen van een toegangs token raadpleegt u [beheerde identiteiten voor Azure-resources gebruiken op een Azure VM om een toegangs token op te halen](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Als u aanvragen voor BLOB-of wachtrij gegevens met Azure AD wilt autoriseren, moet u HTTPS voor die aanvragen gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Zie [toegangs rechten voor opslag gegevens beheren met RBAC](storage-auth-aad-rbac.md)voor meer informatie over RBAC-rollen voor Azure Storage.
- Zie voor meer informatie over het machtigen van toegang tot containers en wacht rijen in uw opslag toepassingen [Azure AD gebruiken met opslag toepassingen](storage-auth-aad-app.md).
- Zie voor meer informatie over het uitvoeren van Azure CLI-en Power shell-opdrachten met Azure AD-referenties [Azure CLI of Power shell-opdrachten uitvoeren met Azure AD-referenties voor toegang tot BLOB-of wachtrij gegevens](storage-auth-aad-script.md).

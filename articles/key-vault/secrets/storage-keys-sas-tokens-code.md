---
title: Shared Acces Signature-tokens in code ophalen | Azure Key Vault
description: De functie voor beheerde opslagaccounts biedt een naadloze integratie tussen Azure Key Vault en een Azure-opslagaccount.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 6530434e36f7c9a9a60a9782bcf2dce7ba447dab
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584910"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Shared Acces Signature-tokens in code ophalen

U kunt uw opslagaccount met SAS-tokens (Shared Access Signature) beheren die zijn opgeslagen in uw sleutelkluis. Zie [Beperkte toegang verlenen tot Azure Storage-resources via SAS](../../storage/common/storage-sas-overview.md) voor meer informatie.

In dit artikel vindt u voorbeelden van .NET-code die een SAS-token ophaalt waarmee bewerkingen worden uitgevoerd. Voor informatie over het maken en opslaan van SAS-tokens, raadpleegt u [Sleutels voor opslagaccounts beheren met Key Vault en de Azure CLI](overview-storage-keys.md) of [Sleutels voor opslagaccounts beheren met Key Vault en Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Codevoorbeelden

In dit voorbeeld haalt de code een SAS-token uit uw sleutelkluis op, deze gebruikt het token om een nieuw opslagaccount te maken en maakt deze een nieuwe Blob service-client.

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

Als uw SAS-token bijna is verlopen, kunt u het SAS-token ophalen uit de sleutelkluis en de code bijwerken.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Beperkte toegang verlenen tot Azure Storage-resources via SAS](../../storage/common/storage-sas-overview.md).
- Meer informatie over [Sleutels voor opslagaccounts beheren met Key Vault en de Azure CLI](overview-storage-keys.md) of [Azure PowerShell](overview-storage-keys-powershell.md).
- Zie [Voorbeelden van sleutels voor beheerde opslagaccounts](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)

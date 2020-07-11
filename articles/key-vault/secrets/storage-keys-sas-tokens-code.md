---
title: Shared Access Signature-tokens ophalen in code | Azure Key Vault
description: De functie Managed Storage-account biedt een naadloze integratie tussen Azure Key Vault en een Azure Storage-account.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: e429115ce2624685c413ae252229964feee70137
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232590"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Shared Acces Signature-tokens in code ophalen

U kunt uw opslag account met SAS-tokens (Shared Access Signature) beheren die zijn opgeslagen in uw sleutel kluis. Zie [granting Limited Access to Azure storage resources using SAS](../../storage/common/storage-sas-overview.md)(Engelstalig) voor meer informatie.

In dit artikel vindt u voor beelden van .NET-code waarmee een SAS-token wordt opgehaald en waarmee bewerkingen worden uitgevoerd. Zie voor meer informatie over het maken en opslaan van SAS-tokens [sleutels voor opslag accounts beheren met Key Vault en de Azure CLI of de](overview-storage-keys.md) [sleutels van het opslag account beheren met Key Vault en Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Codevoorbeelden

In dit voor beeld wordt met de code een SAS-token uit uw sleutel kluis opgehaald, gebruikt om een nieuw opslag account te maken en wordt een nieuwe Blob service-client gemaakt.

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

Als uw Shared Access Signature-token bijna is verlopen, kunt u het token voor de Shared Access-hand tekening ophalen uit de sleutel kluis en de code bijwerken.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [verlenen van beperkte toegang tot Azure storage-resources met behulp van SAS](../../storage/common/storage-sas-overview.md).
- Meer informatie over het [beheren van sleutels voor opslag accounts met Key Vault en de Azure cli](overview-storage-keys.md) of [Azure PowerShell](overview-storage-keys-powershell.md).
- Zie [sleutel voorbeelden voor beheerde opslag accounts](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)

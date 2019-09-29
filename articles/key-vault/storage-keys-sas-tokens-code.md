---
title: Beheerd opslag account Azure Key Vault-Power shell-versie
description: De functie Managed Storage-account biedt een naadloze integratie tussen Azure Key Vault en een Azure Storage-account.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 75561c643c2b8881e4b390be885b77f2fff4971d
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673459"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Shared Acces Signature-tokens in code ophalen

U kunt uw opslag account beheren met de [tokens voor gedeelde toegangs handtekeningen](../storage/common/storage-dotnet-shared-access-signature-part-1.md) in uw sleutel kluis. In dit artikel vindt u C# voor beelden van code waarmee een SAS-token wordt opgehaald en waarmee bewerkingen worden uitgevoerd.  Zie voor meer informatie over het maken en opslaan van SAS-tokens [sleutels voor opslag accounts beheren met Key Vault en de Azure CLI of de](key-vault-ovw-storage-keys.md) [sleutels van het opslag account beheren met Key Vault en Azure PowerShell](key-vault-overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Codevoorbeelden

In dit voor beeld wordt met de code een SAS-token uit uw sleutel kluis opgehaald, gebruikt om een nieuw opslag account te maken en wordt een nieuwe Blob service-client gemaakt.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Als uw Shared Access Signature-token bijna is verlopen, kunt u het token voor de Shared Access-hand tekening ophalen uit de sleutel kluis en de code bijwerken.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [beheren van sleutels voor opslag accounts met Key Vault en de Azure cli](key-vault-ovw-storage-keys.md) of [Azure PowerShell](key-vault-overview-storage-keys-powershell.md).
- Zie [sleutel voorbeelden voor beheerde opslag accounts](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informatie over sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
- [Key Vault Power shell-referentie](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

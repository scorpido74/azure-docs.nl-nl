---
title: Azure Key Vault managed storage-account - PowerShell-versie
description: De functie beheerde opslagaccount biedt een naadloze integratie tussen Azure Key Vault en een Azure-opslagaccount.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: cbd7bd034c5cbbdf5308ec660a96dc52a9a2b310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78200699"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Shared Acces Signature-tokens in code ophalen

U uw opslagaccount beheren met de [tokens voor gedeelde toegangshandtekeningen](../storage/common/storage-dotnet-shared-access-signature-part-1.md) in uw sleutelkluis. In dit artikel vindt u voorbeelden van C#-code waarmee een SAS-token wordt opgehaald en bewerkingen worden uitgevoerd.  Zie [Opslagaccountsleutels beheren met Key Vault en Azure POWERShell voor](key-vault-ovw-storage-keys.md) informatie [Manage storage account keys with Key Vault and Azure PowerShell](key-vault-overview-storage-keys-powershell.md)over het maken en opslaan van SAS-tokens.

## <a name="code-samples"></a>Codevoorbeelden

In dit voorbeeld haalt de code een SAS-token op uit uw sleutelkluis, gebruikt het om een nieuw opslagaccount te maken en maakt u een nieuwe Blob-serviceclient.  

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

Als uw handtekeningtoken voor gedeelde toegang bijna verloopt, u het token voor gedeelde toegangshandtekeningen ophalen uit uw sleutelkluis en de code bijwerken.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [beheren van opslagaccountsleutels met Key Vault en azure CLI](key-vault-ovw-storage-keys.md) of Azure [PowerShell](key-vault-overview-storage-keys-powershell.md).
- Zie [Voorbeelden van sleutels van beheerde opslagaccount](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informatie over sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell-referentie](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

---
title: Zelfstudie - Blobs versleutelen en decoderen met Azure Key Vault
titleSuffix: Azure Storage
description: Meer informatie over het versleutelen en decoderen van een blob met behulp van client-side encryptie met Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c1d26fda57d665cc8d83f594f4efeebebc7bf139
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456886"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Zelfstudie - Blobs versleutelen en decoderen met Azure Key Vault

Deze zelfstudie gaat over het gebruik van client-side storage encryptie met Azure Key Vault. Het begeleidt u door hoe u een blob in een consoletoepassing versleutelen en decoderen met behulp van deze technologieën.

**Geschatte duur:** 20 minuten

Zie Wat is Azure Key Vault voor overzichtsinformatie over Azure Key [Vault?](../../key-vault/general/overview.md)

Zie [Client-Side Encryption en Azure Key Vault voor Microsoft Azure Storage voor](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)overzichtsinformatie over client-side encryptie voor Azure Storage.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een Azure Storage-account
* Visual Studio 2013 of hoger
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Overzicht van versleuteling aan de clientzijde

Zie [Client-Side Encryption en Azure Key Vault voor Microsoft Azure Storage voor](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) een overzicht van client-side encryptie voor Azure Storage

Hier is een korte beschrijving van hoe client side encryptie werkt:

1. De Azure Storage client SDK genereert een content encryption key (CEK), wat een eenmalige symmetrische sleutel is.
2. Klantgegevens worden versleuteld met behulp van deze CEK.
3. De CEK wordt vervolgens verpakt (versleuteld) met behulp van de sleutel encryptiesleutel (KEK). De KEK wordt geïdentificeerd door een sleutel-id en kan een asymmetrisch sleutelpaar of een symmetrische sleutel zijn en lokaal worden beheerd of opgeslagen in Azure Key Vault. De Storage client zelf heeft nooit toegang tot de KEK. Het beroept zich gewoon op de sleutel verpakking algoritme dat wordt geleverd door Key Vault. Klanten kunnen ervoor kiezen om aangepaste providers te gebruiken voor het verpakken van sleutels/ uitpakken als ze dat willen.
4. De versleutelde gegevens worden vervolgens geüpload naar de Azure Storage-service.

## <a name="set-up-your-azure-key-vault"></a>Uw Azure Key Vault instellen

Om verder te gaan met deze zelfstudie, moet u de volgende stappen uitvoeren, die worden beschreven in de zelfstudie [Quickstart: Een geheim instellen en ophalen uit Azure Key Vault met behulp van een .NET-webapp:](../../key-vault/secrets/quick-create-net.md)

* Een sleutelkluis maken.
* Voeg een sleutel of geheim toe aan de sleutelkluis.
* Een toepassing registreren met Azure Active Directory.
* Autoriseer de toepassing om de sleutel of het geheim te gebruiken.

Noteer de ClientID en ClientSecret die zijn gegenereerd bij het registreren van een toepassing met Azure Active Directory.

Maak beide toetsen in de sleutelkluis. We gaan er voor de rest van de tutorial van uit dat je de volgende namen hebt gebruikt: ContosoKeyVault en TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Een consoletoepassing maken met pakketten en AppSettings

Maak in Visual Studio een nieuwe consoletoepassing.

Voeg de benodigde nuget-pakketten toe in de Package Manager Console.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Voeg AppSettings toe aan de App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Voeg de `using` volgende richtlijnen toe en voeg een verwijzing naar System.Configuration toe aan het project.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Een methode toevoegen om een token aan uw consoletoepassing te krijgen

De volgende methode wordt gebruikt door Key Vault-klassen die moeten worden geverifieerd voor toegang tot uw sleutelkluis.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Toegang tot Azure Storage en Key Vault in uw programma

Voeg in de methode Main() de volgende code toe.

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Modellen van key vault-object
> 
> Het is belangrijk om te begrijpen dat er eigenlijk twee Key Vault-objectmodellen zijn om rekening mee te houden: de ene is gebaseerd op de REST API (KeyVault namespace) en de andere is een extensie voor client-side encryptie.
> 
> De Key Vault-client werkt samen met de REST API en begrijpt JSON-websleutels en -geheimen voor de twee soorten dingen die zich in Key Vault bevinden.
> 
> De Key Vault-extensies zijn klassen die specifiek lijken gemaakt voor client-side encryptie in Azure Storage. Ze bevatten een interface voor sleutels (IKey) en klassen op basis van het concept van een Key Resolver. Er zijn twee implementaties van IKey die u moet weten: RSAKey en SymmetricKey. Nu vallen ze toevallig samen met de dingen die in een Key Vault zitten, maar op dit moment zijn het onafhankelijke klassen (dus de sleutel en het geheim die door de Key Vault-client zijn opgehaald, implementeren IKey niet).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Blob versleutelen en uploaden

Voeg de volgende code toe om een blob te versleutelen en te uploaden naar uw Azure-opslagaccount. De **resolveKeyAsync-methode** die wordt gebruikt, retourneert een IKey.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> Als u kijkt naar de BlobEncryptionPolicy constructor, zult u zien dat het een sleutel en / of een resolver kan accepteren. Houd er rekening mee dat u op dit moment geen resolver gebruiken voor versleuteling, omdat deze momenteel geen standaardsleutel ondersteunt.

## <a name="decrypt-blob-and-download"></a>Blob decoderen en downloaden

Decryptie is echt bij het gebruik van de Resolver klassen zinvol. De id van de sleutel die wordt gebruikt voor versleuteling is gekoppeld aan de blob in de metagegevens, dus er is geen reden voor u om de sleutel op te halen en de koppeling tussen sleutel en blob te onthouden. Je hoeft alleen maar om ervoor te zorgen dat de sleutel blijft in Key Vault.   

De privésleutel van een RSA-sleutel blijft in Key Vault, dus voor decryptie, wordt de versleutelde sleutel van de blob-metagegevens die de CEK bevat, naar Key Vault verzonden voor decryptie.

Voeg het volgende toe om de blob te decoderen die je zojuist hebt geüpload.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Er zijn een paar andere soorten resolvers om sleutelbeheer gemakkelijker te maken, waaronder: AggregateKeyResolver en CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Key Vault-geheimen gebruiken

De manier om een geheim te gebruiken met client-side encryptie is via de SymmetricKey klasse, omdat een geheim is in wezen een symmetrische sleutel. Maar, zoals hierboven vermeld, een geheim in Key Vault niet precies in kaart brengen met een SymmetricKey. Er zijn een paar dingen te begrijpen:

* De sleutel in een SymmetricKey moet een vaste lengte zijn: 128, 192, 256, 384 of 512 bits.
* De sleutel in een SymmetricKey moet Base64 gecodeerd zijn.
* Een Key Vault-geheim dat als SymmetricKey wordt gebruikt, moet een inhoudstype van "applicatie/octet-stream" in Key Vault hebben.

Hier is een voorbeeld in PowerShell van het creëren van een geheim in Key Vault dat kan worden gebruikt als een SymmetricKey.
Houd er rekening mee dat de hard gecodeerde waarde, $key, alleen voor demonstratiedoeleinden is. In je eigen code wil je deze sleutel genereren.

```csharp
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

In uw consoletoepassing u hetzelfde gesprek als voorheen gebruiken om dit geheim op te halen als een SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

Dat is alles. Veel plezier!

## <a name="next-steps"></a>Volgende stappen

Zie [Microsoft Azure Storage Client Library voor .NET voor](https://msdn.microsoft.com/library/azure/dn261237.aspx)meer informatie over het gebruik van Microsoft Azure Storage with C#.

Zie Blob Service REST API voor meer informatie over de Blob REST [API.](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Ga voor de meest recente informatie over Microsoft Azure Storage naar het [Microsoft Azure Storage Team-blog](https://blogs.msdn.com/b/windowsazurestorage/).

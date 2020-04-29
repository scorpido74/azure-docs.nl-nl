---
title: 'Zelf studie: blobs versleutelen en ontsleutelen met behulp van Azure Key Vault'
titleSuffix: Azure Storage
description: Meer informatie over het versleutelen en ontsleutelen van een blob met versleuteling aan client zijde met Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c1d26fda57d665cc8d83f594f4efeebebc7bf139
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81456886"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Zelf studie: blobs versleutelen en ontsleutelen met behulp van Azure Key Vault

In deze zelf studie wordt beschreven hoe u gebruik kunt maken van opslag versleuteling aan de client zijde met Azure Key Vault. U wordt begeleid bij het versleutelen en ontsleutelen van een BLOB in een console toepassing met behulp van deze technologieën.

**Geschatte duur:** 20 minuten

Zie [Wat is Azure Key Vault?](../../key-vault/general/overview.md)voor overzichts informatie over Azure Key Vault.

Zie [versleuteling aan client zijde en Azure Key Vault voor Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor overzichts informatie over client-side Encryption voor Azure Storage.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een Azure Storage-account
* Visual Studio 2013 of hoger
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Overzicht van versleuteling aan de client zijde

Zie [versleuteling aan client zijde en Azure Key Vault voor Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor een overzicht van client-side Encryption voor Azure Storage.

Hier volgt een korte beschrijving van de werking van versleuteling aan client zijde:

1. De Azure Storage client-SDK genereert een coderings sleutel voor inhoud (CEK). Dit is een eenmalige symmetrische sleutel.
2. Klant gegevens worden versleuteld met behulp van deze CEK.
3. De CEK wordt vervolgens gewrappt (versleuteld) met behulp van de sleutel versleutelings sleutel (KEK). De KEK wordt geïdentificeerd aan de hand van een sleutel-id en kan een asymmetrisch sleutel paar of een symmetrische sleutel zijn en kan lokaal worden beheerd of opgeslagen in Azure Key Vault. De Storage-client zelf heeft nooit toegang tot de KEK. Hiermee wordt alleen het sleutel terugloop algoritme aangeroepen dat wordt verschaft door Key Vault. Klanten kunnen ervoor kiezen om aangepaste providers te gebruiken voor de sleutel terugloop/uitpakken als ze dat willen.
4. De versleutelde gegevens worden vervolgens geüpload naar de Azure Storage-service.

## <a name="set-up-your-azure-key-vault"></a>Uw Azure Key Vault instellen

Als u wilt door gaan met deze zelf studie, moet u de volgende stappen uitvoeren, zoals beschreven in de [Snelstartgids: een geheim van Azure Key Vault instellen en ophalen met behulp van een .net-Web-app](../../key-vault/secrets/quick-create-net.md):

* Een sleutelkluis maken.
* Voeg een sleutel of geheim toe aan de sleutel kluis.
* Een toepassing registreren bij Azure Active Directory.
* De toepassing toestemming geven om de sleutel of het geheim te gebruiken.

Noteer de ClientID-en ClientSecret die zijn gegenereerd bij het registreren van een toepassing met Azure Active Directory.

Maak beide sleutels in de sleutel kluis. Voor de rest van de zelf studie wordt ervan uitgegaan dat u de volgende namen hebt gebruikt: ContosoKeyVault en TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Een console toepassing maken met pakketten en AppSettings

Maak in Visual Studio een nieuwe console toepassing.

Voeg benodigde nuget-pakketten toe in de Package Manager-console.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Voeg AppSettings toe aan de app. config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Voeg de volgende `using` instructies toe en voeg een verwijzing naar System. Configuration toe aan het project.

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

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Een methode voor het ophalen van een token aan uw console toepassing toevoegen

De volgende methode wordt gebruikt door Key Vault klassen die moeten worden geverifieerd om toegang te krijgen tot uw sleutel kluis.

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

Voeg de volgende code toe aan de methode Main ().

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
> Object modellen Key Vault
> 
> Het is belang rijk om te begrijpen dat er eigenlijk twee Key Vault-object modellen zijn waarmee u rekening moet houden: een is gebaseerd op de REST API (naam ruimte van de sleutel) en de andere is een uitbrei ding voor versleuteling aan de client zijde.
> 
> De Key Vault-client communiceert met de REST API en begrijpt JSON-websleutels en geheimen voor de twee soorten dingen die zijn opgenomen in Key Vault.
> 
> De uitbrei dingen van de Key Vault zijn klassen die specifiek zijn gemaakt voor versleuteling aan client zijde in Azure Storage. Ze bevatten een interface voor sleutels (IKey) en klassen op basis van het concept van een Key resolver. Er zijn twee implementaties van IKey die u moet weten: RSAKey en SymmetricKey. Nu gaan ze samen vallen met de dingen die zijn opgenomen in een Key Vault, maar ze zijn op dit moment onafhankelijke klassen (dus de sleutel en het geheim dat door de Key Vault-client wordt opgehaald, implementeren IKey) niet.
> 
> 

## <a name="encrypt-blob-and-upload"></a>BLOB versleutelen en uploaden

Voeg de volgende code toe om een BLOB te versleutelen en deze te uploaden naar uw Azure-opslag account. De **ResolveKeyAsync** -methode die wordt gebruikt, retourneert een IKey.

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
> Als u de BlobEncryptionPolicy-constructor bekijkt, ziet u dat er een sleutel en/of een resolver kan worden geaccepteerd. Houd er rekening mee dat u nu geen resolver kunt gebruiken voor versleuteling omdat deze momenteel geen standaard sleutel ondersteunt.

## <a name="decrypt-blob-and-download"></a>BLOB ontsleutelen en downloaden

Ontsleuteling is echt wanneer het gebruik van de resolver-klassen zinvol is. De ID van de sleutel die wordt gebruikt voor versleuteling, is gekoppeld aan de BLOB in de meta gegevens, zodat u geen reden hebt om de sleutel op te halen en de koppeling tussen de sleutel en de BLOB te onthouden. U hoeft alleen maar te controleren of de sleutel in Key Vault blijft.   

De persoonlijke sleutel van een RSA-sleutel blijft in Key Vault. voor ontsleuteling vindt de versleutelde sleutel van de BLOB-meta gegevens die de CEK bevat, naar Key Vault voor ontsleuteling.

Voeg het volgende toe om de blob die u zojuist hebt geüpload, te ontsleutelen.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Er zijn een aantal andere soorten resolvers voor het eenvoudiger maken van sleutel beheer, waaronder: AggregateKeyResolver en CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Key Vault geheimen gebruiken

De manier om een geheim te gebruiken met versleuteling aan de client zijde is via de SymmetricKey-klasse, omdat een geheim in feite een symmetrische sleutel is. Maar zoals hierboven vermeld, wordt een geheim in Key Vault niet precies aan een SymmetricKey toegewezen. Er zijn enkele dingen die u moet weten:

* De sleutel in een SymmetricKey moet een vaste lengte hebben: 128, 192, 256, 384 of 512 bits.
* De sleutel in een SymmetricKey moet base64-gecodeerd zijn.
* Een Key Vault geheim dat wordt gebruikt als een SymmetricKey moet het inhouds type ' application/octet-stream ' hebben in Key Vault.

Hier volgt een voor beeld in Power shell van het maken van een geheim in Key Vault dat kan worden gebruikt als een SymmetricKey.
Houd er rekening mee dat de hardcoded waarde, $key, alleen bestemd is voor demonstratie doeleinden. In uw eigen code wilt u deze sleutel genereren.

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

In uw console toepassing kunt u dezelfde aanroep gebruiken als voordat u dit geheim ophaalt als een SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

Dat is alles. Veel plezier!

## <a name="next-steps"></a>Volgende stappen

Zie [Microsoft Azure Storage-client bibliotheek voor .net](https://msdn.microsoft.com/library/azure/dn261237.aspx)voor meer informatie over het gebruik van Microsoft Azure Storage met C#.

Zie [BLOB Service rest API](https://msdn.microsoft.com/library/azure/dd135733.aspx)voor meer informatie over de blob-rest API.

Ga naar de blog van het [Microsoft Azure Storage-team](https://blogs.msdn.com/b/windowsazurestorage/)voor de meest recente informatie over Microsoft Azure Storage.

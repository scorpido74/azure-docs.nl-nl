---
title: Token cache-serialisatie in micro soft-verificatie bibliotheek voor .NET
titleSuffix: Microsoft identity platform
description: Meer informatie over serialisatie en klant-serialisatie van de token cache met behulp van micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cae1a755b9f79ce2fd5084653f7b3c177f29832
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802723"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serialisatie van token cache in MSAL.NET
Nadat een [token is verkregen](msal-acquire-cache-tokens.md), wordt het in de cache opgeslagen door micro soft Authentication Library (MSAL).  De toepassings code moet proberen een token op te halen uit de cache voordat een token wordt verkregen met een andere methode.  In dit artikel wordt de standaard-en aangepaste serialisatie van de token cache in MSAL.NET beschreven.

Dit artikel is voor MSAL.NET 3. x. Als u geïnteresseerd bent in MSAL.NET 2. x, Zie [token cache serialisatie in MSAL.net 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Standaard serialisatie voor mobiele platforms

In MSAL.NET wordt standaard een token cache in het geheugen beschikbaar gesteld. Serialisatie wordt standaard gegeven voor platforms waarop beveiligde opslag beschikbaar is voor een gebruiker als onderdeel van het platform. Dit is het geval voor Universeel Windows-platform (UWP), Xamarin. iOS en Xamarin. Android.

> [!Note]
> Wanneer u een Xamarin. Android-project migreert van MSAL.NET 1. x naar MSAL.NET 3. x, wilt u mogelijk `android:allowBackup="false"` toevoegen aan uw project om te voor komen dat oude tokens in de cache worden teruggestuurd wanneer Visual Studio-implementaties een herstel van lokale opslag activeren. Zie [probleem #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Aangepaste serialisatie voor Windows-bureau blad-apps en web-apps/web-Api's

Vergeet niet dat aangepaste serialisatie niet beschikbaar is op mobiele platformen (UWP, Xamarin. iOS en Xamarin. Android). MSAL definieert al een veilig en uitvoeren van een serialization-mechanisme voor deze platforms. .NET Desktop-en .NET core-toepassingen hebben echter uiteenlopende architecturen en MSAL kan geen mechanisme voor het serialiseren van algemeen gebruik implementeren. Websites kunnen bijvoorbeeld tokens in een redis-cache opslaan of apps op het bureau blad in een versleuteld bestand opslaan. Serialisatie wordt dus niet out-of-the-box meegeleverd. Als u een permanente token cache-toepassing in .NET desktop of .NET core wilt hebben, moet u de serialisatie aanpassen.

De volgende klassen en interfaces worden gebruikt in de serialisatie van de token cache:

- `ITokenCache`, waarmee gebeurtenissen worden gedefinieerd voor het abonneren op aanvragen voor de serialisatie van tokens en voor de methoden voor het serialiseren of deserialiseren van de cache in verschillende indelingen (ADAL v 3.0, MSAL 2. x en MSAL 3. x = ADAL v 5.0).
- `TokenCacheCallback` is een call back die is door gegeven aan de gebeurtenissen, zodat u de serialisatie kunt afhandelen. Ze worden aangeroepen met argumenten van het type `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs` biedt alleen de `ClientId` van de toepassing en een verwijzing naar de gebruiker waarvoor het token beschikbaar is.

  ![Klassediagram](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET maakt token caches voor u en biedt u de `IToken`-cache wanneer u de `UserTokenCache` en `AppTokenCache` eigenschappen van een toepassing aanroept. U moet de interface zelf niet zelf implementeren. Uw verantwoordelijkheid, wanneer u een aangepaste token cache-serialisatie implementeert, is:
> - Reageer op `BeforeAccess` en `AfterAccess` gebeurtenissen (of hun async-versies). De gemachtigde van de `BeforeAccess` is verantwoordelijk voor het deserialiseren van de cache, terwijl de `AfterAccess` één verantwoordelijk is voor het serialiseren van de cache.
> - Onderdeel van deze gebeurtenissen Store of het laden van blobs, die door het gebeurtenis argument worden door gegeven aan de gewenste opslag.

De strategieën verschillen, afhankelijk van of u een token cache-serialisatie schrijft voor een [open bare client toepassing](msal-client-applications.md) (Desktop) of een [vertrouwelijke client toepassing](msal-client-applications.md)) (Web-app/Web-API, daemon-app).

### <a name="token-cache-for-a-public-client"></a>Token cache voor een open bare client 

Sinds MSAL.NET v2. x hebt u verschillende opties voor het serialiseren van de token cache van een open bare client. U kunt de cache alleen serialiseren naar de MSAL.NET-indeling (de Unified Format-cache is gebruikelijk in MSAL en de platformen).  U kunt ook de [verouderde](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) token cache-serialisatie van ADAL v3 ondersteunen.

De token cache-serialisatie aanpassen om de status voor eenmalige aanmelding te delen tussen ADAL.NET 3. x, ADAL.NET 5. x en MSAL.NET wordt uitgelegd in een deel van het volgende voor beeld: [Active-Directory-DotNet-v1-naar-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> De MSAL.NET 1.1.4-preview-token cache-indeling wordt niet meer ondersteund in MSAL 2. x. Als u toepassingen hebt die gebruikmaken van MSAL.NET 1. x, moeten uw gebruikers zich opnieuw aanmelden. U kunt ook de migratie van ADAL 4. x (en 3. x) ondersteunen.

#### <a name="simple-token-cache-serialization-msal-only"></a>Eenvoudige token cache-serialisatie (alleen MSAL)

Hieronder ziet u een voor beeld van een Naïve-implementatie van aangepaste serialisatie van een token cache voor desktop toepassingen. Hier is de token cache van de gebruiker een bestand in dezelfde map als de toepassing.

Nadat u de toepassing hebt gemaakt, schakelt u de serialisatie in door de `TokenCacheHelper.EnableSerialization()`-methode aan te roepen en de toepassings `UserTokenCache`door te geven.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

De `TokenCacheHelper` helper-klasse is gedefinieerd als:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Een voor beeld van een productkwaliteits token op basis van een op het bestand gebaseerde serializer voor open bare client toepassingen (voor bureaublad toepassingen die worden uitgevoerd op Windows, Mac en Linux) is beschikbaar in de open-source bibliotheek van [micro soft. Identity. client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) . U kunt deze in uw toepassingen insluiten vanuit het volgende nuget-pakket: [micro soft. Identity. client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Dubbele token cache-serialisatie (MSAL Unified cache en ADAL v3)

Als u de token cache-serialisatie wilt implementeren met behulp van de Unified cache-indeling (gemeen schappelijk ADAL.NET 4. x, MSAL.NET 2. x en andere MSALs van dezelfde generatie of ouder) op hetzelfde platform, bekijkt u de volgende code:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Deze keer de helper-klasse zoals gedefinieerd als:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Token cache voor een web-app (vertrouwelijke client toepassing)

In web apps of Web-Api's kan de cache gebruikmaken van de sessie, een redis-cache of een-Data Base.

Bewaar in web apps of Web-Api's één token cache per account.  Voor web-apps moet de token cache worden gesleuteld met de account-ID.  Voor web-Api's moet het account worden gefilterd op de hash van het token dat wordt gebruikt om de API aan te roepen. MSAL.NET biedt aangepaste token cache-serialisatie in .NET Framework en .NET core-subplatformen. Gebeurtenissen die worden geactiveerd wanneer de cache wordt geopend, apps kunnen kiezen of de cache moet worden geserialiseerd of gedeserialiseerd. Als er sprake is van vertrouwelijke client toepassingen die gebruikers verwerken (Web-apps waarmee gebruikers zich aanmelden en Web-api's aanroepen, en webtoepassingen stroomafwaartse Web-Api's), kunnen er veel gebruikers zijn en worden de gebruikers parallel verwerkt. Uit veiligheids overwegingen is het aan te raden één cache per gebruiker te serialiseren. Met serialisatie-gebeurtenissen wordt een cache sleutel berekend op basis van de identiteit van de verwerkte gebruiker en wordt een token cache voor die gebruiker geserialiseerd/deserialie.

Voor beelden van het gebruik van token caches voor web apps en Web-Api's zijn beschikbaar in de [zelf studie](https://ms-identity-aspnetcore-webapp-tutorial) voor de ASP.net core-web-app in de phase [2-2-token cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Voor implementaties ziet u de map [TokenCacheProviders](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/Microsoft.Identity.Web/TokenCacheProviders) in de bibliotheek [micro soft-Authentication-Extensions-voor-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (in de map [micro soft. Identity. client. Extensions. Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) ). 

## <a name="next-steps"></a>Volgende stappen
De volgende voor beelden illustreren de token cache-serialisatie.

| Voorbeeld | Platform | Beschrijving|
| ------ | -------- | ----------- |
|[Active-Directory-DotNet-Desktop-MSGraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Bureau blad (WPF) | Windows Desktop .NET (WPF)-toepassing die de Microsoft Graph-API aanroept. ![Topologie](media/msal-net-token-cache-serialization/topology.png)|
|[Active-Directory-DotNet-v1-naar-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Bureau blad (console) | Set Visual Studio-oplossingen die de migratie van Azure AD v 1.0-toepassingen (met behulp van ADAL.NET) illustreren naar Azure AD v 2.0-toepassingen, ook wel geconvergeerde toepassingen (met behulp van MSAL.NET) genoemd, met name de migratie van de [token cache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|

---
title: Serialisatie van tokencache (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over serialisatie en klantserialisatie van de tokencache met Behulp van Microsoft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1bd348ad27d892d0421b13c16ce81bc4f5dfb021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262800"
---
# <a name="token-cache-serialization-in-msalnet"></a>Tokencacheserialisatie in MSAL.NET
Nadat een [token is aangeschaft,](msal-acquire-cache-tokens.md)wordt het in de cache opgeslagen door Microsoft Authentication Library (MSAL).  Toepassingscode moet proberen om een token uit de cache te halen voordat u een token via een andere methode verwerft.  In dit artikel wordt gesproken over standaard- en aangepaste serialisatie van de tokencache in MSAL.NET.

Dit artikel is voor MSAL.NET 3.x. Zie [Seriële serialisatie van de tokencache in MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x)als u geïnteresseerd bent in MSAL.NET 2.x.

## <a name="default-serialization-for-mobile-platforms"></a>Standaardserialisatie voor mobiele platforms

In MSAL.NET wordt standaard een in-memory tokencache geleverd. Serialisatie wordt standaard geleverd voor platforms waar beveiligde opslag beschikbaar is voor een gebruiker als onderdeel van het platform. Dit is het geval voor Universal Windows Platform (UWP), Xamarin.iOS en Xamarin.Android.

> [!Note]
> Wanneer u een Xamarin.Android-project migreert van MSAL.NET 1.x naar `android:allowBackup="false"` MSAL.NET 3.x, u aan uw project toevoegen om te voorkomen dat oude tokens in de cache terugkomen wanneer implementaties van Visual Studio leiden tot een herstel van lokale opslag. Zie [Probleem #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Aangepaste serialisatie voor Windows-bureaubladapps en web-apps/web-API's

Vergeet niet dat aangepaste serialisatie niet beschikbaar is op mobiele platforms (UWP, Xamarin.iOS en Xamarin.Android). MSAL definieert al een veilig en performant serialisatiemechanisme voor deze platforms. .NET desktop- en .NET Core-toepassingen hebben echter verschillende architecturen en MSAL kan geen algemeen serialisatiemechanisme implementeren. Websites kunnen er bijvoorbeeld voor kiezen om tokens op te slaan in een Redis-cache of bureaublad-apps tokens op te slaan in een versleuteld bestand. Dus serialisatie is niet out-of-the-box. Als u een permanente toepassing voor tokencache wilt hebben in .NET-bureaublad of .NET Core, moet u de serialisatie aanpassen.

De volgende klassen en interfaces worden gebruikt in de serialisatie van tokencache:

- `ITokenCache`, die gebeurtenissen definieert om zich te abonneren op serialisatieaanvragen voor tokencache en methoden om de cache te serialiseren of te deserialiseren in verschillende formaten (ADAL v3.0, MSAL 2.x en MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback`is een callback doorgegeven aan de gebeurtenissen, zodat u de serialisatie verwerken. Ze worden genoemd met argumenten `TokenCacheNotificationArgs`van het type.
- `TokenCacheNotificationArgs`alleen de `ClientId` toepassing en een verwijzing naar de gebruiker waarvoor het token beschikbaar is.

  ![Klassendiagram](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET maakt tokencaches voor u `IToken` en biedt u de `UserTokenCache` cache `AppTokenCache` wanneer u de eigenschappen en eigenschappen van een toepassing aanroept. Het is niet de bedoeling dat u de interface zelf implementeert. Uw verantwoordelijkheid, wanneer u een aangepaste token cache serialisatie implementeren, is om:
> - Reageer `BeforeAccess` op `AfterAccess` en "evenementen" (of hun Async-smaken). De `BeforeAccess` gemachtigde is verantwoordelijk voor het deserialiseren van de cache, terwijl de `AfterAccess` cache verantwoordelijk is voor het serialiseren van de cache.
> - Een deel van deze gebeurtenissen slaat blobs op of laadt deze, die door het gebeurtenisargument worden doorgegeven aan de gewenste opslag.

De strategieën zijn anders, afhankelijk van of u een tokencache serialisatie schrijft voor een [openbare clienttoepassing](msal-client-applications.md) (desktop) of een [vertrouwelijke clienttoepassing](msal-client-applications.md)(web-app / web-API, daemon-app).

### <a name="token-cache-for-a-public-client"></a>Tokencache voor een openbare client 

Sinds MSAL.NET v2.x hebt u verschillende opties voor het serialiseren van de tokencache van een openbare client. U de cache alleen serialiseren naar de MSAL.NET-indeling (de cache met uniforme indeling is gebruikelijk op MSAL en de platforms).  U ook de [legacy](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) token cache serialisatie van ADAL V3 ondersteunen.

Het aanpassen van de serialisatie van de tokencache om de enkele aanmeldingsstatus tussen ADAL.NET 3.x, ADAL.NET 5.x en MSAL.NET te delen, wordt uitgelegd in een deel van het volgende voorbeeld: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> De MSAL.NET 1.1.4-preview tokencache-indeling wordt niet langer ondersteund in MSAL 2.x. Als u toepassingen hebt die gebruikmaken van MSAL.NET 1.x, moeten uw gebruikers zich opnieuw aanmelden. Afwisselend wordt de migratie van ADAL 4.x (en 3.x) ondersteund.

#### <a name="simple-token-cache-serialization-msal-only"></a>Eenvoudige tokencacheserialisatie (alleen MSAL)

Hieronder vindt u een voorbeeld van een naïeve implementatie van aangepaste serialisatie van een tokencache voor desktoptoepassingen. Hier is de cache van het gebruikerstoken een bestand in dezelfde map als de toepassing.

Nadat u de toepassing hebt gemaakt, schakelt `TokenCacheHelper.EnableSerialization()` u de `UserTokenCache`serialisatie in door de methode aan te roepen en de toepassing door te geven.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

De `TokenCacheHelper` helperklasse wordt gedefinieerd als:

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

Een voorbeeld van een op een productkwaliteit tokencache-bestand gebaseerde serialisator voor openbare clienttoepassingen (voor desktoptoepassingen die op Windows, Mac en Linux worden uitgevoerd) is beschikbaar in de open-sourcebibliotheek [van Microsoft.Identity.Client.Extensions.Msal.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) U het opnemen in uw toepassingen via het volgende nuget-pakket: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Serialisatie met dubbele tokencache (MSAL unified cache en ADAL v3)

Als u tokencacheserialisatie wilt implementeren met de indeling voor de uniforme cache (gemeenschappelijk voor ADAL.NET 4.x, MSAL.NET 2.x en andere MSALs van dezelfde generatie of ouder, op hetzelfde platform), bekijkt u de volgende code:

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

Deze keer de helperklasse zoals gedefinieerd als:

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

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Tokencache voor een web-app (vertrouwelijke clienttoepassing)

In web-apps of web-API's kan de cache gebruikmaken van de sessie, een Redis-cache of een database.

Houd in web-apps of web-API's één tokencache per account bij.  Voor web-apps moet de tokencache worden ingetoetst door de account-id.  Voor web-API's moet het account worden ingetoetst door de hash van het token dat wordt gebruikt om de API aan te roepen. MSAL.NET biedt aangepaste tokencacheserialisatie in .NET Framework- en .NET Core-subplatforms. Gebeurtenissen worden geactiveerd wanneer de cache wordt geopend, apps kunnen kiezen of ze de cache moeten serialiseren of deserialiseren. Op vertrouwelijke clienttoepassingen die gebruikers verwerken (webapps die gebruikers aanmelden en web-API's bellen en web-API's die downstreamweb-API's aanroepen), kunnen er veel gebruikers zijn en worden de gebruikers parallel verwerkt. Om redenen van veiligheid en prestaties is het onze aanbeveling om één cache per gebruiker te serialiseren. Serialisatiegebeurtenissen berekenen een cachesleutel op basis van de identiteit van de verwerkte gebruiker en serialiseren/deserialiseren van een tokencache voor die gebruiker.

Voorbeelden van het gebruik van tokencaches voor web-apps en web-API's zijn beschikbaar in de [ASP.NET Core-webapp-zelfstudie](https://ms-identity-aspnetcore-webapp-tutorial) in de fase [2-2 Token Cache.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) Kijk voor implementaties naar de map [TokenCacheProviders](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/Microsoft.Identity.Web/TokenCacheProviders) in de [microsoft-authentication-extensions-for-dotnet-bibliotheek](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (in de map [Microsoft.Identity.Client.Extensions.Web.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) 

## <a name="next-steps"></a>Volgende stappen
De volgende voorbeelden illustreren tokencacheserialisatie.

| Voorbeeld | Platform | Beschrijving|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Bureaublad (WPF) | Wpf-toepassing (Windows Desktop .NET) die de Microsoft Graph-API aanroept. ![Topologie](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Bureaublad (console) | Set Visual Studio-oplossingen ter illustratie van de migratie van Azure AD v1.0-toepassingen (met behulp van ADAL.NET) naar Azure AD v2.0-toepassingen, ook wel geconvergeerde toepassingen genoemd (met behulp van MSAL.NET), met name [migratie van tokencache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|

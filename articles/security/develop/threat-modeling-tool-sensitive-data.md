---
title: Gevoelige gegevens - Microsoft Threat Modeling Tool - Azure | Microsoft Documenten
description: oplossingen voor bedreigingen die worden blootgesteld in de Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: ab22e9843ca133274361838eeb49abbe326588dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502231"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Beveiligingsframe: gevoelige gegevens | Mitigaties 
| Product/service | Artikel |
| --------------- | ------- |
| **Grens van machinevertrouwensrelatie** | <ul><li>[Zorg ervoor dat binaire bestanden worden versluierd als ze gevoelige informatie bevatten](#binaries-info)</li><li>[Overweeg om efs (Encrypted File System) te gebruiken om vertrouwelijke gebruikersspecifieke gegevens te beschermen](#efs-user)</li><li>[Ervoor zorgen dat gevoelige gegevens die door de toepassing op het bestandssysteem zijn opgeslagen, zijn versleuteld](#filesystem)</li></ul> | 
| **Webtoepassing** | <ul><li>[Ervoor zorgen dat gevoelige inhoud niet in de cache wordt opgeslagen in de browser](#cache-browser)</li><li>[Delen van de configuratiebestanden van Web App versleutelen die gevoelige gegevens bevatten](#encrypt-data)</li><li>[Het kenmerk Automatisch aanvullen HTML expliciet uitschakelen in gevoelige formulieren en invoer](#autocomplete-input)</li><li>[Ervoor zorgen dat gevoelige gegevens die op het gebruikersscherm worden weergegeven, worden gemaskeerd](#data-mask)</li></ul> | 
| **Database** | <ul><li>[Dynamische gegevensmaskering implementeren om gevoelige blootstelling aan niet-bevoorrechte gebruikers van gevoelige gegevens te beperken](#dynamic-users)</li><li>[Ervoor zorgen dat wachtwoorden worden opgeslagen in gezouten hash-indeling](#salted-hash)</li><li>[Ervoor zorgen dat gevoelige gegevens in databasekolommen worden versleuteld](#db-encrypted)</li><li>[Ervoor zorgen dat versleuteling op databaseniveau (TDE) is ingeschakeld](#tde-enabled)</li><li>[Ervoor zorgen dat databaseback-ups worden versleuteld](#backup)</li></ul> | 
| **Web-API** | <ul><li>[Ervoor zorgen dat gevoelige gegevens die relevant zijn voor web-API niet worden opgeslagen in de opslag van de browser](#api-browser)</li></ul> | 
| Azure Document DB | <ul><li>[Gevoelige gegevens versleutelen die zijn opgeslagen in Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Azure IaaS VM-vertrouwensgrens** | <ul><li>[Azure-schijfversleuteling gebruiken om schijven te versleutelen die door virtuele machines worden gebruikt](#disk-vm)</li></ul> | 
| **Vertrouwensgrens servicestructuur** | <ul><li>[Geheimen versleutelen in Service Fabric-toepassingen](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Beveiligingsmodellering uitvoeren en business units/teams gebruiken waar nodig](#modeling-teams)</li><li>[De toegang tot de functie delen op kritieke entiteiten minimaliseren](#entities)</li><li>[Gebruikers trainen op de risico's van de Dynamics CRM Share-functie en goede beveiligingspraktijken](#good-practices)</li><li>[Een ontwikkelingsstandaardregel opnemen voor het schrijven van config-gegevens in uitzonderingsbeheer](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Azure Storage Service Encryption (SSE) gebruiken voor Gegevens in rust (Voorbeeld)](#sse-preview)</li><li>[Versleuteling aan clientzijde gebruiken om gevoelige gegevens op te slaan in Azure Storage](#client-storage)</li></ul> | 
| **Mobiele client** | <ul><li>[Gevoelige of PII-gegevens versleutelen die zijn geschreven naar lokale opslag van telefoons](#pii-phones)</li><li>[Versluiering van gegenereerde binaire bestanden voordat ze worden gedistribueerd naar eindgebruikers](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[ClientCredentialType instellen op Certificaat of Windows](#cert)</li><li>[WCF-Beveiligingsmodus is niet ingeschakeld](#security)</li></ul> | 

## <a name="ensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>Zorg ervoor dat binaire bestanden worden versluierd als ze gevoelige informatie bevatten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van machinevertrouwensrelatie | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat binaire bestanden worden versluierd als ze gevoelige informatie bevatten, zoals bedrijfsgeheimen, gevoelige bedrijfslogica die niet mag worden teruggedraaid. Dit is om te stoppen met reverse engineering van assemblages. Tools `CryptoObfuscator` zoals kunnen worden gebruikt voor dit doel. |

## <a name="consider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>Overweeg om efs (Encrypted File System) te gebruiken om vertrouwelijke gebruikersspecifieke gegevens te beschermen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van machinevertrouwensrelatie | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Overweeg om het EFS (Encrypted File System) te gebruiken om vertrouwelijke gebruikersspecifieke gegevens te beschermen tegen tegenstanders met fysieke toegang tot de computer. |

## <a name="ensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>Ervoor zorgen dat gevoelige gegevens die door de toepassing op het bestandssysteem zijn opgeslagen, zijn versleuteld

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van machinevertrouwensrelatie | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Ervoor zorgen dat gevoelige gegevens die door de toepassing op het bestandssysteem zijn opgeslagen, worden versleuteld (bijvoorbeeld met Behulp van DPAPI), als EFS niet kan worden afgedwongen |

## <a name="ensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>Ervoor zorgen dat gevoelige inhoud niet in de cache wordt opgeslagen in de browser

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemene webformulieren, MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Browsers kunnen informatie opslaan met het oog op caching en geschiedenis. Deze bestanden in de cache worden opgeslagen in een map, zoals de map Tijdelijke internetbestanden in het geval van Internet Explorer. Wanneer deze pagina's opnieuw worden doorverwezen, wordt ze in de browser weergegeven vanuit de cache. Als gevoelige informatie wordt weergegeven aan de gebruiker (zoals hun adres, creditcardgegevens, burgerservicenummer of gebruikersnaam), kan deze informatie worden opgeslagen in de cache van de browser en dus kunnen worden opgehaald door de cache van de browser te onderzoeken of gewoon op de "Terug" knop van de browser drukken. Stel de waarde van de antwoordkopwaarde voor cache-control in op 'no-store' voor alle pagina's. |

### <a name="example"></a>Voorbeeld
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-cache" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Voorbeeld
Dit kan worden geïmplementeerd via een filter. Het volgende voorbeeld kan worden gebruikt: 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a name="encrypt-sections-of-web-apps-configuration-files-that-contain-sensitive-data"></a><a id="encrypt-data"></a>Delen van de configuratiebestanden van Web App versleutelen die gevoelige gegevens bevatten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [How To: Configuratiesecties versleutelen in ASP.NET 2.0 met Behulp van DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [Een beveiligde configuratieprovider opgeven](https://msdn.microsoft.com/library/68ze1hb2.aspx), Azure Key Vault gebruiken om [toepassingsgeheimen te beschermen](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Stappen** | Configuratiebestanden zoals de Web.config, appsettings.json worden vaak gebruikt om gevoelige informatie vast te houden, waaronder gebruikersnamen, wachtwoorden, databaseverbindingstekenreeksen en versleutelingssleutels. Als u deze informatie niet beschermt, is uw toepassing kwetsbaar voor aanvallers of kwaadwillende gebruikers die gevoelige informatie verkrijgen, zoals namen van accountgebruikers en wachtwoorden, databasenamen en servernamen. Versleutel op basis van het implementatietype (azure/on-prem) de gevoelige secties van config-bestanden met DPAPI of services zoals Azure Key Vault. |

## <a name="explicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>Het kenmerk Automatisch aanvullen HTML expliciet uitschakelen in gevoelige formulieren en invoer

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN: autocomplete attribuut](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [Met behulp van AutoComplete in HTML](https://msdn.microsoft.com/library/ms533032.aspx), HTML [Sanitization Vulnerability](https://technet.microsoft.com/security/bulletin/MS10-071), [Autocomplete.,again?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Stappen** | Het kenmerk automatisch aanvullen geeft aan of een formulier automatisch aanvullen moet hebben. Wanneer automatisch aanvullen is ingeschakeld, voltooit de browser automatisch waarden op basis van waarden die de gebruiker eerder heeft ingevoerd. Wanneer bijvoorbeeld een nieuwe naam en wachtwoord in een formulier wordt ingevoerd en het formulier wordt ingediend, vraagt de browser of het wachtwoord moet worden opgeslagen. Daarna, wanneer het formulier wordt weergegeven, worden de naam en het wachtwoord automatisch ingevuld of ingevuld als de naam wordt ingevoerd. Een aanvaller met lokale toegang kan het duidelijke tekstwachtwoord verkrijgen uit de browsercache. Standaard is automatisch aanvullen ingeschakeld en moet deze expliciet worden uitgeschakeld. |

### <a name="example"></a>Voorbeeld
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a name="ensure-that-sensitive-data-displayed-on-the-user-screen-is-masked"></a><a id="data-mask"></a>Ervoor zorgen dat gevoelige gegevens die op het gebruikersscherm worden weergegeven, worden gemaskeerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Gevoelige gegevens zoals wachtwoorden, creditcardnummers, SSN etc. moeten worden gemaskeerd wanneer ze op het scherm worden weergegeven. Dit om te voorkomen dat onbevoegd personeel toegang krijgt tot de gegevens (bijvoorbeeld schoudersurfende wachtwoorden, ondersteunend personeel dat SSN-nummers van gebruikers bekijkt). Zorg ervoor dat deze gegevenselementen niet zichtbaar zijn in platte tekst en op de juiste manier worden gemaskeerd. Dit moet worden verzorgd terwijl ze worden geaccepteerd als input (bijv.. invoertype="wachtwoord") en weergeven op het scherm (bijvoorbeeld alleen de laatste 4 cijfers van het creditcardnummer weergeven). |

## <a name="implement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>Dynamische gegevensmaskering implementeren om gevoelige blootstelling aan niet-bevoorrechte gebruikers van gevoelige gegevens te beperken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Sql Azure, OnPrem |
| **Kenmerken**              | SQL-versie - V12, SQL-versie - MsSQL2016 |
| **Verwijzingen**              | [Dynamische gegevensmaskering](https://msdn.microsoft.com/library/mt130841) |
| **Stappen** | Het doel van dynamische gegevensmaskering is om de blootstelling van gevoelige gegevens te beperken, waardoor gebruikers die geen toegang zouden moeten hebben tot de gegevens, deze niet mogen bekijken. Dynamische gegevensmaskering is niet bedoeld om te voorkomen dat databasegebruikers rechtstreeks verbinding maken met de database en uitputtende query's uitvoeren die stukken van de gevoelige gegevens blootleggen. Dynamische gegevensmaskering is een aanvulling op andere SQL Server-beveiligingsfuncties (auditing, encryptie, rijniveaubeveiliging...) en het wordt ten zeerste aanbevolen om deze functie in combinatie met hen te gebruiken om de gevoelige gegevens in de Database. Houd er rekening mee dat deze functie alleen wordt ondersteund door SQL Server vanaf 2016 en Azure SQL Database. |

## <a name="ensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>Ervoor zorgen dat wachtwoorden worden opgeslagen in gezouten hash-indeling

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Wachtwoord Hashing met behulp van .NET Crypto API's](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Stappen** | Wachtwoorden mogen niet worden opgeslagen in aangepaste databases voor gebruikersarchief. Wachtwoordhashes moeten in plaats daarvan worden opgeslagen met zoutwaarden. Zorg ervoor dat het zout voor de gebruiker is altijd uniek en u van toepassing b-crypt, s-crypt of PBKDF2 voor het opslaan van het wachtwoord, met een minimale werkfactor iteratie telling van 150.000 lussen om de mogelijkheid van brute forceren te elimineren.| 

## <a name="ensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>Ervoor zorgen dat gevoelige gegevens in databasekolommen worden versleuteld

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | SQL-versie - Alles |
| **Verwijzingen**              | [Gevoelige gegevens versleutelen in SQL-server](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [Hoe een kolom met gegevens versleutelen in SQL Server](https://msdn.microsoft.com/library/ms179331), Versleutelen op [certificaat](https://msdn.microsoft.com/library/ms188061) |
| **Stappen** | Gevoelige gegevens zoals creditcardnummers moeten worden versleuteld in de database. Gegevens kunnen worden versleuteld met behulp van versleuteling op kolomniveau of door een toepassingsfunctie met behulp van de versleutelingsfuncties. |

## <a name="ensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>Ervoor zorgen dat versleuteling op databaseniveau (TDE) is ingeschakeld

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Sql Server Transparent Data Encryption (TDE) begrijpen](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Stappen** | De functie Transparent Data Encryption (TDE) in SQL-server helpt bij het versleutelen van gevoelige gegevens in een database en beschermt de sleutels die worden gebruikt om de gegevens te versleutelen met een certificaat. Dit voorkomt dat iemand zonder de sleutels de gegevens kan gebruiken. TDE beschermt gegevens "in rust", wat betekent dat de gegevens en logbestanden. Het biedt de mogelijkheid om te voldoen aan vele wetten, voorschriften en richtlijnen die in verschillende industrieën zijn vastgesteld. |

## <a name="ensure-that-database-backups-are-encrypted"></a><a id="backup"></a>Ervoor zorgen dat databaseback-ups worden versleuteld

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | SQL Azure, OnPrem |
| **Kenmerken**              | SQL-versie - V12, SQL-versie - MsSQL2014 |
| **Verwijzingen**              | [SQL-databaseback-upversleuteling](https://msdn.microsoft.com/library/dn449489) |
| **Stappen** | SQL Server heeft de mogelijkheid om de gegevens te versleutelen tijdens het maken van een back-up. Door het versleutelingsalgoritme en de encryptor (een certificaat of asymmetrische sleutel) op te geven bij het maken van een back-up, kan men een versleuteld back-upbestand maken. |

## <a name="ensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Ervoor zorgen dat gevoelige gegevens die relevant zijn voor web-API niet worden opgeslagen in de opslag van de browser

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | MVC 5, MVC 6 |
| **Kenmerken**              | Identity Provider - ADFS, Identity Provider - Azure AD |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>In bepaalde implementaties worden gevoelige artefacten die relevant zijn voor de verificatie van de Web API opgeslagen in de lokale opslag van de browser. Bijvoorbeeld Azure AD-verificatieartefacten zoals adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key etc.</p><p>Al deze artefacten zijn beschikbaar, zelfs nadat afmelden of browser is gesloten. Als een tegenstander toegang krijgt tot deze artefacten, kan hij/zij deze opnieuw gebruiken om toegang te krijgen tot de beveiligde bronnen (API's). Zorg ervoor dat alle gevoelige artefacten met betrekking tot web-API niet worden opgeslagen in de opslag van de browser. In gevallen waarin client-side storage onvermijdelijk is (bijvoorbeeld Single Page Applications (SPA) die gebruikmaken van impliciete OpenIdConnect/OAuth-stromen moeten toegangstokens lokaal opslaan), gebruiken opslagkeuzes met geen persistentie. geef bijvoorbeeld de voorkeur aan SessionStorage boven LocalStorage.</p>| 

### <a name="example"></a>Voorbeeld
Het onderstaande JavaScript-fragment is afkomstig van een aangepaste verificatiebibliotheek die verificatieartefacten opslaat in lokale opslag. Dergelijke implementaties moeten worden vermeden. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a name="encrypt-sensitive-data-stored-in-cosmos-db"></a><a id="encrypt-docdb"></a>Gevoelige gegevens versleutelen die zijn opgeslagen in Cosmos DB

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Document DB | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Versleutel gevoelige gegevens op toepassingsniveau voordat u gevoelige gegevens opslaat in document DB of slaat gevoelige gegevens op in andere opslagoplossingen zoals Azure Storage of Azure SQL| 

## <a name="use-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Azure-schijfversleuteling gebruiken om schijven te versleutelen die door virtuele machines worden gebruikt

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure IaaS VM-vertrouwensgrens | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Azure-schijfversleuteling gebruiken om schijven te versleutelen die door uw virtuele machines worden gebruikt](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Stappen** | <p>Azure Disk Encryption is een nieuwe functie die momenteel in preview. Met deze functie u de schijven en gegevensschijven versleutelen die door een Virtuele Auto van IaaS worden gebruikt. Voor Windows worden de schijven versleuteld met behulp van industriestandaard BitLocker-versleutelingstechnologie. Voor Linux worden de schijven versleuteld met behulp van de DM-Crypt-technologie. Dit is geïntegreerd met Azure Key Vault, zodat u de schijfversleutelingssleutels beheren en beheren. De Azure Disk Encryption-oplossing ondersteunt de volgende drie klantversleutelingsscenario's:</p><ul><li>Schakel versleuteling in op nieuwe IaaS VM's die zijn gemaakt van door de klant versleutelde VHD-bestanden en door de klant verstrekte versleutelingssleutels, die zijn opgeslagen in Azure Key Vault.</li><li>Versleuteling inschakelen op nieuwe IaaS VM's die zijn gemaakt vanuit de Azure Marketplace.</li><li>Versleuteling inschakelen op bestaande IaaS VM's die al in Azure worden uitgevoerd.</li></ul>| 

## <a name="encrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Geheimen versleutelen in Service Fabric-toepassingen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Vertrouwensgrens servicestructuur | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Omgeving - Azure |
| **Verwijzingen**              | [Geheimen beheren in Service Fabric-toepassingen](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Stappen** | Geheimen kunnen gevoelige informatie zijn, zoals tekenreeksen voor opslagverbindingen, wachtwoorden of andere waarden die niet in platte tekst mogen worden verwerkt. Gebruik Azure Key Vault om sleutels en geheimen in servicefabric-toepassingen te beheren. |

## <a name="perform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>Beveiligingsmodellering uitvoeren en business units/teams gebruiken waar nodig

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Dynamics CRM | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Beveiligingsmodellering uitvoeren en business units/teams gebruiken waar nodig |

## <a name="minimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>De toegang tot de functie delen op kritieke entiteiten minimaliseren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Dynamics CRM | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | De toegang tot de functie delen op kritieke entiteiten minimaliseren |

## <a name="train-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>Gebruikers trainen op de risico's van de Dynamics CRM Share-functie en goede beveiligingspraktijken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Dynamics CRM | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Gebruikers trainen op de risico's van de Dynamics CRM Share-functie en goede beveiligingspraktijken |

## <a name="include-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>Een ontwikkelingsstandaardregel opnemen voor het schrijven van config-gegevens in uitzonderingsbeheer

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Dynamics CRM | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Voeg een ontwikkelingsstandaardregel toe voor het schrijven van config-details in uitzonderingsbeheer buiten ontwikkeling. Test hiervoor als onderdeel van codereviews of periodieke inspectie.|

## <a name="use-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Azure Storage Service Encryption (SSE) gebruiken voor Gegevens in rust (Voorbeeld)

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | StorageType - Blob |
| **Verwijzingen**              | [Azure Storage Service-versleuteling voor gegevens in rust (voorbeeld)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Stappen** | <p>Azure Storage Service Encryption (SSE) for Data at Rest helpt u uw gegevens te beschermen en te beschermen om te voldoen aan uw organisatorische beveiligings- en nalevingsverplichtingen. Met deze functie worden uw gegevens automatisch versleuteld door Azure Storage voordat deze worden opgeslagen en ontsleutelt voordat ze weer worden opgehaald. De encryptie, decryptie en key management is volledig transparant voor gebruikers. SSE is alleen van toepassing op het blokkeren van blobs, paginablobs en het toevoegen van blobs. De andere soorten gegevens, waaronder tabellen, wachtrijen en bestanden, worden niet versleuteld.</p><p>Werkstroom voor versleuteling en decryptie:</p><ul><li>De klant maakt versleuteling op het opslagaccount mogelijk</li><li>Wanneer de klant nieuwe gegevens (PUT Blob, PUT Block, PUT Page, enz.) naar Blob-opslag schrijft; elke schrijf is versleuteld met behulp van 256-bits AES-encryptie, een van de sterkste blok cijfers beschikbaar</li><li>Wanneer de klant toegang nodig heeft tot gegevens (GET Blob, enz.), worden gegevens automatisch gedecodeerd voordat ze terugkeren naar de gebruiker</li><li>Als versleuteling is uitgeschakeld, worden nieuwe schrijfbewerkingen niet meer versleuteld en blijven bestaande versleutelde gegevens versleuteld totdat ze door de gebruiker worden herschreven. Terwijl versleuteling is ingeschakeld, worden schrijfbewerkingen naar Blob-opslag versleuteld. De status van de gegevens verandert niet met de gebruiker die toggling tussen het inschakelen / uitschakelen van encryptie voor de opslag account</li><li>Alle versleutelingssleutels worden opgeslagen, versleuteld en beheerd door Microsoft</li></ul><p>Houd er rekening mee dat op dit moment de sleutels die voor de versleuteling worden gebruikt, worden beheerd door Microsoft. Microsoft genereert de sleutels oorspronkelijk en beheert de beveiligde opslag van de sleutels en de regelmatige rotatie zoals gedefinieerd door het interne Microsoft-beleid. In de toekomst krijgen klanten de mogelijkheid om hun eigen >-encryptiesleutels te beheren en een migratiepad te bieden van door Microsoft beheerde sleutels naar door de klant beheerde sleutels.</p>| 

## <a name="use-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>Versleuteling aan clientzijde gebruiken om gevoelige gegevens op te slaan in Azure Storage

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Client-Side Encryption en Azure Key Vault for Microsoft Azure Storage](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [Zelfstudie: Blobs versleutelen en decoderen in Microsoft Azure Storage met Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), Gegevens veilig opslaan in Azure Blob Storage met Azure Encryption [Extensions](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Stappen** | <p>Het Azure Storage Client Library for .NET Nuget-pakket ondersteunt het versleutelen van gegevens binnen clienttoepassingen voordat u naar Azure Storage wordt geüpload en gegevens decoderen terwijl u naar de client downloadt. De bibliotheek ondersteunt ook integratie met Azure Key Vault voor het beheer van sleutels voor opslagaccounts. Hier is een korte beschrijving van hoe client side encryptie werkt:</p><ul><li>De Azure Storage client SDK genereert een content encryption key (CEK), een eenmalige symmetrische sleutel</li><li>Klantgegevens worden versleuteld met behulp van deze CEK</li><li>De CEK wordt vervolgens verpakt (versleuteld) met behulp van de sleutel encryptiesleutel (KEK). De KEK wordt geïdentificeerd door een sleutel-id en kan een asymmetrisch sleutelpaar of een symmetrische sleutel zijn en lokaal worden beheerd of opgeslagen in Azure Key Vault. De Storage client zelf heeft nooit toegang tot de KEK. Het beroept zich gewoon op de sleutel verpakking algoritme dat wordt geleverd door Key Vault. Klanten kunnen ervoor kiezen om aangepaste providers te gebruiken voor het inpakken/uitpakken van sleutels als ze dat willen</li><li>De versleutelde gegevens worden vervolgens geüpload naar de Azure Storage-service. Controleer de koppelingen in de sectie referenties voor implementatiedetails op laag niveau.</li></ul>|

## <a name="encrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>Gevoelige of PII-gegevens versleutelen die zijn geschreven naar lokale opslag van telefoons

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Mobiele client | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, Xamarin  |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Instellingen en functies op uw apparaten beheren met Microsoft Intune-beleid](https://docs.microsoft.com/mem/intune/configuration/), [Keychain Valet](https://components.xamarin.com/view/square.valet) |
| **Stappen** | <p>Als de toepassing gevoelige informatie schrijft, zoals de PII van de gebruiker (e-mail, telefoonnummer, voornaam, achternaam, voorkeuren enz.) - op het bestandssysteem van mobiel, dan moet het worden versleuteld voordat u naar het lokale bestandssysteem schrijft. Als de toepassing een bedrijfstoepassing is, onderzoekt u de mogelijkheid om toepassingen te publiceren met Windows Intune.</p>|

### <a name="example"></a>Voorbeeld
Intune kan worden geconfigureerd met het volgende beveiligingsbeleid om gevoelige gegevens te beveiligen: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Voorbeeld
Als de toepassing geen bedrijfstoepassing is, gebruikt u vervolgens platform-meegeleverde keystore, sleutelhangers om versleutelingssleutels op te slaan, waarbij cryptografische bewerking op het bestandssysteem kan worden uitgevoerd. Onderstaand codefragment laat zien hoe u toegang krijgt tot de sleutel van de sleutelhanger met xamarin: 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a name="obfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>Versluiering van gegenereerde binaire bestanden voordat ze worden gedistribueerd naar eindgebruikers

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Mobiele client | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Crypto-obfuscation voor .Net](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Stappen** | Gegenereerde binaire bestanden (assemblages binnen apk) moeten worden versluierd om reverse engineering van assemblages te stoppen. Tools `CryptoObfuscator` zoals kunnen worden gebruikt voor dit doel. |

## <a name="set-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>ClientCredentialType instellen op Certificaat of Windows

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | .NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Versterken](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Stappen** | Met behulp van een UsernameToken met een plaintext wachtwoord over een onversleuteld kanaal stelt het wachtwoord bloot aan aanvallers die de SOAP-berichten kunnen snuiven. Serviceproviders die de UsernameToken gebruiken, kunnen wachtwoorden accepteren die in plaintext worden verzonden. Het verzenden van plaintext wachtwoorden via een onversleuteld kanaal kan de referentie bloot stellen aan aanvallers die kunnen snuiven de SOAP-bericht. | 

### <a name="example"></a>Voorbeeld
De volgende WCF-serviceproviderconfiguratie maakt gebruik van gebruikersnaamtoken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
ClientCredentialType instellen op Certificaat of Windows. 

## <a name="wcf-security-mode-is-not-enabled"></a><a id="security"></a>WCF-Beveiligingsmodus is niet ingeschakeld

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, .NET Framework 3 |
| **Kenmerken**              | Beveiligingsmodus - Transport, Beveiligingsmodus - Bericht |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [Fundamentals van WCF Security CoDe Magazine](https://www.codemag.com/article/0611051) |
| **Stappen** | Er is geen transport- of berichtbeveiliging gedefinieerd. Toepassingen die berichten verzenden zonder transport- of berichtbeveiliging kunnen de integriteit of vertrouwelijkheid van de berichten niet garanderen. Wanneer een WCF-beveiligingsbinding is ingesteld op Geen, worden zowel de transport- als de berichtbeveiliging uitgeschakeld. |

### <a name="example"></a>Voorbeeld
Met de volgende configuratie stelt u de beveiligingsmodus in op Geen. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Voorbeeld
Beveiligingsmodus Voor alle servicebindingen zijn er vijf mogelijke beveiligingsmodi: 
* Geen. Schakelt de beveiliging uit. 
* Vervoer. Maakt gebruik van transportbeveiliging voor wederzijdse verificatie en berichtbeveiliging. 
* Bericht. Gebruikt berichtbeveiliging voor wederzijdse verificatie en berichtbeveiliging. 
* Zowel. Hiermee u instellingen voor transport- en berichtbeveiliging leveren (alleen MSMQ ondersteunt dit). 
* TransportWithMessageCredential. Referenties worden doorgegeven met het bericht en berichtbeveiliging en serververificatie worden geleverd door de transportlaag. 
* Alleen transportcredential. Clientreferenties worden doorgegeven met de transportlaag en er wordt geen berichtbeveiliging toegepast. Gebruik transport- en berichtbeveiliging om de integriteit en vertrouwelijkheid van berichten te beschermen. De onderstaande configuratie vertelt de service om transportbeveiliging te gebruiken met berichtreferenties.
  ```
  <system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
  </system.serviceModel> 
  ```

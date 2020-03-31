---
title: De Problemen met Azure Data Lake Analytics U-SQL oplossen vanwege de upgrade van .NET Framework 4.7.2
description: Problemen met U-SQL oplossen vanwege de upgrade naar .NET Framework 4.7.2.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213588"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics is een upgrade naar het .NET Framework v4.7.2

De standaardruntijd van Azure Data Lake Analytics wordt geüpgraded van .NET Framework v4.5.2 naar .NET Framework v4.7.2. Deze wijziging brengt een klein risico met zich mee dat wijzigingen worden doorgemaakt als uw U-SQL-code aangepaste samenstellingen gebruikt en deze aangepaste verzamelingen .NET-bibliotheken gebruiken.

Deze upgrade van .NET Framework 4.5.2 naar versie 4.7.2 betekent dat het .NET Framework dat is geïmplementeerd in een U-SQL-runtime (de standaardruntijd) nu altijd 4.7.2 is. Er is geen optie naast elkaar voor .NET Framework-versies.

Nadat deze upgrade naar .NET Framework 4.7.2 is voltooid, wordt de beheerde code van het systeem uitgevoerd als versie 4.7.2, waarbij de gebruiker bibliotheken zoals de aangepaste U-SQL-assemblages in de achterwaartscompatibele modus wordt uitgevoerd die geschikt is voor de versie die de assemblage is gegenereerd voor.

- Als uw verzamel-DLL's worden gegenereerd voor versie 4.5.2, wordt ze in het geïmplementeerde framework behandeld als 4.5.2-bibliotheken, met (op enkele uitzonderingen na) 4.5.2-semantiek.
- U nu aangepaste U-SQL-samenstellingen gebruiken die gebruik maken van functies van versie 4.7.2, als u zich richt op de .NET Framework 4.7.2.

Vanwege deze upgrade naar .NET Framework 4.7.2 is er een mogelijkheid om baanbrekende wijzigingen in uw U-SQL-taken in te voeren die gebruikmaken van .NET aangepaste assemblages. We raden u aan om te controleren op problemen met achterwaartse compatibiliteit met behulp van de onderstaande procedure.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Controleren op problemen met achterwaartse compatibiliteit

Controleer op het potentieel van problemen met het verbreken van achterwaartse compatibiliteit door de .NET-compatibiliteitscontroles uit te voeren op uw .NET-code in uw aangepaste U-SQL-samenstellingen.

> [!Note]
> De tool detecteert geen werkelijke wijzigingen. het identificeert alleen zogenaamde .NET API's die (voor bepaalde inputs) problemen kunnen veroorzaken. Als u op de hoogte wordt gesteld van een probleem, kan uw code nog steeds in orde zijn, maar u moet meer details inchecken.

1. Voer de achterwaartse compatibiliteitscontrole uit op uw .NET-dllen door
   1. De Visual Studio-extensie gebruiken bij [.NET Portability Analyzer Visual Studio Extension](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Downloaden en gebruiken van de standalone tool van [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). Instructies voor het uitvoeren van standalone tool zijn op [GitHub dotnetapiport breken veranderingen](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. Voor 4.7.2. compatibiliteit, `read isRetargeting == True` identificeert mogelijke problemen.
2. Als het hulpprogramma aangeeft of uw code mogelijk wordt beïnvloed door een van de mogelijke backwards-incompatibilities (enkele veelvoorkomende voorbeelden van onverenigbaarheden worden hieronder weergegeven), u verder controleren door
   1. Uw code analyseren en identificeren of uw code waarden doorgeeft aan de getroffen API's
   1. Voer een runtime-controle uit. De runtime-implementatie gebeurt niet naast elkaar in ADLA. U vóór de upgrade een runtime-controle uitvoeren met behulp van de lokale runtime-run van VisualStudio met een lokale .NET Framework 4.7.2 tegen een representatieve gegevensset.
3. Als u inderdaad wordt beïnvloed door een achterwaartse incompatibiliteit, neemt u de nodige stappen om deze te repareren (zoals het herstellen van uw gegevens of codelogica).

In de meeste gevallen mag u geen last hebben van achterwaartse onverenigbaarheid.

## <a name="timeline"></a>Tijdlijn

U controleren op de implementatie van de nieuwe runtime hier [Runtime problemen oplossen,](runtime-troubleshoot.md)en door te kijken naar een eerdere succesvolle taak.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>Wat als ik mijn code niet op tijd kan laten beoordelen

U uw taak indienen tegen de oude runtime-versie (die is gebouwd targeting 4.5.2), maar als gevolg van het ontbreken van .NET Framework side-by-side mogelijkheden, zal het nog steeds alleen draaien in 4.5.2 compatibiliteitsmodus. U nog steeds een aantal van de backwards-compatibility problemen als gevolg van dit gedrag.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Wat zijn de meest voorkomende backwards-compatibility problemen die u tegenkomen

De meest voorkomende backwards-incompatibilities die de checker waarschijnlijk zal identificeren zijn (we hebben deze lijst gegenereerd door de controle op onze eigen interne ADLA-taken uit te voeren), welke bibliotheken worden beïnvloed (let op: dat u de bibliotheken slechts indirect bellen, dus het is belangrijk om de vereiste actie te ondernemen #1 om te controleren of uw taken worden beïnvloed) en mogelijke acties om te verhelpen. Opmerking: In bijna alle gevallen voor onze eigen banen, de waarschuwingen bleek te zijn valse positieven als gevolg van de smalle aard van de meeste brekende veranderingen.

- IAsyncResult.CompletedSynchronously eigenschap moet correct zijn voor de resulterende taak te voltooien
  - Wanneer u TaskFactory.FromAsync aanroept, moet de implementatie van de eigenschap IAsyncResult.CompletedSynchronously correct zijn om de resulterende taak te voltooien. Dat wil zeggen, de eigenschap moet terugkeren waar als, en alleen als, de uitvoering synchroon voltooid. Voorheen werd het pand niet gecontroleerd.
  - Getroffen bibliotheken: mscorlib, System.Threading.Tasks
  - Aanbevolen actie: Ervoor zorgen dat TaskFactory.FromAsync correct wordt geretourneerd

- DataObject.GetData haalt nu gegevens op als UTF-8
  - Voor apps die zich richten op de .NET Framework 4 of die worden uitgevoerd op de .NET Framework 4.5.1 of eerdere versies, haalt DataObject.GetData HTML-geformatteerde gegevens op als een ASCII-tekenreeks. Als gevolg hiervan worden niet-ASCII-tekens (tekens waarvan de ASCII-codes groter zijn dan 0x7F) vertegenwoordigd door twee willekeurige tekens.#N##N#Voor apps die zich `DataObject.GetData` richten op het .NET Framework 4.5 of hoger en worden uitgevoerd op het .NET Framework 4.5.2, worden HTML-geformatteerde gegevens opgehaald als UTF-8, die tekens groter dan 0x7F correct vertegenwoordigt.
  - Beïnvloed bibliotheken: Glo
  - Aanbevolen actie: ervoor zorgen dat opgehaalde gegevens de gewenste indeling zijn

- XmlWriter gooit op ongeldige surrogaatparen
  - Voor apps die zich richten op de .NET Framework 4.5.2 of eerdere versies, is het schrijven van een ongeldig surrogaatpaar met uitzonderingsterugbehandeling niet altijd een uitzondering. Voor apps die zich richten op de .NET Framework 4.6, een poging om een ongeldig surrogaat paar te schrijven gooit een `ArgumentException`.
  - Beïnvloedbibliotheken: System.Xml, System.Xml.ReaderWriter
  - Aanbevolen actie: Zorg ervoor dat u geen ongeldig surrogaatpaar schrijft dat een argumentuitzondering veroorzaakt

- HtmlTextWriter maakt `<br/>` element niet correct weer
  - Vanaf het .NET Framework 4.6 `HtmlTextWriter.RenderEndTag()` wordt `<BR />` aanbellen `HtmlTextWriter.RenderBeginTag()` en met `<BR />` een element correct invoegen (in plaats van twee)
  - Beïnvloedbibliotheken: System.Web
  - Aanbevolen actie: Zorg ervoor dat `<BR />` u het bedrag invoegt dat u verwacht te zien, zodat er geen willekeurig gedrag wordt gezien in de productietaak

- CreateDefaultAuthorizationContext bellen met een nulargument is gewijzigd
  - De implementatie van de AuthorizationContext die `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` door een oproep wordt teruggegeven aan het argument met een null-autorisatieHet argument is gewijzigd in het .NET Framework 4.6.
  - Getroffen bibliotheken: System.IdentityModel
  - Aanbevolen actie: ervoor zorgen dat u het nieuwe verwachte gedrag verwerkt wanneer er een nulautorisatiebeleid is
  
- RSACng laadt nu correct RSA-toetsen van niet-standaard sleutelgrootte
  - In .NET Framework-versies vóór 4.6.2 hebben klanten met niet-standaardsleutelgroottes `GetRSAPublicKey()` `GetRSAPrivateKey()` voor RSA-certificaten geen toegang tot deze sleutels via de uitbreidingsmethoden en extensiemethoden. A `CryptographicException` met het bericht "De gevraagde sleutelgrootte wordt niet ondersteund" wordt gegooid. Met het .NET Framework 4.6.2 is dit probleem opgelost. Op dezelfde `RSA.ImportParameters()` `RSACng.ImportParameters()` manier, en nu werken met `CryptographicException`niet-standaard sleutelmaten zonder gooien 's.
  - Getroffen bibliotheken: mscorlib, System.Core
  - Aanbevolen actie: ervoor zorgen dat RSA-toetsen werken zoals verwacht

- Pad colon controles zijn strenger
  - In .NET Framework 4.6.2 zijn een aantal wijzigingen aangebracht ter ondersteuning van niet eerder ondersteunde paden (zowel in lengte als in formaat). De controle op de syntaxis van de juiste stationsscheidingsteken (dubbele punt) werd correcter gemaakt, wat de bijwerking had van het blokkeren van sommige URI-paden in een paar geselecteerde pad-API's waar ze vroeger werden getolereerd.
  - Getroffen bibliotheken: mscorlib, System.Runtime.Extensions
  - Voorgestelde actie:

- Oproepen naar claimsidentiteitsconstructeurs
  - Vanaf het .NET Framework 4.6.2 is er `T:System.Security.Claims.ClaimsIdentity` een wijziging `T:System.Security.Principal.IIdentity` in de `P:System.Security.Claims.ClaimsIdentify.Actor` manier waarop constructors met een parameter de eigenschap instelden. Als `T:System.Security.Principal.IIdentity` het argument `T:System.Security.Claims.ClaimsIdentity` een object `P:System.Security.Claims.ClaimsIdentify.Actor` is `T:System.Security.Claims.ClaimsIdentity` en de `null`eigenschap `P:System.Security.Claims.ClaimsIdentify.Actor` van dat object `M:System.Security.Claims.ClaimsIdentity.Clone` dat niet is, wordt de eigenschap bevestigd met behulp van de methode. In framework 4.6.1 en eerdere `P:System.Security.Claims.ClaimsIdentify.Actor` versies wordt de eigenschap als bestaande referentie gekoppeld. Door deze wijziging, te beginnen met het .NET `P:System.Security.Claims.ClaimsIdentify.Actor` Framework 4.6.2, is de eigenschap van het nieuwe `T:System.Security.Claims.ClaimsIdentity` object niet gelijk aan de `P:System.Security.Claims.ClaimsIdentify.Actor` eigenschap van het `T:System.Security.Principal.IIdentity` argument van de constructeur. In de .NET Framework 4.6.1 en eerdere versies is deze gelijk.
  - Getroffen bibliotheken: mscorlib
  - Aanbevolen actie: Ervoor zorgen dat ClaimsIdentity werkt zoals verwacht op nieuwe runtime

- Serialisatie van besturingstekens met DataContractJsonSerializer is nu compatibel met ECMAScript V6 en V8
  - In het .NET framework 4.6.2 en eerdere versies heeft de DataContractJsonSerializer een aantal speciale controletekens, zoals \b, \f en \t, niet geserialneerd op een manier die compatibel was met de ECMAScript V6- en V8-standaarden. Vanaf het .NET Framework 4.7 is serialisatie van deze besturingselementtekens compatibel met ECMAScript V6 en V8.
  - Getroffen bibliotheken: System.Runtime.Serialization.Json
  - Aanbevolen actie: Zorg voor hetzelfde gedrag met DataContractJsonSerializer

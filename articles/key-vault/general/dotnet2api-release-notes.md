---
title: Key Vault .NET 2.x API Release Notes| Microsoft Documenten
description: .NET-ontwikkelaars gebruiken deze API om te coderen voor Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 436b9c1569d7c33f79a126cd4d0513bac9385d8a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431915"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 - Release Notes and Migration Guide
Met de volgende informatie u migreren naar de 2.0-versie van de Azure Key Vault-bibliotheek voor C# en .NET.  Apps die zijn geschreven voor eerdere versies moeten worden bijgewerkt om de nieuwste versie te ondersteunen.  Deze wijzigingen zijn nodig om nieuwe en verbeterde functies, zoals **Key Vault-certificaten,** volledig te ondersteunen.

## <a name="key-vault-certificates"></a>Key Vault-certificaten

Key Vault-certificaten beheren x509-certificaten en ondersteunen het volgende gedrag:  

* Maak certificaten via een sleutelkluiscreatieproces of importeer bestaand certificaat. Dit omvat zowel door zichzelf ondertekende als door de Certificaatautoriteit (CA) gegenereerde certificaten.
* Sla x509-certificaatopslag veilig op en beheer deze zonder interactie met behulp van privésleutelmateriaal.  
* Definieer beleidsregels die Key Vault leiden om de levenscyclus van het certificaat te beheren.  
* Geef contactgegevens op voor levenscyclusgebeurtenissen, zoals verloopwaarschuwingen en vernieuwingsmeldingen.  
* Certificaten automatisch verlengen met geselecteerde emittenten (Key Vault-partner X509-certificaatleveranciers en certificaatautoriteiten).* Ondersteuningscertificaat van alternatieve (niet-partner) biedt en certificaatautoriteiten (ondersteunt geen automatische verlenging).  

## <a name="net-support"></a>Ondersteuning voor .NET

* **.NET 4.0** wordt niet ondersteund door de 2.0-versie van de Azure Key Vault .NET-bibliotheek
* **.NET Framework 4.5.2** wordt ondersteund door de 2.0-versie van de Azure Key Vault .NET-bibliotheek
* **.NET Standard 1.4** wordt ondersteund door de 2.0-versie van de Azure Key Vault .NET-bibliotheek

## <a name="namespaces"></a>Naamruimten

* De naamruimte voor **modellen** wordt gewijzigd van **Microsoft.Azure.KeyVault** naar **Microsoft.Azure.KeyVault.Models**.
* De **naamruimte van Microsoft.Azure.KeyVault.Internal** wordt verwijderd.
* De volgende Azure SDK-afhankelijkheden naamruimten hebben 

    - **Hyak.Common** is nu **Microsoft.Rest**.
    - **Hyak.Common.Internals** is nu **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Wijzigingen typen

* *Geheim* veranderd in *SecretBundle*
* *Woordenboek* gewijzigd in *IDictionary*
* *Lijst\<T>, tekenreeks []* gewijzigd in *\<IList T>*
* *NextList* gewijzigd in *NextPageLink*

## <a name="return-types"></a>Retourtypen

* **KeyList** en **SecretList** retourneert nu *IPage\<T>* in plaats van *ListKeysResponse*
* De gegenereerde **BackupKeyAsync** retourneert nu *BackupKeyResult*, dat *waarde* bevat (back-up blob). Voorheen werd de methode verpakt en alleen de waarde geretourneerd.

## <a name="exceptions"></a>Uitzonderingen

* *KeyVaultClientException* wordt gewijzigd in *KeyVaultErrorException*
* De servicefout is gewijzigd van *uitzondering. Fout* in *uitzondering. Body.Error.Message*.
* Aanvullende informatie uit het foutbericht verwijderd voor **[JsonExtensionData]**.

## <a name="constructors"></a>Constructors

* In plaats van een *HttpClient* als constructorargument te accepteren, accepteert de constructor alleen *HttpClientHandler* of *DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Gedownloade pakketten

Wanneer een client een afhankelijkheid van Key Vault verwerkt, worden de volgende pakketten gedownload:

### <a name="previous-package-list"></a>Vorige pakketlijst

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Huidige pakketlijst

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Klassewijzigingen

* **UnixEpoch** klasse is verwijderd.
* **Base64UrlConverter** klasse is omgedoopt tot **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Andere wijzigingen

* Ondersteuning voor de configuratie van het beleid voor het opnieuw proberen van KV-bewerkingen op tijdelijke fouten is toegevoegd aan deze versie van de API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Voor de bewerkingen die een *kluis*hebben geretourneerd, was het retourtype een klasse die een **vault-eigenschap** bevatte. Het retourtype is nu *Vault*.
* *PermissionsToKeys* and *PermissionsToSecrets* are now *Permissions.Keys* and *Permissions.Secrets*
* Bepaalde wijzigingen in retourtypen zijn ook van toepassing op het besturingsvlak.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Het pakket is opgesplitst in **Microsoft.Azure.KeyVault.Extensions** en **Microsoft.Azure.KeyVault.Cryptography** voor de cryptografiebewerkingen.


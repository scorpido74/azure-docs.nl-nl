---
title: azcopy-aanmelding | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy-aanmeld opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 754baa66d79d169f830332f3c39660f1d71f608a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527911"
---
# <a name="azcopy-login"></a>azcopy login

Meldt zich aan bij Azure Active Directory om toegang te krijgen tot Azure Storage resources.

## <a name="synopsis"></a>Samen vatting

Meld u aan bij Azure Active Directory om toegang te krijgen tot Azure Storage resources.

Als u wilt worden gemachtigd voor uw Azure Storage-account, moet u de rol van **BLOB-gegevens** voor de opslag in uw gebruikers account toewijzen in de context van het opslag account, de bovenliggende resource groep of het bovenliggende abonnement.

Met deze opdracht worden de versleutelde aanmeldings gegevens voor de huidige gebruiker in de cache opgeslagen met de ingebouwde mechanismen van het besturings systeem.

Raadpleeg de voor beelden voor meer informatie.

> [!IMPORTANT]
> Als u een omgevings variabele instelt met behulp van de opdracht regel, wordt die variabele leesbaar in de opdracht regel geschiedenis. U kunt variabelen met referenties uit de opdracht regel geschiedenis wissen. Als u wilt voor komen dat variabelen in uw geschiedenis worden weer gegeven, kunt u een script gebruiken om de gebruiker om referenties te vragen en de omgevings variabele in te stellen.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](storage-use-azcopy-blobs.md)
- [Gegevens overdragen met AzCopy en bestandopslag](storage-use-azcopy-files.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

Interactief aanmelden met de standaard-Tenant-ID voor AAD die is ingesteld op algemeen:

```azcopy
azcopy login
```

Interactief aanmelden met een opgegeven Tenant-ID:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Meld u aan met de door het systeem toegewezen identiteit van een virtuele machine (VM):

```azcopy
azcopy login --identity
```

Meld u aan met de door de gebruiker toegewezen identiteit van een virtuele machine en een client-ID van de service-identiteit:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Meld u aan met de door de gebruiker toegewezen identiteit van een virtuele machine en een object-ID van de service-identiteit:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Meld u aan met de door de gebruiker toegewezen identiteit van een virtuele machine en een resource-ID van de service-identiteit:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Meld u aan als service-principal met behulp van een client geheim. Stel de omgevings variabele AZCOPY_SPA_CLIENT_SECRET in op het client geheim voor de geheim verificatie van de Service-Principal.

```azcopy
azcopy login --service-principal --application-id "YOUR_APP_ID" --tenant-id "YOUR_TENANT_ID"
```

Meld u aan als service-principal met behulp van een certificaat en wacht woord. Stel de omgevings variabele AZCOPY_SPA_CERT_PASSWORD in op het wacht woord van het certificaat voor autorisatie van de Service-Principal op basis van certificaten.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Zorg ervoor dat/path/to/my/CERT wordt behandeld als een pad naar een PEM-of PKCS12/pfx-profiel-bestand. AzCopy is niet bereikbaar in het systeem certificaat archief om uw certificaat te verkrijgen.

--het certificaatpad is verplicht bij het uitvoeren van de Service-Principal auth op basis van certificaten.

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|--Aad-eind punt|Het Azure Active Directory-eind punt dat moet worden gebruikt. De standaard waarde ( `https://login.microsoftonline.com` ) is correct voor de open bare Azure-Cloud. Stel deze para meter in wanneer u een verificatie uitvoert in een nationale Cloud. Zie [Azure AD-verificatie-eind punten](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).
Deze vlag is niet nodig voor Managed Service Identity.|
|--reeks van toepassing-id|De toepassings-ID van de door de gebruiker toegewezen identiteit. Vereist voor Service Principal auth.|
|--teken reeks voor certificaatpad|Het pad naar het certificaat voor SPN-verificatie. Vereist voor verificatie op basis van een service-principal voor certificaten.|
|-h,--Help|Help-inhoud voor de aanmeldings opdracht weer geven.|
|--identiteit|Meld u aan met de identiteit van de virtuele machine, ook wel Managed Service Identity (MSI) genoemd.|
|--identiteit-client-id-teken reeks|De client-ID van de door de gebruiker toegewezen identiteit.|
|--identiteits teken reeks-object-id|De object-ID van de door de gebruiker toegewezen identiteit.|
|--identiteits teken reeks van bron-id|De resource-ID van de door de gebruiker toegewezen identiteit.|
|--Service-Principal|Meld u aan via SPN (Service Principal Name) met behulp van een certificaat of een geheim. Het client geheim of het certificaat wachtwoord moet in de juiste omgevings variabele worden geplaatst. Typ `AzCopy env` om namen en beschrijvingen van omgevings variabelen weer te geven.|
|--teken reeks voor Tenant-id| de Tenant-ID van Azure Active Directory die moet worden gebruikt voor de interactieve aanmelding van OAuth-apparaten.|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|
|--vertrouwd-micro soft-achtervoegsels teken reeks   |Hiermee geeft u aanvullende domein achtervoegsels op waar Azure Active Directory aanmeldings tokens kunnen worden verzonden.  De standaard waarde is *. core.Windows.net;*. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. Alle hier vermelde waarden worden toegevoegd aan de standaard instelling. Voor beveiliging moet u Microsoft Azure domeinen hier alleen plaatsen. Scheid meerdere vermeldingen met een punt komma.|

## <a name="see-also"></a>Zie tevens

- [azcopy](storage-ref-azcopy.md)

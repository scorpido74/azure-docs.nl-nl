---
title: azcopy login | Microsoft Documenten
description: Dit artikel bevat referentie-informatie voor de azcopy login commando.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d07d1a706635a7f269a9a51769ae6f8bbf57df3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295410"
---
# <a name="azcopy-login"></a>azcopy login

Logt in bij Azure Active Directory om toegang te krijgen tot Azure Storage-bronnen.

## <a name="synopsis"></a>Synopsis

Meld u aan bij Azure Active Directory om toegang te krijgen tot Azure Storage-bronnen.

Als u wilt worden geautoriseerd voor uw Azure Storage-account, moet u de rol **opslagblobgegevensbijdrage aan** uw gebruikersaccount toewijzen in de context van het opslagaccount, de bovenliggende brongroep of het bovenliggende abonnement.

Deze opdracht cache gecodeerde inloggegevens voor de huidige gebruiker met behulp van het os ingebouwde mechanismen.

Zie de voorbeelden voor meer informatie.

> [!IMPORTANT]
> Als u een omgevingsvariabele instelt met behulp van de opdrachtregel, is die variabele leesbaar in de geschiedenis van de opdrachtregel. Overweeg variabelen te wissen die referenties bevatten uit de geschiedenis van de opdrachtregel. Om te voorkomen dat variabelen in uw geschiedenis worden weergegeven, u een script gebruiken om de gebruiker om hun referenties te vragen en de omgevingsvariabele in te stellen.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overbrengen met AzCopy- en Blob-opslag](storage-use-azcopy-blobs.md)
- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

Log interactief in met standaard AAD-tenant-id ingesteld op gemeenschappelijk:

```azcopy
azcopy login
```

Log interactief in met een opgegeven tenant-id:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Log in met de door het systeem toegewezen identiteit van een virtuele machine (VM):

```azcopy
azcopy login --identity
```

Meld u aan met de door de gebruiker toegewezen identiteit van een VM en een client-id van de service-identiteit:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Meld u aan met de door de gebruiker toegewezen identiteit van een VM en een object-id van de service-identiteit:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Meld u aan met de door de gebruiker toegewezen identiteit van een VM en een resource-id van de service-identiteit:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Log in als serviceprincipal met behulp van een klantgeheim. Stel de omgevingsvariabele AZCOPY_SPA_CLIENT_SECRET voor het klantgeheim voor secret based service principal auth.

```azcopy
azcopy login --service-principal
```

Log in als serviceprincipal met een certificaat en wachtwoord. Stel de omgevingsvariabele in AZCOPY_SPA_CERT_PASSWORD het wachtwoord van het certificaat in voor de hoofdautorisatie van de service op basis van cert.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Zorg ervoor dat u /path/to/my/cert behandelt als een pad naar een PEM- of PKCS12-bestand. AzCopy bereikt niet de systeemcert store om uw certificaat te verkrijgen.

--certificaat-pad is verplicht bij het doen van cert-based service principal auth.

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|--aad-eindpunt|Het Azure Active Directory-eindpunt dat moet worden gebruikt. De standaardwaarde (`https://login.microsoftonline.com`) is correct voor de openbare Azure-cloud. Stel deze parameter in bij het verifiëren in een nationale cloud. Zie [eindpunten azure AD-verificatie](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).
Deze vlag is niet nodig voor Managed Service Identity.|
|--application-id-tekenreeks|Toepassings-id van door de gebruiker toegewezen identiteit. Vereist voor service principal auth.|
|--certificaat-padtekenreeks|Pad naar certificaat voor SPN-verificatie. Vereist voor certificaatgebaseerde serviceprincipal auth.|
|-h, --help|Help-inhoud weergeven voor de aanmeldingsopdracht.|
|--identiteit|inloggen met behulp van de identiteit van de virtuele machine, ook wel managed service identity (MSI) genoemd.|
|--identity-client-id tekenreeks|Client-id van door de gebruiker toegewezen identiteit.|
|tekenreeks --identiteit-object-id|Object-id van door de gebruiker toegewezen identiteit.|
|--identity-resource-id-tekenreeks|Resource-id van door de gebruiker toegewezen identiteit.|
|--service-principal|Log in via SPN (Service Principal Name) met behulp van een certificaat of een geheim. Het clientgeheim of certificaatwachtwoord moet in de juiste omgevingsvariabele worden geplaatst. Typ `AzCopy env` om namen en beschrijvingen van omgevingsvariabelen te bekijken.|
|--tenant-id-tekenreeks| de Azure active directory tenant ID die moet worden gebruikt voor interactieve aanmelding op OAuth-apparaten.|

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps uint32|Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.|
|--uitvoer-type tekenreeks|Indeling van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is "tekst".|

## <a name="see-also"></a>Zie ook

- [azcopie](storage-ref-azcopy.md)

---
title: Beveiligings handleiding Azure Storage Explorer | Microsoft Docs
description: Beveiligings richtlijnen voor Azure Storage Explorer
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: d9dea7cd0cc22cc8a1e0aa5c93ece76d689de0e0
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835439"
---
# <a name="azure-storage-explorer-security-guide"></a>Azure Storage Explorer-beveiligings handleiding

Met Microsoft Azure Storage Explorer kunt u gemakkelijk met Azure Storage gegevens veilig en veilig werken in Windows, macOS en Linux. Als u deze richt lijnen volgt, kunt u ervoor zorgen dat uw gegevens beveiligd blijven.

## <a name="general"></a>Algemeen

- **Gebruik altijd de nieuwste versie van Storage Explorer.** Storage Explorer releases kunnen beveiligings updates bevatten. Het up-to-date houden van de algemene veiligheid.
- **Maak alleen verbinding met bronnen die u vertrouwt.** Gegevens die u downloadt van niet-vertrouwde bronnen kunnen schadelijk zijn, en het uploaden van gegevens naar een niet-vertrouwde bron kan leiden tot verloren of gestolen gegevens.
- **Gebruik HTTPS wanneer dat mogelijk is.** Storage Explorer maakt standaard gebruik van HTTPS. In sommige scenario's kunt u HTTP gebruiken, maar HTTP moet alleen worden gebruikt als een laatste redmiddel.
- **Zorg ervoor dat alleen de benodigde machtigingen worden verleend aan de personen die deze nodig hebben.** Vermijd te ruimen wanneer u iedereen toegang verleent tot uw resources.
- **Wees voorzichtig bij het uitvoeren van kritieke bewerkingen.** Bepaalde bewerkingen, zoals verwijderen en overschrijven, zijn onomkeerbaar en kunnen leiden tot gegevens verlies. Zorg ervoor dat u met de juiste resources werkt voordat u deze bewerkingen uitvoert.

## <a name="choosing-the-right-authentication-method"></a>De juiste verificatie methode kiezen

Storage Explorer biedt verschillende manieren om toegang te krijgen tot uw Azure Storage resources. Welke methode u ook kiest, hier volgen onze aanbevelingen.

### <a name="azure-ad-authentication"></a>Azure Active Directory-verificatie

De eenvoudigste en veiligste manier om toegang te krijgen tot uw Azure Storage-resources is om u aan te melden met uw Azure-account. Aanmelden maakt gebruik van Azure AD-verificatie, waarmee u het volgende kunt doen:

- Geef toegang tot specifieke gebruikers en groepen.
- De toegang tot specifieke gebruikers en groepen op elk gewenst moment intrekken.
- Toegangs voorwaarden afdwingen, zoals het vereisen van multi-factor Authentication.

U wordt aangeraden Azure AD-verificatie waar mogelijk te gebruiken.

In deze sectie worden de twee op Azure AD gebaseerde technologieën beschreven die kunnen worden gebruikt voor het beveiligen van uw opslag resources.

#### <a name="role-based-access-control-rbac"></a>Toegangsbeheer op basis van rollen (RBAC)

[Met Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) beschikt u over een nauw keurig toegangs beheer voor uw Azure-resources. RBAC-rollen en-machtigingen kunnen worden beheerd vanuit de Azure Portal.

Storage Explorer ondersteunt RBAC-toegang tot opslag accounts, blobs en wacht rijen. Als u toegang tot bestands shares of tabellen nodig hebt, moet u RBAC-rollen toewijzen die machtigingen verlenen voor het weer geven van de sleutel van het opslag account.

#### <a name="access-control-lists-acls"></a>ACL’s (toegangsbeheerlijsten)

Met [toegangs beheer lijsten (acl's)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) kunt u toegang tot bestanden en mappen op het niveau van ADLS Gen2 BLOB-containers beheren. U kunt uw Acl's beheren met Storage Explorer.

### <a name="shared-access-signatures-sas"></a>Shared Access Signatures (SAS)

Als u geen gebruik kunt maken van Azure AD-verificatie, raden we u aan om gedeelde toegangs handtekeningen te gebruiken. Met Shared Access signatures kunt u het volgende doen:

- Anonieme beperkte toegang bieden om resources te beveiligen.
- Een SAS direct intrekken als deze wordt gegenereerd op basis van een Shared Access Policy (SAP).

Met Shared Access signatures kunt u echter het volgende niet doen:

- Beperk wie een SAS mag gebruiken. Een geldige SAS kan worden gebruikt door iedereen die het heeft.
- Een SAS intrekken als deze niet is gegenereerd op basis van een Shared Access Policy (SAP).

Bij het gebruik van SAS in Storage Explorer raden wij de volgende richt lijnen aan:

- **Beperk de distributie van SAS-tokens en Uri's.** Distribueer alleen SAS-tokens en Uri's naar vertrouwde personen. Het beperken van SAS-distributie vermindert de kans dat een SAS kan worden misbruikt.
- **Gebruik alleen SAS-tokens en Uri's van entiteiten die u vertrouwt.**
- **Gebruik SAP (Shared Access Policies) bij het genereren van SAS-tokens en Uri's, indien mogelijk.** Een SAS op basis van een gedeeld toegangs beleid is veiliger dan een bare SAS, omdat de SA'S kunnen worden ingetrokken door de SAP te verwijderen.
- **Tokens genereren met minimale toegang tot bronnen en machtigingen.** Minimale machtigingen beperken de mogelijke schade die kan worden uitgevoerd als een SAS wordt gebruikt.
- **Genereer tokens die alleen geldig zijn voor als dat nodig is.** Een korte levens duur is met name belang rijk voor bare SA'S, omdat het niet mogelijk is om deze in te trekken nadat deze is gegenereerd.

> [!IMPORTANT]
> Bij het delen van SAS-tokens en Uri's voor het oplossen van problemen, zoals in service aanvragen of fout rapporten, moet u altijd ten minste het hand tekening gedeelte van de SA'S redigeren.

### <a name="storage-account-keys"></a>Opslagaccountsleutels

Met sleutels voor het opslag account wordt onbeperkte toegang tot de services en bronnen in een opslag account verleend. Daarom raden we u aan het gebruik van sleutels te beperken voor toegang tot resources in Storage Explorer. Gebruik de RBAC-functies of SAS om in plaats daarvan toegang te bieden.

Sommige RBAC-rollen verlenen machtigingen voor het ophalen van sleutels voor opslag accounts. Personen met deze rollen kunnen de machtigingen die worden verleend of geweigerd door RBAC, omzeilen. We raden aan deze machtiging niet toe te kennen, tenzij dit nodig is.

Storage Explorer probeert de sleutel van het opslag account, indien beschikbaar, te gebruiken om aanvragen te verifiëren. U kunt deze functie uitschakelen in instellingen (**Services > opslag Accounts > gebruik van sleutels uitschakelen**). Sommige functies bieden geen ondersteuning voor RBAC, zoals werken met klassieke opslag accounts. Dergelijke functies vereisen nog steeds sleutels en worden niet beïnvloed door deze instelling.

Als u sleutels moet gebruiken voor toegang tot uw opslag resources, raden we de volgende richt lijnen aan:

- **Deel uw account sleutels niet met iedereen.**
- **Behandel de sleutels van uw opslag account zoals wacht woorden.** Als u uw sleutels toegankelijk moet maken, gebruikt u oplossingen voor beveiligde opslag, zoals [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

> [!NOTE]
> Als u denkt dat de sleutel van het opslag account is gedeeld of gedistribueerd per ongeluk, kunt u nieuwe sleutels voor uw opslag account genereren vanuit de Azure Portal.

### <a name="public-access-to-blob-containers"></a>Open bare toegang tot BLOB-containers

Met Storage Explorer kunt u het toegangs niveau van uw Azure Blob Storage-containers wijzigen. Niet-private BLOB-containers bieden anonieme lees toegang tot gegevens in deze containers.

Als u open bare toegang inschakelt voor een BLOB-container, raden we u aan de volgende richt lijnen te volgen:

- **Schakel open bare toegang niet in voor een BLOB-container die mogelijk gevoelige gegevens bevat.** Zorg ervoor dat uw BLOB-container geen persoonlijke gegevens bevat.
- **Upload mogelijk gevoelige gegevens niet naar een BLOB-container met toegang tot BLOB of container.** 

## <a name="next-steps"></a>Volgende stappen

- [Aanbevelingen voor beveiliging](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)

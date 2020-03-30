---
title: Benaderingen voor gebruikersmigratie
titleSuffix: Azure AD B2C
description: Migreer gebruikersaccounts van een andere identiteitsprovider naar Azure AD B2C met behulp van de bulkimport- of naadloze migratiemethoden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b3ee069985fd39288a562d3caafc50b12290c060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332336"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Gebruikers migreren naar Azure AD B2C

Voor het migreren van een andere identiteitsprovider naar Azure Active Directory B2C (Azure AD B2C) moet mogelijk ook bestaande gebruikersaccounts worden gemigreerd. Twee migratiemethoden worden hier besproken, *bulkimport* en *naadloze migratie.* Met beide benaderingen moet u een toepassing of script schrijven waarmee de [Microsoft Graph-API](manage-user-accounts-graph-api.md) wordt gebruikt om gebruikersaccounts te maken in Azure AD B2C.

## <a name="bulk-import"></a>Bulkimport

In de bulkimportstroom voert uw migratietoepassing de volgende stappen uit voor elk gebruikersaccount:

1. Lees het gebruikersaccount van de oude identiteitsprovider, inclusief de huidige referenties (gebruikersnaam en wachtwoord).
1. Maak een overeenkomstig account in uw Azure AD B2C-map met de huidige referenties.

Gebruik de bulkimportstroom in een van deze twee situaties:

- U hebt toegang tot de plaintext-referenties van een gebruiker (gebruikersnaam en wachtwoord).
- De referenties zijn versleuteld, maar u ze decoderen.

Zie [Azure AD B2C-gebruikersaccounts beheren met Microsoft Graph](manage-user-accounts-graph-api.md)voor informatie over het programmatisch maken van gebruikersaccounts.

## <a name="seamless-migration"></a>Naadloze migratie

Gebruik de naadloze migratiestroom als plaintext-wachtwoorden in de oude identiteitsprovider niet toegankelijk zijn. Bijvoorbeeld wanneer:

- Het wachtwoord wordt opgeslagen in een eenrichtingsversleutelde indeling, zoals met een hash-functie.
- Het wachtwoord wordt door de oude identiteitsprovider opgeslagen op een manier die u niet openen. Bijvoorbeeld wanneer de identiteitsprovider referenties valideert door een webservice te bellen.

De naadloze migratiestroom vereist nog steeds bulkmigratie van gebruikersaccounts, maar gebruikt vervolgens een [aangepast beleid](custom-policy-get-started.md) om een [REST-API](custom-policy-rest-api-intro.md) (die u maakt) op te vragen om het wachtwoord van elke gebruiker bij de eerste aanmelding in te stellen.

De naadloze migratiestroom kent dus twee fasen: *bulkimport* en *setreferenties.*

### <a name="phase-1-bulk-import"></a>Fase 1: Bulkimport

1. Uw migratietoepassing leest de gebruikersaccounts van de oude identiteitsprovider.
1. De migratietoepassing maakt overeenkomstige gebruikersaccounts in uw Azure AD B2C-map, maar *stelt geen wachtwoorden in.*

### <a name="phase-2-set-credentials"></a>Fase 2: Referenties instellen

Nadat bulkmigratie van de accounts is voltooid, voeren uw aangepaste beleid en REST API het volgende uit wanneer een gebruiker zich aanmeldt:

1. Lees het Azure AD B2C-gebruikersaccount dat overeenkomt met het ingevoerde e-mailadres.
1. Controleer of het account is gemarkeerd voor migratie door een booleaanse extensieattribuut te evalueren.
    - Als het extensiekenmerk terugkeert, `True`roept u uw REST-API aan om het wachtwoord te valideren tegen de verouderde identiteitsprovider.
      - Als de REST-API vaststelt dat het wachtwoord onjuist is, geeft u een vriendschappelijke fout terug aan de gebruiker.
      - Als de REST-API bepaalt dat het wachtwoord correct is, schrijft u het wachtwoord `False`naar het Azure AD B2C-account en wijzigt u het kenmerk booleaanse extensie in .
    - Als het kenmerk booleaanse extensie terugkeert, `False`gaat u het aanmeldingsproces gewoon voort.

Zie het voorbeeld van de naadloze [gebruikersmigratie op](https://aka.ms/b2c-account-seamless-migration) GitHub om een voorbeeld van aangepast beleid en REST-API te bekijken.

![Stroomdiagram van de naadloze migratiebenadering voor gebruikersmigratie](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagram: Naadloze migratiestroom*

## <a name="best-practices"></a>Aanbevolen procedures

### <a name="security"></a>Beveiliging

De naadloze migratiebenadering maakt gebruik van uw eigen aangepaste REST-API om de referenties van een gebruiker te valideren ten opzichte van de verouderde identiteitsprovider.

**U moet uw REST API beschermen tegen brute-force-aanvallen.** Een aanvaller kan meerdere wachtwoorden indienen in de hoop uiteindelijk de referenties van een gebruiker te raden. Om dergelijke aanvallen te helpen verslaan, stopt u met het serveren van aanvragen aan uw REST-API wanneer het aantal aanmeldingspogingen een bepaalde drempel overschrijdt. Beveilig ook de communicatie tussen Azure AD B2C en uw REST API. Zie [Secure RESTful API](secure-rest-api.md)voor meer informatie over het beveiligen van uw RESTful API's voor productie.

### <a name="user-attributes"></a>Gebruikerskenmerken

Niet alle informatie in de verouderde identiteitsprovider moet worden gemigreerd naar uw Azure AD B2C-map. Identificeer de juiste set gebruikerskenmerken die u moet opslaan in Azure AD B2C voordat u migreert.

- **DO-winkel** in Azure AD B2C
  - Gebruikersnaam, wachtwoord, e-mailadressen, telefoonnummers, lidmaatschapsnummers/id's.
  - Toestemmingsmarkeringen voor privacybeleid en licentieovereenkomsten voor eindgebruikers.
- **NIET opslaan** in Azure AD B2C
  - Gevoelige gegevens zoals creditcardnummers, burgerservicenummers (SSN), medische dossiers of andere gegevens die worden gereguleerd door instanties voor naleving door de overheid of de industrie.
  - Marketing- of communicatievoorkeuren, gebruikersgedrag en inzichten.

### <a name="directory-clean-up"></a>Opschonen van directory

Voordat u het migratieproces start, u van de gelegenheid gebruik maken om uw directory op te schonen.

- Identificeer de set gebruikerskenmerken die moeten worden opgeslagen in Azure AD B2C en migreer alleen wat u nodig hebt. Indien nodig u [aangepaste kenmerken](custom-policy-custom-attributes.md) maken om meer gegevens over een gebruiker op te slaan.
- Als u migreert vanuit een omgeving met meerdere verificatiebronnen (elke toepassing heeft bijvoorbeeld een eigen gebruikersmap), migreert u naar een uniform account in Azure AD B2C.
- Als meerdere toepassingen verschillende gebruikersnamen hebben, u ze allemaal opslaan in een Azure AD B2C-gebruikersaccount met behulp van de verzameling identiteiten. Met betrekking tot het wachtwoord, laat de gebruiker kiezen en zet het in de directory. Met de naadloze migratie moet bijvoorbeeld alleen het gekozen wachtwoord worden opgeslagen in het Azure AD B2C-account.
- Ongebruikte gebruikersaccounts verwijderen voordat u overstapt of migreer geen verouderde accounts.

### <a name="password-policy"></a>Wachtwoordbeleid

Als de accounts die u migreert een zwakkere wachtwoordsterkte hebben dan de [sterke wachtwoordsterkte](../active-directory/authentication/concept-sspr-policy.md) die wordt afgedwongen door Azure AD B2C, u de sterke wachtwoordvereiste uitschakelen. Zie De [eigenschap Wachtwoordbeleid](manage-user-accounts-graph-api.md#password-policy-property)voor meer informatie .

## <a name="next-steps"></a>Volgende stappen

De [azure-ad-b2c/user-migration](https://github.com/azure-ad-b2c/user-migration) repository op GitHub bevat een naadloos voorbeeld van het aangepaste migratiebeleid en een voorbeeld van restapi-code:

[Naadloos aangepast beleid voor gebruikersmigratie & VOORBEELD van REST API-code](https://aka.ms/b2c-account-seamless-migration)

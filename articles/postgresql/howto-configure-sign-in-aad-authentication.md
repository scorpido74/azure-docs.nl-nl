---
title: Azure Active Directory gebruiken - Azure Database voor PostgreSQL - Single Server
description: Meer informatie over het instellen van Azure Active Directory (AAD) voor verificatie met Azure Database voor PostgreSQL - Single Server
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f5588503825281f407ddbbc2c1c57cd94a9c7ee6
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804704"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Azure Active Directory gebruiken voor verificatie met PostgreSQL

In dit artikel vindt u de stappen doorlopen waarmee azure Active Directory-toegang met Azure Database voor PostgreSQL worden geconfigureerd en hoe u verbinding maken met een Azure AD-token.

> [!IMPORTANT]
> Azure AD-verificatie voor Azure Database voor PostgreSQL is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

## <a name="setting-the-azure-ad-admin-user"></a>De Azure AD-beheerder instellen

Alleen Azure AD-beheerders kunnen gebruikers maken/inschakelen voor Azure AD-gebaseerde verificatie. We raden u aan de Azure AD-beheerder niet te gebruiken voor regelmatige databasebewerkingen, omdat deze gebruikersmachtigingen heeft verhoogd (bijvoorbeeld CREATEDB).

Als u de Azure AD-beheerder wilt instellen (u een gebruiker of een groep gebruiken), voert u de volgende stappen uit

1. Selecteer in de Azure-portal de instantie van Azure Database voor PostgreSQL die u wilt inschakelen voor Azure AD.
2. Selecteer Active Directory-beheerder onder Instellingen:

![azure-advertentiebeheerder instellen][2]

3. Selecteer een geldige Azure AD-gebruiker in de klanttenant als Azure AD-beheerder.

> [!IMPORTANT]
> Bij het instellen van de beheerder wordt een nieuwe gebruiker toegevoegd aan de Azure Database voor PostgreSQL-server met volledige beheerdersmachtigingen. De Azure AD-beheergebruiker in Azure Database voor `azure_ad_admin`PostgreSQL heeft de rol .

Er kan slechts één Azure AD-beheerder worden gemaakt per PostgreSQL-server en de selectie van een andere is de bestaande Azure AD-beheerder die voor de server is geconfigureerd, overschreven. U een Azure AD-groep opgeven in plaats van dat een afzonderlijke gebruiker meerdere beheerders heeft. Houd er rekening mee dat u zich dan aanmeldt met de groepsnaam voor beheerdoeleinden.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Verbinding maken met Azure Database voor PostgreSQL met Azure AD

In het volgende diagram op hoog niveau wordt een overzicht gegeven van de werkstroom van het gebruik van Azure AD-verificatie met Azure Database voor PostgreSQL:

![verificatiestroom][1]

We hebben de Azure AD-integratie ontworpen om te werken met algemene PostgreSQL-hulpprogramma's zoals psql, die niet op Azure AD-bewust zijn en alleen ondersteuning bieden voor het opgeven van gebruikersnaam en wachtwoord wanneer u verbinding maakt met PostgreSQL. We geven het Azure AD-token door als het wachtwoord zoals weergegeven in de afbeelding hierboven.

We hebben momenteel de volgende klanten getest:

- psql-commandline (gebruik de variabele PGPASSWORD om het token te passeren, zie hieronder)
- Azure Data Studio (met de PostgreSQL-extensie)
- Andere libpq-gebaseerde clients (bijv. gemeenschappelijke toepassingskaders en ORM's)

> [!NOTE]
> Houd er rekening mee dat het gebruik van het Azure AD-token met pgAdmin momenteel niet wordt ondersteund, omdat het een hard-gecodeerde beperking van 256 tekens voor wachtwoorden heeft (die het token overschrijdt).

Dit zijn de stappen die een gebruiker/toepassing moet uitvoeren om te verifiëren met Azure AD hieronder beschreven:

### <a name="step-1-authenticate-with-azure-ad"></a>Stap 1: Verifiëren met Azure AD

Zorg ervoor dat de [Azure CLI is geïnstalleerd.](/cli/azure/install-azure-cli)

Roep het Azure CLI-hulpprogramma aan om te verifiëren met Azure AD. Het vereist dat u uw Azure AD-gebruikers-ID en het wachtwoord te geven.

```azurecli-interactive
az login
```

Met deze opdracht wordt een browservenster gestart naar de azure AD-verificatiepagina.

> [!NOTE]
> U Azure Cloud Shell ook gebruiken om deze stappen uit te voeren.
> Houd er rekening mee dat wanneer u Azure AD-toegangstoken ophaalt `az login` in de Azure Cloud Shell, u expliciet opnieuw moet bellen en inloggen (in het afzonderlijke venster met een code). Na dat teken `get-access-token` in de opdracht zal werken zoals verwacht.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Stap 2: Azure AD-toegangstoken ophalen

Roep het Azure CLI-hulpprogramma aan om een toegangstoken voor de azure AD-geverifieerde gebruiker vanaf stap 1 te verkrijgen om toegang te krijgen tot Azure Database voor PostgreSQL.

Voorbeeld (voor Public Cloud):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

De bovenstaande resourcewaarde moet precies worden opgegeven zoals aangegeven. Voor andere clouds kan de resourcewaarde worden opgezocht met:

```azurecli-interactive
az cloud show
```

Voor Azure CLI-versie 2.0.71 en hoger kan de opdracht worden opgegeven in de volgende handigere versie voor alle clouds:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

Nadat de verificatie is geslaagd, retourneert Azure AD een toegangstoken:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Het token is een Base 64-tekenreeks die alle informatie over de geverifieerde gebruiker codeert en die is gericht op de Azure Database voor PostgreSQL-service.

> [!NOTE]
> De geldigheid van het toegangstoken ligt tussen de 5 minuten en 60 minuten. We raden u aan het toegangstoken te krijgen vlak voordat u de aanmelding bij Azure Database voor PostgreSQL start.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Stap 3: Token gebruiken als wachtwoord voor inloggen met PostgreSQL

Wanneer u verbinding maakt, moet u het toegangstoken gebruiken als het PostgreSQL-gebruikerswachtwoord.

Wanneer u `psql` de opdrachtregelclient gebruikt, moet het `PGPASSWORD` toegangstoken worden doorgegeven via de `psql` omgevingsvariabele, omdat het toegangstoken de wachtwoordlengte overschrijdt die rechtstreeks kan worden geaccepteerd:

Windows-voorbeeld:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Linux/macOS Voorbeeld:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Nu u een verbinding met Azure Database voor PostgreSQL starten, zoals u normaal gesproken zou doen:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

U bent nu geverifieerd naar uw PostgreSQL-server met Azure AD-verificatie.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Azure AD-gebruikers maken in Azure Database voor PostgreSQL

Als u een Azure AD-gebruiker wilt toevoegen aan uw Azure Database for PostgreSQL-database, voert u de volgende stappen uit na het verbinden (zie latere sectie over verbinding maken):

1. Controleer eerst of de `<user>@yourtenant.onmicrosoft.com` Azure AD-gebruiker een geldige gebruiker is in de AZURE AD-tenant.
2. Meld u aan bij uw Azure Database voor PostgreSQL-instantie als de Azure AD-beheerder.
3. Rol `<user>@yourtenant.onmicrosoft.com` maken in Azure Database voor PostgreSQL.
4. Maak `<user>@yourtenant.onmicrosoft.com` een lid van de rol azure_ad_user. Dit mag alleen worden gegeven aan Azure AD-gebruikers.

**Voorbeeld:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Als u een gebruiker via Azure AD authenticeert, geeft deze de gebruiker geen machtigingen voor toegang tot objecten in de Azure Database voor PostgreSQL-database. U moet de gebruiker handmatig de vereiste machtigingen verlenen.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Azure AD-groepen maken in Azure Database voor PostgreSQL

Als u een Azure AD-groep wilt inschakelen voor toegang tot uw database, gebruikt u hetzelfde mechanisme als voor gebruikers, maar geeft u in plaats daarvan de groepsnaam op:

**Voorbeeld:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Bij het inloggen gebruiken leden van de groep hun persoonlijke toegangstokens, maar ondertekenen ze met de groepsnaam die is opgegeven als gebruikersnaam.

## <a name="token-validation"></a>Tokenvalidatie

Azure AD-verificatie in Azure Database voor PostgreSQL zorgt ervoor dat de gebruiker bestaat in de PostgreSQL-server en controleert de geldigheid van het token door de inhoud van het token te valideren. De volgende tokenvalidatiestappen worden uitgevoerd:

- Token is ondertekend door Azure AD en is niet geknoeid met
- Token is uitgegeven door Azure AD voor de tenant die is gekoppeld aan de server
- Token is niet verlopen
- Token is voor de Azure Database for PostgreSQL-bron (en niet voor een andere Azure-bron)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Bestaande PostgreSQL-gebruikers migreren naar Azure AD-gebaseerde verificatie

U Azure AD-verificatie inschakelen voor bestaande gebruikers. Er zijn twee gevallen te overwegen:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Case 1: Gebruikersnaam Van PostgreSQL komt overeen met de Azure AD-gebruikersnaam

In het onwaarschijnlijke geval dat uw bestaande gebruikers al overeenkomen `azure_ad_user` met de azure AD-gebruikersnamen, u de rol aan hen verlenen om ze in te schakelen voor Azure AD-verificatie:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Ze kunnen zich nu aanmelden met Azure AD-referenties in plaats van hun eerder geconfigureerde PostgreSQL-gebruikerswachtwoord te gebruiken.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Case 2: De gebruikersnaam van PostgreSQL is anders dan de Azure AD-gebruikersnaam

Als een PostgreSQL-gebruiker niet bestaat in Azure AD of een andere gebruikersnaam heeft, u Azure AD-groepen gebruiken om te verifiëren als deze PostgreSQL-gebruiker. U bestaande Azure Database voor PostgreSQL-gebruikers migreren naar Azure AD door een Azure AD-groep te maken met een naam die overeenkomt met de PostgreSQL-gebruiker en vervolgens rolazure_ad_user toe te kennen aan de bestaande PostgreSQL-gebruiker:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Hier in gaat u ervan uit dat u een groep 'DBReadUser' hebt gemaakt in uw Azure AD. Gebruikers die tot die groep behoren, kunnen zich nu als gebruiker aanmelden bij de database.

## <a name="next-steps"></a>Volgende stappen

* De algemene concepten voor [Azure Active Directory-verificatie met Azure Database voor PostgreSQL - Enkele server controleren](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png

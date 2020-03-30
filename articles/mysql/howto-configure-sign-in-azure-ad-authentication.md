---
title: Azure Active Directory gebruiken - Azure Database voor MySQL
description: Meer informatie over het instellen van Azure Active Directory (Azure AD) voor verificatie met Azure Database voor MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0403edadd491609c2c88d5b5ac6980d97163f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299002"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Azure Active Directory gebruiken om te authenticeren met MySQL

In dit artikel vindt u de stappen doorlopen waarmee azure Active Directory-toegang met Azure Database voor MySQL worden geconfigureerd en hoe u verbinding maken met een Azure AD-token.

> [!IMPORTANT]
> Azure AD-verificatie voor Azure Database voor MySQL is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

## <a name="setting-the-azure-ad-admin-user"></a>De Azure AD-beheerder instellen

Alleen een Azure AD-beheerder kan gebruikers maken/inschakelen voor Azure AD-gebaseerde verificatie. Als u de gebruiker van de AD-beheerder wilt maken en Azure wilt gebruiken, voert u de volgende stappen uit

1. Selecteer in de Azure-portal de instantie van Azure Database voor MySQL die u wilt inschakelen voor Azure AD.
2. Selecteer Active Directory-beheerder onder Instellingen:

![azure-advertentiebeheerder instellen][2]

3. Selecteer een geldige Azure AD-gebruiker in de klanttenant als Azure AD-beheerder.

> [!IMPORTANT]
> Bij het instellen van de beheerder wordt een nieuwe gebruiker toegevoegd aan de Azure Database voor MySQL-server met volledige beheerdersmachtigingen.

Er kan slechts één Azure AD-beheerder per MySQL-server worden gemaakt en de selectie van een andere is de bestaande Azure AD-beheerder die voor de server is geconfigureerd, overschreven.

In een toekomstige release ondersteunen we het opgeven van een Azure AD-groep in plaats van een individuele gebruiker om meerdere beheerders te hebben, maar dit wordt momenteel nog niet ondersteund.

Nadat u de beheerder hebt geconfigureerd, u zich nu aanmelden:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Verbinding maken met Azure Database voor MySQL met Azure AD

In het volgende diagram op hoog niveau wordt de werkstroom van het gebruik van Azure AD-verificatie met Azure Database voor MySQL samengevat:

![verificatiestroom][1]

We hebben de Azure AD-integratie ontworpen om te werken met algemene MySQL-hulpprogramma's zoals de mysql CLI, die niet op Azure AD-bewust zijn en alleen ondersteuning bieden voor het opgeven van gebruikersnaam en wachtwoord bij het maken van verbinding met MySQL. We geven het Azure AD-token door als het wachtwoord zoals weergegeven in de afbeelding hierboven.

We hebben momenteel de volgende klanten getest:

- MySQLWorkbench 
- Mysql CLI

We hebben ook getest meest voorkomende toepassing drivers, u details te zien aan het einde van deze pagina.

Dit zijn de stappen die een gebruiker/toepassing moet uitvoeren om te verifiëren met Azure AD hieronder beschreven:

### <a name="step-1-authenticate-with-azure-ad"></a>Stap 1: Verifiëren met Azure AD

Zorg ervoor dat de [Azure CLI is geïnstalleerd.](/cli/azure/install-azure-cli)

Roep het Azure CLI-hulpprogramma aan om te verifiëren met Azure AD. Het vereist dat u uw Azure AD-gebruikers-ID en het wachtwoord te geven.

```
az login
```

Met deze opdracht wordt een browservenster gestart naar de azure AD-verificatiepagina.

> [!NOTE]
> U Azure Cloud Shell ook gebruiken om deze stappen uit te voeren.
> Houd er rekening mee dat wanneer u Azure AD-toegangstoken ophaalt `az login` in de Azure Cloud Shell, u expliciet opnieuw moet bellen en inloggen (in het afzonderlijke venster met een code). Na dat teken `get-access-token` in de opdracht zal werken zoals verwacht.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Stap 2: Azure AD-toegangstoken ophalen

Roep het Azure CLI-hulpprogramma aan om een toegangstoken voor de azure AD-geverifieerde gebruiker vanaf stap 1 te verkrijgen om toegang te krijgen tot Azure Database voor MySQL.

Voorbeeld (voor Public Cloud):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

De bovenstaande resourcewaarde moet precies worden opgegeven zoals aangegeven. Voor andere clouds kan de resourcewaarde worden opgezocht met:

```shell
az cloud show
```

Voor Azure CLI-versie 2.0.71 en hoger kan de opdracht worden opgegeven in de volgende handigere versie voor alle clouds:

```shell
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

Het token is een Base 64-tekenreeks die alle informatie over de geverifieerde gebruiker codeert en die is gericht op de Azure Database voor MySQL-service.

> [!NOTE]
> De geldigheid van het toegangstoken ligt tussen de 5 minuten en 60 minuten. We raden u aan het toegangstoken te krijgen vlak voordat u de aanmelding bij Azure Database voor MySQL start.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Stap 3: Token gebruiken als wachtwoord voor inloggen met MySQL

Wanneer u verbinding maakt, moet u het toegangstoken gebruiken als het MySQL-gebruikerswachtwoord. Wanneer u GUI-clients zoals MySQLWorkbench gebruikt, u de bovenstaande methode gebruiken om het token op te halen. 

Bij het gebruik van de CLI u deze korte hand gebruiken om verbinding te maken: 

**Voorbeeld (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Let op de instelling 'cleartext-plugin' - u moet een vergelijkbare configuratie gebruiken met andere clients om ervoor te zorgen dat het token naar de server wordt verzonden zonder dat het wordt gehasht.

U bent nu geverifieerd naar uw MySQL-server met Azure AD-verificatie.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Azure AD-gebruikers maken in Azure Database voor MySQL

Als u een Azure AD-gebruiker wilt toevoegen aan uw Azure Database for MySQL-database, voert u de volgende stappen uit na het maken van verbinding (zie latere sectie over verbinding maken):

1. Controleer eerst of de `<user>@yourtenant.onmicrosoft.com` Azure AD-gebruiker een geldige gebruiker is in de AZURE AD-tenant.
2. Meld u aan bij uw Azure Database voor MySQL-exemplaar als de Azure AD-beheerder.
3. Maak `<user>@yourtenant.onmicrosoft.com` de gebruiker in Azure Database voor MySQL.

**Voorbeeld:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Voor gebruikersnamen die meer dan 32 tekens bevatten, wordt aanbevolen om in plaats daarvan een alias te gebruiken die moet worden gebruikt bij het verbinden: 

Voorbeeld:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Als u een gebruiker via Azure AD authenticeert, geeft deze de gebruiker geen machtigingen voor toegang tot objecten in de Azure Database voor MySQL-database. U moet de gebruiker handmatig de vereiste machtigingen verlenen.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Azure AD-groepen maken in Azure Database voor MySQL

Als u een Azure AD-groep wilt inschakelen voor toegang tot uw database, gebruikt u hetzelfde mechanisme als voor gebruikers, maar geeft u in plaats daarvan de groepsnaam op:

**Voorbeeld:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Bij het inloggen gebruiken leden van de groep hun persoonlijke toegangstokens, maar ondertekenen ze met de groepsnaam die is opgegeven als gebruikersnaam.

## <a name="token-validation"></a>Tokenvalidatie

Azure AD-verificatie in Azure Database voor MySQL zorgt ervoor dat de gebruiker in de MySQL-server bestaat en controleert de geldigheid van het token door de inhoud van het token te valideren. De volgende tokenvalidatiestappen worden uitgevoerd:

-   Token is ondertekend door Azure AD en is niet geknoeid met
-   Token is uitgegeven door Azure AD voor de tenant die is gekoppeld aan de server
-   Token is niet verlopen
-   Token is voor de Azure Database for MySQL-bron (en niet voor een andere Azure-bron)

## <a name="compatibility-with-application-drivers"></a>Compatibiliteit met toepassingsstuurprogramma's

De meeste stuurprogramma's worden ondersteund, maar zorg ervoor dat de instellingen voor het verzenden van het wachtwoord in duidelijke tekst te gebruiken, zodat het token wordt verzonden zonder wijziging.

* C/C++
  * libmysqlclient: ondersteund
  * mysql-connector-c++: ondersteund
* Java
  * Connector/J (mysql-connector-java): Ondersteund, `useSSL` moet instelling gebruiken
* Python
  * Connector/Python: ondersteund
* Ruby
  * mysql2: Ondersteund
* .NET
  * mysql-connector-net: Ondersteund, moeten plugin toevoegen voor mysql_clear_password
  * mysql-net/MySqlConnector: ondersteund
* Node.js
  * mysqljs: Niet ondersteund (verzendt geen token in cleartext zonder patch)
  * node-mysql2: ondersteund
* Perl
  * DBD::mysql: Ondersteund
  * Net::MySQL: Niet ondersteund
* Aan de slag
  * go-sql-driver: ondersteund, `?tls=true&allowCleartextPasswords=true` toevoegen aan verbindingstekenreeks

## <a name="next-steps"></a>Volgende stappen

* De algemene concepten voor [Azure Active Directory-verificatie met Azure Database voor MySQL controleren](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png

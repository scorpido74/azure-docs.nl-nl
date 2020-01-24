---
title: Azure Active Directory-Azure Database for MySQL-één server gebruiken
description: Meer informatie over het instellen van Azure Active Directory (Azure AD) voor verificatie met Azure Database for MySQL-één server
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 10dae81bf0ca8958f7c10aebef501fc604c4839c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76706048"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Azure Active Directory gebruiken voor verificatie met MySQL

Dit artikel begeleidt u stapsgewijs door de stappen voor het configureren van Azure Active Directory toegang met Azure Database for MySQL en hoe u verbinding maakt met behulp van een Azure AD-token.

> [!IMPORTANT]
> Azure AD-verificatie voor Azure Database for MySQL is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="setting-the-azure-ad-admin-user"></a>De gebruiker van de Azure AD-beheerder instellen

Alleen een gebruiker van Azure AD-beheerder kan gebruikers maken/inschakelen voor verificatie op basis van Azure AD. Voer de volgende stappen uit om een gebruiker met Azure AD-beheerder te maken:

1. Selecteer in de Azure Portal het exemplaar van Azure Database for MySQL dat u wilt inschakelen voor Azure AD.
2. Selecteer onder instellingen de optie Active Directory beheerder:

![Azure AD-beheerder instellen][2]

3. Selecteer een geldige Azure AD-gebruiker in de Tenant van de klant als Azure AD-beheerder.

> [!IMPORTANT]
> Bij het instellen van de beheerder wordt een nieuwe gebruiker toegevoegd aan de Azure Database for MySQL-server met volledige beheerders machtigingen.

Er kan slechts één Azure AD-beheerder per MySQL-server worden gemaakt en de bestaande Azure AD-beheerder die voor de server is geconfigureerd, wordt overschreven door de selectie van een andere.

In een toekomstige versie bieden we ondersteuning voor het opgeven van een Azure AD-groep in plaats van een afzonderlijke gebruiker om meerdere beheerders te hebben, maar dit wordt momenteel nog niet ondersteund.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Azure AD-gebruikers maken in Azure Database for MySQL

Als u een Azure AD-gebruiker wilt toevoegen aan uw Azure Database for MySQL-data base, voert u de volgende stappen uit nadat u verbinding hebt gemaakt (zie verderop in dit onderwerp voor verbinding maken):

1. Controleer eerst of de Azure AD-gebruikers `<user>@yourtenant.onmicrosoft.com` een geldige gebruiker is in de Azure AD-Tenant.
2. Meld u aan bij uw Azure Database for MySQL-exemplaar als de gebruiker van de Azure AD-beheerder.
3. Maak gebruikers `<user>@yourtenant.onmicrosoft.com` in Azure Database for MySQL.

**Voorbeeld:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Voor gebruikers namen die 32 tekens overschrijden, is het raadzaam om in plaats daarvan een alias te gebruiken voor gebruik bij het maken van verbinding: 

Voorbeeld:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Het verifiëren van een gebruiker via Azure AD geeft de gebruiker geen machtigingen voor toegang tot objecten binnen de Azure Database for MySQL data base. U moet de gebruiker hand matig de vereiste machtigingen verlenen.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Azure AD-groepen maken in Azure Database for MySQL

Als u een Azure AD-groep wilt inschakelen voor toegang tot uw data base, gebruikt u hetzelfde mechanisme als voor gebruikers, maar geeft u in plaats daarvan de groeps naam op:

**Voorbeeld:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Wanneer u zich aanmeldt, gebruiken leden van de groep hun persoonlijke toegangs tokens, maar u moet zich aanmelden met de groeps naam die is opgegeven als de gebruikers naam.

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Verbinding maken met Azure Database for MySQL met behulp van Azure AD

In het volgende diagram op hoog niveau wordt een overzicht gegeven van de werk stroom van het gebruik van Azure AD-verificatie met Azure Database for MySQL:

![verificatie stroom][1]

We hebben de integratie van Azure AD ontworpen om te werken met algemene MySQL-hulpprogram ma's zoals de MySQL CLI, die niet bekend zijn met Azure AD en die alleen ondersteuning bieden voor het opgeven van de gebruikers naam en het wacht woord wanneer er verbinding wordt gemaakt met MySQL. De Azure AD-token wordt als wacht woord door gegeven, zoals in de bovenstaande afbeelding.

Momenteel zijn de volgende clients getest:

- MySQLWorkbench 
- MySQL CLI

We hebben ook de meest algemene toepassings Stuur Programma's getest. u kunt meer informatie bekijken aan het einde van deze pagina.

Dit zijn de stappen die een gebruiker/toepassing nodig heeft om zich te verifiëren met Azure AD zoals hieronder wordt beschreven:

### <a name="step-1-authenticate-with-azure-ad"></a>Stap 1: verifiëren met Azure AD

Zorg ervoor dat de [Azure cli is geïnstalleerd](/cli/azure/install-azure-cli).

Roep het Azure CLI-hulp programma aan om te verifiëren met Azure AD. Hiervoor moet u uw gebruikers-ID en wacht woord voor Azure AD opgeven.

```
az login
```

Met deze opdracht wordt een browser venster geopend op de Azure AD-verificatie pagina.

> [!NOTE]
> U kunt Azure Cloud Shell ook gebruiken om deze stappen uit te voeren.
> Houd er rekening mee dat bij het ophalen van een Azure AD-toegangs token in de Azure Cloud Shell u `az login` expliciet aanroept en weer aanmeldt (in het afzonderlijke venster met een code). Nadat het aanmelden in de `get-access-token` opdracht werkt zoals verwacht.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Stap 2: het Azure AD-toegangs Token ophalen

Roep het Azure CLI-hulp programma aan om een toegangs token te verkrijgen voor de door Azure AD geverifieerde gebruiker uit stap 1 om toegang te krijgen tot Azure Database for MySQL.

Voor beeld (voor open bare Cloud):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

De bovenstaande resource waarde moet exact worden opgegeven zoals wordt weer gegeven. Voor andere Clouds kan de resource waarde worden opgezocht met:

```shell
az cloud show
```

Voor Azure CLI versie 2.0.71 en hoger kan de opdracht worden opgegeven in de volgende, handigere versie voor alle Clouds:

```shell
az account get-access-token --resource-type oss-rdbms
```

Nadat de verificatie is geslaagd, wordt een toegangs token door Azure AD geretourneerd:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Het token is een basis teken reeks van 64 waarmee alle informatie over de geverifieerde gebruiker wordt gecodeerd en die is gericht op de Azure Database for MySQL-service.

> [!NOTE]
> De geldigheid van het toegangs token is een wille keurige periode van 5 minuten tot 60 minuten. We raden u aan het toegangs token op te halen voordat u de aanmelding initieert naar Azure Database for MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Stap 3: token als wacht woord gebruiken voor aanmelding met MySQL

Wanneer u verbinding maakt, moet u het toegangs token gebruiken als het MySQL-gebruikers wachtwoord. Wanneer u GUI-clients gebruikt, zoals MySQLWorkbench, kunt u de bovenstaande methode gebruiken om het token op te halen. 

Wanneer u de CLI gebruikt, kunt u deze korte hand gebruiken om verbinding te maken: 

**Voor beeld (Linux/macOS):**

MySQL-h mydb.mysql.database.azure.com \--User user@tenant.onmicrosoft.com@mydb \--Enable-outlees bare-plugin \--Password =`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`  

Let op de instelling ' Enable-anti-tekst-plugin ': u moet een vergelijk bare configuratie met andere clients gebruiken om ervoor te zorgen dat het token wordt verzonden naar de server zonder hash-bewerking.

U bent nu geverifieerd voor uw MySQL-server met behulp van Azure AD-verificatie.

## <a name="token-validation"></a>Token validatie

Met Azure AD-verificatie in Azure Database for MySQL wordt gegarandeerd dat de gebruiker bestaat op de MySQL-server en wordt de geldigheid van het token gecontroleerd door de inhoud van het token te valideren. De volgende stappen voor het valideren van tokens worden uitgevoerd:

-   Het token is ondertekend door Azure AD en is niet geknoeid
-   Het token is uitgegeven door Azure AD voor de Tenant die aan de server is gekoppeld
-   Het token is niet verlopen
-   Het token is voor de Azure Database for MySQL resource (en niet een andere Azure-resource)

## <a name="compatibility-with-application-drivers"></a>Compatibiliteit met toepassings Stuur Programma's

De meeste Stuur Programma's worden ondersteund, maar u moet de instellingen voor het verzenden van het wacht woord in ongecodeerde tekst gebruiken, zodat het token zonder aanpassing wordt verzonden.

* C/C++
  * libmysqlclient: ondersteund
  * mysql-connector-c + +: ondersteund
* Java
  * Connector/J (mysql-connector-java): ondersteund, moeten `useSSL` instelling gebruiken
* Python
  * Connector/python: ondersteund
* Ruby
  * mysql2: ondersteund
* .NET
  * mysql-connector-net: ondersteund, moet invoeg toepassing voor mysql_clear_password toevoegen
  * MySQL-net-MySqlConnector: ondersteund
* Node.js
  * mysqljs: niet ondersteund (verzendt geen token in ongecodeerde tekst zonder patch)
  * node-mysql2: ondersteund
* Perl
  * DBD:: mysql: ondersteund
  * Net:: MySQL: niet ondersteund
* Go
  * Go-SQL-stuur programma: ondersteund, `?tls=true&allowCleartextPasswords=true` toevoegen aan connection string

## <a name="next-steps"></a>Volgende stappen

* Bekijk de algemene concepten voor [Azure Active Directory verificatie met Azure database for MySQL-enkele server](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png

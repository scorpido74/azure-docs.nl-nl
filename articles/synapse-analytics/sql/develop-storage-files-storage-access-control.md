---
title: Toegang tot opslagaccount beheren voor SQL on-demand (voorbeeld)
description: Beschrijft hoe SQL on-demand (preview) toegang krijgt tot Azure Storage en hoe u opslagtoegang voor SQL on-demand beheren in Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424032"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Beheer de toegang tot opslagaccount voor SQL on-demand (preview) in Azure Synapse Analytics

Een SQL on-demand (preview)-query leest bestanden rechtstreeks uit Azure Storage. Aangezien het opslagaccount een object is dat buiten de SQL on-demand bron valt, zijn de juiste referenties vereist. Een gebruiker heeft de toepasselijke machtigingen nodig die zijn verleend om de vereiste referentie te gebruiken. In dit artikel worden de typen referenties beschreven die u gebruiken en hoe referenties worden opgezocht voor SQL- en Azure AD-gebruikers.

## <a name="supported-storage-authorization-types"></a>Ondersteunde opslagautorisatietypen

Een gebruiker die is aangemeld bij een SQL on-demand bron moet gemachtigd zijn om toegang te krijgen tot en query de bestanden in Azure Storage. Er worden drie autorisatietypen ondersteund:

- [Handtekening voor gedeelde toegang](#shared-access-signature)
- [Beheerde identiteit](#managed-identity)
- [Gebruikersidentiteit](#user-identity)

> [!NOTE]
> [Azure AD-doorvoer](#force-azure-ad-pass-through) is het standaardgedrag wanneer u een werkruimte maakt. Als u het gebruikt, hoeft u geen referenties te maken voor elk opslagaccount dat wordt geopend met AD-aanmeldingen. U [dit gedrag uitschakelen.](#disable-forcing-azure-ad-pass-through)

In de onderstaande tabel vindt u de verschillende autorisatietypen die worden ondersteund of binnenkort worden ondersteund.

| Autorisatietype                    | *SQL-gebruiker*    | *Azure AD-gebruiker*     |
| ------------------------------------- | ------------- | -----------    |
| [Sas](#shared-access-signature)       | Ondersteund     | Ondersteund      |
| [Beheerde identiteit](#managed-identity) | Niet ondersteund | Niet ondersteund  |
| [Gebruikersidentiteit](#user-identity)       | Niet ondersteund | Ondersteund      |

### <a name="shared-access-signature"></a>Handtekening voor gedeelde toegang

**Shared access signature (SAS)** biedt gedelegeerde toegang tot bronnen in een opslagaccount. Met SAS kan een klant klanten toegang verlenen tot bronnen in een opslagaccount zonder accountsleutels te delen. SAS geeft u gedetailleerde controle over het type toegang dat u verleent aan clients die een SAS hebben, inclusief geldigheidsinterval, verleende machtigingen, acceptabel IP-adresbereik en het aanvaardbare protocol (https/http).

U een SAS-token krijgen door te navigeren naar de **Azure-portal -> Storage Account -> Handtekening voor gedeelde toegang -> Machtigingen configureren > SAS en verbindingstekenreeks genereren.**

> [!IMPORTANT]
> Wanneer een SAS-token wordt gegenereerd, bevat het een vraagteken ('?') aan het begin van het token. Als u het token wilt gebruiken in SQL on-demand, moet u het vraagteken ('?') verwijderen bij het maken van een referentie. Bijvoorbeeld:
>
> SAS-token: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLFDSSMANd0ef9BrIPBNJ3VYEIq78%3D

### <a name="user-identity"></a>Gebruikersidentiteit

**Gebruikersidentiteit**, ook wel 'doorgeefbewerking' genoemd, is een autorisatietype waarbij de identiteit van de Azure AD-gebruiker die is aangemeld bij SQL on-demand wordt gebruikt om gegevenstoegang te autoriseren. Voordat u toegang krijgt tot de gegevens, moet de Azure Storage-beheerder machtigingen verlenen aan de Azure AD-gebruiker. Zoals aangegeven in de bovenstaande tabel, wordt deze niet ondersteund voor het SQL-gebruikerstype.

> [!NOTE]
> Als u [Azure AD-doorvoer gebruikt,](#force-azure-ad-pass-through) hoeft u geen referenties te maken voor elk opslagaccount dat wordt geopend met AD-aanmeldingen.

> [!IMPORTANT]
> U moet een rol voor opslagblobgegevens/inzender/lezer hebben om uw identiteit te gebruiken om toegang te krijgen tot de gegevens.
> Zelfs als u eigenaar bent van een opslagaccount, moet u uzelf nog steeds toevoegen aan een van de rollen Opslagblobgegevens.
>
> Voor meer informatie over toegangsbeheer in Azure Data Lake Store Gen2 controleert u het artikel [Access-besturingselement in Azure Data Lake Storage Gen2.](../../storage/blobs/data-lake-storage-access-control.md)
>

### <a name="managed-identity"></a>Beheerde identiteit

**Managed Identity** wordt ook wel MSI genoemd. Het is een functie van Azure Active Directory (Azure AD) die Azure-services biedt voor SQL on-demand. Ook implementeert het een automatisch beheerde identiteit in Azure AD. Deze identiteit kan worden gebruikt om het verzoek om gegevenstoegang in Azure Storage te autoriseren.

Voordat u toegang krijgt tot de gegevens, moet de Azure Storage-beheerder machtigingen verlenen aan Beheerde identiteit voor toegang tot de gegevens. Het verlenen van machtigingen voor Managed Identity gebeurt op dezelfde manier als het verlenen van toestemming aan elke andere Azure AD-gebruiker.

## <a name="create-credentials"></a>Referenties maken

Als u een bestand wilt opvragen dat zich in Azure Storage bevindt, heeft uw SQL on-demand eindpunt een referentie op serverniveau nodig die de verificatiegegevens bevat. Er wordt een referentie toegevoegd door [REFERENTIE MAKEN](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)uit te voeren . U moet een argument referentienaam opgeven. Het moet overeenkomen met een deel van het pad of het hele pad met gegevens in Opslag (zie hieronder).

> [!NOTE]
> Het argument FOR CRYPTOGRAPHIC PROVIDER wordt niet ondersteund.

Voor alle ondersteunde autorisatietypen kunnen referenties wijzen op een account, een container, een map (niet-root) of één bestand.

DE NAAM VAN DE REFERENTIE moet het volledige pad overeenkomen met de container, map of het bestand in de volgende indeling:`<prefix>://<storage_account_path>/<storage_path>`

| Externe gegevensbron       | Voorvoegsel | Pad met opslagaccount                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 '<storage_path>' is een pad binnen uw opslag dat verwijst naar de map of het bestand dat u wilt lezen.

> [!NOTE]
> Er is een `UserIdentity` speciale referentienaam die [Azure AD-doorlaat afdwingt.](#force-azure-ad-pass-through) Lees meer over het effect dat het heeft op [het opzoeken](#credential-lookup) van referenties tijdens het uitvoeren van query's.

Optioneel kan de beheerder, om een gebruiker toe te staan een referentie te maken of te laten vallen, een toestemming voor het wijzigen van referenties aan een gebruiker verlenen/WEIGEREN:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Ondersteunde opslag- en autorisatietypen

U de volgende combinaties van autorisatie- en Azure-opslagtypen gebruiken:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *Sas*               | Ondersteund      | Wordt niet ondersteund   | Ondersteund     |
| *Beheerde identiteit* | Niet ondersteund  | Niet ondersteund    | Niet ondersteund |
| *Gebruikersidentiteit*    | Ondersteund      | Ondersteund        | Ondersteund     |

### <a name="examples"></a>Voorbeelden

Afhankelijk van het [autorisatietype](#supported-storage-authorization-types)u referenties maken met de onderstaande Syntaxis T-SQL.

**Handtekening voor gedeelde toegang en Blob-opslag**

Exchange <*mystorageaccountnaam*> met uw werkelijke opslagaccountnaam en <*mijn opslagaccountcontainernaam*> met de werkelijke containernaam:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Gebruikersidentiteit en Azure Data Lake Storage Gen1**

Exchange <*mystorageaccountnaam*> met uw werkelijke opslagaccountnaam en <*mijn opslagaccountcontainernaam*> met de werkelijke containernaam:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Gebruikersidentiteit en Azure Data Lake Storage Gen2**

Exchange <*mystorageaccountnaam*> met uw werkelijke opslagaccountnaam en <*mijn opslagaccountcontainernaam*> met de werkelijke containernaam:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Azure AD-doorvoer forceren

Het forceren van een Azure AD-doorvoer is een `UserIdentity`standaardgedrag dat wordt bereikt door een speciale referentienaam, die automatisch wordt gemaakt tijdens de inrichting van Azure Synapse-werkruimte. Het dwingt het gebruik van een Azure AD-doorvoer voor elke query van elke Azure AD-aanmelding, die optreedt ondanks het bestaan van andere referenties.

> [!NOTE]
> Azure AD-doorgeeffunctie is een standaardgedrag. U hoeft geen referenties te maken voor elk opslagaccount dat wordt geopend door AD-aanmeldingen.

Als u [Azure AD-doorvoer voor elke query](#disable-forcing-azure-ad-pass-through)hebt uitgeschakeld en deze opnieuw wilt inschakelen, voert u het selectievakje uit:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Als u een Azure AD-doorgeefing voor een specifieke gebruiker `UserIdentity` wilt inschakelen, u referentie-machtigingen voor referenties verlenen aan die specifieke gebruiker. In het volgende voorbeeld u een Azure AD-doorgeeftermijn forceren voor een user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Zie [credential lookup](#credential-lookup)voor meer informatie over hoe SQL on-demand referenties vindt om te gebruiken.

## <a name="disable-forcing-azure-ad-pass-through"></a>Azure AD-doorvoer forceren uitschakelen

U [het forceren van Azure AD-doorvoer voor elke query](#force-azure-ad-pass-through)uitschakelen. Als u deze `Userdentity` wilt uitschakelen, laat u de referentie vallen met:

```sql
DROP CREDENTIAL [UserIdentity];
```

Als u de inschakeling opnieuw wilt inschakelen, raadpleegt u de sectie [Force Azure AD-doorvoer.](#force-azure-ad-pass-through)

Als u het forceren van Azure AD-doorvoer voor een `UserIdentity` specifieke gebruiker wilt uitschakelen, u een referentie-machtiging voor referenties voor een bepaalde gebruiker weigeren. In het volgende voorbeeld wordt een afdwinging van Azure AD-doorreis voor een user_name:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Zie [credential lookup](#credential-lookup)voor meer informatie over hoe SQL on-demand referenties vindt die moeten worden gebruikt.

## <a name="grant-permissions-to-use-credential"></a>Machtigingen verlenen om referenties te gebruiken

Als u de referentie wilt gebruiken, moet een gebruiker referentiestoestemming hebben voor een specifieke referentie. Voer het als u een referenties-machtiging op een storage_credential voor een specific_user uit om een verwijzingsmachtiging te verlenen voor een specific_user:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Om een vloeiende Azure AD-doorgeefervaring te garanderen, hebben alle gebruikers `UserIdentity` standaard het recht om de referentie te gebruiken. Dit wordt bereikt door een automatische uitvoering van de volgende instructie op Azure Synapse-werkruimtevoorziening:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Opzoeking van referenties

Bij het toestaan van query's wordt het opzoeken van referenties gebruikt om toegang te krijgen tot een opslagaccount en is deze gebaseerd op de volgende regels:

- Gebruiker is aangemeld als een Azure AD-aanmelding

  - Als er een UserIdentity-referentie bestaat en de gebruiker er referentiemachtigingen op heeft, wordt Azure AD-doorvoer gebruikt, anders [wordt opzoekinreferentie per pad](#lookup-credential-by-path)

- Gebruiker is ingelogd als een SQL-login

  - [Opzoekreferenties per pad gebruiken](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>Opzoekreferenties per pad

Als het forceren van Azure AD-doorvoer is uitgeschakeld, wordt het opzoeken van referenties gebaseerd op het opslagpad (eerst diepte) en het bestaan van een referenties-machtiging voor die specifieke referentie. Wanneer er meerdere referenties zijn die kunnen worden gebruikt om toegang te krijgen tot hetzelfde bestand, gebruikt SQL on-demand de meest specifieke.  

Hieronder vindt u een voorbeeld van een query over het volgende bestandspad: *account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext*

Het opzoeken van referenties wordt in deze volgorde voltooid:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Als een gebruiker geen referenties-machtiging heeft op referentienummer 5, controleert SQL on-demand of de gebruiker referenties toestemming heeft voor een referentie die een niveau hoger is totdat de referenties worden gevonden waarop de gebruiker referenties heeft. Als een dergelijke toestemming niet wordt gevonden, wordt een foutbericht geretourneerd.

### <a name="credential-and-path-level"></a>Referentie- en padniveau

Afhankelijk van de gewenste padvorm zijn de volgende vereisten aanwezig voor het uitvoeren van query's:

- Als de query meerdere bestanden target (mappen, met of zonder wildcards), moet een gebruiker toegang hebben tot een referentie op ten minste het hoofdmapniveau (containerniveau). Dit toegangsniveau is nodig omdat aanbiedingsbestanden relatief zijn uit de hoofdmap (Azure Storage-beperkingen)
- Als de query zich richt op één bestand, moet een gebruiker toegang hebben tot een referentie op elk niveau, omdat SQL on-demand rechtstreeks toegang heeft tot het bestand, dat wil zeggen zonder mappen met vermelding.

|                  | *Account* | *Hoofdmap* | *Elke andere map* | *Bestand*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Eén bestand*    | Ondersteund | Ondersteund        | Ondersteund             | Ondersteund     |
| *Meerdere bestanden* | Ondersteund | Ondersteund        | Niet ondersteund         | Niet ondersteund |

## <a name="next-steps"></a>Volgende stappen

In de onderstaande artikelen u meer te weten komen over het opvragen van verschillende maptypen, bestandstypen en het maken en gebruiken van weergaven:

- [Query enkel CSV-bestand](query-single-csv-file.md)
- [Querymappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)
- [Specifieke bestanden opvragen](query-specific-files.md)
- [Parketbestanden voor query's](query-parquet-files.md)
- [Weergaven maken en gebruiken](create-use-views.md)
- [JSON-bestanden opvragen](query-json-files.md)
- [Geneste querygenese](query-parquet-nested-types.md)

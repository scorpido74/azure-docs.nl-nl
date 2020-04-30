---
title: Toegang tot opslag accounts beheren voor SQL op aanvraag (preview)
description: Hierin wordt beschreven hoe SQL on-demand (preview) toegang krijgt tot Azure Storage en hoe u opslag toegang voor SQL on-Demand kunt beheren in azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424032"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Toegang tot opslag accounts beheren voor SQL on-demand (preview) in azure Synapse Analytics

Met een SQL on demand-query (preview) worden bestanden rechtstreeks van Azure Storage gelezen. Omdat het opslag account een object is dat extern is voor de SQL on-demand-resource, moeten de juiste referenties worden opgegeven. Een gebruiker heeft de toepasselijke machtigingen nodig om de vereiste referentie te gebruiken. In dit artikel worden de typen referenties beschreven die u kunt gebruiken en hoe de zoek opdracht voor referenties wordt aangenomen voor SQL-en Azure AD-gebruikers.

## <a name="supported-storage-authorization-types"></a>Ondersteunde typen opslag autorisatie

Een gebruiker die zich heeft aangemeld bij een SQL on demand-resource moet gemachtigd zijn om toegang te krijgen tot de bestanden in Azure Storage en om deze op te vragen. Drie autorisatie typen worden ondersteund:

- [Handtekening voor gedeelde toegang](#shared-access-signature)
- [Beheerde identiteit](#managed-identity)
- [Gebruikers identiteit](#user-identity)

> [!NOTE]
> [Azure AD Pass-Through](#force-azure-ad-pass-through) is het standaard gedrag bij het maken van een werk ruimte. Als u deze gebruikt, hoeft u geen referenties te maken voor elk opslag account dat wordt geopend met behulp van AD-aanmeldingen. U kunt [Dit gedrag uitschakelen](#disable-forcing-azure-ad-pass-through).

In de onderstaande tabel vindt u de verschillende autorisatie typen die worden ondersteund of binnenkort worden ondersteund.

| Autorisatietype                    | *SQL-gebruiker*    | *Azure AD-gebruiker*     |
| ------------------------------------- | ------------- | -----------    |
| [GEBASEERD](#shared-access-signature)       | Ondersteund     | Ondersteund      |
| [Beheerde identiteit](#managed-identity) | Niet ondersteund | Niet ondersteund  |
| [Gebruikers identiteit](#user-identity)       | Niet ondersteund | Ondersteund      |

### <a name="shared-access-signature"></a>Handtekening voor gedeelde toegang

**Shared Access Signature (SAS)** biedt gedelegeerde toegang tot resources in een opslag account. Met SAS kan een klant clients toegang verlenen tot bronnen in een opslag account zonder account sleutels te delen. SAS geeft u nauw keurige controle over het type toegang dat u verleent aan clients die een SAS hebben, inclusief het geldigheids interval, het verlenen van machtigingen, het acceptabele IP-adres bereik en het geaccepteerde Protocol (https/http).

U kunt een SAS-token verkrijgen door te navigeren naar het **Azure Portal->-opslag Account > Shared Access Signature-> machtigingen configureren: > SAS en Connection String genereren.**

> [!IMPORTANT]
> Wanneer een SAS-token wordt gegenereerd, bevat het een vraag teken ('? ') aan het begin van het token. Als u het token in SQL on-demand wilt gebruiken, moet u het vraag teken ('? ') verwijderen bij het maken van een referentie. Bijvoorbeeld:
>
> SAS-token:? SV = 2018-03-28&SS = bfqt&SRT = SCO&SP = rwdlacup&se = 2019-04-18T20:42:12Z&St = 2019-04-18T12:42:12Z&SPR = https&sig = lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78% 3D

### <a name="user-identity"></a>Gebruikers identiteit

De identiteit van de **gebruiker**, ook wel ' Pass-through ' genoemd, is een autorisatie type waarbij de identiteit van de Azure AD-gebruiker die is aangemeld bij SQL on-demand wordt gebruikt voor het autoriseren van gegevens toegang. Voordat de gegevens worden geopend, moet de Azure Storage-beheerder machtigingen verlenen aan de Azure AD-gebruiker. Zoals aangegeven in de bovenstaande tabel, wordt het niet ondersteund voor het SQL-gebruikers type.

> [!NOTE]
> Als u [Azure AD Pass-](#force-azure-ad-pass-through) through gebruikt, hoeft u geen referenties te maken voor elk opslag account dat wordt geopend met behulp van AD-aanmeldingen.

> [!IMPORTANT]
> U moet beschikken over een rol voor opslag-blobgegevens/Inzender/lezer om uw identiteit te gebruiken voor toegang tot de gegevens.
> Zelfs als u eigenaar bent van een opslag account, moet u deze nog steeds toevoegen aan een van de BLOB-gegevens rollen van de opslag.
>
> Voor meer informatie over toegangs beheer in Azure Data Lake Store Gen2 raadpleegt u het [toegangs beheer in azure data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md) artikel.
>

### <a name="managed-identity"></a>Beheerde identiteit

**Beheerde identiteit** wordt ook wel MSI genoemd. Het is een functie van Azure Active Directory (Azure AD) die Azure-Services biedt voor SQL op aanvraag. Het implementeert ook een automatisch beheerde identiteit in azure AD. Deze identiteit kan worden gebruikt om de aanvraag voor toegang tot gegevens in Azure Storage te autoriseren.

Voordat de gegevens worden geopend, moet de Azure Storage-beheerder machtigingen verlenen voor de beheerde identiteit voor toegang tot de gegevens. Het verlenen van machtigingen aan beheerde identiteit gebeurt op dezelfde manier als het verlenen van machtigingen aan andere gebruikers van Azure AD.

## <a name="create-credentials"></a>Referenties maken

Als u een query wilt uitvoeren op een bestand dat zich in Azure Storage bevindt, moet uw SQL on-demand-eind punt beschikken over een referentie op server niveau die de verificatie gegevens bevat. Er wordt een referentie toegevoegd door het uitvoeren van [Credential maken](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). U moet een argument voor de referentie naam opgeven. Dit moet overeenkomen met een deel van het pad of het volledige pad naar de gegevens in de opslag (zie hieronder).

> [!NOTE]
> Het argument voor de CRYPTOGRAFISCHe PROVIDER wordt niet ondersteund.

Voor alle ondersteunde autorisatie typen kunnen referenties verwijzen naar een account, een container, een wille keurige map (niet-basis) of één bestand.

De referentie naam moet overeenkomen met het volledige pad naar de container, map of het bestand, in de volgende indeling:`<prefix>://<storage_account_path>/<storage_path>`

| Externe gegevens bron       | Voorvoegsel | Pad naar het opslag account                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>. blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>. dfs.core.windows.net              |

 ' <storage_path> ' is een pad in uw opslag dat verwijst naar de map of het bestand dat u wilt lezen.

> [!NOTE]
> Er is een speciale referentie `UserIdentity` naam die [Azure AD Pass-Through afdwingt](#force-azure-ad-pass-through). Lees de informatie over het effect dat het heeft op het [opzoeken van referenties](#credential-lookup) tijdens het uitvoeren van query's.

Om een gebruiker toe te staan een referentie te maken of te verwijderen, kan de beheerder de machtigingen van referenties aan een gebruiker toestaan of weigeren:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Ondersteunde opslag en autorisatie typen

U kunt de volgende combi Naties van autorisatie-en Azure Storage typen gebruiken:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *GEBASEERD*               | Ondersteund      | Niet ondersteund   | Ondersteund     |
| *Beheerde identiteit* | Niet ondersteund  | Niet ondersteund    | Niet ondersteund |
| *Gebruikers identiteit*    | Ondersteund      | Ondersteund        | Ondersteund     |

### <a name="examples"></a>Voorbeelden

Afhankelijk van het [autorisatie type](#supported-storage-authorization-types), kunt u referenties maken met behulp van de onderstaande T-SQL-syntaxis.

**Shared Access Signature en Blob Storage**

Exchange <*mystorageaccountname*> met de werkelijke naam van het opslag account en <*mystorageaccountcontainername*> met de daad werkelijke container naam:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Gebruikers-id en Azure Data Lake Storage Gen1**

Exchange <*mystorageaccountname*> met de werkelijke naam van het opslag account en <*mystorageaccountcontainername*> met de daad werkelijke container naam:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Gebruikers-id en Azure Data Lake Storage Gen2**

Exchange <*mystorageaccountname*> met de werkelijke naam van het opslag account en <*mystorageaccountcontainername*> met de daad werkelijke container naam:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Azure AD Pass-Through afdwingen

Het afdwingen van een Pass-through van Azure AD is een standaard gedrag dat wordt bereikt door `UserIdentity`een speciale referentie naam, die automatisch wordt gemaakt tijdens het inrichten van Azure Synapse Workspace. Hiermee wordt het gebruik van een Azure AD-passthrough geforceerd voor elke query van elke Azure AD-aanmelding, die ondanks het bestaan van andere referenties plaatsvindt.

> [!NOTE]
> Azure AD Pass-Through is een standaard gedrag. U hoeft geen referenties te maken voor elk opslag account dat wordt gebruikt door AD-aanmeldingen.

Als u [voor elke query het afdwingen van Azure AD Pass-](#disable-forcing-azure-ad-pass-through)through hebt uitgeschakeld, voert u de volgende opdracht uit om het opnieuw in te scha kelen:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Als u het afdwingen van een Azure AD Pass-Through wilt inschakelen voor een specifieke gebruiker, kunt u de `UserIdentity` referentie machtiging voor de desbetreffende gebruiker toekennen. In het volgende voor beeld wordt het afdwingen van een Pass-Through Azure AD-bewerking voor een user_name ingeschakeld:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Zie [referenties zoeken](#credential-lookup)voor meer informatie over hoe SQL on-demand referenties voor gebruik detecteert.

## <a name="disable-forcing-azure-ad-pass-through"></a>Forceren van Azure AD Pass-Through uitschakelen

U kunt het [afdwingen van Azure AD Pass-Through uitschakelen voor elke query](#force-azure-ad-pass-through). Als u dit wilt uitschakelen, `Userdentity` verwijdert u de referentie met:

```sql
DROP CREDENTIAL [UserIdentity];
```

Als u het opnieuw wilt inschakelen, raadpleegt u de sectie [geforceerde Azure AD Pass-Through](#force-azure-ad-pass-through) .

Als u het afdwingen van Azure AD Pass-Through wilt uitschakelen voor een specifieke gebruiker, kunt u een VERWIJZINGs machtiging `UserIdentity` voor een bepaalde gebruiker weigeren. In het volgende voor beeld wordt het afdwingen van Azure AD Pass-Through voor een user_name uitgeschakeld:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Zie [referenties zoeken](#credential-lookup)voor meer informatie over hoe SQL on-demand referenties voor gebruik vindt.

## <a name="grant-permissions-to-use-credential"></a>Machtigingen verlenen voor het gebruik van referenties

Als u de referentie wilt gebruiken, moet een gebruiker over de machtiging referenties beschikken voor een specifieke referentie. Als u een machtiging wilt verlenen voor een storage_credential voor een specific_user, voert u de volgende handelingen uit:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Om ervoor te zorgen dat een soepele Pass-Through Azure AD-ervaring wordt gebruikt, hebben alle gebruikers standaard een recht `UserIdentity` om de referentie te gebruiken. Dit wordt bereikt door een automatische uitvoering van de volgende instructie bij het inrichten van de Azure Synapse-werk ruimte:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Referenties zoeken

Bij het autoriseren van query's wordt het opzoeken van referenties gebruikt voor toegang tot een opslag account en is gebaseerd op de volgende regels:

- De gebruiker is aangemeld als een Azure AD-aanmelding

  - Als er een UserIdentity-referentie bestaat en de gebruiker hiervoor referentie machtigingen heeft, wordt Azure AD Pass-Through gebruikt, anders [referenties zoeken op pad](#lookup-credential-by-path)

- De gebruiker is aangemeld als een SQL-aanmelding

  - [Opzoek referentie op pad](#lookup-credential-by-path) gebruiken

### <a name="lookup-credential-by-path"></a>Referentie zoeken op pad

Als het afdwingen van Azure AD Pass-Through is uitgeschakeld, wordt de zoek opdracht voor referenties gebaseerd op het opslagpad (diepte eerst) en het bestaan van een VERWIJZINGs machtiging voor die specifieke referentie. Als er meerdere referenties zijn die kunnen worden gebruikt voor toegang tot hetzelfde bestand, wordt in SQL op aanvraag de meest specifieke versie gebruikt.  

Hieronder ziet u een voor beeld van een query over het volgende bestandspad: *account.DFS.core.Windows.net/filesystem/Folder1/.../folderN/fileX.ext*

De zoek opdracht voor referenties wordt in deze volg orde voltooid:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Als een gebruiker geen referenties voor het referentie nummer 5 heeft, controleert SQL on demand of de gebruiker over de machtiging voor een referentie die zich één niveau heeft bevindt, wordt verwezen naar de referenties waarnaar de gebruiker verwijst naar de machtiging. Als deze machtiging niet wordt gevonden, wordt er een fout bericht weer gegeven.

### <a name="credential-and-path-level"></a>Referentie niveau en pad

Afhankelijk van de gewenste Padselectie, zijn de volgende vereisten van kracht voor het uitvoeren van query's:

- Als de query is gericht op meerdere bestanden (mappen, met of zonder joker tekens), moet een gebruiker toegang hebben tot een referentie op ten minste het niveau van de hoofdmap (container niveau). Dit toegangs niveau is nodig omdat de lijst met bestanden relatief is ten opzichte van de hoofdmap (Azure Storage beperkingen)
- Als de query is gericht op één bestand, moet een gebruiker toegang hebben tot een referentie op een wille keurig niveau als SQL on-demand toegang heeft tot het bestand rechtstreeks, dat wil zeggen, zonder mappen weer te geven.

|                  | *Account* | *Hoofdmap* | *Een andere map* | *Bestand*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Eén bestand*    | Ondersteund | Ondersteund        | Ondersteund             | Ondersteund     |
| *Meerdere bestanden* | Ondersteund | Ondersteund        | Niet ondersteund         | Niet ondersteund |

## <a name="next-steps"></a>Volgende stappen

In de onderstaande artikelen vindt u informatie over hoe u een query kunt uitvoeren op verschillende soorten mappen, bestands typen en het maken en gebruiken van weer gaven:

- [Query uitvoeren op één CSV-bestand](query-single-csv-file.md)
- [Query mappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)
- [Query's uitvoeren op specifieke bestanden](query-specific-files.md)
- [Query uitvoeren op Parquet-bestanden](query-parquet-files.md)
- [Weergaven maken en gebruiken](create-use-views.md)
- [Query uitvoeren op JSON-bestanden](query-json-files.md)
- [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md)

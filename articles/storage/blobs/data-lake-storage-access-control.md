---
title: Overzicht van toegangscontrole in Azure Data Lake Storage Gen2 | Microsoft Documenten
description: Begrijpen hoe toegangscontrole werkt in Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 93c21656a768ae458572e0b4917412c8103b2f2d
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992212"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Toegangsbeheer in Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementeert een toegangscontrolemodel dat zowel RBAC) als POSIX-achtige toegangscontrolelijsten (ACL's) ondersteunt voor Azure role-based access control.Azure Azure Data Lake Storage Gen2 implements an access control model that supports both Azure role-based access control (RBAC) and POSIX-like access control lists (ACL's). In dit artikel worden de basisprincipes van het toegangscontrolemodel voor Data Lake Storage Gen2 samengevat.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

RBAC gebruikt roltoewijzingen om sets machtigingen effectief toe te passen op *beveiligingsprincipals.* Een *beveiligingsprincipal* is een object dat een gebruiker, groep, serviceprincipal of beheerde identiteit vertegenwoordigt die is gedefinieerd in Azure Active Directory (AD) dat toegang tot Azure-bronnen aanvraagt.

Deze Azure-resources zijn doorgaans beperkt tot bronnen op het hoogste niveau (bijvoorbeeld: Azure Storage-accounts). In het geval van Azure Storage en dus Azure Data Lake Storage Gen2 is dit mechanisme uitgebreid naar de containerbron (bestandssysteem).

Zie [Toegang verlenen tot Azure blob- en wachtrijgegevens met RBAC in de Azure-portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie over het toewijzen van rollen aan beveiligingsprincipals in het bereik van uw opslagaccount.

> [!NOTE]
> Een gastgebruiker kan geen roltoewijzing maken.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>De impact van roltoewijzingen op toegangscontrolelijsten op bestands- en directoryniveau

Tijdens het gebruik van RBAC-roltoewijzingen is het een krachtig mechanisme om toegangsmachtigingen te beheren, is het een zeer grof korrelig mechanisme ten opzichte van ACL's. De kleinste granulariteit voor RBAC is op containerniveau en dit zal worden geëvalueerd met een hogere prioriteit dan ACL's. Als u een rol toewijst aan een beveiligingsprincipal in het bereik van een container, heeft die beveiligingsprincipal daarom het autorisatieniveau dat is gekoppeld aan die rol voor ALLE mappen en bestanden in die container, ongeacht ACL-toewijzingen.

Wanneer een beveiligingsprincipal RBAC-gegevensmachtigingen krijgt via een [ingebouwde rol](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)of via een aangepaste rol, worden deze machtigingen eerst geëvalueerd op autorisatie van een aanvraag. Als de gevraagde bewerking is geautoriseerd door de RBAC-toewijzingen van de beveiligingsprincipal, wordt de autorisatie onmiddellijk opgelost en worden er geen extra ACL-controles uitgevoerd. Als de beveiligingsprincipal geen RBAC-toewijzing heeft of de bewerking van de aanvraag niet overeenkomt met de toegewezen machtiging, worden ACL-controles uitgevoerd om te bepalen of de beveiligingsprincipal gemachtigd is om de gevraagde bewerking uit te voeren.

> [!NOTE]
> Als de beveiligingsprincipal de ingebouwde roltoewijzing storage blob data-eigenaar heeft toegewezen, wordt de beveiligingsprincipal beschouwd als een *supergebruiker* en krijgt hij volledige toegang tot alle muterende bewerkingen, inclusief het instellen van de eigenaar van een directory of bestand, evenals ACL's voor mappen en bestanden waarvoor ze niet de eigenaar zijn. Supergebruikerstoegang is de enige geautoriseerde manier om de eigenaar van een resource te wijzigen.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>SAS-verificatie (Shared Key en Shared Access Signature)

Azure Data Lake Storage Gen2 ondersteunt Shared Key- en SAS-methoden voor verificatie. Een kenmerk van deze verificatiemethoden is dat er geen identiteit is gekoppeld aan de beller en daarom kan de op beveiligingsprincipal permission-based authorization niet worden uitgevoerd.

In het geval van Shared Key krijgt de beller effectief 'supergebruiker' toegang, wat betekent dat volledige toegang tot alle bewerkingen op alle resources, inclusief het instellen van eigenaar en het wijzigen van ACL's.

SAS-tokens bevatten toegestane machtigingen als onderdeel van het token. De machtigingen in het SAS-token worden effectief toegepast op alle autorisatiebeslissingen, maar er worden geen extra ACL-controles uitgevoerd.

## <a name="access-control-lists-on-files-and-directories"></a>Toegangscontrolelijsten voor bestanden en mappen

U een beveiligingsprincipal koppelen aan een toegangsniveau voor bestanden en mappen. Deze associaties worden vastgelegd in een *toegangscontrolelijst (ACL).* Elk bestand en elke map in uw opslagaccount heeft een lijst met toegangsbeheer.

> [!NOTE]
> ACL's zijn alleen van toepassing op beveiligingsprincipals in dezelfde tenant. 

Als u een rol hebt toegewezen aan een beveiligingsprincipal op opslagaccountniveau, u toegangscontrolelijsten gebruiken om die beveiligingsprincipal verhoogde toegang te verlenen tot specifieke bestanden en mappen.

U geen toegangscontrolelijsten gebruiken om een toegangsniveau te bieden dat lager is dan een niveau dat wordt toegekend door een roltoewijzing. Als u bijvoorbeeld de rol [Opslagblob-gegevensinzender toewijst](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) aan een beveiligingsprincipal, u geen toegangscontrolelijsten gebruiken om te voorkomen dat de beveiligingsprincipal naar een map schrijft.


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Machtigingen voor bestands- en mapniveau instellen met behulp van toegangscontrolelijsten

Zie een van de volgende artikelen om machtigingen voor bestands- en directoryniveau in te stellen:

|||
|--------|-----------|
|Azure Opslagverkenner |[Azure Storage Explorer gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md#managing-access)|
|.NET |[.NET gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Java gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md)|
|Python|[Python gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[PowerShell gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md)|
|Azure CLI|[Azure CLI gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md)|
|REST-API |[Pad - Bijwerken](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Als de beveiligingsprincipal een *serviceprincipal* is, is het belangrijk om de object-id van de serviceprincipal te gebruiken en niet de object-id van de gerelateerde app-registratie. Als u de object-id van de serviceprincipal wilt ophalen, opent u de Azure CLI en gebruikt u deze opdracht: `az ad sp show --id <Your App ID> --query objectId`. zorg ervoor dat `<Your App ID>` u de tijdelijke aanduiding vervangt door de app-id van uw app-registratie.

### <a name="types-of-access-control-lists"></a>Typen toegangscontrolelijsten

Er zijn twee soorten toegangscontrolelijsten: *toegangs-ACL's* en *standaardACL's*.

Met toegangs-ACL's beheert u de toegang tot een object. Bestanden en mappen hebben beide Toegangs-ACL's.

StandaardAL's zijn sjablonen van ACL's die zijn gekoppeld aan een map die de toegangsacl.l.a. bepalen voor onderliggende items die onder die map zijn gemaakt. Bestanden hebben geen Standaard-ACL's.

Zowel toegangsacl.n. als standaardACL's hebben dezelfde structuur.

> [!NOTE]
> Het wijzigen van de standaardACL op een ouder heeft geen invloed op de toegang ACL of standaard ACL van onderliggende items die al bestaan.

### <a name="levels-of-permission"></a>Machtigingsniveaus

De machtigingen voor een containerobject zijn **Lezen,** **Schrijven**en **Uitvoeren**en kunnen worden gebruikt op bestanden en mappen zoals weergegeven in de volgende tabel:

|            |    File     |   Directory |
|------------|-------------|----------|
| **Lezen (L)** | Kan de inhoud van een bestand lezen | Lees **Read** en **uitvoer** vereist om de inhoud van de map weer te geven |
| **Schrijven (S)** | Kan schrijven of toevoegen aan een bestand | Schrijven **Write** en **uitvoeren** vereist om onderliggende items in een map te maken |
| **Uitvoeren (U)** | Betekent niets in het kader van Data Lake Storage Gen2 | Vereist om de onderliggende items van een map te doorkruisen |

> [!NOTE]
> Als u machtigingen verleent door alleen ACL's (geen RBAC) te gebruiken, moet u de beveiligingsprincipal **machtigingen voor** uitvoeren aan de container en aan elke map in de hiërarchie van mappen die naar het bestand leiden, toestaan.

#### <a name="short-forms-for-permissions"></a>Korte formulieren voor machtigingen

**LSU** wordt gebruikt om **Lezen + Schrijven + Uitvoeren** aan te geven. Er bestaat een verkorte numerieke vorm. Hierbij is **Lezen = 4**, **Schrijven = 2** en **Uitvoeren = 1**. De som van deze getallen vertegenwoordigt de machtigingen. Hier volgen enkele voorbeelden.

| Numerieke vorm | Verkorte vorm |      Wat het betekent     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Lezen + Schrijven + Uitvoeren |
| 5            | `R-X`        | Lezen + Uitvoeren         |
| 4            | `R--`        | Lezen                   |
| 0            | `---`        | Geen machtigingen         |

#### <a name="permissions-inheritance"></a>Overname machtigingen

In het POSIX-model dat wordt gebruikt door Data Lake Storage Gen2, worden machtigingen voor een artikel op het artikel zelf opgeslagen. Met andere woorden, machtigingen voor een item kunnen niet worden overgenomen van de bovenliggende items als de machtigingen zijn ingesteld nadat het onderliggende item al is gemaakt. Machtigingen worden alleen overgenomen als er standaardmachtigingen zijn ingesteld op de bovenliggende items voordat de onderliggende items zijn gemaakt.

### <a name="common-scenarios-related-to-permissions"></a>Algemene scenario's met machtigingen

In de volgende tabel worden enkele veelvoorkomende scenario's weergegeven om u te helpen begrijpen welke machtigingen nodig zijn om bepaalde bewerkingen op een opslagaccount uit te voeren.

|    Bewerking             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Lees Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Toevoegen aan Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Gegevens verwijderen.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Data.txt maken          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lijst /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lijst /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lijst /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Schrijfmachtigingen voor het bestand zijn niet vereist om het te verwijderen, zolang de vorige twee voorwaarden waar zijn.

### <a name="users-and-identities"></a>Gebruikers en identiteiten

Elk bestand en elke map heeft verschillende machtigingen voor deze identiteiten:

- De gebruiker die eigenaar is
- De groep die eigenaar is
- Benoemde gebruikers
- Benoemde groepen
- Benoemde serviceprincipals
- Benoemde beheerde identiteiten
- Alle andere gebruikers

De identiteiten van gebruikers en groepen zijn Azure Active Directory-identiteiten (Azure AD). Tenzij anders vermeld, kan een *gebruiker*, in de context van Data Lake Storage Gen2, verwijzen naar een Azure AD-gebruiker, serviceprincipal, beheerde identiteit of beveiligingsgroep.

#### <a name="the-owning-user"></a>De gebruiker die eigenaar is

De gebruiker die het item heeft gemaakt, wordt automatisch de gebruiker die eigenaar is van het item. Een gebruiker die eigenaar is kan:

* De machtigingen wijzigen van een bestand waarvan hij eigenaar is.
* De groep die eigenaar van een bestand is wijzigen, zolang deze gebruiker ook lid is van de doelgroep.

> [!NOTE]
> De eigenaar kan de eigenaar van een bestand of map *niet* wijzigen. Alleen supergebruikers kunnen de eigenaar van een bestand of map wijzigen.

#### <a name="the-owning-group"></a>De groep die eigenaar is

In de POSIX ACL's is elke gebruiker gekoppeld aan een *primaire groep.* Gebruiker 'Alice' kan bijvoorbeeld deel uitmaken van de groep 'financiën'. Alice kan ook tot meerdere groepen behoren, maar één groep wordt altijd aangeduid als hun primaire groep. Wanneer Els in POSIX een bestand maakt, wordt de groep die eigenaar van het bestand is als haar hoofdgroep ingesteld. In dit geval is dit 'Financiën'. De groep die eigenaar is, gedraagt zich op dezelfde manier als toegewezen machtigingen voor andere gebruikers/groepen.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>De eigenaargroep toewijzen voor een nieuw bestand of map

* **Geval 1**: De hoofdmap "/". Deze map wordt gemaakt wanneer een Data Lake Storage Gen2-container wordt gemaakt. In dit geval wordt de eigenaargroep ingesteld op de gebruiker die de container heeft gemaakt als dit met OAuth is gedaan. Als de container is gemaakt met behulp van Gedeelde sleutel, een Account SAS of een Service SAS, worden de eigenaar en de eigenaar van de groep ingesteld op **$superuser**.
* **Zaak 2** (Elk ander geval): Wanneer een nieuw item wordt gemaakt, wordt de eigenaargroep gekopieerd uit de bovenliggende map.

##### <a name="changing-the-owning-group"></a>De eigenaargroep wijzigen

De groep die eigenaar is kan worden gewijzigd door:
* Alle supergebruikers.
* De gebruiker die eigenaar is, als deze gebruiker ook lid is van de doelgroep.

> [!NOTE]
> De eigenaargroep kan de ACL's van een bestand of map niet wijzigen.  Terwijl de eigenaargroep is ingesteld op de gebruiker die het account heeft gemaakt in het geval van de hoofdmap, **geval 1** hierboven, is één gebruikersaccount niet geldig voor het verstrekken van machtigingen via de eigenaargroep. U kunt deze machtiging toewijzen aan een geldige gebruikersgroep, indien van toepassing.

### <a name="access-check-algorithm"></a>Algoritme voor toegangscontrole

De volgende pseudocode vertegenwoordigt het algoritme voor toegangscontrole voor opslagaccounts.

```console
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>Het masker

Zoals geïllustreerd in het algoritme voor toegangscontrole, beperkt het masker de toegang voor benoemde gebruikers, de eigenaargroep en benoemde groepen.  

> [!NOTE]
> Voor een nieuwe Data Lake Storage Gen2-container wordt het masker voor de toegangsacl van de rootdirectory ("/") standaard ingesteld op 750 voor mappen en 640 voor bestanden. Bestanden ontvangen de X-bit niet omdat deze niet relevant is voor bestanden in een alleen-opslagsysteem.
>
> Het masker kan per gesprek worden opgegeven. Hierdoor kunnen verschillende verbruikende systemen, zoals clusters, verschillende effectieve maskers hebben voor hun bestandsbewerkingen. Als een masker is opgegeven op een bepaald verzoek, wordt het standaardmasker volledig overschreven.

#### <a name="the-sticky-bit"></a>De vergrendelde bit

De sticky bit is een meer geavanceerde functie van een POSIX container. In het kader van Data Lake Storage Gen2 is het onwaarschijnlijk dat de kleverige bit nodig zal zijn. Kortom, als de plakkerige bit is ingeschakeld in een map, kan een onderliggend item alleen worden verwijderd of hernoemd door de eigenaar van het onderliggende item.

De plakkerige bit wordt niet weergegeven in de Azure-portal.

### <a name="default-permissions-on-new-files-and-directories"></a>Standaardmachtigingen voor nieuwe bestanden en mappen

Wanneer een nieuw bestand of map wordt gemaakt onder een bestaande map, bepaalt de standaardACL op de bovenliggende map:

- De standaard ACL van een onderliggende map en toegang tot ACL.
- De toegang van ACL (bestanden hebben geen standaard ACL) van een onderliggend bestand.

#### <a name="umask"></a>umask

Bij het maken van een bestand of map wordt umask gebruikt om de manier waarop de standaardAL's op het onderliggende item zijn ingesteld, te wijzigen. umask is een 9-bits waarde op bovenliggende mappen die een RWX-waarde bevat voor **het bezitten van gebruikers,** **het bezitten van groepen**en **andere**.

Het umask voor Azure Data Lake Storage Gen2 is een constante waarde die is ingesteld op 007. Deze waarde vertaalt zich in:

| umask-component     | Numerieke vorm | Verkorte vorm | Betekenis |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Kopieer de standaard ACL van de ouder voor het bezitten van de gebruiker naar de toegang van het kind acl | 
| umask.owning_group  |    0         |   `---`      | Kopieer de standaard ACL van de bovenliggende groep voor het bezitten van de standaardACL naar de toegang van het kind | 
| umask.other         |    7         |   `RWX`      | Verwijder voor de overige machtigingen alle machtigingen voor de toegang van het kind ACL |

De umask-waarde die wordt gebruikt door Azure Data Lake Storage Gen2 betekent effectief dat de waarde voor **andere** kinderen nooit standaard wordt verzonden op nieuwe kinderen, ongeacht wat de standaard ACL aangeeft. 

In de volgende pseudocode ziet u hoe het umask wordt toegepast bij het maken van de ACL's voor een onderliggend item.

```console
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Veelgestelde vragen over ACL's in Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>Moet ik ondersteuning voor ACL's inschakelen?

Nee. Toegangsbeheer via ACL's is ingeschakeld voor een opslagaccount zolang de functie Hiërarchische naamruimte (HNS) is ingeschakeld.

Als HNS is uitgeschakeld, zijn de Azure RBAC-autorisatieregels nog steeds van toepassing.

### <a name="what-is-the-best-way-to-apply-acls"></a>Wat is de beste manier om ACL's toe te passen?

Gebruik azure AD-beveiligingsgroepen altijd als de toegewezen hoofdsom in ACL's. Weersta de mogelijkheid om individuele gebruikers of serviceprincipals rechtstreeks toe te wijzen. Met deze structuur u gebruikers of serviceprincipals toevoegen en verwijderen zonder dat u ACL's opnieuw hoeft toe te passen op een hele directorystructuur. ) In plaats daarvan hoeft u ze alleen maar toe te voegen of te verwijderen uit de juiste Azure AD-beveiligingsgroep. Houd er rekening mee dat ACL's niet worden overgenomen en daarom moet de ACL voor elk bestand en submap opnieuw worden bijgewerkt. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Welke machtigingen zijn nodig om een map en de inhoud ervan opnieuw te verwijderen?

- De beller heeft 'super-user' machtigingen,

of

- De bovenliggende map moet schrijf - uitvoermachtigingen hebben.
- De map die moet worden verwijderd en elke map erin, vereist machtigingen voor lezen + schrijven + uitvoeren.

> [!NOTE]
> U hebt geen Schrijfmachtigingen nodig om bestanden in mappen te verwijderen. Ook kan de hoofdmap "/" nooit worden verwijderd.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Wie is de eigenaar van een bestand of directory?

De maker van een bestand of directory wordt de eigenaar. In het geval van de hoofdmap is dit de identiteit van de gebruiker die de container heeft gemaakt.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Welke groep is ingesteld als de eigenaar van een bestand of map bij het maken?

De eigenaargroep wordt gekopieerd uit de eigenaargroep van de bovenliggende map waaronder het nieuwe bestand of de nieuwe map wordt gemaakt.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Ik ben de eigenaar van een bestand, maar ik heb niet de RWX machtigingen die ik nodig heb. Wat moet ik doen?

De gebruiker die eigenaar is kan de machtigingen van het bestand wijzigen en zichzelf de vereiste LSU-machtigingen geven.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Waarom zie ik soms GUID's in ACL's?

Er wordt een GUID weergegeven als de vermelding een gebruiker vertegenwoordigt en die gebruiker niet meer bestaat in Azure AD. Dit gebeurt doorgaans wanneer gebruikers het bedrijf verlaten of hun accounts zijn verwijderd in Azure AD. Bovendien hebben serviceprincipals en beveiligingsgroepen geen User Principal Name (UPN) om ze te identificeren en worden ze dus vertegenwoordigd door hun OID-attribuut (a guid).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Hoe stel ik ACL's correct in voor een serviceprincipal?

Wanneer u ACL's definieert voor serviceprincipals, is het belangrijk om de Object ID (OID) van de *serviceprincipal* te gebruiken voor de app-registratie die u hebt gemaakt. Het is belangrijk op te merken dat geregistreerde apps een aparte serviceprincipal hebben in de specifieke Azure AD-tenant. Geregistreerde apps hebben een OID die zichtbaar is in de Azure-portal, maar de *serviceprincipal* heeft een andere (andere) OID.

Als u de OID voor de serviceprincipal wilt krijgen die `az ad sp show` overeenkomt met een app-registratie, u de opdracht gebruiken. Geef de toepassings-id op als parameter. Hier is een voorbeeld over het verkrijgen van de OID voor de service principal die overeenkomt met een app registratie met App ID = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Voer de volgende opdracht uit in de Azure CLI:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID wordt weergegeven.

Wanneer u de juiste OID voor de serviceprincipal hebt, gaat u naar de pagina **Access beheren van** de Storage Explorer om de OID toe te voegen en de juiste machtigingen voor de OID toe te wijzen. Zorg ervoor dat u **Opslaan**selecteert.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Ondersteunt Data Lake Storage Gen2 de overerving van ACL's?

Azure RBAC-toewijzingen erven. Toewijzingen vloeien uit de bronnen van abonnements-, resourcegroepen en opslagaccounten naar de containerbron.

ACL's erven niet. StandaardAL's kunnen echter worden gebruikt om ACL's in te stellen voor onderliggende submappen en bestanden die onder de bovenliggende map zijn gemaakt. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Waar kan ik meer informatie over het POSIX-model voor toegangsbeheer?

* [POSIX met behulp van toegangsbeheerlijsten op Linux](https://www.linux.com/news/posix-acls-linux)
* [HDFS-machtigingshandleiding](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Veelgestelde vragen over POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL in Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL met behulp van toegangscontrolelijsten op Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Zie ook

* [Overzicht van Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)

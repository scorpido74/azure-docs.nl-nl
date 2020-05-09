---
title: Overzicht van Access Control in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Begrijpen hoe toegangs beheer werkt in Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: dd23745f811cf67aa5e7ef7aa96b877b5980c270
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793122"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Toegangsbeheer in Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementeert een model voor toegangs beheer dat zowel op rollen gebaseerd toegangs beheer (RBAC) als POSIX-achtige Acl's (toegangs beheer lijsten) ondersteunt. Dit artikel bevat een overzicht van de basis beginselen van het toegangs beheer model voor Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

RBAC gebruikt roltoewijzingen om effectief machtigingen sets toe te passen op *beveiligings-principals*. Een *beveiligingsprincipal is een object dat een gebruiker* , groep, Service-Principal of beheerde identiteit vertegenwoordigt die is gedefinieerd in azure Active Directory (AD) dat toegang tot Azure-resources aanvraagt.

Normaal gesp roken zijn deze Azure-resources beperkt tot resources op het hoogste niveau (bijvoorbeeld: Azure Storage accounts). In het geval van Azure Storage, en dus Azure Data Lake Storage Gen2, is dit mechanisme uitgebreid naar de container bron (bestands systeem).

Zie voor meer informatie over het toewijzen van rollen aan beveiligings-principals in het bereik van uw opslag account [toegang verlenen aan Azure Blob en gegevens wachtrij met RBAC in het Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Een gast gebruiker kan geen roltoewijzing maken.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>De impact van roltoewijzingen op het niveau van toegangs beheer lijsten op bestands-en mapniveau

Hoewel het gebruik van RBAC-roltoewijzingen een krachtig mechanisme is om toegangs machtigingen te beheren, is het een zeer grof mechanisme dat relatief is ten opzichte van Acl's. De kleinste granulatie voor RBAC bevindt zich op het niveau van de container en deze wordt geëvalueerd met een hogere prioriteit dan Acl's. Als u een rol toewijst aan een beveiligingsprincipal in het bereik van een container, heeft die beveiligings-principal daarom het autorisatie niveau dat aan die rol is gekoppeld voor alle mappen en bestanden in die container, ongeacht de toewijzing van de toegangs beheer lijst.

Wanneer aan een beveiligingsprincipal RBAC-gegevens machtigingen worden verleend via een [ingebouwde rol](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)of via een aangepaste rol, worden deze machtigingen eerst geëvalueerd op basis van de autorisatie van een aanvraag. Als de aangevraagde bewerking wordt geautoriseerd door de RBAC-toewijzingen van de beveiligingsprincipal, wordt de autorisatie onmiddellijk opgelost en worden er geen extra ACL-controles uitgevoerd. Als de beveiligingsprincipal geen RBAC-toewijzing heeft of de bewerking van de aanvraag niet overeenkomt met de toegewezen machtiging, worden er ook ACL'S-controles uitgevoerd om te bepalen of de beveiligings-principal gemachtigd is om de aangevraagde bewerking uit te voeren.

> [!NOTE]
> Als aan de beveiligingsprincipal de ingebouwde roltoewijzing voor de BLOB-gegevens eigenaar is toegewezen, wordt de beveiligingsprincipal beschouwd als een *super gebruiker* en krijgt hij volledige toegang tot alle muteren-bewerkingen, inclusief het instellen van de eigenaar van een map of bestand, evenals de acl's voor mappen en bestanden waarvoor ze niet de eigenaar zijn. Toegang voor Super gebruikers is de enige geautoriseerde manier om de eigenaar van een resource te wijzigen.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Gedeelde sleutel-en Shared Access Signature-verificatie (SAS)

Azure Data Lake Storage Gen2 ondersteunt gedeelde sleutel-en SAS-methoden voor verificatie. Een kenmerk van deze verificatie methoden is dat er geen identiteit aan de oproepende functie is gekoppeld en daarom niet op basis van machtigingen voor de machtigings bevoegdheid voor beveiliging kan worden uitgevoerd.

In het geval van een gedeelde sleutel krijgt de aanroeper in feite de toegang ' supergebruiker ', wat betekent dat volledige toegang tot alle bewerkingen op alle resources, inclusief het instellen van eigenaar en het wijzigen van Acl's.

SAS-tokens bevatten toegestane machtigingen als onderdeel van het token. De machtigingen die zijn opgenomen in het SAS-token worden effectief toegepast op alle autorisatie beslissingen, maar er worden geen extra ACL'S-controles uitgevoerd.

## <a name="access-control-lists-on-files-and-directories"></a>Toegangs beheer lijsten voor bestanden en mappen

U kunt een beveiligingsprincipal koppelen aan een toegangs niveau voor bestanden en mappen. Deze koppelingen worden vastgelegd in een *toegangs beheer lijst (ACL)*. Elk bestand en elke map in uw opslag account heeft een toegangs beheer lijst.

> [!NOTE]
> Acl's zijn alleen van toepassing op beveiligings-principals in dezelfde Tenant. 

Als u een rol aan een beveiligingsprincipal op het niveau van het opslag account hebt toegewezen, kunt u toegangs beheer lijsten gebruiken om die beveiligingsprincipal verhoogde toegang tot specifieke bestanden en mappen te verlenen.

U kunt geen toegangs beheer lijsten gebruiken om een niveau van toegang te bieden dat lager is dan een niveau dat door een roltoewijzing wordt toegekend. Als u bijvoorbeeld de rol van [BLOB-gegevens](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) voor het maken van een beveiligings-principal toewijst aan een beveiligingsprincipal, kunt u geen toegangs beheer lijsten gebruiken om te voor komen dat de beveiligingsprincipal naar een map schrijft.


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Machtigingen voor bestanden en mapniveau instellen met behulp van toegangs beheer lijsten

Zie een van de volgende artikelen voor het instellen van machtigingen voor bestands-en mapniveau:

|||
|--------|-----------|
|Azure Opslagverkenner |[Gebruik Azure Storage Explorer voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md#managing-access)|
|.NET |[.NET gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Java gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md)|
|Python|[Python gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[Power shell gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md)|
|Azure CLI|[Azure CLI gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md)|
|REST-API |[Pad-bijwerken](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Als de beveiligingsprincipal een *service* -principal is, is het belang rijk dat u de object-id van de Service-Principal gebruikt en niet de object-id van de gerelateerde app-registratie. Als u de object-ID van de Service-Principal wilt ophalen, opent u de Azure CLI en `az ad sp show --id <Your App ID> --query objectId`gebruikt u deze opdracht:. Zorg ervoor dat u de `<Your App ID>` tijdelijke aanduiding vervangt door de app-id van de app-registratie.

### <a name="types-of-access-control-lists"></a>Typen toegangs beheer lijsten

Er zijn twee soorten toegangs beheer lijsten: *toegangs-acl's* en *standaard-acl's*.

Met toegangs-ACL's beheert u de toegang tot een object. Bestanden en mappen hebben beide Toegangs-ACL's.

Standaard-Acl's zijn sjablonen van Acl's die zijn gekoppeld aan een map die de toegangs-Acl's bepaalt voor alle onderliggende items die zijn gemaakt in die map. Bestanden hebben geen Standaard-ACL's.

Zowel toegangs-Acl's als standaard-Acl's hebben dezelfde structuur.

> [!NOTE]
> Het wijzigen van de standaard-ACL voor een bovenliggend item heeft geen invloed op de toegangs-ACL of de standaard-ACL van onderliggende items die al bestaan.

### <a name="levels-of-permission"></a>Machtigings niveaus

De machtigingen voor een container object zijn **lezen**, **schrijven**en **uitvoeren**en kunnen worden gebruikt voor bestanden en mappen, zoals wordt weer gegeven in de volgende tabel:

|            |    File     |   Directory |
|------------|-------------|----------|
| **Lezen (L)** | Kan de inhoud van een bestand lezen | Moet worden **gelezen** en **uitgevoerd** om de inhoud van de map weer te geven |
| **Schrijven (S)** | Kan schrijven of toevoegen aan een bestand | **Schrijven** en **uitvoeren** is vereist om onderliggende items in een map te maken |
| **Uitvoeren (U)** | Betekent niets in de context van Data Lake Storage Gen2 | Vereist om de onderliggende items van een map door te bladeren |

> [!NOTE]
> Als u machtigingen verleent met behulp van alleen Acl's (geen RBAC) en vervolgens een beveiligings-principal Lees-of schrijf toegang wilt verlenen voor een bestand, moet u de beveiligings-principal **uitvoerings** machtigingen voor de container geven en aan elke map in de hiërarchie van mappen die tot het bestand leiden.

#### <a name="short-forms-for-permissions"></a>Korte formulieren voor machtigingen

**LSU** wordt gebruikt om **Lezen + Schrijven + Uitvoeren** aan te geven. Er bestaat een verkorte numerieke vorm. Hierbij is **Lezen = 4**, **Schrijven = 2** en **Uitvoeren = 1**. De som van deze getallen vertegenwoordigt de machtigingen. Hier volgen enkele voorbeelden.

| Numerieke vorm | Verkorte vorm |      Wat het betekent     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Lezen + Schrijven + Uitvoeren |
| 5            | `R-X`        | Lezen + Uitvoeren         |
| 4            | `R--`        | Lezen                   |
| 0            | `---`        | Geen machtigingen         |

#### <a name="permissions-inheritance"></a>Overname van machtigingen

In het POSIX-stijl model dat wordt gebruikt door Data Lake Storage Gen2, worden machtigingen voor een item opgeslagen op het item zelf. Met andere woorden, machtigingen voor een item kunnen niet worden overgenomen van de bovenliggende items als de machtigingen zijn ingesteld nadat het onderliggende item al is gemaakt. Machtigingen worden alleen overgenomen als standaard machtigingen zijn ingesteld voor de bovenliggende items voordat de onderliggende items zijn gemaakt.

### <a name="common-scenarios-related-to-permissions"></a>Algemene scenario's met machtigingen

De volgende tabel bevat enkele algemene scenario's die u helpen te begrijpen welke machtigingen nodig zijn om bepaalde bewerkingen uit te voeren op een opslag account.

|    Bewerking             |    /    | Oregon | Port land | Data. txt     |
|--------------------------|---------|----------|-----------|--------------|
| Gegevens lezen. txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Toevoegen aan data. txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Data. txt verwijderen          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Data. txt maken          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Orderverzamellijst                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| /Oregon/weer geven           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| /Oregon/Portland/weer geven  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Schrijf machtigingen voor het bestand zijn niet vereist om het te verwijderen, zolang de vorige twee voor waarden waar zijn.

### <a name="users-and-identities"></a>Gebruikers en identiteiten

Elk bestand en elke map heeft afzonderlijke machtigingen voor deze identiteiten:

- De gebruiker die eigenaar is
- De groep die eigenaar is
- Benoemde gebruikers
- Benoemde groepen
- Benoemde service-principals
- Benoemde beheerde identiteiten
- Alle andere gebruikers

De identiteiten van gebruikers en groepen zijn Azure Active Directory-identiteiten (Azure AD). Tenzij anders vermeld, kan een *gebruiker*in de context van data Lake Storage Gen2 verwijzen naar een Azure AD-gebruiker, Service-Principal, beheerde identiteit of beveiligings groep.

#### <a name="the-owning-user"></a>De gebruiker die eigenaar is

De gebruiker die het item heeft gemaakt, wordt automatisch de gebruiker die eigenaar is van het item. Een gebruiker die eigenaar is kan:

* De machtigingen wijzigen van een bestand waarvan hij eigenaar is.
* De groep die eigenaar van een bestand is wijzigen, zolang deze gebruiker ook lid is van de doelgroep.

> [!NOTE]
> De gebruiker die eigenaar is, kan de gebruiker die eigenaar is van een bestand of map *niet* wijzigen. Alleen super gebruikers kunnen de gebruiker die eigenaar is van een bestand of map wijzigen.

#### <a name="the-owning-group"></a>De groep die eigenaar is

In de POSIX Acl's is elke gebruiker gekoppeld aan een *primaire groep*. Bijvoorbeeld: gebruiker Anja kan behoren tot de groep ' Finance '. Anja kan ook deel uitmaken van meerdere groepen, maar één groep wordt altijd aangeduid als de primaire groep. Wanneer Els in POSIX een bestand maakt, wordt de groep die eigenaar van het bestand is als haar hoofdgroep ingesteld. In dit geval is dit 'Financiën'. De groep die eigenaar is, gedraagt zich op dezelfde manier als toegewezen machtigingen voor andere gebruikers/groepen.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>De groep die eigenaar is toewijzen aan een nieuw bestand of nieuwe map

* Voor **Beeld 1**: de hoofdmap '/'. Deze map wordt gemaakt wanneer een Data Lake Storage Gen2-container wordt gemaakt. In dit geval wordt de groep die eigenaar is ingesteld op de gebruiker die de container heeft gemaakt als deze is uitgevoerd met behulp van OAuth. Als de container is gemaakt met behulp van gedeelde sleutel, een account-SAS of een service-SA'S, wordt de groep eigenaar en eigenaar ingesteld op **$superuser**.
* Voor **Beeld 2** (elk ander geval): wanneer een nieuw item wordt gemaakt, wordt de groep die eigenaar is gekopieerd van de bovenliggende map.

##### <a name="changing-the-owning-group"></a>De groep die eigenaar is wijzigen

De groep die eigenaar is kan worden gewijzigd door:
* Alle supergebruikers.
* De gebruiker die eigenaar is, als deze gebruiker ook lid is van de doelgroep.

> [!NOTE]
> De groep die eigenaar is, kan de Acl's van een bestand of map niet wijzigen.  Als de groep die eigenaar is, is ingesteld op de gebruiker die het account heeft gemaakt in het geval van de hoofdmap, bovenstaande **aanvraag 1** hierboven, is één gebruikers account niet geldig voor het opgeven van machtigingen via de groep die eigenaar is. U kunt deze machtiging toewijzen aan een geldige gebruikersgroep, indien van toepassing.

### <a name="access-check-algorithm"></a>Algoritme voor toegangscontrole

De volgende pseudocode vertegenwoordigt het algoritme voor toegangs controle voor opslag accounts.

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

Zoals geïllustreerd in het algoritme voor toegangs controle, beperkt het masker de toegang voor benoemde gebruikers, de groep die eigenaar is en benoemde groepen.  

> [!NOTE]
> Voor een nieuwe Data Lake Storage Gen2-container wordt het masker voor de toegangs-ACL van de hoofdmap (/) standaard ingesteld op 750 voor directory's en 640 voor bestanden. Bestanden ontvangen niet de X-bit omdat deze niet van toepassing is op bestanden in een alleen-opslag systeem.
>
> Het masker kan worden opgegeven per oproep. Hierdoor kunnen verschillende verbruiks systemen, zoals clusters, verschillende efficiënte maskers hebben voor de bestands bewerkingen. Als voor een bepaalde aanvraag een masker is opgegeven, wordt het standaard masker volledig overschreven.

#### <a name="the-sticky-bit"></a>De vergrendelde bit

De sticky bit is een meer geavanceerde functie van een POSIX-container. In de context van Data Lake Storage Gen2 is het niet waarschijnlijk dat de sticky-bit nodig is. Als de sticky bit is ingeschakeld in een directory, kan een onderliggend item alleen worden verwijderd of hernoemd door de gebruiker die eigenaar is van het onderliggende item.

De sticky bit wordt niet weer gegeven in de Azure Portal.

### <a name="default-permissions-on-new-files-and-directories"></a>Standaard machtigingen voor nieuwe bestanden en mappen

Wanneer een nieuw bestand of nieuwe map wordt gemaakt onder een bestaande map, bepaalt de standaard-ACL voor de bovenliggende map het volgende:

- De standaard-ACL en toegangs-ACL voor een onderliggende map.
- De toegangs-ACL van een onderliggend bestand (bestanden hebben geen standaard-ACL).

#### <a name="umask"></a>umask

Bij het maken van een bestand of map wordt umask gebruikt om te wijzigen hoe de standaard-Acl's worden ingesteld voor het onderliggende item. umask is een 9-bits waarde op bovenliggende mappen met een LSU-waarde voor de **gebruiker die eigenaar**is, de groep die **eigenaar**is en **andere**.

De umask voor het Azure Data Lake Storage Gen2 van een constante waarde die is ingesteld op 007. Deze waarde wordt omgezet naar:

| onderdeel umask     | Numerieke vorm | Verkorte vorm | Betekenis |
|---------------------|--------------|------------|---------|
| umask. owning_user   |    0         |   `---`      | Voor de gebruiker die eigenaar is, kopieert u de standaard-ACL van het bovenliggende object naar de toegangs-ACL van het onderliggende item | 
| umask. owning_group  |    0         |   `---`      | Kopieer voor de groep die eigenaar is de standaard-ACL van het bovenliggende object naar de toegangs-ACL van het onderliggende item | 
| umask. Overig         |    7         |   `RWX`      | Verwijder voor andere alle machtigingen voor de toegangs-ACL van het onderliggende item |

De waarde voor umask die wordt gebruikt door Azure Data Lake Storage Gen2 effectief betekent dat de waarde voor **Overige** nooit standaard wordt verzonden op nieuwe kinderen, ongeacht wat de standaard-ACL aangeeft. 

De volgende pseudocode laat zien hoe de umask wordt toegepast bij het maken van de Acl's voor een onderliggend item.

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Veelgestelde vragen over Acl's in Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>Moet ik ondersteuning voor ACL's inschakelen?

Nee. Toegangs beheer via Acl's is ingeschakeld voor een opslag account zolang de functie voor hiërarchische naam ruimte (HNS) is ingeschakeld.

Als HNS is uitgeschakeld, zijn de Azure RBAC-autorisatie regels nog steeds van toepassing.

### <a name="what-is-the-best-way-to-apply-acls"></a>Wat is de beste manier om Acl's toe te passen?

Gebruik altijd Azure AD-beveiligings groepen als de toegewezen Principal in Acl's. Houd de kans om afzonderlijke gebruikers of service-principals rechtstreeks toe te wijzen. Met deze structuur kunt u gebruikers of service-principals toevoegen en verwijderen zonder dat u de Acl's opnieuw hoeft toe te passen op een volledige mapstructuur. In plaats daarvan moet u deze eenvoudigweg toevoegen aan of verwijderen uit de juiste Azure AD-beveiligings groep. Houd er wel van uit dat Acl's niet worden overgenomen en dat Acl's opnieuw moeten worden toegepast. hiervoor moet de toegangs beheer lijst voor elk bestand en elke submap worden bijgewerkt. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Welke machtigingen zijn vereist voor het recursief verwijderen van een map en de inhoud ervan?

- De aanroeper heeft machtigingen voor super gebruiker,

of

- De bovenliggende map moet de machtigingen schrijven + uitvoeren hebben.
- Voor de map die moet worden verwijderd en voor elke map erin zijn de machtigingen lezen + schrijven + uitvoeren vereist.

> [!NOTE]
> U hebt geen schrijf machtigingen nodig voor het verwijderen van bestanden in mappen. De hoofdmap '/' kan ook nooit worden verwijderd.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Wie is de eigenaar van een bestand of map?

De maker van een bestand of map wordt de eigenaar. In het geval van de hoofdmap is dit de identiteit van de gebruiker die de container heeft gemaakt.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Welke groep wordt ingesteld als de groep die eigenaar is van een bestand of map bij het maken?

De groep die eigenaar is, wordt gekopieerd uit de groep die eigenaar is van de bovenliggende map waaronder het nieuwe bestand of de map wordt gemaakt.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Ik ben de gebruiker die eigenaar is van een bestand, maar ik heb niet de LSU-machtigingen die ik nodig heb. Wat moet ik doen?

De gebruiker die eigenaar is kan de machtigingen van het bestand wijzigen en zichzelf de vereiste LSU-machtigingen geven.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Waarom zie ik soms GUID'S in Acl's?

Er wordt een GUID weer gegeven als de vermelding een gebruiker vertegenwoordigt en die gebruiker niet meer bestaat in azure AD. Dit gebeurt doorgaans wanneer gebruikers het bedrijf verlaten of hun accounts zijn verwijderd in Azure AD. Daarnaast hebben service-principals en beveiligings groepen geen UPN (User Principal Name) om ze te identificeren en worden ze vertegenwoordigd door hun OID-kenmerk (een GUID).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Zijn de Acl's voor een Service-Principal Hoe kan ik correct ingesteld?

Wanneer u Acl's voor service-principals definieert, is het belang rijk dat u de object-ID (OID) van de *Service-Principal* gebruikt voor de app-registratie die u hebt gemaakt. Het is belang rijk te weten dat geregistreerde apps een afzonderlijke service-principal hebben in de specifieke Azure AD-Tenant. Geregistreerde apps hebben een OID die zichtbaar is in de Azure Portal, maar de *Service-Principal* heeft een andere (andere) OID.

Als u de OID wilt ophalen voor de service-principal die overeenkomt met een app-registratie `az ad sp show` , kunt u de opdracht gebruiken. Geef de toepassings-ID op als de para meter. Hier volgt een voor beeld voor het verkrijgen van de OID voor de service-principal die overeenkomt met een app-registratie met App ID = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Voer de volgende opdracht uit in de Azure CLI:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID wordt weer gegeven.

Wanneer u de juiste OID voor de Service-Principal hebt, gaat u naar de Storage Explorer Access-pagina **beheren** om het OID toe te voegen en de juiste machtigingen voor de OID toe te wijzen. Zorg ervoor dat u **Opslaan**selecteert.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Ondersteunt Data Lake Storage Gen2 overname van Acl's?

De toewijzingen van Azure RBAC nemen toe. Toewijzingen stroomt van de resources van het abonnement, de resource groep en het opslag account naar de container resource.

Acl's nemen niet over. Standaard-Acl's kunnen echter worden gebruikt om Acl's in te stellen voor onderliggende submappen en bestanden die zijn gemaakt in de bovenliggende map. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Waar kan ik meer informatie over het POSIX-model voor toegangsbeheer?

* [POSIX met behulp van toegangsbeheerlijsten op Linux](https://www.linux.com/news/posix-acls-linux)
* [De machtigings gids HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Veelgestelde vragen over POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL in Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL met behulp van toegangs beheer lijsten op Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Zie ook

* [Overzicht van Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)

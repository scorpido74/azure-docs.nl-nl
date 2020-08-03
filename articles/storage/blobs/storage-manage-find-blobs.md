---
title: Gegevens in Azure Blob Storage beheren en zoeken met Blob-index (preview)
description: Leer hoe u BLOB index Tags kunt gebruiken om blob-objecten te categoriseren, beheren en doorzoeken.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 5b41609ec2b7cc9880fb22a76b9e3b40c315bc3c
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499871"
---
# <a name="manage-and-find-data-on-azure-blob-storage-with-blob-index-preview"></a>Gegevens in Azure Blob Storage beheren en zoeken met Blob-index (preview)

Als gegevens sets groter en groter worden, kan het zoeken naar een specifiek object in een gegevens zee lastig en frustrerend zijn. BLOB index biedt mogelijkheden voor gegevens beheer en-detectie met behulp van sleutel kenmerken van index Tags, waarmee u objecten kunt categoriseren en zoeken binnen één container of in alle containers in uw opslag account. Later, als de vereisten van gegevens wijzigingen, kunnen objecten dynamisch opnieuw worden gecategoriseerd door de index Tags bij te werken terwijl de huidige container organisatie nog in-place is. Het gebruik van BLOB-index kan de ontwikkeling vereenvoudigen door het samen voegen van uw blobgegevens en de bijbehorende index kenmerken voor dezelfde service. bieden u de mogelijkheid om efficiënte en schaal bare toepassingen te bouwen met systeem eigen functies. 

Met Blob index kunt u:

- Uw blobs dynamisch categoriseren met index Tags voor sleutel waarden voor gegevens beheer
- Snel specifieke gecodeerde blobs vinden in één container of een hele opslag account
- Voorwaardelijk gedrag voor BLOB-Api's opgeven op basis van de evaluatie van index Tags
- Gebruik index Tags voor geavanceerde besturings elementen op BLOB-platform functies zoals [levenscyclus beheer](storage-lifecycle-management-concepts.md)

Denk na over het scenario waarin u miljoenen blobs in uw opslag account hebt geschreven en die toegankelijk zijn voor veel verschillende toepassingen. U wilt alle gerelateerde gegevens uit één project zoeken, maar u weet niet wat het bereik is omdat de gegevens kunnen worden verdeeld over meerdere containers met verschillende regels voor de naamgeving van blobs. U weet echter dat uw toepassingen alle gegevens uploaden met tags op basis van hun respectievelijke project en beschrijving. In plaats van miljoenen blobs te doorzoeken en namen en eigenschappen te vergelijken, kunt u gewoon gebruiken `Project = Contoso` als detectie criterium. Met Blob-index worden alle containers in het hele opslag account gefilterd, zodat u alleen de set van 50 blobs uit kunt vinden en retour neren `Project = Contoso` . 

Zie [BLOB-index gebruiken om gegevens te beheren en te zoeken](storage-blob-index-how-to.md)om aan de slag te gaan met voor beelden van het gebruik van BLOB-index.

## <a name="blob-index-tags-and-data-management"></a>Index Tags en Gegevensbeheer van blobs

Container-en BLOB-naam voor voegsels zijn eendimensionale categorisatie voor uw opgeslagen gegevens. Met Blob index kunt u nu meerdere dimensies categoriseren voor al uw [BLOB-gegevens typen (blok, toevoegen of pagina)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) met toegepaste kenmerk Tags. Deze multi-dimensionale categorisatie wordt systeem eigen geïndexeerd en beschikbaar gemaakt zodat u snel uw gegevens kunt opvragen en zoeken.

Houd rekening met de volgende vijf blobs in uw opslag account:
>
> container1/transaction.csv  
> container2/campaign.docx  
> Foto's/bannerphoto.png  
> archieven/voltooide/2019review.pdf  
> Logboeken/2020/01/01/logfile.txt  
>

Deze blobs zijn momenteel gescheiden met behulp van een voor voegsel van de container/virtuele map/BLOB-naam. Met Blob index kunt u een index label kenmerk van `Project = Contoso` op deze vijf blobs instellen om ze samen te categoriseren en daarbij hun huidige voorvoegsel organisatie te behouden. Dit elimineert de nood zaak om gegevens te verplaatsen door de mogelijkheid om gegevens te filteren en te zoeken met behulp van de multi-dimensionale index van het opslag platform.

## <a name="setting-blob-index-tags"></a>Labels voor BLOB-index instellen

BLOB-index Tags zijn sleutel-waardeparen die kunnen worden toegepast op nieuwe of bestaande objecten in uw opslag account. U kunt de index Tags tijdens het upload proces opgeven met behulp van de PutBlob-, Putblock List-of CopyBlob-bewerkingen en de optionele x-MS-Tags-header. Als u al blobs in uw opslag account hebt, kunt u SetBlobTags aanroepen met een opgemaakt XML-document waarin de kenmerken van de BLOB-index code worden opgegeven in de hoofd tekst van de aanvraag. 

Bekijk de volgende voor beelden van tags die kunnen worden ingesteld

U kunt één tag op uw BLOB Toep assen om te beschrijven wanneer de verwerking van uw gegevens is voltooid.
>
> "processedDate" = ' 2020-01-01 '
>
U kunt meerdere labels op uw BLOB Toep assen om de gegevens beter te beschrijven.
>
> "Project" = ' Contoso '  
> ' Ingedeeld ' = ' True '  
> "Status" = ' niet verwerkt '  
> "Priority" = "01" 
>

Als u de bestaande kenmerken van de index code wilt wijzigen, moet u eerst de bestaande label kenmerken ophalen, de label kenmerken wijzigen en vervangen door de SetBlobTags-bewerking. Als u alle index Tags uit de BLOB wilt verwijderen, roept u de SetBlobTags-bewerking aan zonder dat er label kenmerken zijn opgegeven. Als blob-index Tags zijn een subresource voor de inhoud van de BLOB-gegevens, wordt door SetBlobTags geen onderliggende inhoud gewijzigd en wordt de laatste wijzigings tijd of eTag (entiteits code) van de BLOB niet gewijzigd. U kunt index Tags maken of wijzigen voor alle huidige basis-blobs en eerdere versies. Labels op moment opnamen of zachte verwijderde blobs kunnen echter niet worden gewijzigd. 

De volgende limieten gelden voor BLOB-index Tags:
- Elke Blob kan Maxi maal 10 BLOB-index Tags hebben
- Label sleutels moeten tussen 1 en 128 tekens lang zijn
- Label waarden moeten tussen de 0 en 256 tekens lang zijn
- Code sleutels en waarden zijn hoofdletter gevoelig
- Code sleutels en waarden bieden alleen ondersteuning voor teken reeks gegevens typen. getallen, datums, tijden of speciale tekens worden opgeslagen als teken reeksen
- Code sleutels en waarden moeten voldoen aan de volgende naamgevings regels:
  - Alfanumerieke numerieke tekens: a-z, A-Z, 0-9
  - Speciale tekens: spatie, plus teken, minteken, punt, dubbele punt, gelijkteken, onderstrepings teken, slash

## <a name="getting-and-listing-blob-index-tags"></a>Labels voor BLOB-indexen ophalen en weer geven

BLOB-index Tags worden naast de BLOB-gegevens opgeslagen als subbron en kunnen onafhankelijk van de onderliggende BLOB-gegevens inhoud worden opgehaald. Eenmaal ingesteld kunnen de BLOB-index Tags voor één BLOB direct worden opgehaald en gecontroleerd met de bewerking GetBlobTags. De bewerking ListBlobs met `include:tags` para meter retourneert ook alle blobs in een container, samen met de toegepaste labels van de BLOB-index. 

Voor alle blobs met ten minste één BLOB-index label wordt het aantal x-MS-Tags geretourneerd in de ListBlobs-, GetBlob-en GetBlobProperties-bewerkingen, waarmee het aantal BLOB-index Tags dat in de BLOB bestaat, wordt aangegeven.

## <a name="finding-data-using-blob-index-tags"></a>Gegevens zoeken met behulp van BLOB-index Tags

Als er BLOB-index Tags zijn ingesteld op uw blobs, worden deze sleutel/waarde-kenmerken door de indexerings engine beschikbaar gemaakt in een multi-dimensionale index. Terwijl de index Tags op de BLOB bestaan en direct kunnen worden opgehaald, kan het enige tijd duren voordat de BLOB-index is bijgewerkt met de vernieuwde index label kenmerken. Zodra de BLOB-index is bijgewerkt, kunt u nu profiteren van de systeem eigen query-en detectie mogelijkheden die worden geboden door Blob Storage.

Met de bewerking FindBlobsByTags kunt u een gefilterde retourset van blobs ophalen waarvan de index Tags overeenkomen met een bepaalde BLOB-index query-expressie. BLOB-index ondersteunt filteren op alle containers in uw opslag account of u kunt het filteren op slechts één container bereiken. Omdat alle sleutels en waarden van de BLOB-index code teken reeksen zijn, gebruiken de ondersteunde relationele Opera tors een lexografische die wordt gesorteerd op de index label waarden.

De volgende criteria zijn van toepassing op filtering van BLOB-index:
-   Code sleutels moeten tussen dubbele aanhalings tekens worden geplaatst (")
-   Label waarden en container namen moeten tussen enkele aanhalings tekens worden geplaatst (')
-   Het teken @ mag alleen worden gefilterd op een specifieke container naam (bijvoorbeeld @container = containerName)
- Filters worden toegepast met lexografische sorteren op teken reeksen
-   Dezelfde gevarieerde bereik bewerkingen op dezelfde sleutel zijn ongeldig (bijvoorbeeld ' positie ' > ' 10 ' en ' rangs >= ' 15 ')
- Als u REST gebruikt voor het maken van een filter expressie, moeten de tekens URI-code ring zijn

In de onderstaande tabel ziet u alle geldige Opera tors voor FindBlobsByTags:

|  Operator  |  Beschrijving  | Voorbeeld |
|------------|---------------|---------|
|     =      |     Is gelijk aan     | "Status" = ' wordt uitgevoerd ' | 
|     >      |  Groter dan |  "Datum" > ' 2018-06-18 ' |
|     >=     |  Groter dan of gelijk aan | "Priority" >= ' 5 ' | 
|     <      |  Kleiner dan    | ' Leeftijd ' < ' 32 ' |
|     <=     |  Kleiner dan of gelijk aan  | ' Bedrijf ' <= ' Contoso ' |
|    EN     |  Logische en  | ' Positie ' >= ' 010 ' en ' Rank ' < ' 100 ' |
| @container |  Bereik naar een specifieke container   | @container= ' videofiles ' en ' status ' = ' gereed ' |

> [!NOTE]
> Zorg dat u bekend bent met lexicographical-volg orde bij het instellen en uitvoeren van query's op Tags.
> - Getallen worden vóór letters gesorteerd. Getallen worden gesorteerd op basis van het eerste cijfer.
> - Hoofd letters worden gesorteerd vóór kleine letters.
> - Symbolen zijn niet standaard. Sommige symbolen worden vóór numerieke waarden gesorteerd. Andere symbolen worden vóór of na letters gesorteerd.
>

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Voorwaardelijke BLOB-bewerkingen met Blob index Tags
In REST versies 2019-10-10 en hoger ondersteunen de meeste [BLOB service-api's](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) nu een voorwaardelijke header, x-MS-if-Tags, zodat de bewerking alleen kan worden uitgevoerd als aan de opgegeven voor waarde voor de BLOB-index is voldaan. Als niet aan de voor waarde wordt voldaan, krijgt u `error 412: The condition specified using HTTP conditional header(s) is not met` .

De header x-MS-if-Tags kan worden gecombineerd met de andere bestaande HTTP-voorwaardelijke headers (if-match, if-none-match, enz.).  Als er meerdere voorwaardelijke headers in een aanvraag worden gegeven, moeten ze allemaal de waarde True evalueren voordat de bewerking kan worden voltooid.  Alle voorwaardelijke headers worden in feite gecombineerd met logische en. 

In de onderstaande tabel ziet u alle geldige Opera tors voor voorwaardelijke bewerkingen:

|  Operator  |  Beschrijving  | Voorbeeld |
|------------|---------------|---------|
|     =      |     Is gelijk aan     | "Status" = ' wordt uitgevoerd ' |
|     <>     |   Is niet gelijk aan   | "Status"  <>  voltooid  | 
|     >      |  Groter dan |  "Datum" > ' 2018-06-18 ' |
|     >=     |  Groter dan of gelijk aan | "Priority" >= ' 5 ' | 
|     <      |  Kleiner dan    | ' Leeftijd ' < ' 32 ' |
|     <=     |  Kleiner dan of gelijk aan  | ' Bedrijf ' <= ' Contoso ' |
|    EN     |  Logische en  | ' Positie ' >= ' 010 ' en ' Rank ' < ' 100 ' |
|     OF     |  Logische of   | "Status" = ' done ' of ' Priority ' >= ' 05 ' |

> [!NOTE]
> Er zijn twee extra Opera Tors, niet gelijk aan en logische of, die zijn toegestaan in de header Conditional x-MS-if-Tags voor een BLOB-bewerking, maar die niet aanwezig zijn in de FindBlobsByTags-bewerking.

## <a name="platform-integrations-with-blob-index-tags"></a>Platform integraties met Blob index Tags

Met de labels van BLOB-indexen kunt u niet alleen uw blobgegevens categoriseren, beheren en zoeken, maar ook integraties met andere Blob service functies, zoals [levenscyclus beheer](storage-lifecycle-management-concepts.md). 

### <a name="lifecycle-management"></a>Levenscyclus beheer

Met het nieuwe blobIndexMatch als een regel filter in levenscyclus beheer kunt u gegevens verplaatsen naar koele lagen of gegevens verwijderen op basis van de index Tags die zijn toegepast op uw blobs. Zo kunt u nauw keuriger in uw regels en alleen gegevens verplaatsen of verwijderen als ze overeenkomen met de opgegeven label criteria.

U kunt een overeenkomende BLOB-index instellen als een zelfstandig filter dat is ingesteld in een levenscyclus regel om acties op gelabelde gegevens toe te passen. U kunt ook een voor voegsel combi neren en een BLOB-index die overeenkomt met meer specifieke gegevens sets. Het Toep assen van meerdere filters op een levenscyclus regel is een logische en-bewerking, zodat de actie alleen van toepassing is als alle filter criteria overeenkomen. 

De volgende voor beeld-levenscyclus beheer regel is van toepassing op blok-blobs in de container ' videofiles ' en lagen van blobs om opslag alleen te archiveren als de gegevens overeenkomen met de criteria van de BLOB-index code van ```"Status" = 'Processed' AND "Source" == 'RAW'``` .

# <a name="portal"></a>[Portal](#tab/azure-portal)
![Voor beeld van een overeenkomende regel voor een BLOB-index voor levenscyclus beheer in Azure Portal](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)
   
# <a name="json"></a>[JSON](#tab/json)
```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```
---

## <a name="permissions-and-authorization"></a>Machtigingen en autorisatie

U kunt toegang tot de BLOB-index toestaan met een van de volgende methoden:

- Door gebruik te maken van op rollen gebaseerd toegangs beheer (RBAC) om machtigingen te verlenen aan een Azure Active Directory (Azure AD)-beveiligingsprincipal. Micro soft raadt u aan Azure AD te gebruiken voor superieure beveiliging en gebruiks gemak. Zie [toegang tot blobs en wacht rijen toestaan met Azure Active Directory](../common/storage-auth-aad.md)voor meer informatie over het gebruik van Azure AD met Blob-bewerkingen.
- Door gebruik te maken van een Shared Access Signature (SAS) voor het delegeren van toegang tot de BLOB-index. Zie voor meer informatie over gedeelde toegangs handtekeningen [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../common/storage-sas-overview.md).
- Door de toegangs sleutels voor het account te gebruiken om bewerkingen met gedeelde sleutel te autoriseren. Zie [autoriseren met gedeelde sleutel](/rest/api/storageservices/authorize-with-shared-key)voor meer informatie.

BLOB-index Tags zijn een subresource voor de BLOB-gegevens. Een gebruiker met machtigingen of een SAS-token om blobs te lezen of te schrijven heeft mogelijk geen toegang tot de index Tags van de blob. 

### <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer 
Aanroepers die gebruikmaken van een [Aad-identiteit](../common/storage-auth-aad.md) , kunnen de volgende machtigingen krijgen voor het werken met Blob-index Tags. 

|   BLOB-bewerkingen   |  RBAC-actie   |
|---------------------|----------------|
| Blobs zoeken op labels  | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/filter/actie |
| BLOB-Tags instellen         | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/Tags/schrijven | 
| BLOB-labels ophalen         | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/tags/lezen |

Aanvullende machtigingen die gescheiden zijn van de onderliggende BLOB-gegevens, zijn vereist voor het uitvoeren van de labels. De rol voor het overdragen van blobgegevens wordt aan alle drie van deze machtigingen toegekend. De gegevens lezer van de opslag-BLOB krijgt de alleen blobs zoeken op labels en de machtigingen voor BLOB-labels ophalen.

### <a name="sas-permissions"></a>SAS-machtigingen 
Aanroepers die gebruikmaken van een [Shared Access Signature (SAS),](../common/storage-sas-overview.md) kunnen machtigingen met een bereik krijgen voor het uitvoeren van BLOB-Tags.
#### <a name="blob-sas"></a>BLOB SAS
De volgende machtigingen kunnen worden verleend in een Blob service SAS om toegang tot BLOB-index tags toe te staan. Alleen lees-en schrijf machtigingen voor de BLOB zijn voldoende om de index Tags te kunnen lezen of schrijven.

|  Machtiging  |  URI-symbool  | Toegestane bewerkingen |
|--------------|--------------|--------------------|
|  Index Tags  |      t         | BLOB-index Tags voor een BLOB ophalen en instellen |

#### <a name="container-sas"></a>Container SAS
De volgende machtigingen kunnen worden verleend in container service-SA'S om filteren op BLOB-tags toe te staan.  De machtiging voor de BLOB-lijst is niet voldoende om filter-blobs toe te staan op index Tags.

|  Machtiging  |  URI-symbool  | Toegestane bewerkingen |
|--------------|--------------|--------------------|
| Index Tags     |      f       | Blobs met Blob-index Tags zoeken | 

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Kiezen tussen de meta gegevens-en BLOB-index Tags 
Zowel BLOB index Tags als meta gegevens bieden de mogelijkheid om wille keurige, door de gebruiker gedefinieerde sleutel/waarde op te slaan naast een BLOB-resource. Beide kunnen worden opgehaald en direct worden ingesteld, zonder de inhoud van de BLOB te retour neren of te wijzigen. Het is mogelijk om meta gegevens en index Tags te gebruiken.

Er worden echter alleen BLOB index Tags automatisch geïndexeerd en query's gemaakt door de systeem eigen BLOB-service. Meta gegevens kunnen niet zelf worden geïndexeerd en er wordt een query uitgevoerd, tenzij u een afzonderlijke service, zoals [Azure Search](../../search/search-blob-ai-integration.md), gebruikt. BLOB index Tags hebben ook extra machtigingen voor lezen/filteren en schrijven die gescheiden zijn van de onderliggende BLOB-gegevens. Meta gegevens gebruiken dezelfde machtigingen als de BLOB en worden geretourneerd als HTTP-headers in de GetBlob-of GetBlobProperties-bewerkingen. BLOB-index Tags worden op rest versleuteld met een door [micro soft beheerde sleutel](../common/storage-service-encryption.md) , terwijl meta gegevens worden versleuteld met dezelfde versleutelings sleutel die is opgegeven voor BLOB-gegevens. 

De volgende tabel bevat een overzicht van de verschillen tussen de index Tags voor meta gegevens en blob:

|              |   Metagegevens   |   Index Tags voor BLOB  |
|--------------|--------------|--------------------|
| **Limieten**         | Geen numerieke limiet; totaal 8 KB; niet hoofdletter gevoelig | 10 labels per BLOB Max; 768 bytes per tag; hoofdletter gevoelig |
| **Updates**      | Niet toegestaan op de archief laag; SetBlobMetadata vervangt alle bestaande meta gegevens; SetBlobMetadata wijzigt de laatst gewijzigde-tijd van de BLOB | Toegestaan voor alle toegangs lagen. SetBlobTags vervangt alle bestaande tags. SetBlobTags wijzigt de laatste wijzigings tijd van de BLOB niet |
| **Storage**        | Opgeslagen met de BLOB-gegevens |  Subresource voor de BLOB-gegevens | 
| **& Query's indexeren** | N/A systeem eigen; moet een afzonderlijke service gebruiken, zoals Azure Search | Ja, systeem eigen indexering en query functies ingebouwd in Blob Storage |
| **Versleuteling** | Versleuteld op rest met dezelfde versleutelings sleutel die wordt gebruikt voor BLOB-gegevens |  Versleuteld op rest met een door micro soft beheerde versleutelings sleutel |
| **Prijzen**   | De grootte van meta gegevens is opgenomen in de opslag kosten voor een BLOB |    Vaste kosten per index code | 
| **Antwoord voor koptekst** | Meta gegevens die zijn geretourneerd als headers in GetBlob en GetBlobProperties | TagCount geretourneerd in GetBlob of GetBlobProperties; Tags die alleen worden geretourneerd in GetBlobTags en ListBlobs |
| **Machtigingen**  |    Lees-of schrijf machtigingen voor BLOB-gegevens worden uitgebreid naar meta gegevens |    Er zijn aanvullende machtigingen vereist voor het lezen/filteren of schrijven van Tags |

## <a name="pricing"></a>Prijzen
Prijzen voor BLOB-indexen zijn momenteel beschikbaar als open bare preview-versie en kunnen worden gewijzigd voor algemene Beschik baarheid. Klanten betalen voor het totale aantal BLOB-index Tags binnen een opslag account, gemiddeld over de maand. Er zijn geen kosten voor de indexerings engine. Aanvragen voor SetBlobTags, GetBlobTags en FindBlobsByTags worden in rekening gebracht op basis van hun respectieve bewerkings typen. Zie [prijzen voor blok-BLOB voor meer informatie](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Regionale Beschik baarheid en ondersteuning voor opslag accounts

BLOB-index is momenteel alleen beschikbaar voor Algemeen v2-accounts (GPv2) met een hiërarchische naam ruimte (HNS) is uitgeschakeld. Algemeen-accounts (GPV1) worden niet ondersteund, maar u kunt een upgrade uitvoeren voor een GPv1-account naar een GPv2-account. Zie [overzicht van Azure Storage-account](../common/storage-account-overview.md)voor meer informatie over opslag accounts.

In de open bare preview-versie is Blob index momenteel alleen beschikbaar in de volgende regio's selecteren:
- Canada - midden
- Canada - oost
- Frankrijk - centraal
- Frankrijk - zuid

Zie [BLOB-index gebruiken om gegevens te beheren en te zoeken](storage-blob-index-how-to.md)om aan de slag te gaan.

> [!IMPORTANT]
> Zie de sectie voor waarden in dit artikel. Zie de sectie uw abonnement registreren in dit artikel voor meer informatie over het inschrijven van de preview-versie. U moet uw abonnement registreren voordat u de BLOB-index in uw opslag accounts kunt gebruiken.

### <a name="register-your-subscription-preview"></a>Uw abonnement registreren (preview-versie)
Omdat de BLOB-index alleen in open bare preview is, moet u uw abonnement registreren voordat u de functie kunt gebruiken. Voer de volgende Power shell-of CLI-opdrachten uit om een aanvraag in te dienen.

#### <a name="register-by-using-powershell"></a>Registreren met behulp van Power shell
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Registreren met behulp van Azure CLI
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a>Voor waarden en bekende problemen (preview-versie)
In deze sectie worden bekende problemen en voor waarden in de huidige open bare preview van de BLOB-index beschreven. Net als bij de meeste previews mag deze functie niet worden gebruikt voor werk belastingen van de productie, totdat het het gedrag bereikt dat kan veranderen.

-   Voor de preview moet u eerst uw abonnement registreren voordat u de BLOB-index voor uw opslag account in de preview-regio's kunt gebruiken.
-   Alleen GPv2-accounts worden momenteel ondersteund in de preview-versie. BLOB-, BlockBlobStorage-en HNS-DataLake Gen2-accounts worden momenteel niet ondersteund met een BLOB-index. GPv1-accounts worden niet ondersteund.
-   Als u pagina-blobs met index Tags uploadt, blijven de tags op dit moment behouden. U moet de tags instellen na het uploaden van een pagina-blob.
-   Wanneer het bereik van het filter is beperkt tot één container, @container kan de eigenschap alleen worden door gegeven als alle index Tags in de filter expressie gelijkheids controles zijn (Key = waarde). 
-   Wanneer u de operator Range gebruikt met de voor waarde, kunt u alleen dezelfde index code sleutel naam opgeven (leeftijd > ' 013 ' en leeftijd < ' 100 ').
-   Versie beheer en BLOB-index worden momenteel niet ondersteund. BLOB-index Tags blijven behouden voor versies, maar worden momenteel niet door gegeven aan de BLOB-index engine.
-   Failover van account wordt momenteel niet ondersteund. De BLOB-index wordt mogelijk niet goed bijgewerkt na een failover.
-   Levenscyclus beheer ondersteunt momenteel alleen gelijkheids controles met overeenkomende BLOB-index.
-   CopyBlob kopieert geen blob-index Tags van de bron-BLOB naar de nieuwe doel-blob. U kunt de labels opgeven die u wilt Toep assen op de doel-BLOB tijdens de Kopieer bewerking. 
- CopyBlob (asynchroon kopiëren) vanuit een ander opslag account met toegepaste labels op de doel-BLOB zorgt er momenteel voor dat de BLOB-index engine de BLOB en de bijbehorende tags in de Filterset niet retourneert. Het is raadzaam om CopyBlob van URL (Sync Copy) te gebruiken in de tijdelijke oplossing.
-   Tags blijven behouden bij het maken van moment opnamen. het promo veren van een moment opname wordt momenteel niet ondersteund en kan resulteren in een lege tag-set.

## <a name="faq"></a>Veelgestelde vragen

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>Kan de BLOB-index Help me filteren en inhoud in mijn blobs doorzoeken? 
Nee, index Tags van blobs kunnen u helpen bij het vinden van de blobs die u zoekt. Als u wilt zoeken in uw blobs, gebruikt u query versnelling of Azure Search.

### <a name="are-there-any-special-considerations-regarding-blob-index-tag-values"></a>Zijn er speciale overwegingen met betrekking tot de waarden van BLOB-index Tags?
BLOB-index Tags bieden alleen ondersteuning voor teken reeks gegevens typen en query's retour neren resultaten met lexicographical-volg orde. Voor getallen wordt het aanbevolen om het nummer in te vullen. Voor datum en tijd is het raadzaam om op te slaan als een ISO 8601-compatibele indeling.

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>Zijn er labels voor BLOB-indexen en Azure Resource Manager labels?
Nee, met Azure Resource Manager Tags kunt u beheer vlak resources zoals abonnementen, resource groepen en opslag accounts ordenen. BLOB-index Tags bieden object beheer en detectie op gegevens vlak bronnen zoals blobs in een opslag account.

## <a name="next-steps"></a>Volgende stappen

Zie [BLOB-index gebruiken om gegevens te beheren en te zoeken](storage-blob-index-how-to.md)voor een voor beeld van het gebruik van BLOB-index.

Meer informatie over [levenscyclus beheer](storage-lifecycle-management-concepts.md) en het instellen van een regel met overeenkomende BLOB-index.


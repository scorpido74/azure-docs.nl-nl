---
title: Broncodebeheer
description: Meer informatie over het configureren van bronbeheer in Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/09/2019
ms.openlocfilehash: 2108f2f9098fe6da8ee4666b30605bed14164484
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414725"
---
# <a name="source-control-in-azure-data-factory"></a>Bronbeheer in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De gebruikersinterface-ervaring (Azure Data Factory) heeft twee ervaringen beschikbaar voor visuele ontwerpen:

- Auteur rechtstreeks met de Data Factory-service
- Auteur met Azure Repos Git- of GitHub-integratie

> [!NOTE]
> Alleen het rechtstreeks schrijven met de Data Factory-service wordt ondersteund in de Azure Government Cloud.

## <a name="author-directly-with-the-data-factory-service"></a>Auteur rechtstreeks met de Data Factory-service

Tijdens het schrijven rechtstreeks met de Data Factory-service, de enige manier om wijzigingen op te slaan is via de **knop Alles publiceren.** Eenmaal geklikt, worden alle wijzigingen die u hebt aangebracht, rechtstreeks gepubliceerd in de Data Factory-service. 

![Publicatiemodus](media/author-visually/data-factory-publish.png)

Rechtstreeks schrijven met de Data Factory-service heeft de volgende beperkingen:

- De service Data Factory bevat geen opslagplaats voor het opslaan van de JSON-entiteiten voor uw wijzigingen.
- De Data Factory-service is niet geoptimaliseerd voor samenwerking of versiebeheer.

> [!NOTE]
> Rechtstreeks ontwerpen met de Data Factory-service wordt uitgeschakeld in de UX van Azure Data Factory wanneer een Git-repository is geconfigureerd. Wijzigingen kunnen rechtstreeks in de service worden aangebracht via PowerShell of een SDK.

## <a name="author-with-azure-repos-git-integration"></a>Ontwerpen met Git-integratie van Azure-opslagplaatsen

Visuele ontwerpen met Azure Repos Git-integratie ondersteunt bronbeheer en samenwerking voor het werken aan uw pijplijnen in gegevensfabrieken. U een gegevensfabriek koppelen aan een Azure Repos Git-organisatieopslagplaats voor bronbeheer, samenwerking, versiebeheer, enzovoort. Eén Azure Repos Git-organisatie kan meerdere opslagplaatsen hebben, maar een Azure Repos Git-repository kan worden gekoppeld aan slechts één gegevensfabriek. Als u geen Azure Repos-organisatie of -opslagplaats hebt, volgt u [deze instructies](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) om uw resources te maken.

> [!NOTE]
> U script- en gegevensbestanden opslaan in een Azure Repos Git-opslagplaats. U moet de bestanden echter handmatig uploaden naar Azure Storage. Een Pijplijn gegevensfabriek uploadt niet automatisch script- of gegevensbestanden die zijn opgeslagen in een Azure Repos Git-opslagplaats naar Azure Storage.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Een Azure Repos Git-opslagplaats configureren met Azure Data Factory

U een Azure Repos Git-opslagplaats configureren met een gegevensfabriek via twee methoden.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Configuratiemethode 1: startpagina Azure Data Factory

Selecteer op de startpagina van Azure Data Factory de optie **Coderepository instellen**.

![Een Azure Repos-coderepository configureren](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Configuratiemethode 2: UX-ontwerpcanvas
Selecteer in het ux-ontwerpcanvas azure datafactory het vervolgkeuzemenu **Gegevensfabriek** en selecteer **Coderepository instellen**.

![De coderepository-instellingen voor UX-ontwerpen configureren](media/author-visually/configure-repo-2.png)

Beide methoden openen het configuratievenster voor archiefinstellingen.

![De instellingen voor codeopslagplaatsconfigureren](media/author-visually/repo-settings.png)

In het configuratiedeelvenster worden de volgende azure repos-coderepository-instellingen weergegeven:

| Instelling | Beschrijving | Waarde |
|:--- |:--- |:--- |
| **Repository Type** | Het type Azure Repos-coderepository.<br/> | Azure DevOps Git of GitHub |
| **Azure Active Directory** | Uw Azure AD-tenantnaam. | `<your tenant name>` |
| **Azure Repos-organisatie** | Uw naam van uw Azure Repos-organisatie. U de naam van `https://{organization name}.visualstudio.com`uw Azure Repos-organisatie zoeken op. U [zich aanmelden bij uw Azure Repos-organisatie](https://www.visualstudio.com/team-services/git/) om toegang te krijgen tot uw Visual Studio-profiel en uw repositories en projecten te bekijken. | `<your organization name>` |
| **Projectname** | Uw Azure Repos-projectnaam. U uw Azure Repos-projectnaam zoeken op `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **RepositoryName** | Uw naam van uw Azure Repos-coderepository. Azure Repos-projecten bevatten Git-repositories om uw broncode te beheren naarmate uw project groeit. U een nieuwe opslagplaats maken of een bestaande opslagplaats gebruiken die al in uw project is. | `<your Azure Repos code repository name>` |
| **Samenwerkingsbranche** | Uw Azure Repos-samenwerkingsbranch die wordt gebruikt voor publicatie. Standaard is `master`het. Wijzig deze instelling voor het geval u bronnen van een andere branch wilt publiceren. | `<your collaboration branch name>` |
| **Hoofdmap** | Uw hoofdmap in uw Azure Repos-samenwerkingsbranch. | `<your root folder name>` |
| **Bestaande gegevensfabriekbronnen importeren in opslagplaats** | Hiermee geeft u op of bestaande gegevensfabrieksbronnen uit **het canvas ux-ontwerpen** moeten worden geïmporteerd in een Azure Repos Git-opslagplaats. Selecteer het vak om uw gegevensfabriekresources te importeren in de bijbehorende Git-opslagplaats in JSON-indeling. Met deze actie wordt elke resource afzonderlijk geëxporteerd (dat wil zeggen dat de gekoppelde services en gegevenssets worden geëxporteerd naar afzonderlijke JSON's). Wanneer dit vak niet is geselecteerd, worden de bestaande resources niet geïmporteerd. | Geselecteerd (standaard) |
| **Vertakking om resource in te importeren** | Hiermee geeft u op in welke vertakking de bronnen van de gegevensfabriek (pijplijnen, gegevenssets, gekoppelde services enz.) worden geïmporteerd. U resources importeren in een van de volgende branches: a. Samenwerking b. Nieuwe c maken. Bestaande gebruiken |  |

> [!NOTE]
> Als u Microsoft Edge gebruikt en geen waarden ziet in de vervolgkeuzelijst Azure DevOps-account, voegt u https://*.visualstudio.com toe aan de lijst met vertrouwde sites.

### <a name="use-a-different-azure-active-directory-tenant"></a>Een andere Azure Active Directory-tenant gebruiken

U kunt een Git-opslagplaats maken in een andere Azure Active Directory-tenant. Als u een andere Azure AD-tenant wilt opgeven, moet u beheerdersmachtigingen hebben voor het Azure-abonnement dat u gebruikt.

### <a name="use-your-personal-microsoft-account"></a>Uw persoonlijke Microsoft-account gebruiken

Als u een persoonlijk Microsoft-account wilt gebruiken voor Git-integratie, u uw persoonlijke Azure Repo koppelen aan de Active Directory van uw organisatie.

1. Voeg als gast uw persoonlijke Microsoft-account toe aan de Active Directory van uw organisatie. Zie [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen in de Azure-portal](../active-directory/b2b/add-users-administrator.md)voor meer informatie.

2. Meld u aan bij de Azure-portal met uw persoonlijke Microsoft-account. Schakel vervolgens over naar de Active Directory van uw organisatie.

3. Ga naar de azure devOps-sectie, waar u nu uw persoonlijke repo ziet. Selecteer de repo en maak verbinding met Active Directory.

Na deze configuratiestappen is uw persoonlijke repo beschikbaar wanneer u Git-integratie instelt in de Gebruikersinterface van de Gegevensfabriek.

Zie [Uw Azure DevOps-organisatie verbinden met Azure Active Directory voor](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)meer informatie over het verbinden van Azure Repos met de Active Directory van uw organisatie.

## <a name="author-with-github-integration"></a>Ontwerpen met GitHub-integratie

Visueel ontwerpen met GitHub-integratie ondersteunt bronbeheer en samenwerking voor het werken aan uw pijplijnen in gegevensfabrieken. U een gegevensfabriek koppelen aan een GitHub-accountopslagplaats voor bronbeheer, samenwerking, versiebeheer. Eén GitHub-account kan meerdere repositories hebben, maar een GitHub-repository kan worden gekoppeld aan slechts één gegevensfabriek. Als je geen GitHub-account of repository hebt, volg je [deze instructies](https://github.com/join) om je bronnen te maken.

De GitHub-integratie met Data Factory ondersteunt zowel [https://github.com](https://github.com)openbare GitHub (dat wil zeggen) als GitHub Enterprise. U zowel openbare als private GitHub-repositories gebruiken met Data Factory zolang u hebt gelezen en schrijf toestemming voor de repository in GitHub.

Als u een GitHub-repo wilt configureren, moet u beheerdersmachtigingen hebben voor het Azure-abonnement dat u gebruikt.

Bekijk de volgende video voor een negen minuten durende introductie en demonstratie van deze functie:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Een GitHub-opslagplaats configureren met Azure Data Factory

U een GitHub-opslagplaats met een gegevensfabriek configureren via twee methoden.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Configuratiemethode 1: startpagina Azure Data Factory

Selecteer op de startpagina van Azure Data Factory de optie **Coderepository instellen**.

![Een Azure Repos-coderepository configureren](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Configuratiemethode 2: UX-ontwerpcanvas

Selecteer in het ux-ontwerpcanvas azure datafactory het vervolgkeuzemenu **Gegevensfabriek** en selecteer **Coderepository instellen**.

![De coderepository-instellingen voor UX-ontwerpen configureren](media/author-visually/configure-repo-2.png)

Beide methoden openen het configuratievenster voor archiefinstellingen.

![Instellingen voor GitHub-repository](media/author-visually/github-integration-image2.png)

In het configuratiedeelvenster worden de volgende Instellingen voor de GitHub-opslagplaats weergegeven:

| **Instelling** | **Beschrijving**  | **Waarde**  |
|:--- |:--- |:--- |
| **Repository Type** | Het type Azure Repos-coderepository. | GitHub |
| **GitHub Enterprise gebruiken** | Selectievakje om GitHub Enterprise te selecteren | niet geselecteerd (standaard) |
| **GitHub Enterprise URL** | De URL van de GitHub Enterprise-hoofdlocatie (moet HTTPS zijn voor de lokale GitHub Enterprise-server). Bijvoorbeeld: https://github.mydomain.com. Alleen vereist als **Gebruik GitHub Enterprise** is geselecteerd | `<your GitHub enterprise url>` |                                                           
| **GitHub-account** | Uw GitHub-accountnaam. Deze naam is te\/vinden op https: /github.com/{accountname}/{repository name}. Als u naar deze pagina navigeert, moet u GitHub OAuth-referenties invoeren op uw GitHub-account. | `<your GitHub account name>` |
| **Naam van opslagplaats**  | Uw GitHub-coderepository-naam. GitHub-accounts bevatten Git-repositories om uw broncode te beheren. Je een nieuwe repository maken of een bestaande repository gebruiken die al in je account staat. | `<your repository name>` |
| **Samenwerkingsbranche** | Uw GitHub-samenwerkingskantoor dat wordt gebruikt voor publicatie. Standaard is het master. Wijzig deze instelling voor het geval u bronnen van een andere branch wilt publiceren. | `<your collaboration branch>` |
| **Hoofdmap** | Je hoofdmap in je GitHub-samenwerkingsbranch. |`<your root folder name>` |
| **Bestaande gegevensfabriekbronnen importeren in opslagplaats** | Hiermee geeft u op of bestaande gegevensfabrieksbronnen uit het UX-ontwerpcanvas moeten worden geïmporteerd in een GitHub-opslagplaats. Selecteer het vak om uw gegevensfabriekresources te importeren in de bijbehorende Git-opslagplaats in JSON-indeling. Met deze actie wordt elke resource afzonderlijk geëxporteerd (dat wil zeggen dat de gekoppelde services en gegevenssets worden geëxporteerd naar afzonderlijke JSON's). Wanneer dit vak niet is geselecteerd, worden de bestaande resources niet geïmporteerd. | Geselecteerd (standaard) |
| **Vertakking om resource in te importeren** | Hiermee geeft u op in welke vertakking de bronnen van de gegevensfabriek (pijplijnen, gegevenssets, gekoppelde services enz.) worden geïmporteerd. U resources importeren in een van de volgende branches: a. Samenwerking b. Nieuwe c maken. Bestaande gebruiken |  |

### <a name="known-github-limitations"></a>Bekende GitHub beperkingen

- U script- en gegevensbestanden opslaan in een GitHub-opslagplaats. U moet de bestanden echter handmatig uploaden naar Azure Storage. Een Data Factory-pijplijn uploadt niet automatisch script- of gegevensbestanden die zijn opgeslagen in een GitHub-opslagplaats naar Azure Storage.

- GitHub Enterprise met een versie ouder dan 2.14.0 werkt niet in de Microsoft Edge-browser.

- GitHub-integratie met de visuele ontwerptools van Data Factory werkt alleen in de algemeen beschikbare versie van Data Factory.

- Er kunnen maximaal 1.000 entiteiten per resourcetype (zoals pijplijnen en gegevenssets) worden opgehaald uit één GitHub-filiaal. Als deze limiet wordt bereikt, wordt voorgesteld om uw middelen te splitsen in aparte fabrieken. Azure DevOps Git heeft deze beperking niet.

## <a name="switch-to-a-different-git-repo"></a>Overschakelen naar een andere Git repo

Als u wilt overschakelen naar een andere Git-repo, klikt u op het pictogram **Instellingen voor Git Repo** in de rechterbovenhoek van de overzichtspagina gegevensfabriek. Als u het pictogram niet zien, moet u de cache van uw lokale browser wissen. Selecteer het pictogram om de koppeling met de huidige repo te verwijderen.

![Pictogram Git](media/author-visually/remove-repo.png)

Zodra het deelvenster Archiefinstellingen wordt weergegeven, selecteert u **Git verwijderen**. Voer de naam van uw gegevensfabriek in en klik op **Bevestigen** om de Git-opslagplaats te verwijderen die is gekoppeld aan uw gegevensfabriek.

![De koppeling met de huidige Git-repo verwijderen](media/author-visually/remove-repo2.png)

Nadat u de koppeling met de huidige repo hebt verwijderd, u uw Git-instellingen configureren om een andere repo te gebruiken en vervolgens bestaande gegevensfabrieksbronnen importeren in de nieuwe repo. 

## <a name="version-control"></a>Versiebeheer

Versiebeheersystemen (ook wel _bronbeheer_genoemd) laten ontwikkelaars samenwerken aan code en wijzigingen bijhouden die zijn aangebracht in de codebasis. Broncontrole is een essentieel hulpmiddel voor multi-developer projecten.

### <a name="creating-feature-branches"></a>Functiebranches maken

Elke Azure Repos Git-opslagplaats die is gekoppeld aan een gegevensfabriek heeft een samenwerkingsbranch. (`master` is de standaard samenwerkingsbranch). Gebruikers kunnen ook functiebranches maken door op **+ Nieuwe branch** te klikken in de vervolgkeuzelijst branch. Zodra het nieuwe vertakking wordt weergegeven, voert u de naam van uw functievertakking in.

![Een nieuwe vertakking maken](media/author-visually/new-branch.png)

Wanneer u klaar bent om de wijzigingen van uw functievertakking samen te voegen naar uw samenwerkingsbranch, klikt u op de vervolgkeuzelijst branch en selecteert **u Trekaanvraag maken**. Met deze actie gaat u naar Azure Repos Git, waar u pull-aanvragen indienen, codebeoordelingen uitvoeren en wijzigingen samenvoegen in uw samenwerkingsbranche. (`master` is de standaard). U mag alleen publiceren naar de Data Factory-service vanuit uw samenwerkingsbranche. 

![Een nieuw pull-verzoek maken](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Publicatie-instellingen configureren

Als u de publicatiebranch wilt configureren - dat wil zeggen `publish_config.json` de vertakking waar Resource Manager-sjablonen worden opgeslagen , voegt u een bestand toe aan de hoofdmap in de samenwerkingsbranch. Data Factory leest dit bestand, `publishBranch`zoekt naar het veld en maakt een nieuwe branch (als deze nog niet bestaat) met de opgegeven waarde. Vervolgens worden alle Resource Manager-sjablonen opgeslagen op de opgegeven locatie. Bijvoorbeeld:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Wanneer u een nieuwe publicatiebranch opgeeft, verwijdert Data Factory de vorige publicatiebranch niet. Als u de vorige publicatiebranch wilt verwijderen, verwijdert u deze handmatig.

> [!NOTE]
> Data Factory leest `publish_config.json` het bestand alleen wanneer het de fabriek laadt. Als u de fabriek al in de portal hebt geladen, vernieuwt u de browser om de wijzigingen van kracht te laten worden.

### <a name="publish-code-changes"></a>Codewijzigingen publiceren

Nadat u wijzigingen in de`master` samenwerkingsbranch hebt samengevoegd (is de standaardinstelling), klikt u op **Publiceren** om uw codewijzigingen handmatig te publiceren in de hoofdvertakking in de service Gegevensfabriek.

![Wijzigingen in de datafabriekservice publiceren](media/author-visually/publish-changes.png)

Er wordt een zijvenster geopend waarin u bevestigt dat de publicatiebranch en in afwachting van wijzigingen juist zijn. Zodra u uw wijzigingen hebt geverifieerd, klikt u op **OK** om de publicatie te bevestigen.

![De juiste publicatiebranch bevestigen](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> De hoofdbranch is niet representatief voor wat er wordt geïmplementeerd in de Data Factory-service. De hoofdvertakking *moet* handmatig worden gepubliceerd naar de Data Factory-service.

## <a name="advantages-of-git-integration"></a>Voordelen van Git-integratie

-   **Broncontrole**. Aangezien uw werklast van uw gegevensfabriek cruciaal wordt, wilt u uw fabriek integreren met Git om verschillende voordelen voor bronbeheer te benutten, zoals:
    -   Mogelijkheid om wijzigingen bij te houden/te controleren.
    -   Mogelijkheid om wijzigingen die bugs geïntroduceerd terug te keren.
-   **Gedeeltelijke saves**. Als u veel wijzigingen aanbrengt in uw fabriek, zult u zich realiseren dat u in de normale LIVE-modus uw wijzigingen niet als concept opslaan, omdat u er niet klaar voor bent of u uw wijzigingen niet wilt verliezen in het geval uw computer crasht. Met Git-integratie u uw wijzigingen stapsgewijs blijven opslaan en alleen publiceren naar de fabriek wanneer u er klaar voor bent. Git fungeert als een enscenering plaats voor uw werk, totdat u uw wijzigingen getest naar uw tevredenheid.
-   **Samenwerking en controle**. Als je meerdere teamleden hebt die deelnemen aan dezelfde fabriek, kun je je teamgenoten met elkaar laten samenwerken via een codereviewproces. U uw fabriek ook zo instellen dat niet elke bijdrager aan de fabriek toestemming heeft om naar de fabriek te implementeren. Teamleden mogen alleen wijzigingen aanbrengen via Git, maar alleen bepaalde mensen in het team mogen de wijzigingen in de fabriek "publiceren".
-   **Diffs weergeven**. In de Git-modus krijg je een mooi diff van de payload te zien die op het punt staat om gepubliceerd te worden naar de fabriek. In deze diff ziet u alle resources/entiteiten die zijn gewijzigd/toegevoegd/verwijderd sinds de laatste keer dat u in uw fabriek bent gepubliceerd. Op basis van deze diff u verder gaan met publiceren, of teruggaan en uw wijzigingen controleren en later terugkomen.
-   **Betere CI/CD**. Als u de Git-modus gebruikt, u de releasepijplijn zo configureren dat deze automatisch wordt geactiveerd zodra er wijzigingen zijn aangebracht in de dev-fabriek. U ook de eigenschappen in uw fabriek aanpassen die beschikbaar zijn als parameters in de sjabloon Resourcemanager. Het kan handig zijn om alleen de vereiste set eigenschappen als parameters te houden en al het andere hard gecodeerd te hebben.
-   **Betere prestaties**. Een gemiddelde fabriek laadt tien keer sneller in de Git-modus dan in de reguliere LIVE-modus, omdat de bronnen worden gedownload via Git.

## <a name="best-practices-for-git-integration"></a>Aanbevolen procedures voor Git-integratie

### <a name="permissions"></a>Machtigingen

Meestal wilt u niet dat elk teamlid machtigingen heeft om de fabriek bij te werken. De volgende instellingen voor machtigingen worden aanbevolen:

*   Alle teamleden moeten machtigingen voor de gegevensfabriek hebben gelezen.
*   Alleen een selecte groep mensen mag publiceren naar de fabriek. Om dit te doen, moeten ze de rol **van Data Factory-bijdrager** hebben op de resourcegroep waarin de fabriek zich bevindt. Zie Rollen en machtigingen [voor Azure Data Factory voor](concepts-roles-permissions.md)meer informatie over machtigingen.
   
Het wordt aanbevolen om geen directe check-ins toe te staan aan de samenwerkingsbranche. Deze beperking kan helpen voorkomen bugs als elke check-in zal gaan door middel van een pull request review proces beschreven in [Het maken van functie takken](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Wachtwoorden uit Azure Key Vault gebruiken

Het wordt aanbevolen om Azure Key Vault te gebruiken om verbindingstekenreeksen of wachtwoorden op te slaan voor Linked Services in Data Factory. Om veiligheidsredenen slaan we dergelijke geheime informatie niet op in Git, dus wijzigingen in Gekoppelde services worden onmiddellijk gepubliceerd in de Azure Data Factory-service.

Het gebruik van Key Vault maakt continue integratie en implementatie ook eenvoudiger, omdat u deze geheimen niet hoeft te verstrekken tijdens de implementatie van resourcebeheersjablonen.

## <a name="troubleshooting-git-integration"></a>Problemen met Git-integratie oplossen

### <a name="stale-publish-branch"></a>Verouderde publicatie-tak

Als de publicatiebranch niet synchroon loopt met de hoofdvertakking en verouderde bronnen bevat, ondanks een recente publicatie, probeert u de volgende stappen te volgen:

1. Uw huidige Git-opslagplaats verwijderen
1. Configureer Git opnieuw met dezelfde instellingen, maar zorg ervoor dat **bestaande gegevensfabriekbronnen importeren naar opslagplaats** is geselecteerd en kies Nieuwe **branch**
1. Een pull-aanvraag maken om de wijzigingen samen te voegen in de samenwerkingsbranch 

Hieronder vindt u enkele voorbeelden van situaties die een verouderde publicatiebranch kunnen veroorzaken:
- Een gebruiker heeft meerdere branches. In een functiebranch hebben ze een gekoppelde service verwijderd die niet akv-gekoppelde services heeft (niet-AKV-gekoppelde services worden onmiddellijk gepubliceerd, ongeacht of ze zich in Git bevinden of niet) en hebben ze de functietak nooit samengevoegd tot de samenwerkingsbrnach.
- Een gebruiker heeft de gegevensfabriek gewijzigd met de SDK of PowerShell
- Een gebruiker verplaatste alle bronnen naar een nieuwe branch en probeerde voor het eerst te publiceren. Gekoppelde services moeten handmatig worden gemaakt bij het importeren van resources.
- Een gebruiker uploadt handmatig een niet AKV-gekoppelde service of een INTEGRATIE Runtime JSON. Ze verwijzen naar die bron van een andere bron, zoals een gegevensset, gekoppelde service of pijplijn. Een niet-AKV-gekoppelde service die via de UX is gemaakt, wordt onmiddellijk gepubliceerd omdat de referenties moeten worden versleuteld. Als u een gegevensset uploadt waarin naar die gekoppelde service wordt verwezen en probeert te publiceren, staat de UX deze toe omdat deze bestaat in de git-omgeving. Het zal worden afgewezen op het moment publiceren, omdat het niet bestaat in de data fabriek service.

## <a name="provide-feedback"></a>Feedback geven
Selecteer **Feedback** om opmerkingen te maken over functies of om Microsoft op de hoogte te stellen van problemen met het hulpprogramma:

![Feedback](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Pijplijnen programmatisch controleren en beheren](monitor-programmatically.md)voor meer informatie over het bewaken en beheren van pijplijnen.
* Zie Continue integratie en [levering (CI/CD) in Azure Data Factory](continuous-integration-deployment.md)als u continue integratie en implementatie wilt implementeren.

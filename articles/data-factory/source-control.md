---
title: Broncodebeheer
description: Meer informatie over het configureren van broncode beheer in Azure Data Factory
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
ms.openlocfilehash: 1f31bb78fced6db2a26559cbd098407823161dfc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928768"
---
# <a name="source-control-in-azure-data-factory"></a>Broncode beheer in Azure Data Factory

De Azure Data Factory gebruikers interface-ervaring (UX) heeft twee ervaringen die beschikbaar zijn voor visuele ontwerpen:

- Rechtstreeks ontwerpen met de Data Factory-Service
- Auteur met Azure opslag plaatsen git of GitHub-integratie

> [!NOTE]
> Het is alleen mogelijk rechtstreeks te ontwerpen met de Data Factory-service in de Azure Government Cloud.

## <a name="author-directly-with-the-data-factory-service"></a>Rechtstreeks ontwerpen met de Data Factory-Service

Bij het rechtstreeks ontwerpen met de Data Factory-Service, is de enige manier om wijzigingen op te slaan via de knop **Alles publiceren** . Wanneer u eenmaal hebt geklikt, worden alle wijzigingen die u hebt aangebracht, rechtstreeks naar de Data Factory-service gepubliceerd. 

![Publicatie modus](media/author-visually/data-factory-publish.png)

Bij het ontwerpen van de Data Factory-Service gelden de volgende beperkingen:

- De Data Factory-service bevat geen opslag plaats voor het opslaan van de JSON-entiteiten voor uw wijzigingen.
- De Data Factory-service is niet geoptimaliseerd voor samen werking of versie beheer.

> [!NOTE]
> Als u een Git-opslag plaats configureert, wordt de Data Factory-service direct in de Azure Data Factory UX gemaakt. Wijzigingen kunnen rechtstreeks aan de service worden aangebracht via Power shell of een SDK.

## <a name="author-with-azure-repos-git-integration"></a>Auteur met Azure opslag plaatsen Git-integratie

Visual authoring met Azure opslag plaatsen Git-integratie ondersteunt broncode beheer en samen werking voor werk op uw data factory-pijp lijnen. U kunt een data factory koppelen aan een Azure opslag plaatsen Git-opslag plaats voor broncode beheer, samen werking, versies, enzovoort. Eén Azure opslag plaatsen Git-organisatie kan meerdere opslag plaatsen hebben, maar een Azure opslag plaatsen Git-opslag plaats kan slechts worden gekoppeld aan één data factory. Als u geen Azure opslag plaatsen-organisatie of-opslag plaats hebt, volgt u [deze instructies](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) om uw resources te maken.

> [!NOTE]
> U kunt script-en gegevens bestanden opslaan in een Azure opslag plaatsen Git-opslag plaats. U moet de bestanden echter hand matig uploaden naar Azure Storage. Een Data Factory pijp lijn uploadt niet automatisch script-of gegevens bestanden die zijn opgeslagen in een Azure opslag plaatsen Git-opslag plaats naar Azure Storage.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Een Azure opslag plaatsen Git-opslag plaats met Azure Data Factory configureren

U kunt een Azure opslag plaatsen Git-opslag plaats met een data factory via twee methoden configureren.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Configuratie methode 1: Azure Data Factory start pagina

Op de start pagina van Azure Data Factory selecteert u **code opslagplaats instellen**.

![Een Azure opslag plaatsen code-opslag plaats configureren](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Configuratie methode 2: UX ontwerpen canvas
Selecteer in het Azure Data Factory UX-bewerkings canvas de vervolg keuzelijst **Data Factory** en selecteer vervolgens **code opslagplaats instellen**.

![De instellingen van de code opslagplaats voor UX ontwerpen configureren](media/author-visually/configure-repo-2.png)

Met beide methoden wordt het configuratie deel venster opslagplaats instellingen geopend.

![De instellingen voor de code opslagplaats configureren](media/author-visually/repo-settings.png)

In het deel venster configuratie worden de volgende instellingen voor Azure opslag plaatsen code opslagplaats weer gegeven:

| Instelling | Beschrijving | Waarde |
|:--- |:--- |:--- |
| **Type opslag plaats** | Het type van de Azure opslag plaatsen code-opslag plaats.<br/> | Azure DevOps git of GitHub |
| **Azure Active Directory** | De naam van uw Azure AD-Tenant. | `<your tenant name>` |
| **Azure opslag plaatsen-organisatie** | De naam van uw Azure opslag plaatsen-organisatie. U kunt de naam van uw Azure opslag plaatsen-organisatie vinden op `https://{organization name}.visualstudio.com`. U kunt [zich aanmelden bij uw Azure opslag plaatsen-organisatie](https://www.visualstudio.com/team-services/git/) om toegang te krijgen tot uw Visual Studio-profiel en uw opslag plaatsen en projecten te bekijken. | `<your organization name>` |
| **ProjectName** | De naam van uw Azure opslag plaatsen-project. U kunt de naam van uw Azure opslag plaatsen-project vinden op `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **Opslagplaats** | De naam van de opslag plaats van uw Azure opslag plaatsen-code. Azure opslag plaatsen-projecten bevatten Git-opslag plaatsen om uw bron code te beheren naarmate uw project groeit. U kunt een nieuwe opslag plaats maken of een bestaande opslag plaats gebruiken die al in uw project voor komt. | `<your Azure Repos code repository name>` |
| **Collaboration Branch** | Uw Azure opslag plaatsen Collaboration-vertakking die wordt gebruikt voor het publiceren. De standaard instelling is `master`. Wijzig deze instelling als u resources wilt publiceren vanuit een andere vertakking. | `<your collaboration branch name>` |
| **Hoofdmap** | Uw hoofdmap in uw Azure opslag plaatsen Collaboration-vertakking. | `<your root folder name>` |
| **Bestaande Data Factory-resources importeren in opslag plaats** | Hiermee geeft u op of bestaande data factory resources van het UX- **ontwerp canvas** in een Azure opslag plaatsen Git-opslag plaats moeten worden geïmporteerd. Schakel het selectie vakje in om uw data factory-resources te importeren in de bijbehorende Git-opslag plaats in JSON-indeling. Deze actie exporteert elke resource afzonderlijk (dat wil zeggen, de gekoppelde services en gegevens sets worden geëxporteerd naar afzonderlijke JSONs). Als dit selectie vakje niet is ingeschakeld, worden de bestaande resources niet geïmporteerd. | Geselecteerd (standaard) |
| **Vertakking waarvoor de resource moet worden geïmporteerd** | Hiermee wordt aangegeven in welke vertakking de data factory resources (pijp lijnen, gegevens sets, gekoppelde services, enzovoort) worden geïmporteerd. U kunt resources importeren in een van de volgende vertakkingen: a. Samen werking b. Nieuwe c maken. Bestaande gebruiken |  |

> [!NOTE]
> Als u micro soft Edge gebruikt en er geen waarden in de vervolg keuzelijst van uw Azure DevOps-account worden weer geven, voegt u https://*. Visual Studio. com toe aan de lijst met vertrouwde websites.

### <a name="use-a-different-azure-active-directory-tenant"></a>Een andere Azure Active Directory Tenant gebruiken

U kunt een Git-opslagplaats maken in een andere Azure Active Directory-tenant. Als u een andere Azure AD-Tenant wilt opgeven, hebt u beheerders machtigingen nodig voor het Azure-abonnement dat u gebruikt.

### <a name="use-your-personal-microsoft-account"></a>Uw persoonlijke Microsoft-account gebruiken

Als u een persoonlijk Microsoft-account wilt gebruiken voor git-integratie, kunt u uw persoonlijke Azure-opslag plaats koppelen aan de Active Directory van uw organisatie.

1. Voeg uw persoonlijke Microsoft-account toe aan de Active Directory van uw organisatie als gast. Zie [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../active-directory/b2b/add-users-administrator.md)voor meer informatie.

2. Meld u aan bij de Azure Portal met uw persoonlijke Microsoft-account. Schakel vervolgens over naar de Active Directory van uw organisatie.

3. Ga naar de sectie Azure DevOps, waar u nu uw persoonlijke opslag plaats ziet. Selecteer de opslag plaats en maak verbinding met Active Directory.

Na deze configuratie stappen is uw persoonlijke opslag plaats beschikbaar wanneer u Git-integratie instelt in de gebruikers interface van Data Factory.

Zie [uw Azure DevOps-organisatie verbinden met Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)voor meer informatie over het verbinden van Azure opslag plaatsen met de Active Directory van uw organisatie.

## <a name="author-with-github-integration"></a>Auteur met GitHub-integratie

Visual authoring met GitHub-integratie ondersteunt broncode beheer en samen werking voor werk op uw data factory-pijp lijnen. U kunt een data factory koppelen aan een GitHub-account opslagplaats voor broncode beheer, samen werking, versies. Eén GitHub-account kan meerdere opslag plaatsen bevatten, maar een GitHub-opslag plaats kan slechts worden gekoppeld aan één data factory. Als u geen GitHub-account of-opslag plaats hebt, volgt u [deze instructies](https://github.com/join) om uw resources te maken.

De GitHub-integratie met Data Factory ondersteunt zowel open bare GitHub (dat wil zeggen, [https://github.com](https://github.com)) als github Enter prise. U kunt zowel open bare als persoonlijke GitHub-opslag plaatsen gebruiken met Data Factory zolang u lees-en schrijf machtigingen hebt voor de opslag plaats in GitHub.

Als u een GitHub opslag plaats wilt configureren, moet u over beheerders rechten beschikken voor het Azure-abonnement dat u gebruikt.

Bekijk de volgende video voor een inleiding en demonstratie van negen minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Een GitHub-opslag plaats met Azure Data Factory configureren

U kunt een GitHub-opslag plaats met een data factory via twee methoden configureren.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Configuratie methode 1: Azure Data Factory start pagina

Op de start pagina van Azure Data Factory selecteert u **code opslagplaats instellen**.

![Een Azure opslag plaatsen code-opslag plaats configureren](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Configuratie methode 2: UX ontwerpen canvas

Selecteer in het Azure Data Factory UX-bewerkings canvas de vervolg keuzelijst **Data Factory** en selecteer vervolgens **code opslagplaats instellen**.

![De instellingen van de code opslagplaats voor UX ontwerpen configureren](media/author-visually/configure-repo-2.png)

Met beide methoden wordt het configuratie deel venster opslagplaats instellingen geopend.

![Instellingen voor GitHub-opslag plaats](media/author-visually/github-integration-image2.png)

In het deel venster configuratie worden de volgende instellingen voor de GitHub-opslag plaats weer gegeven:

| **Instelling** | **Beschrijving**  | **Waarde**  |
|:--- |:--- |:--- |
| **Type opslag plaats** | Het type van de Azure opslag plaatsen code-opslag plaats. | GitHub |
| **GitHub Enter prise gebruiken** | Selectie vakje om GitHub Enter prise te selecteren | selectie opheffen (standaard) |
| **GitHub Enter prise-URL** | De basis-URL van de GitHub-onderneming. Bijvoorbeeld: https://github.mydomain.com. Alleen vereist als **use github Enter prise** is geselecteerd | `<your GitHub enterprise url>` |                                                           
| **GitHub-account** | De naam van uw GitHub-account. Deze naam kan worden gevonden vanuit https:\//github.com/{account name}/{repository name}. Als u naar deze pagina navigeert, wordt u gevraagd om GitHub OAuth-referenties in te voeren voor uw GitHub-account. | `<your GitHub account name>` |
| **Naam van opslag plaats**  | De naam van de opslag plaats van uw GitHub-code. GitHub-accounts bevatten Git-opslag plaatsen voor het beheren van de bron code. U kunt een nieuwe opslag plaats maken of een bestaande opslag plaats gebruiken die al in uw account is. | `<your repository name>` |
| **Collaboration Branch** | Uw GitHub-samenwerkings vertakking die wordt gebruikt voor het publiceren. Standaard is dit het hoofd. Wijzig deze instelling als u resources wilt publiceren vanuit een andere vertakking. | `<your collaboration branch>` |
| **Hoofdmap** | Uw hoofdmap in uw GitHub-samenwerkings vertakking. |`<your root folder name>` |
| **Bestaande Data Factory-resources importeren in opslag plaats** | Hiermee geeft u op of bestaande data factory resources van het UX-ontwerp canvas in een GitHub-opslag plaats moeten worden geïmporteerd. Schakel het selectie vakje in om uw data factory-resources te importeren in de bijbehorende Git-opslag plaats in JSON-indeling. Deze actie exporteert elke resource afzonderlijk (dat wil zeggen, de gekoppelde services en gegevens sets worden geëxporteerd naar afzonderlijke JSONs). Als dit selectie vakje niet is ingeschakeld, worden de bestaande resources niet geïmporteerd. | Geselecteerd (standaard) |
| **Vertakking waarvoor de resource moet worden geïmporteerd** | Hiermee wordt aangegeven in welke vertakking de data factory resources (pijp lijnen, gegevens sets, gekoppelde services, enzovoort) worden geïmporteerd. U kunt resources importeren in een van de volgende vertakkingen: a. Samen werking b. Nieuwe c maken. Bestaande gebruiken |  |

### <a name="known-github-limitations"></a>Bekende GitHub-beperkingen

- U kunt script-en gegevens bestanden opslaan in een GitHub-opslag plaats. U moet de bestanden echter hand matig uploaden naar Azure Storage. Een Data Factory pijp lijn uploadt niet automatisch script-of gegevens bestanden die zijn opgeslagen in een GitHub-opslag plaats naar Azure Storage.

- GitHub Enter prise met een oudere versie dan 2.14.0 werkt niet in de micro soft Edge-browser.

- GitHub-integratie met de Data Factory Visual authoring-hulpprogram ma's werkt alleen in de algemeen beschik bare versie van Data Factory.

## <a name="switch-to-a-different-git-repo"></a>Overschakelen naar een andere Git-opslag plaats

Als u wilt overschakelen naar een andere Git-opslag plaats, klikt u op het pictogram **Git opslag plaats-instellingen** in de rechter bovenhoek van de pagina overzicht van Data Factory. Als u het pictogram niet kunt zien, wist u de cache van de lokale browser. Selecteer het pictogram om de koppeling met de huidige opslag plaats te verwijderen.

![Git-pictogram](media/author-visually/remove-repo.png)

Zodra het deel venster instellingen van de opslag plaats wordt weer gegeven, selecteert u **Git verwijderen**. Voer uw data factory naam in en klik op **bevestigen** om de Git-opslag plaats te verwijderen die is gekoppeld aan uw Data Factory.

![De koppeling met de huidige Git-opslag plaats verwijderen](media/author-visually/remove-repo2.png)

Nadat u de koppeling met de huidige opslag plaats hebt verwijderd, kunt u uw Git-instellingen configureren voor het gebruik van een andere opslag plaats en vervolgens bestaande Data Factory resources importeren in de nieuwe opslag plaats. 

## <a name="version-control"></a>Versiebeheer

Met versie besturings systemen (ook wel bekend als _broncode beheer_) kunnen ontwikkel aars samen werken aan code en wijzigingen bijhouden die zijn aangebracht in de code basis. Broncode beheer is een essentieel hulp programma voor projecten met meerdere ontwikkel aars.

### <a name="creating-feature-branches"></a>Functie vertakkingen maken

Elke Azure opslag plaatsen Git-opslag plaats die is gekoppeld aan een data factory heeft een Collaboration Branch. (`master` is de standaard-Collaboration Branch). Gebruikers kunnen ook onderdeel vertakkingen maken door op **+ nieuwe vertakking** in de vervolg keuzelijst vertakking te klikken. Zodra het deel venster nieuwe vertakking wordt weer gegeven, voert u de naam van uw functie vertakking in.

![Een nieuwe vertakking maken](media/author-visually/new-branch.png)

Wanneer u klaar bent om de wijzigingen van uw functie vertakking samen te voegen met uw vertakking voor samen werking, klikt u op de vervolg keuzelijst vertakking en selecteert u **pull-aanvraag maken**. Met deze actie gaat u naar Azure opslag plaatsen Git waar u pull-aanvragen kunt genereren, code beoordelingen moet uitvoeren en wijzigingen kunt samen voegen in uw samenwerkings vertakking. (`master` is de standaard instelling). U mag alleen publiceren naar de Data Factory-service vanuit uw vertakking voor samen werking. 

![Een nieuwe pull-aanvraag maken](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Publicatie-instellingen configureren

Voor het configureren van de publicatie vertakking-dat wil zeggen, de vertakking waar Resource Manager-sjablonen worden opgeslagen: Voeg een `publish_config.json` bestand toe aan de hoofdmap in de collaboration-vertakking. Data Factory dit bestand leest, zoekt naar het veld `publishBranch`en maakt een nieuwe vertakking (als deze nog niet bestaat) met de waarde die u opgeeft. Vervolgens worden alle Resource Manager-sjablonen op de opgegeven locatie opgeslagen. Bijvoorbeeld:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Wanneer u een nieuwe publicatie vertakking opgeeft, wordt Data Factory de vorige Publish-vertakking niet verwijderd. Als u de vorige Publish-vertakking wilt verwijderen, moet u deze hand matig verwijderen.

> [!NOTE]
> Data Factory leest alleen het `publish_config.json` bestand wanneer het de fabriek laadt. Als u de fabriek al hebt geladen in de portal, vernieuwt u de browser om uw wijzigingen van kracht te laten worden.

### <a name="publish-code-changes"></a>Code wijzigingen publiceren

Nadat u de wijzigingen hebt samengevoegd in de collaboration Branch (`master` de standaard instelling is), klikt u op **publiceren** om de wijzigingen in de code in de hoofd vertakking hand matig te publiceren naar de Data Factory-service.

![Wijzigingen publiceren in de Data Factory-Service](media/author-visually/publish-changes.png)

Er wordt een deel venster geopend waarin u bevestigt dat de publicatie vertakking en in behandeling zijnde wijzigingen juist zijn. Nadat u uw wijzigingen hebt gecontroleerd, klikt u op **OK** om de publicatie te bevestigen.

![De juiste publicatie vertakking bevestigen](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> De hoofd vertakking is niet representatief voor wat er in de Data Factory-service is geïmplementeerd. De master branch *moet* hand matig worden gepubliceerd naar de Data Factory-service.

## <a name="advantages-of-git-integration"></a>Voor delen van Git-integratie

-   **Broncode beheer**. Als uw data factory-workloads cruciaal worden, wilt u uw fabriek met git integreren om diverse voor delen van broncode beheer te benutten, zoals in het volgende:
    -   De mogelijkheid om wijzigingen bij te houden/te controleren.
    -   De mogelijkheid om wijzigingen die fouten hebben geïntroduceerd te herstellen.
-   **Gedeeltelijk opgeslagen**. Wanneer u een groot aantal wijzigingen in uw fabriek doorvoert, zult u realiseren dat u de wijzigingen niet kunt opslaan als concept, omdat u niet gereed bent, of als u uw wijzigingen niet wilt kwijt raken wanneer uw computer vastloopt. Met git-integratie kunt u de wijzigingen incrementeel blijven opslaan en alleen publiceren naar de fabriek wanneer u klaar bent. Git fungeert als staging-plaats voor uw werk, totdat u uw wijzigingen in uw tevredenheid hebt getest.
-   **Samen werking en beheer**. Als u meerdere team leden hebt die deel nemen aan dezelfde Factory, kunt u uw team genoten met elkaar laten samen werken via een code controle proces. U kunt ook uw fabriek instellen, zodat niet elke bijdrager aan de Factory toestemming heeft om te implementeren in de fabriek. Team leden mogen alleen wijzigingen aanbrengen via git, maar alleen bepaalde personen in het team mogen de wijzigingen in de fabriek publiceren.
-   Het **verschil wordt weer gegeven**. In de Git-modus ziet u een goed vergelijkend verschil van de nettolading die u op het punt staat te publiceren naar de fabriek. Dit verschil toont u alle resources/entiteiten die zijn gewijzigd/toegevoegd/verwijderd sinds de laatste keer dat u uw fabriek hebt gepubliceerd. Op basis van dit verschil kunt u door gaan met publiceren, of teruggaan en de wijzigingen controleren en later terugkomen.
-   **Betere CI/cd**. Als u de Git-modus gebruikt, kunt u uw release pijplijn zo configureren dat deze automatisch wordt geactiveerd zodra er wijzigingen zijn aangebracht in de dev Factory. U kunt ook de eigenschappen in uw fabriek aanpassen die beschikbaar zijn als para meters in de Resource Manager-sjabloon. Het kan handig zijn om alleen de vereiste set eigenschappen als para meters te gebruiken en alle andere gegevens vast te maken.
-   **Betere prestaties**. Een gemiddelde fabriek wordt tien keer sneller in de Git-modus geladen dan in de normale modus LIVE, omdat de resources worden gedownload via git.

## <a name="best-practices-for-git-integration"></a>Aanbevolen procedures voor git-integratie

### <a name="permissions"></a>Machtigingen

Normaal gesp roken wilt u niet dat elk teamlid gemachtigd is om de fabriek bij te werken. De volgende machtigings instellingen worden aanbevolen:

*   Alle team leden moeten lees machtigingen hebben voor de data factory.
*   Alleen een select-set met personen mag publiceren naar de Factory. Hiervoor moeten ze de rol **Data Factory Inzender** hebben op de fabriek. Zie [rollen en machtigingen voor Azure Data Factory](concepts-roles-permissions.md)voor meer informatie over machtigingen.
   
Het is raadzaam om directe incheckers niet toe te staan voor de vertakking voor samen werking. Deze beperking kan helpen te voor komen dat er fouten optreden bij elke check-in om een pull-aanvraag beoordelings proces te door lopen dat wordt beschreven in [functie vertakkingen maken](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Wacht woorden van Azure Key Vault gebruiken

Het is raadzaam om Azure Key Vault te gebruiken voor het opslaan van verbindings reeksen of wacht woorden voor Data Factory gekoppelde services. Uit veiligheids overwegingen worden deze geheime gegevens niet opgeslagen in Git, zodat eventuele wijzigingen in gekoppelde services onmiddellijk worden gepubliceerd in de Azure Data Factory-service.

Het gebruik van Key Vault zorgt ook voor continue integratie en implementatie, omdat u deze geheimen niet hoeft op te geven tijdens de implementatie van Resource Manager-sjablonen.

## <a name="troubleshooting-git-integration"></a>Problemen met git-integratie oplossen

### <a name="stale-publish-branch"></a>Verouderde publicatie vertakking

Als de publicatie vertakking niet is gesynchroniseerd met de Master vertakking en verouderde bronnen bevat ondanks een recente publicatie, voert u de volgende stappen uit:

1. Uw huidige Git-opslag plaats verwijderen
1. Configureer Git opnieuw met dezelfde instellingen, maar zorg ervoor dat **bestaande Data Factory resources importeren in opslag plaats** is geselecteerd en kies **nieuwe vertakking**
1. Alle resources uit uw samenwerkings vertakking verwijderen
1. Een pull-aanvraag maken om de wijzigingen aan de collaboration Branch samen te voegen 

## <a name="provide-feedback"></a>Feedback geven
Selecteer **feedback** om commentaar te geven over functies of om micro soft op de hoogte te stellen over problemen met het hulp programma:

![Feedback](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [pijp lijnen bewaken en beheren via een programma](monitor-programmatically.md)voor meer informatie over het controleren en beheren van pijp lijnen.
* Zie [doorlopende integratie en levering (CI/cd) in azure Data Factory](continuous-integration-deployment.md)voor het implementeren van doorlopende integratie en implementatie.

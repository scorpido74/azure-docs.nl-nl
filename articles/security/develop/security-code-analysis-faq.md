---
title: Veelgestelde vragen over documentatie voor analyse van micro soft-beveiligings code
description: Dit artikel bevat een veelgestelde vragen over de uitbrei ding voor de analyse van beveiligings codes van micro soft
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 846f0ecdd49fc1c501893209b60fa9acc8a32ed2
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242334"
---
# <a name="frequently-asked-questions"></a>Veelgestelde vragen
Vragen? Bekijk de volgende veelgestelde vragen voor meer informatie.

## <a name="general-faq"></a>Algemene veelgestelde vragen

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Kan ik de uitbrei ding installeren op mijn Visual Studio Team Foundation Server-exemplaar in plaats van op een Azure DevOps-exemplaar?

Nee. De extensie is niet beschikbaar voor het downloaden en installeren van Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Moet ik de analyse van micro soft-beveiligings code uitvoeren met mijn build? 

Misschien. Dit is afhankelijk van het type analyse programma. De bron code is mogelijk het enige wat vereist is, of de bouw uitvoer kan vereist zijn.

Referentie scanner (CredScan) analyseert bijvoorbeeld bestanden in de mappen structuur van de code opslagplaats. Vanwege deze analyse kunt u de taken voor het maken van beveiligings analyse logboeken CredScan en publiceren in een zelfstandige build gebruiken om resultaten te verkrijgen.

Voor andere hulpprogram ma's, zoals BinSkim, die artefacten na het bouwen analyseren, is de build eerst vereist.

### <a name="can-i-break-my-build-when-results-are-found"></a>Kan ik mijn build opdelen wanneer er resultaten worden gevonden?

Ja. U kunt een build-kraken introduceren wanneer een hulp programma een probleem of probleem in het logboek bestand meldt. Voeg alleen de taak build-Analysis toe, en schakel het selectie vakje in voor elk hulp programma waarvoor u de build wilt verstoren.

In de gebruikers interface van de taak na de analyse kunt u ervoor kiezen om de build te verstoren wanneer een hulp programma alleen fouten of zowel fouten als waarschuwingen rapporteert.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Hoe verschillen de opdracht regel argumenten in azure DevOps van die argumenten in de zelfstandige bureau blad-hulpprogram ma's? 

De Azure DevOps-build-taken zijn in het algemeen direct wrappers rond de opdracht regel argumenten van de beveiligings hulpprogramma's. U kunt argumenten door geven aan een build-taak, wat u normaal gesp roken doorgeeft aan een opdracht regel programma.

Merk bare verschillen:

- Hulpprogram ma's worden uitgevoerd vanuit de bronmap van de agent $ (build. SourcesDirectory) of van% BUILD_SOURCESDIRECTORY%. Een voor beeld is\_C:\agent work\1\s.
- Paden in de argumenten kunnen relatief zijn ten opzichte van de hoofdmap van de eerder vermelde bron directory. Paden kunnen ook absoluut zijn. U krijgt absolute paden met behulp van Azure DevOps build-variabelen of door een on-premises agent uit te voeren met bekende implementatie locaties van lokale bronnen.
- Hulpprogram ma's geven automatisch een pad naar een uitvoer bestand of een map. Als u een uitvoer locatie voor een build-taak opgeeft, wordt die locatie vervangen door een pad naar de bekende locatie van de logboeken op de build-agent
- Enkele aanvullende opdracht regel argumenten zijn gewijzigd voor sommige hulpprogram ma's. Een voor beeld hiervan is het toevoegen of verwijderen van opties die ervoor zorgen dat er geen GUI wordt gestart.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Kan ik een build-taak zoals referentie scanner uitvoeren in meerdere opslag plaatsen in een Azure DevOps-build?

Nee. Het uitvoeren van de hulpprogram ma's voor beveiligde ontwikkel aars over meerdere opslag plaatsen in één pijp lijn wordt niet ondersteund.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>Het opgegeven uitvoer bestand wordt niet gemaakt of het opgegeven uitvoer bestand kan niet worden gevonden

Met de build-taken wordt een aantal gebruikers invoer gefilterd. De locatie van het gegenereerde uitvoer bestand wordt voor deze vraag specifiek bijgewerkt naar een algemene locatie op de build-agent. Zie de volgende vragen voor meer informatie over deze locatie.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Waar worden de uitvoer bestanden gegenereerd door de hulpprogram ma's die worden opgeslagen? 

De build-taken voegen automatisch uitvoer paden toe aan deze bekende locatie op de build-agent: $ (agent.\_BuildDirectory) sdt\logs. Omdat we op deze locatie standaard worden gestandaardiseerd, hebben alle teams die logboeken voor code analyse produceren of gebruiken toegang tot de uitvoer.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Kan ik een build in de wachtrij plaatsen om deze taken uit te voeren op een gehoste build-agent? 

Ja. Alle taken en hulpprogram ma's in de uitbrei ding kunnen worden uitgevoerd op een gehoste build-agent.

>[!NOTE]
> De anti-malware scanner build-taak vereist een build-agent met Windows Defender ingeschakeld. Gehoste Visual Studio 2017 en hoger bieden een dergelijke agent. De taak bouwen wordt niet uitgevoerd op de gehoste agent van Visual Studio 2015.
>
> Hoewel hand tekeningen niet kunnen worden bijgewerkt op deze agents, moeten hand tekeningen altijd minder dan drie uur oud zijn.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Kan ik deze bouw taken uitvoeren als onderdeel van een release pijplijn, in tegens telling tot een build-pijp lijn?

In de meeste gevallen moet u Ja.

Azure DevOps biedt echter geen ondersteuning voor het uitvoeren van taken in release pijplijnen wanneer deze taken artefacten publiceren. Dit gebrek aan ondersteuning verhindert dat de taak voor het publiceren van beveiligings analyse logboeken kan worden uitgevoerd in een release pijplijn. In plaats daarvan mislukt de taak met een beschrijving van het fout bericht.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Van waar worden de hulpprogram ma's gedownload met de build-taken?

Met Build tasks kunt u de NuGet-pakketten van het hulp programma downloaden van de [Azure DevOps Package Management-feed](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). Het maken van taken kan ook knooppunt pakket beheer gebruiken. dit moet vooraf worden geïnstalleerd op de build-agent. Een voor beeld van een dergelijke installatie is de opdracht **NPM install tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Welk effect heeft het installeren van de uitbrei ding op mijn Azure DevOps-organisatie? 

Bij de installatie worden de taken voor het maken van de beveiliging die worden geboden door de uitbrei ding, beschikbaar voor alle gebruikers in uw organisatie. Wanneer u een Azure-pijp lijn maakt of bewerkt, zijn deze taken beschikbaar in de lijst met verzamelings taken. Anders is het installeren van de uitbrei ding in uw Azure DevOps-organisatie geen effect. Bij de installatie worden geen account instellingen, project instellingen of pijp lijnen gewijzigd.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>Is het installeren van de uitbrei ding mijn bestaande Azure-pijp lijnen wijzigen? 

Nee. Door de uitbrei ding te installeren, kunt u de taken voor het maken van de beveiliging toevoegen aan uw pijp lijnen. U moet nog steeds build-definities toevoegen of bijwerken, zodat de hulpprogram ma's kunnen samen werken met uw bouw proces.

## <a name="task-specific-faq"></a>Taak gerichte Veelgestelde vragen

Vragen die specifiek zijn voor het bouwen van taken worden vermeld in deze sectie.

### <a name="credential-scanner"></a>Referentie scanner

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Wat zijn veelvoorkomende onderdrukkings scenario's en voor beelden?

Hier vindt u meer informatie over twee van de meest voorkomende onderdrukkingen scenario's.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Alle instanties van een gegeven geheim binnen het opgegeven pad onderdrukken

De hash-sleutel van het geheim van het uitvoer bestand CredScan is vereist, zoals wordt weer gegeven in het volgende voor beeld.

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> De hash-sleutel wordt gegenereerd door een deel van de overeenkomende waarde of bestands inhoud. Elke bron code revisie kan de hash-sleutel wijzigen en de onderdrukkings regel uitschakelen.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Alle geheimen in een opgegeven bestand onderdrukken of het geheimen-bestand zelf onderdrukken

De bestands expressie kan een bestands naam zijn. Het kan ook het grondtal van een volledig bestandspad of een bestands naam zijn. Jokertekens worden niet ondersteund.

In de volgende voor beelden ziet u hoe u \<het bestand InputPath > \src\JS\lib\angular.js onderdrukt

Voor beelden van geldige onderdrukkings regels:

- \<InputPath > \src\JS\lib\angular.js-onderdrukt het bestand in het opgegeven pad
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- hoek. js: alle bestanden met dezelfde naam onderdrukken

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> Alle toekomstige geheimen die worden toegevoegd aan het bestand worden ook automatisch onderdrukt.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Wat zijn de aanbevolen richt lijnen voor het beheren van geheimen?

Het is handig om snel vastgelegde geheimen te detecteren en de Risico's te verminderen. Maar voor komt u dat geheimen nog beter zijn ingecheckt.

Micro soft heeft een vroegtijdige preview van Credential scanner Code Analyzer uitgebracht als onderdeel van de [micro soft DevLabs-extensie](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio) voor Visual Studio. De analyse functie is een vroege preview-versie. Het biedt ontwikkel aars een inline-ervaring voor het detecteren van potentiële geheimen in hun code. Door dit te doen, biedt de Analyzer ontwikkel aars ook de mogelijkheid om deze problemen in realtime op te lossen.

Zie het blog bericht [geheimen veilig beheren in de Cloud](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)voor meer informatie.

De volgende bronnen helpen u veilig geheimen te beheren en toegang te krijgen tot gevoelige informatie in uw toepassingen:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD-Managed Service Identity (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Beheerde identiteiten in Azure App Service en Azure Functions](../../app-service/overview-managed-identity.md)
 - [AppAuthentication-bibliotheek](../../key-vault/service-to-service-authentication.md)

#### <a name="can-i-write-my-own-custom-searchers"></a>Kan ik mijn eigen aangepaste zoekers schrijven?

Referentie scanner is afhankelijk van een set inhouds zoekacties die meestal worden gedefinieerd in het bestand buildsearchers. XML. Het bestand bevat een matrix met XML-geserialiseerde objecten die een **ContentSearcher** -object vertegenwoordigen. Het programma wordt gedistribueerd met een aantal goed geteste zoek functies. Maar u kunt ook uw eigen aangepaste zoek functies implementeren.

Een inhouds zoekmachine wordt als volgt gedefinieerd:

- **Naam**: De beschrijvende naam van de zoek functie die moet worden gebruikt in de uitvoer bestanden voor referentie scanners. We raden u aan de Camel-Case-naamgevings Conventie voor namen van zoek functies te gebruiken.
- **RuleId**: De stabiele dekkende ID van de zoek functie:
    - Een standaard zoekmachine voor referentie scanners is toegewezen aan een **RuleId** -waarde zoals CSCAN0010, CSCAN0020 of CSCAN0030. Het laatste cijfer is gereserveerd voor het mogelijk samen voegen of delen van zoek groepen via reguliere expressies (regex).
    - De **RuleId** -waarde voor een aangepaste zoek functie moet een eigen naam ruimte hebben. Voor beelden zijn onder\<andere\>CSCAN naam ruimte 0010\<,\>CSCAN-namespace 0020 en\<CSCAN\>-namespace 0030.
    - Een volledig gekwalificeerde Zoek naam is de combi natie van een **RuleId** -waarde en een naam van een zoek programma. Voor beelden zijn CSCAN0010. KeyStoreFiles en CSCAN0020. Base64EncodedCertificate.
- **ResourceMatchPattern**: Regex van bestands extensies die moeten worden gecontroleerd op de zoek functie.
- **ContentSearchPatterns**: Een matrix met teken reeksen die overeenkomen met regex-instructies. Als er geen Zoek patronen zijn gedefinieerd, worden alle bestanden geretourneerd die overeenkomen met de waarde van **ResourceMatchPattern** .
- **ContentSearchFilters**: Een matrix met teken reeksen die regex-instructies bevatten om zoek functie-specifieke fout-positieven te filteren.
- **MatchDetails**: Een beschrijvende bericht, oplossings instructies of beide moeten worden toegevoegd voor elk treffer van de zoek functie.
- **Aanbeveling**: De suggesties: veld inhoud voor een overeenkomst met de PREfast Report-indeling.
- **Ernst**: Een geheel getal dat het Ernst niveau van een probleem weergeeft. Het hoogste Ernst niveau heeft de waarde 1.

  ![XML met instellingen voor referentie scanner](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn-analyse functies

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Wat zijn veelvoorkomende fouten bij het gebruik van de Roslyn-analyse taken?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>Het project is hersteld met een onjuiste versie van micro soft. NetCore. app

Het volledige fout bericht:

Optreedt Het project is hersteld met behulp van micro soft. NetCore. app versie *x. x. x*, maar met de huidige instellingen versie *y. y. y* wordt in plaats daarvan gebruikt. Om dit probleem op te lossen, moet u ervoor zorgen dat dezelfde instellingen worden gebruikt voor herstel en voor volgende bewerkingen, zoals bouwen of publiceren. Dit probleem kan zich doorgaans voordoen als de eigenschap RuntimeIdentifier is ingesteld tijdens het maken of publiceren, maar niet tijdens het terugzetten.

Omdat Roslyn-analyse taken worden uitgevoerd als onderdeel van compilatie, moet de bron structuur op de build-machine een samenstelbaar status hebben.

In een stap tussen uw belangrijkste build-en Roslyn-analyse stappen is het mogelijk dat de bron structuur in een status staat die het maken van het gebouw verhindert. Deze extra stap is waarschijnlijk **dotnet. exe Publish**. Probeer de stap voor het herstellen van NuGet net vóór de Roslyn-analysen stap te dupliceren. Deze gedupliceerde stap kan de bron structuur mogelijk weer in een samenstelbaar gebied plaatsen.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>CSC. exe kan geen analyse-exemplaar maken

Het volledige fout bericht:

' CSC. exe ' is afgesloten met fout code 1--er kan geen exemplaar van Analyzer *AAAA* worden gemaakt van C:\\*BBBB*. dll: Kan bestand of Assembly ' micro soft. CodeAnalysis, Version =*x. x. x. x*, Culture = neutral, PublicKeyToken = 31bf3856ad364e35 ' of een van de bijbehorende afhankelijkheden niet laden. Het systeem het opgegeven bestand vinden niet."

Zorg ervoor dat uw compiler Roslyn-analyse functies ondersteunt. Het uitvoeren van de opdracht **CSC. exe/version** moet een versie waarde van 2,6 of hoger rapporteren.

Soms kan een. csproj-bestand de installatie van de Visual Studio voor het bouwen van de machine overschrijven door te verwijzen naar een pakket van Microsoft.Net. compilers. Als u geen specifieke versie van de compiler wilt gebruiken, verwijdert u verwijzingen naar Microsoft.Net. compilers. Controleer anders of de versie van het pakket waarnaar wordt verwezen ook 2,6 of hoger is.

Probeer het pad naar het fouten logboek op te halen dat is opgegeven in de optie **CSC. exe/errorlog** . De optie en het pad worden weer gegeven in het logboek voor de taak Roslyn-Analyseën maken. Ze zien er ongeveer als **/errorlog: f:\ts-Services-123\_work\456\s\Some\Project\Code\Code.csproj.Sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>De C# versie van het Compileer programma is niet recent genoeg

Als u de nieuwste versies van de C# compiler wilt downloaden, gaat u naar [Microsoft.net. compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers). Als u de geïnstalleerde versie wilt downloaden, voert u **CSC. exe/version** uit vanaf een opdracht prompt. Zorg ervoor dat u verwijst naar een Microsoft.Net. compilers NuGet-pakket met versie 2,6 of hoger.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>MSBuild-en VSBuild-logboeken zijn niet gevonden

De Roslyn-analyse functies bouwen de taak moet een query uitvoeren op Azure DevOps voor het MSBuild-logboek van de MSBuild-build-taak. Als de taak analyse functie onmiddellijk na de MSBuild-taak wordt uitgevoerd, is het logboek nog niet beschikbaar. Plaats andere taken tussen de MSBuild-taak en de Roslyn-analyse taken. Voor beelden van andere taken zijn BinSkim en anti-malware scanner.

## <a name="next-steps"></a>Volgende stappen

Als u aanvullende hulp nodig hebt, is ondersteuning van micro soft security code Analysis beschikbaar vanaf 9:00 uur 's nachts tot 5:00 uur Pacific (standaard tijd).

  - Onboarding Neem contact op met uw technische account beheerders om aan de slag te gaan.
  
  - Voor Ons team een e-mail sturen met de [ondersteuning voor analyse van beveiligings codes van micro soft](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request).

  >[!NOTE] 
  >Mogelijk hebt u geen betaalde ondersteunings relatie met micro soft. Het is ook mogelijk dat u een ondersteunings aanbieding hebt die voor komt dat u Services aanschaft vanuit de Phoenix-catalogus. Als aan een van deze voor waarden wordt voldaan, gaat u naar de [Start pagina van de ondersteunings services](https://www.microsoft.com/enterprise/services/support) voor meer informatie.

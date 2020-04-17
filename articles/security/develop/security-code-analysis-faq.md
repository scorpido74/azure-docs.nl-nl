---
title: Veelgestelde vragen over documentatie over microsoft-beveiligingscodeanalyse
description: Dit artikel bevat een veelgestelde vragen over de Microsoft Security Code Analysis-extensie
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
ms.openlocfilehash: cb04a8e5a6d8c982a35cb5c448e4b6d93825bf73
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460219"
---
# <a name="frequently-asked-questions"></a>Veelgestelde vragen
Heb je vragen? Bekijk de volgende FAQ voor meer informatie.

## <a name="general-faq"></a>Algemene faq

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Kan ik de extensie installeren op mijn Visual Studio Team Foundation Server-exemplaar in plaats van op een Azure DevOps-exemplaar?

Nee. De extensie is niet beschikbaar voor downloaden en installeren voor Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Moet ik Microsoft Security Code Analysis uitvoeren met mijn build? 

Misschien. Het hangt af van het type analysetool. De broncode is mogelijk het enige dat nodig is, of de build-uitvoer kan nodig zijn.

Credential Scanner (CredScan) analyseert bijvoorbeeld bestanden binnen de mapstructuur van de coderepository. Vanwege deze analyse u de credscan- en publicatielogboeken uitvoeren om taken in een zelfstandige build te bouwen om resultaten te krijgen.

Voor andere tools zoals BinSkim die post-build artefacten analyseren, is de build eerst vereist.

### <a name="can-i-break-my-build-when-results-are-found"></a>Kan ik mijn build breken wanneer er resultaten worden gevonden?

Ja. U een buildbreak introduceren wanneer een hulpprogramma een probleem of probleem in het logboekbestand rapporteert. Voeg gewoon de opdracht Post-Analysis build toe en schakel het selectievakje in voor elk gereedschap waarvoor u de build wilt breken.

In de gebruikersinterface van de taak Post-Analyse u ervoor kiezen om de build te verbreken wanneer een hulpprogramma alleen fouten of fouten en waarschuwingen rapporteert.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Hoe verschillen de opdrachtregelargumenten in Azure DevOps van die argumenten in de zelfstandige bureaubladhulpprogramma's? 

De azure DevOps-buildtaken zijn grotendeels directe wikkels rond de argumenten voor de opdrachtregel van de beveiligingshulpprogramma's. U als argumenten doorgeven aan een buildtaak alles wat u normaal gesproken doorgeeft aan een opdrachtregelgereedschap.

Merkbare verschillen:

- Hulpprogramma's worden uitgevoerd vanuit de bronmap van de agent $(Build.SourcesDirectory) of vanaf %BUILD_SOURCESDIRECTORY%. Een voorbeeld is C:\agent\_work\1\s.
- Paden in de argumenten kunnen relatief zijn ten opzichte van de hoofdmap van de eerder genoemde bronmap. Paden kunnen ook absoluut zijn. U krijgt absolute paden door Azure DevOps Build-variabelen te gebruiken of door een on-premises agent uit te voeren met bekende implementatielocaties van lokale resources.
- Hulpprogramma's bieden automatisch een uitvoerbestandspad of -map. Als u een uitvoerlocatie opgeeft voor een buildtaak, wordt die locatie vervangen door een pad naar onze bekende locatie van logboeken op de buildagent
- Sommige aanvullende opdrachtregelargumenten worden voor sommige gereedschappen gewijzigd. Een voorbeeld is de toevoeging of verwijdering van opties die ervoor zorgen dat er geen GUI wordt gestart.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Kan ik een buildtaak zoals Credential Scanner uitvoeren in meerdere repositories in een Azure DevOps Build?

Nee. Het uitvoeren van de hulpprogramma's voor veilige ontwikkeling in meerdere repositories in één pijplijn wordt niet ondersteund.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>Het uitvoerbestand dat ik heb opgegeven, wordt niet gemaakt of ik kan het uitvoerbestand dat ik heb opgegeven niet vinden

De buildtaken filteren sommige gebruikersinvoer. Voor deze vraag specifiek, ze updaten de locatie van de gegenereerde uitvoer bestand om een gemeenschappelijke locatie op de build agent. Zie de volgende vragen voor meer informatie over deze locatie.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Waar worden de uitvoerbestanden gegenereerd door de opgeslagen hulpprogramma's? 

De buildtaken voegen automatisch uitvoerpaden toe aan deze bekende locatie op de\_buildagent: sdt\logs van $(Agent.BuildDirectory). Omdat we op deze locatie standaardiseren, hebben alle teams die code-analyselogboeken produceren of consumeren toegang tot de uitvoer.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Kan ik een build in de wachtrij zetten om deze taken uit te voeren op een gehoste build-agent? 

Ja. Alle taken en hulpprogramma's in de extensie kunnen worden uitgevoerd op een gehoste build-agent.

>[!NOTE]
> De buildtaak Anti-Malware Scanner vereist een buildagent met Windows Defender ingeschakeld. Hosted Visual Studio 2017 en later bieden een dergelijke agent. De buildtaak wordt niet uitgevoerd op de Visual Studio 2015 hosted agent.
>
> Hoewel handtekeningen niet kunnen worden bijgewerkt op deze agents, moeten handtekeningen altijd minder dan drie uur oud zijn.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Kan ik deze buildtaken uitvoeren als onderdeel van een releasepijplijn in plaats van een buildpijplijn?

In de meeste gevallen wel, ja.

Azure DevOps ondersteunt echter geen taken in releasepijplijnen wanneer deze taken artefacten publiceren. Dit gebrek aan ondersteuning voorkomt dat de taak Logboeken voor publicatiebeveiligingsanalyselogboeken met succes wordt uitgevoerd in een releasepijplijn. De taak mislukt in plaats daarvan met een beschrijvend foutbericht.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Van waar downloaden de bouwtaken de tools?

Buildtaken kunnen de NuGet-pakketten van de hulpprogramma's downloaden uit de [Azure DevOps Package Management-feed.](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json) Buildtaken kunnen ook Node Package Manager gebruiken, dat vooraf moet worden geïnstalleerd op de buildagent. Een voorbeeld van een dergelijke installatie is de opdracht **npm installeren tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Welk effect heeft het installeren van de extensie op mijn Azure DevOps-organisatie? 

Bij de installatie worden de beveiligingstaken die door de extensie worden geleverd, beschikbaar voor alle gebruikers in uw organisatie. Wanneer u een Azure Pipeline maakt of bewerkt, zijn deze taken beschikbaar in de lijst met verzamelingen voor buildtaken. Anders heeft het installeren van de extensie in uw Azure DevOps-organisatie geen effect. De installatie wijzigt geen accountinstellingen, projectinstellingen of pijplijnen.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>Wijzigt het installeren van de extensie mijn bestaande Azure Pipelines? 

Nee. Als u de extensie installeert, zijn de beveiligingstaken beschikbaar voor toevoeging aan uw pijplijnen. U moet nog steeds builddefinities toevoegen of bijwerken, zodat de hulpprogramma's kunnen werken met uw bouwproces.

## <a name="task-specific-faq"></a>Veelgestelde vragen over taak

Vragen die specifiek zijn voor het bouwen van taken worden in deze sectie weergegeven.

### <a name="credential-scanner"></a>Referentiescanner

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Wat zijn veelvoorkomende onderdrukkingsscenario's en voorbeelden?

Hier zijn details van twee van de meest voorkomende onderdrukkingsscenario's.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Alle gebeurtenissen van een bepaald geheim binnen het opgegeven pad onderdrukken

De hash-toets van het geheim van het CredScan-uitvoerbestand is vereist, zoals in het volgende voorbeeld wordt weergegeven.

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
> De hashsleutel wordt gegenereerd door een deel van de overeenkomende waarde of bestandsinhoud. Elke broncoderevisie kan de hash-sleutel wijzigen en de onderdrukkingsregel uitschakelen.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Om alle geheimen in een bepaald bestand te onderdrukken of om het geheimenbestand zelf te onderdrukken

De bestandsexpressie kan een bestandsnaam zijn. Het kan ook het basisnaamgedeelte zijn van een volledig bestandspad of een bestandsnaam. Jokertekens worden niet ondersteund.

In de volgende voorbeelden ziet \<u hoe u het bestand InputPath>\src\JS\lib\angular.js onderdrukken

Voorbeelden van geldige onderdrukkingsregels:

- \<InputPath>\src\JS\lib\angular.js - onderdrukt het bestand in het opgegeven pad
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js - onderdrukt elk bestand met dezelfde naam

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
> Alle toekomstige geheimen die aan het bestand worden toegevoegd, worden ook automatisch onderdrukt.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Wat zijn aanbevolen richtlijnen voor het beheren van geheimen?

Met de volgende bronnen u veilig geheimen beheren en toegang krijgen tot gevoelige informatie vanuit uw toepassingen:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD Managed Service Identity (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Beheerde identiteiten in Azure App Service en Azure-functies](../../app-service/overview-managed-identity.md)
 - [AppAuthentication-bibliotheek](../../key-vault/general/service-to-service-authentication.md)


Zie de blogpost Geheimen [beheren veilig in de cloud](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)voor meer informatie.

#### <a name="can-i-write-my-own-custom-searchers"></a>Kan ik mijn eigen aangepaste zoekers schrijven?

Credential Scanner is gebaseerd op een set van inhoud zoekers die vaak worden gedefinieerd in de buildsearchers.xml bestand. Het bestand bevat een array van XML-geserialiseerde objecten die een **ContentSearcher-object** vertegenwoordigen. Het programma wordt gedistribueerd met een set van goed geteste zoekers. Maar u uw eigen aangepaste zoekers ook implementeren.

Een inhoudszoeker wordt als volgt gedefinieerd:

- **Naam:** de beschrijvende zoeknaam die moet worden gebruikt in uitvoerbestanden van de referentiescanner. We raden u aan de camel-case naamgevingconventie te gebruiken voor zoeknamen.
- **RuleId**: De stabiele ondoorzichtige ID van de zoeker:
    - Aan een standaardzoeker van de referentiescanner is een **RuleId-waarde** toegewezen, zoals CSCAN0010, CSCAN0020 of CSCAN0030. Het laatste cijfer is gereserveerd voor het mogelijk samenvoegen of delen van zoekgroepen via reguliere expressies (regex).
    - De **RuleId-waarde** voor een aangepaste zoeker moet een eigen naamruimte hebben. Voorbeelden hiervan zijn\<CSCAN- Namespace\>0010, CSCAN-\<Namespace\>0020 en CSCAN-\<Namespace\>0030.
    - Een volledig gekwalificeerde zoeknaam is de combinatie van een **RuleId-waarde** en een zoeknaam. Voorbeelden hiervan zijn CSCAN0010. KeyStoreFiles en CSCAN0020. Base64EncodedCertificaat.
- **ResourceMatchPattern:** Regex van bestandsextensies om te controleren tegen de zoeker.
- **ContentSearchPatterns:** een array met tekenreeksen die regex-instructies bevatten die overeenkomen. Als er geen zoekpatronen zijn gedefinieerd, worden alle bestanden die overeenkomen met de **resourcematchpatroonwaarde** geretourneerd.
- **ContentSearchFilters:** een reeks tekenreeksen met regex-instructies om zoekerspecifieke false positives te filteren.
- **MatchDetails:** een beschrijvend bericht, mitigatie-instructies, of beide worden toegevoegd voor elke wedstrijd van de zoeker.
- **Aanbeveling**: De inhoud van het suggestieveld voor een overeenkomst met de PREfast-rapportindeling.
- **Ernst**: een geheel getal dat het ernstniveau van een probleem weergeeft. Het hoogste ernstniveau heeft de waarde 1.

  ![XML met opstelling Referentiescanner](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn Analyzers

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Wat zijn veelvoorkomende fouten bij het gebruik van de taak Roslyn Analyzers?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>Het project is hersteld met een verkeerde Microsoft.NETCore.App-versie

De volledige foutmelding:

"Fout: Het project is hersteld met Microsoft.NETCore.App versie *x.x.x*, maar met de huidige instellingen zou versie *y.y.y* in plaats daarvan worden gebruikt. Als u dit probleem wilt oplossen, controleert u of dezelfde instellingen worden gebruikt voor herstel en voor volgende bewerkingen, zoals bouwen of publiceren. Dit probleem kan meestal optreden als de eigenschap RuntimeIdentifier is ingesteld tijdens het bouwen of publiceren, maar niet tijdens het herstellen."

Omdat Roslyn Analyzers-taken worden uitgevoerd als onderdeel van de compilatie, moet de bronstructuur op de buildmachine in een bouwbare staat zijn.

Een stap tussen uw belangrijkste build en Roslyn Analyzers stappen kunnen hebben de bronboom in een staat die gebouw voorkomt. Deze extra stap is waarschijnlijk **dotnet.exe publiceren**. Probeer de stap te dupliceren die een NuGet-restauratie doet vlak voor de stap van Roslyn Analyzers. Met deze gedupliceerde stap kan de bronstructuur weer in een bouwbare staat worden geplaatst.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe kan geen analyse-instantie maken

De volledige foutmelding:

"'csc.exe' verlaten met foutcode 1 - Een instantie van analyzer *AAAA* kan niet worden gemaakt van C:\\*BBBB*.dll : Kon geen bestand of assemblage laden 'Microsoft.CodeAnalysis, Version=*X.X.X.X. X*, Culture=neutral, PublicKeyToken=31bf3856ad364e35' of een van de afhankelijkheden. Het systeem kan het opgegeven bestand niet vinden."

Zorg ervoor dat uw compiler Roslyn Analyzers ondersteunt. Als u de opdracht **csc.exe /versie** uitvoert, moet een versiewaarde van 2,6 of hoger worden gemeld.

Soms kan een .csproj-bestand de Visual Studio-installatie van de buildmachine overschrijven door te verwijzen naar een pakket van Microsoft.Net.Compilers. Als u niet van plan bent een specifieke versie van de compiler te gebruiken, verwijdert u verwijzingen naar Microsoft.Net.Compilers. Zorg er anders voor dat de versie van het pakket waarnaar wordt verwezen ook 2,6 of hoger is.

Probeer het foutlogboekpad te krijgen, dat is opgegeven in de optie **csc.exe /errorlog.** De optie en het pad worden weergegeven in het logboek voor de buildtaak roslyn Analyzers. Ze kunnen er ongeveer uitzien als **/errorlog:F:\ts-services-123\_werk\456\s\Some\Project\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>De C# compiler versie is niet recent genoeg

Ga naar [Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers)om de nieuwste versies van de C#-compiler te krijgen. Voer **csc.exe /version** uit om de geïnstalleerde versie te krijgen met een opdrachtprompt. Controleer of u verwijst naar een NuGet-pakket van Microsoft.Net.Compilers dat versie 2.6 of hoger is.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>MSBuild- en VSBuild-logboeken worden niet gevonden

De roslyn Analyzers-buildtaak moet Azure DevOps voor het MSBuild-logboek opvragen vanuit de MSBuild-buildtaak. Als de analyzer-taak direct na de MSBuild-taak wordt uitgevoerd, is het logboek nog niet beschikbaar. Plaats andere taken tussen de MSBuild-taak en de taak Roslyn Analyzers. Voorbeelden van andere taken zijn BinSkim en Anti-Malware Scanner.

## <a name="next-steps"></a>Volgende stappen

Als u extra hulp nodig hebt, is Microsoft Security Code Analysis Support beschikbaar van maandag tot en met vrijdag van 9:00 tot 17:00 uur Pacific Standard Time.

- Onboarding: Raadpleeg onze [Onboarding documentatie](security-code-analysis-onboard.md)
  
- Ondersteuning: E-mail ons team bij [Microsoft Security Code Analysis Support](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)
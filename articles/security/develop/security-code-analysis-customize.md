---
title: Taken voor microsoft-beveiligingscodeanalyse aanpassen
titleSuffix: Azure
description: In dit artikel worden de taken in de microsoft security code-extensie aangepast
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
ms.openlocfilehash: 6cdf892651407defc21f359a8e3b326b4af63b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499993"
---
# <a name="configure-and-customize-the-build-tasks"></a>De buildtaken configureren en aanpassen

In dit artikel worden de configuratieopties die beschikbaar zijn in elk van de buildtaken in detail beschreven. Het artikel begint met de taken voor beveiligingscodeanalysetools. Het eindigt met de nabewerkingstaken.

## <a name="anti-malware-scanner-task"></a>Taak anti-malwarescanner

>[!NOTE]
> De buildtaak Anti-Malware Scanner vereist een buildagent met Windows Defender ingeschakeld. Hosted Visual Studio 2017 en later bieden een dergelijke agent. De buildtaak wordt niet uitgevoerd op de Visual Studio 2015 hosted agent.
>
> Hoewel handtekeningen niet kunnen worden bijgewerkt op deze agents, moeten handtekeningen altijd minder dan drie uur oud zijn.

Details van de taakconfiguratie worden weergegeven in de volgende schermafbeelding en tekst.

![De buildtaak Anti-Malware Scanner configureren](./media/security-tools/5-add-anti-malware-task600.png)

In het vak **Tekst** van de schermafbeelding is **Basic** geselecteerd. Selecteer **Aangepast** om opdrachtregelargumenten op te geven die de scan aanpassen.

Windows Defender gebruikt de Windows Update-client om handtekeningen te downloaden en te installeren. Als de handtekeningupdate mislukt op uw buildagent, is de **HRESULT-foutcode** waarschijnlijk afkomstig van Windows Update.

Zie [Windows Update-foutcodes per component](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) en het TechNet-artikel Windows Update Agent - [Foutcodes](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)voor meer informatie over windows update-fouten en de beperking daarvan.

Voor informatie over yaml-configuratie voor deze taak, raadpleegt u onze [Anti-Malware YAML-opties](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>BinSkim-taak

> [!NOTE]
> Voordat u de taak BinSkim uitvoeren, moet uw build aan een van de volgende voorwaarden voldoen:
>  - Uw build produceert binaire artefacten uit beheerde code.
>  - U hebt binaire artefacten vastgelegd die u wilt analyseren met BinSkim.

Details van de taakconfiguratie worden weergegeven in de volgende schermafbeelding en -lijst.

![De buildtaak BinSkim configureren](./media/security-tools/7-binskim-task-details.png)

- Stel de buildconfiguratie in op Foutopsporing, zodat .pdb-foutopsporingsbestanden worden geproduceerd. BinSkim gebruikt deze bestanden om problemen in de uitvoerbinaire bestanden terug te brengen naar de broncode.
- Ga als u voorkomen dat u uw eigen opdrachtregel onderzoekt en maakt:
     - Selecteer **Basic**in de lijst **Type** .
     - Selecteer **Analyseren** in **Analyze**de lijst Functie .
- Voer **in Target**een of meer specificeerders in voor een bestand, map of filterpatroon. Deze specificeerders besluiten tot een of meer binaire bestanden die moeten worden geanalyseerd:
    - Meerdere opgegeven doelen moeten worden gescheiden door een puntkomma (;).
    - Een specificer kan één bestand zijn of wildcards bevatten.
    - Directory specificaties moeten \\altijd eindigen met *.
    - Voorbeelden:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- Als u **Opdrachtregel** selecteert in de lijst **Type,** moet u binskim.exe uitvoeren:
     - Zorg ervoor dat de eerste argumenten voor binskim.exe het werkwoord **analyseren** gevolgd door een of meer padspecificaties. Elk pad kan een volledig pad of een pad ten opzichte van de bronmap zijn.
     - Meerdere doelpaden moeten worden gescheiden door een spatie.
     - U de optie **/o** of **/output** weglaten. De uitvoerwaarde wordt voor u toegevoegd of vervangen.
     - Standaard configuratie van de opdrachtregel worden als volgt weergegeven.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > De \\trailing * is belangrijk als u mappen voor het doel opgeeft.

Zie de [gebruikershandleiding binskim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)voor meer informatie over de opdrachtregelargumenten, regels op id of afsluiten.

Voor informatie over de YAML-configuratie voor deze taak, raadpleegt u onze [BinSkim YAML-opties](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Taak Referentiescanner

Details van de taakconfiguratie worden weergegeven in de volgende schermafbeelding en -lijst.

![De opbouwtaak van de referentiescanner configureren](./media/security-tools/3-taskdetails.png)

Beschikbare opties zijn onder meer:

  - **Uitvoernotatie**: Beschikbare waarden zijn onder andere **TSV,** **CSV**, **SARIF**en **PREfast**.
  - **Gereedschapsversie:** We raden u aan **Laatste te**selecteren.
  - **Map scannen:** de map van de opslagplaats die moet worden gescand.
  - **Zoektermen Bestandstype:** de opties voor het zoeken naar het bestand van zoekers dat wordt gebruikt voor het scannen.
  - **Onderdrukkingsbestand:** een [JSON-bestand](https://json.org/) kan problemen in het uitvoerlogboek onderdrukken. Zie het gedeelte Veelgestelde vragen van dit artikel voor meer informatie over onderdrukkingsscenario's.
  - **Verbose Output**: Vanzelfsprekend.
  - **Batchgrootte:** het aantal gelijktijdige threads dat wordt gebruikt om de referentiescanner uit te voeren. De standaardwaarde is 20. Mogelijke waarden variëren van 1 tot en met 2.147.483.647.
  - **Match Timeout:** De hoeveelheid tijd in seconden om een zoeker match te proberen voordat u de cheque verlaat.
  - **Buffergrootte voor bestandsscanlezen:** de grootte in bytes van de buffer die wordt gebruikt terwijl de inhoud wordt gelezen. De standaardwaarde is 524.288.  
  - **Maximum File Scan Read Bytes**: Het maximum aantal bytes dat je uit een bestand moet lezen tijdens de inhoudsanalyse. De standaardwaarde is 104.857.600.
  - **Besturingselementopties** > **Voer deze taak uit:** geeft op wanneer de taak wordt uitgevoerd. Selecteer **Aangepaste voorwaarden** om complexere voorwaarden op te geven.
  - **Versie:** De versie van de buildtaak binnen Azure DevOps. Deze optie wordt niet vaak gebruikt.

Voor informatie over de YAML-configuratie voor deze taak, raadpleegt u onze [Credential Scanner YAML-opties](yaml-configuration.md#credential-scanner-task)

## <a name="microsoft-security-risk-detection-task"></a>Microsoft-taak voor detectie van beveiligingsrisico's

> [!NOTE]
> U moet een account maken en configureren met de MSRD-service (Microsoft Security Risk Detection) voordat u de MSRD-taak gebruikt. Deze service vereist een apart onboardingproces. In tegenstelling tot de meeste andere taken in deze extensie, vereist deze taak een apart abonnement met MSRD.
>
> Raadpleeg [Microsoft Security Risk Detection](https://aka.ms/msrddocs) en Microsoft Security Risk [Detection: How To](https://docs.microsoft.com/security-risk-detection/how-to/) voor instructies.

Details voor het configureren van deze taak worden weergegeven in de volgende lijst. Voor elk UI-element u over dat element zweven om hulp te krijgen.

   - **Azure DevOps Service Endpoint Name for MSRD:** A generic type Azure DevOps service endpoint stores your onboarded MSRD instance URL and your REST API access token. Als u een dergelijk eindpunt hebt gemaakt, u dit hier opgeven. Selecteer anders de koppeling **Beheren** om een nieuw serviceeindpunt voor deze MSRD-taak te maken en te configureren.
   - **Account-id**: een GUID die kan worden opgehaald uit de URL van het MSRD-account.
   - **URL's naar binaries:** een lijst met vrijgekomen url's met puntkomma's. De fuzzing machine maakt gebruik van deze URL's om de binaries te downloaden.
   - **URL's van de seedbestanden:** een lijst met vrijgekomen URL's met puntkomma's. De fuzzing machine maakt gebruik van deze URL's om de zaden te downloaden. Het opgeven van deze waarde is optioneel als de seedbestanden samen met de binaire bestanden worden gedownload.
   - **OS Platform Type:** Het besturingssysteem (OS) platform van machines die de fuzzing taak draaien. Beschikbare waarden zijn **Windows** en **Linux.**
   - **Windows Edition / Linux Edition**: De OS-editie van machines die de fuzzing job draaien. U de standaardwaarde overschrijven als uw machines een andere OS-editie hebben.
   - **Package Installation Script:** Uw script dat moet worden uitgevoerd op een testmachine. Dit script installeert het testdoelprogramma en de afhankelijkheden ervan voordat de fuzzing-taak wordt ingediend.
   - **Parameters voor het indienen van vacatures:**
       - **Seed Directory:** Het pad naar de directory op de fuzzing machine die de zaden bevat.
       - **Seed Extension**: De bestandsnaam extensie van de zaden.
       - **Test Driver Executable:** Het pad naar het doel uitvoerbare bestand op de fuzzing machine.
       - **Uitvoerbare architectuur voor teststuurprogramma's:** de architectuur van het doeluitvoerbestand. Beschikbare waarden zijn **x86** en **amd64**.
       - **Argumenten voor teststuurprogramma's:** de opdrachtregelargumenten worden doorgegeven aan het uitvoerbare testbestand. Het argument "%testfile%", inclusief de aanhalingstekens, wordt automatisch vervangen door het volledige pad naar het doelbestand. Dit bestand wordt ontleed door de testrijder en is vereist.
       - **Test Driver Process Exits Upon Test Completion**: Schakel dit selectievakje in als het testrijder na voltooiing moet worden beëindigd. Maak het duidelijk als de testrijder met geweld moet worden gesloten.
       - **Maximale duur (in seconden):** Een schatting van de langste redelijkerwijs verwachte tijd die het doelprogramma nodig heeft om een invoerbestand te ontleden. Hoe nauwkeuriger de schatting, hoe efficiënter de fuzzing app draait.
       - **Testdriver kan herhaaldelijk worden uitgevoerd:** Schakel dit selectievakje in als het testrijder herhaaldelijk kan worden uitgevoerd zonder afhankelijk te zijn van een permanente of gedeelde globale status.
       - **Testdriver kan worden hernoemd:** Schakel dit selectievakje in als het uitvoerbare bestand van het teststuurprogramma kan worden hernoemd en nog steeds correct werkt.
       - **De Fuzzing-toepassing wordt uitgevoerd als een enkel besturingssysteemproces:** Schakel dit selectievakje in als het testrijder onder één besturingssysteemproces wordt uitgevoerd. Maak het duidelijk als de testdriver extra processen voortbrengt.

Voor informatie over de YAML-configuratie voor deze taak, raadpleegt u onze [Microsoft Security Risk Detection YAML-opties](yaml-configuration.md#microsoft-security-risk-detection-task)

## <a name="roslyn-analyzers-task"></a>Roslyn Analyzers taak

> [!NOTE]
> Voordat u de taak Roslyn Analyzers uitvoeren, moet uw build aan deze voorwaarden voldoen:
>
> - Je builddefinitie bevat de ingebouwde MSBuild- of VSBuild-buildtaak om C# of Visual Basic-code samen te stellen. De taak van de analysators is gebaseerd op de invoer en uitvoer van de ingebouwde taak om de MSBuild-compilatie uit te voeren met Roslyn-analysators ingeschakeld.
> - De buildagent die deze buildtaak uitvoert, heeft Visual Studio 2017 versie 15.5 of hoger geïnstalleerd, zodat deze compilerversie 2.6 of hoger gebruikt.

Details van de taakconfiguratie worden weergegeven in de volgende lijst en notitie.

Beschikbare opties zijn onder meer:

- **Regelset:** Waarden zijn **SDL vereist,** **SDL aanbevolen**of uw eigen aangepaste regelset.
- **Analysers-versie:** we raden u aan **Laatste te**selecteren.
- **Compiler waarschuwingen onderdrukkingbestand:** een tekstbestand met een lijst met waarschuwingen ID's die worden onderdrukt.
- **Besturingselementopties** > **Voer deze taak uit:** geeft op wanneer de taak wordt uitgevoerd. Kies **Aangepaste voorwaarden** om complexere voorwaarden op te geven.

> [!NOTE]
>
> - Roslyn Analyzers zijn geïntegreerd met de compiler en kan alleen worden uitgevoerd als onderdeel van csc.exe compilatie. Daarom vereist deze taak dat de compileropdracht die eerder in de build is uitgevoerd, opnieuw moet worden afgespeeld of uitgevoerd. Deze herhaling of uitvoering wordt gedaan door Visual Studio Team Services (VSTS) op te vragen voor de MSBuild-taaklogboeken.
>
>   Er is geen andere manier voor de taak om de MSBuild-compilatieopdrachtregel betrouwbaar te krijgen van de builddefinitie. We hebben overwogen een tekstvak uit vrije vorm toe te voegen zodat gebruikers hun opdrachtregels kunnen invoeren. Maar dan zou het moeilijk zijn om die commandolijnen up-to-date en in sync met de belangrijkste build te houden.
>
>   Aangepaste builds vereisen het opnieuw afspelen van de hele set opdrachten, niet alleen compileropdrachten. In deze gevallen is het inschakelen van Roslyn Analyzers niet triviaal of betrouwbaar.
>
> - Roslyn Analyzers zijn geïntegreerd met de compiler. Om te worden aangeroepen, roslyn Analyzers vereisen compilatie.
>
>   Deze nieuwbouwtaak wordt uitgevoerd door het opnieuw samenstellen van C# projecten die al zijn gebouwd. De nieuwe taak gebruikt alleen de buildtaken MSBuild en VSBuild in dezelfde build- of builddefinitie als de oorspronkelijke taak. In dit geval wordt deze echter gebruikt met Roslyn Analyzers ingeschakeld.
>
>   Als de nieuwe taak op dezelfde agent als de oorspronkelijke taak wordt uitgevoerd, overschrijft *s* de uitvoer van de nieuwe taak de uitvoer van de oorspronkelijke taak in de map s-bronnen. Hoewel de build-uitvoer hetzelfde is, raden we u aan MSBuild uit te voeren, uitvoer naar de map met de artefacten staging te kopiëren en vervolgens Roslyn Analyzers uit te voeren.

Voor extra bronnen voor de taak Roslyn Analyzers raadpleegt u [De op Roslyn gebaseerde analyzers](https://docs.microsoft.com/dotnet/standard/analyzers/) op Microsoft Docs.

U vindt het analyzer-pakket dat is geïnstalleerd en gebruikt door deze buildtaak op de NuGet-pagina [Microsoft.CodeAnalysis.FxCopAnalyzers.](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)

Voor informatie over de YAML-configuratie voor deze taak, raadpleegt u onze [Roslyn Analyzers YAML-opties](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>TSLint- taak

Ga voor meer informatie over TSLint naar de [TSLint GitHub repo.](https://github.com/palantir/tslint)

>[!NOTE] 
>Zoals u wellicht weet, zegt de [TSLint GitHub repo-startpagina](https://github.com/palantir/tslint) dat TSLint ergens in 2019 zal worden afgeschaft. Microsoft onderzoekt [ESLint](https://github.com/eslint/eslint) als een alternatieve taak.

Voor informatie over de YAML-configuratie voor deze taak, raadpleegt u onze [TSLint YAML-opties](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Taak Logboeken voor beveiligingsanalyse publiceren

Details van de taakconfiguratie worden weergegeven in de volgende schermafbeelding en -lijst.

![De buildtaak Logboeken voor publicatiebeveiliginganalyselogboeken configureren](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Artefactnaam:** elke tekenreeks-id.
- **Artefacttype:** Afhankelijk van uw selectie u logboeken publiceren naar uw Azure DevOps-server of naar een gedeeld bestand dat toegankelijk is voor de buildagent.
- **Gereedschappen:** U ervoor kiezen om logboeken te bewaren voor specifieke gereedschappen, of u **Alle gereedschappen** selecteren om alle logboeken te behouden.

Voor informatie over de YAML-configuratie voor deze taak, raadpleegt u onze [YAML-opties voor publicatiebeveiligingslogboeken](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Taak beveiligingsrapport

Details van de configuratie van het beveiligingsrapport worden weergegeven in de volgende schermafbeelding en -lijst.

![De buildtaak beveiligingsrapport configureren](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Rapporten:** Selecteer een van de **indelingen Pipeline Console,** **TSV-bestand**en **Html-bestand.** Voor elke geselecteerde indeling wordt één rapportbestand gemaakt.
- **Hulpmiddelen:** Selecteer de gereedschappen in uw builddefinitie waarvoor u een overzicht wilt van gedetecteerde problemen. Voor elk geselecteerd gereedschap is er mogelijk een optie om te selecteren of u alleen fouten ziet of dat fouten en waarschuwingen in het overzichtsrapport worden gezien.
- **Geavanceerde opties:** Als er geen logboeken zijn voor een van de geselecteerde gereedschappen, u ervoor kiezen om een waarschuwing of een fout in te loggen. Als u een fout inlogt, mislukt de taak.
- **Map Basislogboeken:** U de map basislogboeken aanpassen waar logboeken te vinden zijn. Maar deze optie wordt meestal niet gebruikt.

Voor informatie over de YAML-configuratie voor deze taak, raadpleegt u onze [YAML-opties voor beveiligingsrapport YAML](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>Taak na analyse

Details van de taakconfiguratie worden weergegeven in de volgende schermafbeelding en -lijst.

![De opdracht voor het bouwen na analyse configureren](./media/security-tools/a-post-analysis600.png)

- **Tools:** Selecteer de gereedschappen in uw builddefinitie waarvoor u voorwaardelijk een buildbreak wilt injecteren. Voor elk geselecteerd gereedschap is er mogelijk een optie om te selecteren of u alleen fouten of op beide fouten en waarschuwingen wilt verbreken.
- **Rapport**: U optioneel de resultaten schrijven die de buildbreak veroorzaken. De resultaten worden geschreven naar het Azure DevOps-consolevenster en het logboekbestand.
- **Geavanceerde opties:** Als er geen logboeken zijn voor een van de geselecteerde gereedschappen, u ervoor kiezen om een waarschuwing of een fout in te loggen. Als u een fout inlogt, mislukt de taak.

Voor informatie over yaml-configuratie voor deze taak, raadpleegt u onze [Post Analysis YAML-opties](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Volgende stappen

Voor informatie over yaml-gebaseerde configuratie verwijzen wij u naar onze [YAML-configuratiehandleiding.](yaml-configuration.md)

Als u nog vragen heeft over de security code analysis extensie en de aangeboden tools, kijk dan op [onze FAQ pagina.](security-code-analysis-faq.md)

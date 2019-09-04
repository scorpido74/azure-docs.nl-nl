---
title: Aanpassings handleiding voor de analyse van beveiligings code Microsoft Azure
description: In dit artikel wordt beschreven hoe u de taken in de uitbrei ding analyse van micro soft-beveiligings code aanpast
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
ms.openlocfilehash: c0d49c3ce06f6fa72daf7aff466ef65e09ced09a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241801"
---
# <a name="configure-and-customize-the-build-tasks"></a>De build-taken configureren en aanpassen

In dit artikel worden de configuratie opties beschreven die beschikbaar zijn in elk van de build-taken. Het artikel begint met de taken voor hulpprogram ma's voor het analyseren van beveiligings code. De taak wordt beëindigd met de taken na de verwerking.

## <a name="anti-malware-scanner-task"></a>Anti-Malware-Scanner taak

>[!NOTE]
> De anti-malware scanner build-taak vereist een build-agent met Windows Defender ingeschakeld. Gehoste Visual Studio 2017 en hoger bieden een dergelijke agent. De taak bouwen wordt niet uitgevoerd op de gehoste agent van Visual Studio 2015.
>
> Hoewel hand tekeningen niet kunnen worden bijgewerkt op deze agents, moeten hand tekeningen altijd minder dan drie uur oud zijn.

Details van taak configuratie worden weer gegeven in de volgende scherm afbeelding en tekst.

![De taak voor het maken van een anti-malware scanner configureren](./media/security-tools/5-add-anti-malware-task600.png)

In de keuze lijst **type** van de scherm opname is **standaard** geselecteerd. Selecteer **aangepast** om opdracht regel argumenten op te geven waarmee de scan wordt aangepast.

Windows Defender gebruikt de Windows Update-client om hand tekeningen te downloaden en te installeren. Als de handtekening update mislukt in uw build-agent, komt de **HRESULT** -fout code waarschijnlijk van Windows Update.

[Windows Update Zie fout codes per onderdeel](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) en het TechNet [-artikel Windows Update Agent-fout codes](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)voor meer informatie over Windows Update fouten en de oplossing ervan.

## <a name="binskim-task"></a>BinSkim-taak

> [!NOTE]
> Voordat u de BinSkim-taak kunt uitvoeren, moet uw build voldoen aan een van de volgende voor waarden:
>    - Uw build produceert binaire artefacten van beheerde code.
>    - U hebt binaire artefacten doorgevoerd die u wilt analyseren met BinSkim.

Details van taak configuratie worden weer gegeven in de volgende scherm afbeelding en lijst.

![De BinSkim-build-taak configureren](./media/security-tools/7-binskim-task-details.png)

- Stel de build-configuratie in op debug, zodat er fout opsporings bestanden voor PDB worden geproduceerd. BinSkim gebruikt deze bestanden om problemen in de binaire uitvoer bestanden terug te wijzen naar de bron code.
- Ga als volgt te voor komen en uw eigen opdracht regel te maken:
     - Selecteer in de lijst **type** de optie **Basic**.
     - Selecteer **analyseren**in de lijst met **functies** .
- Voer bij **doel**een of meer specificaties in voor een bestand, map of filter patroon. Deze specificaties worden omgezet in een of meer binaire bestanden die moeten worden geanalyseerd:
    - Meerdere opgegeven doelen moeten worden gescheiden door een punt komma (;).
    - Een aanduiding kan één bestand zijn of joker tekens bevatten.
    - De directory specificaties moeten altijd eindigen \\op *.
    - Voorbeelden:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- Als u de **opdracht regel** in de lijst **type** selecteert, moet u binskim. exe uitvoeren:
     - Zorg ervoor dat de eerste argumenten voor binskim. exe de woorden **analyse** zijn, gevolgd door een of meer padspecificatie. Elk pad kan een volledig pad zijn of een pad dat relatief is ten opzichte van de bronmap.
     - Meerdere doel paden moeten worden gescheiden door een spatie.
     - U kunt de optie **/o** of **/output** weglaten. De uitvoer waarde wordt toegevoegd of vervangen.
     - De standaard-opdracht regel configuraties worden als volgt weer gegeven.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > Het afsluitende \\sterretje is belang rijk als u directory's voor het doel opgeeft.

Zie de [Gebruikers handleiding voor BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)voor meer informatie over BinSkim-opdracht regel argumenten, regels op id of afsluit codes.

## <a name="credential-scanner-task"></a>Taak referentie scanner

Details van taak configuratie worden weer gegeven in de volgende scherm afbeelding en lijst.

![De taak voor het maken van referentie scanner configureren](./media/security-tools/3-taskdetails.png)

Beschik bare opties zijn:

  - **Uitvoer indeling**: Beschik bare waarden zijn **TSV**, **CSV**, **SARIF**en **PREfast**.
  - **Versie van het hulp programma**: U wordt aangeraden om het **meest recente**te selecteren.
  - **Map scannen**: De map voor de opslag plaats die moet worden gescand.
  - **Zoek functies bestands type**: De opties voor het zoeken naar het Zoek bestand dat wordt gebruikt voor het scannen.
  - **Onderdrukkings bestand**: Een [JSON](https://json.org/) -bestand kan problemen in het uitvoer logboek onderdrukken. Zie de sectie Veelgestelde vragen van dit artikel voor meer informatie over onderdrukkings scenario's.
  - **Uitgebreide uitvoer**: Zelf uitleg.
  - **Batch grootte**: Het aantal gelijktijdige threads dat wordt gebruikt om referentie scanner uit te voeren. De standaardwaarde is 20. Mogelijke waarden variëren van 1 tot en met 2.147.483.647.
  - **Overeenkomende time-out**: De hoeveelheid tijd in seconden die nodig is voor het afstemmen van een zoek programma voordat de controle wordt afgebroken.
  - **Grootte van Lees buffer voor bestands scan**: De grootte in bytes van de buffer die wordt gebruikt terwijl de inhoud wordt gelezen. De standaard waarde is 524.288.  
  - **Maximum aantal gelezen bytes van bestands scan**: Het maximum aantal bytes dat tijdens de inhouds analyse van een bestand moet worden gelezen. De standaard waarde is 104.857.600.
  - **Beheer opties** > **Voer deze taak uit**: Hiermee geeft u op wanneer de taak wordt uitgevoerd. Selecteer **aangepaste voor waarden** om complexere voor waarden op te geven.
  - **Version**: De versie van de taak build in azure DevOps. Deze optie wordt niet regel matig gebruikt.

## <a name="microsoft-security-risk-detection-task"></a>Micro soft-taak risico detectie

> [!NOTE]
> U moet een account maken en configureren met de micro soft security risico Detection-service (MSRD) voordat u de MSRD-taak gebruikt. Deze service vereist een afzonderlijk voorbereidings proces. In tegens telling tot de meeste andere taken in deze uitbrei ding, is voor deze taak een afzonderlijk abonnement met MSRD vereist.
>
> Raadpleeg de [micro soft-beveiligings Risico's](https://aka.ms/msrddocs) en [detectie van micro soft-beveiligings Risico's: ](https://docs.microsoft.com/security-risk-detection/how-to/) Instructies.

Details voor het configureren van deze taak worden weer gegeven in de volgende lijst. Voor elk UI-element kunt u de muis aanwijzer boven dat element houden om hulp te krijgen.

   - **Azure DevOps service-eindpunt naam voor MSRD**: Met een Gene riek type Azure DevOps service-eind punt worden de URL van uw onboarded MSRD-exemplaar en uw REST API toegangs token opgeslagen. Als u een dergelijk eind punt hebt gemaakt, kunt u dit hier opgeven. Als dat niet het geval is, selecteert u de koppeling **beheren** om een nieuw service-eind punt voor deze MSRD-taak te maken en te configureren.
   - **Account-id**: Een GUID die kan worden opgehaald uit de URL van het MSRD-account.
   - **Url's naar binaire bestanden**: Een door punt komma's gescheiden lijst met openbaar beschik bare Url's. De fuzzy-computer gebruikt deze Url's om de binaire bestanden te downloaden.
   - **Url's van de Seed-bestanden**: Een door punt komma's gescheiden lijst met openbaar beschik bare Url's. Op de computer met fuzzy worden deze Url's gebruikt om de zaden te downloaden. Het opgeven van deze waarde is optioneel als de Seed-bestanden worden gedownload met de binaire bestanden.
   - **Type besturings systeem**: Het besturings systeem (OS)-platform van machines waarop de taak wordt uitgevoerd. Beschik bare waarden zijn **Windows** en **Linux**.
   - **Windows-editie/Linux-editie**: De versie van het besturings systeem van machines waarop de taak wordt uitgevoerd. U kunt de standaard waarde overschrijven als uw computers een andere versie van het besturings systeem hebben.
   - **Pakket installatie script**: Uw script dat moet worden uitgevoerd op een test machine. Met dit script worden het test doel programma en de afhankelijkheden ervan geïnstalleerd voordat de taak wordt verzonden.
   - **Para meters voor taak verzending**:
       - **Seed-map**: Het pad naar de map op de fuzzy computer die de zaden bevat.
       - **Seed-extensie**: De bestandsnaam extensie van de zaden.
       - **Uitvoerbaar stuur programma testen**: Het pad naar het uitvoer bare bestand van het doel op de fuzzy-computer.
       - **Uitvoer bare architectuur van stuur programma testen**: De architectuur van het uitvoer bare bestand van het doel. Beschik bare waarden zijn **x86** en **amd64**.
       - **Argumenten voor testen van Stuur Programma's**: De opdracht regel argumenten die zijn door gegeven aan het uitvoer bare bestand van de test. Het argument% test file%, inclusief de aanhalings tekens, wordt automatisch vervangen door het volledige pad naar het doel bestand. Dit bestand wordt geparseerd door het test stuur programma en is vereist.
       - Het testen van het **stuur programma wordt afgesloten wanneer de test is voltooid**: Schakel dit selectie vakje in als het test stuur programma na voltooiing moet worden beëindigd. Schakel deze optie uit als het test stuur programma geforceerd moet worden gesloten.
       - **Maximum duur (in seconden)** : Een schatting van de langste redelijkerwijs verwachte tijd die het doel programma nodig heeft voor het parseren van een invoer bestand. Hoe nauw keuriger de schatting, des te efficiëntere uitvoeringen van de app.
       - Het **test stuur programma kan herhaaldelijk worden uitgevoerd**: Schakel dit selectie vakje in als het test stuur programma herhaaldelijk kan worden uitgevoerd zonder dat dit afhankelijk is van een permanente of gedeelde globale status.
       - De naam van het **test stuur programma kan worden gewijzigd**: Schakel dit selectie vakje in als het uitvoer bare bestand van het test stuur programma kan worden hernoemd en nog steeds correct werkt.
       - **De toepassing die u wilt uitvoeren, wordt uitgevoerd als één besturingssysteem proces**: Schakel dit selectie vakje in als het test stuur programma wordt uitgevoerd onder één installatie van een besturings systeem. Schakel deze optie uit als het test stuur programma extra processen heeft gestart.

## <a name="roslyn-analyzers-task"></a>Roslyn-analyse taak

> [!NOTE]
> Voordat u de Roslyn-analyse taken kunt uitvoeren, moet uw build voldoen aan de volgende voor waarden:
> - De build-definitie bevat de ingebouwde MSBuild-of VSBuild-bouw taak voor C# het compileren of Visual Basic van code. De taak analyse functies is afhankelijk van de invoer en uitvoer van de ingebouwde taak voor het uitvoeren van de MSBuild-compilatie met Roslyn-analyse functies ingeschakeld.
> - De build-agent die deze build-taak uitvoert, heeft Visual Studio 2017 versie 15,5 of hoger geïnstalleerd, zodat de compiler versie 2,6 of hoger wordt gebruikt.

Details van taak configuratie worden weer gegeven in de volgende lijst en opmerking.

Beschik bare opties zijn:

- **RuleSet**: Waarden zijn **sdl vereist**, **SDL wordt aanbevolen**of uw eigen aangepaste regelset.
- **Versie van analyse**functies: U wordt aangeraden om het **meest recente**te selecteren.
- **Onderdrukkings bestand voor waarschuwingen van compiler**: Een tekst bestand met een lijst met waarschuwings-Id's die worden onderdrukt.
- **Beheer opties** > **Voer deze taak uit**: Hiermee geeft u op wanneer de taak wordt uitgevoerd. Kies **aangepaste voor waarden** om complexere voor waarden op te geven.

> [!NOTE]
> - Roslyn-analyse functies zijn geïntegreerd met de compiler en kunnen alleen worden uitgevoerd als onderdeel van de CSC. exe-compilatie. Deze taak vereist daarom de compiler opdracht die eerder in de build werd uitgevoerd om opnieuw te worden afgespeeld of opnieuw uit te voeren. Deze herhaling of uitvoering wordt uitgevoerd door een query uit te voeren op Visual Studio Team Services (VSTS) voor de MSBuild build-taak Logboeken.
>
>   Er is geen andere manier om de opdracht regel voor het compileren van MSBuild op betrouw bare wijze op te halen uit de build-definitie. U wordt aangeraden een tekstvak voor vrije vorm toe te voegen om gebruikers in staat te stellen hun opdracht regels in te voeren. Het is echter lastig om deze opdracht regels up-to-date te houden en te synchroniseren met de hoofd build.
>
>   Aangepaste builds vereisen een herhaling van de volledige set opdrachten, niet alleen voor Compileer opdrachten. In dergelijke gevallen is het inschakelen van Roslyn-analyse functies niet mogelijk en niet betrouwbaar.
>
> - Roslyn-analyse functies zijn geïntegreerd met de compiler. Voor het aanroepen van Roslyn-analyse functies is compilatie vereist.
>
>   Deze nieuwe build-taak wordt geïmplementeerd door C# projecten die al zijn gebouwd opnieuw te compileren. In de nieuwe taak worden alleen de taken MSBuild en VSBuild gemaakt in dezelfde build-of build-definitie als de oorspronkelijke taak. In dit geval maakt de nieuwe taak echter gebruik van Roslyn-analyse functies ingeschakeld.
>
>   Als de nieuwe taak wordt uitgevoerd op dezelfde agent als de oorspronkelijke taak, overschrijft de uitvoer van de nieuwe taak de uitvoer van de oorspronkelijke taak in de map *s* sources. Hoewel de uitvoer van de build hetzelfde is, adviseren we om MSBuild uit te voeren, uitvoer te kopiëren naar de map voor het klaarzetten van artefacten en vervolgens Roslyn-analyse functies uit te voeren.

Bekijk voor aanvullende bronnen voor de taak Roslyn-analysen [de op Roslyn gebaseerde analyse](https://docs.microsoft.com/dotnet/standard/analyzers/) functies op Microsoft docs.

U kunt het analyse pakket dat is geïnstalleerd en wordt gebruikt door deze build-taak vinden op de NuGet-pagina [micro soft. CodeAnalysis. FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

## <a name="tslint-task"></a>TSLint-taak

Ga naar de [TSLint github opslag plaats](https://github.com/palantir/tslint)voor meer informatie over TSLint.

>[!NOTE] 
>Als u op de hoogte bent van de [TSLint github opslag plaats](https://github.com/palantir/tslint) -start pagina, wordt aangegeven dat TSLint op een andere locatie in 2019 wordt afgeschaft. Micro soft onderzoekt [ESLint](https://github.com/eslint/eslint) als een alternatieve taak.

## <a name="publish-security-analysis-logs-task"></a>Taak beveiligings analyse logboeken publiceren

Details van taak configuratie worden weer gegeven in de volgende scherm afbeelding en lijst.

![De taak voor het maken van Logboeken voor het publiceren van beveiligings analyses configureren](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Artefact naam**: Een wille keurige teken reeks-id.
- **Type artefact**: Afhankelijk van uw selectie kunt u Logboeken publiceren naar uw Azure DevOps-server of naar een gedeeld bestand dat toegankelijk is voor de build-agent.
- **Hulpprogram ma's**: U kunt ervoor kiezen om logboeken voor specifieke hulpprogram ma's te bewaren of u kunt **alle Hulpprogram ma's** selecteren om alle logboeken te bewaren.

## <a name="security-report-task"></a>Beveiligings rapport taak

Details van de configuratie van het beveiligings rapport worden weer gegeven in de volgende scherm afbeelding en lijst.

![De taak voor het maken van beveiligings rapporten configureren](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Rapporten**: Selecteer een van de **pijplijn console**, het **TSV-bestand**en de **HTML-bestands** indelingen. Voor elke geselecteerde indeling wordt één rapport bestand gemaakt.
- **Hulpprogram ma's**: Selecteer de hulpprogram ma's in de build-definitie waarvoor u een overzicht van de gedetecteerde problemen wilt. Voor elk geselecteerd hulp programma kunt u ook kiezen of u alleen fouten wilt weer geven of fouten en waarschuwingen in het samenvattings rapport wilt weer geven.
- **Geavanceerde opties**: Als er geen logboeken voor een van de hulpprogram ma's zijn geselecteerd, kunt u een waarschuwing of een fout registreren. Als u een fout registreert, mislukt de taak.
- **Basis logboeken map**: U kunt de map basis logboeken aanpassen waarin de logboeken moeten worden gevonden. Deze optie wordt meestal niet gebruikt.

## <a name="post-analysis-task"></a>Taak na de analyse

Details van taak configuratie worden weer gegeven in de volgende scherm afbeelding en lijst.

![De taak voor het maken van een post-analyse configureren](./media/security-tools/a-post-analysis600.png)

- **Hulpprogram ma's**: Selecteer de hulpprogram ma's in de build-definitie waarvoor u een compilatie-afbreek voorwaarde voorwaardelijk wilt injecteren. Voor elk geselecteerd hulp programma kunt u een optie selecteren om te bepalen of u alleen op fouten wilt onderbreken of op fouten en waarschuwingen.
- **Rapport**: U kunt eventueel de resultaten schrijven die de build onderbreken veroorzaken. De resultaten worden geschreven naar het venster en het logboek bestand van de Azure DevOps-console.
- **Geavanceerde opties**: Als er geen logboeken voor een van de hulpprogram ma's zijn geselecteerd, kunt u een waarschuwing of een fout registreren. Als u een fout registreert, mislukt de taak.

## <a name="next-steps"></a>Volgende stappen

Bekijk [onze pagina met veelgestelde vragen](security-code-analysis-faq.md)als u meer vragen hebt over de uitbrei ding voor de analyse van beveiligings code en de aangeboden hulpprogram ma's.

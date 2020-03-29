---
title: Overzicht van documentatiedocumentatie van Microsoft Security Code Analysis
description: Dit artikel is een overzicht van de Microsoft Security Code Analysis-extensie
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
ms.openlocfilehash: 963bc909b69962cded0a50d717e3a653d3d69769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851465"
---
# <a name="about-microsoft-security-code-analysis"></a>Informatie over Microsoft Security Code Analysis

Met de Microsoft Security Code Analysis-extensie kunnen teams analyse van beveiligingscode toevoegen aan hun Azure DevOps continuous integration and delivery (CI/CD) pipelines. Deze analyse wordt aanbevolen door de [Secure Development Lifecycle (SDL)](https://www.microsoft.com/securityengineering/sdl/practices) experts van Microsoft.

Een consistente UX vereenvoudigt de beveiliging door de complexiteit van hardlooptools te verbergen. Met nuGet-gebaseerde levering van de tools, teams niet langer nodig om de installatie of update van tooling te beheren. Met zowel command-line als basisinterfaces voor buildtaken kunnen alle gebruikers zoveel controle hebben over de tools als ze willen.

Teams kunnen ook gebruik maken van krachtige nabewerkingsmogelijkheden, zoals:

- Logboeken publiceren voor retentie.
- Het genereren van bruikbare, op ontwikkelaars gerichte rapporten.
- Buildbreaks configureren bij regressietests.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Waarom zou ik Microsoft Security Code Analysis gebruiken?

### <a name="security-simplified"></a>Beveiliging vereenvoudigd

Het toevoegen van hulpprogramma's voor Microsoft Security Code Analysis aan uw Azure DevOps-pijplijn is net zo eenvoudig als het toevoegen van nieuwe taken. Pas de taken aan of gebruik hun standaardgedrag. Taken worden uitgevoerd als onderdeel van uw Azure DevOps-pijplijn en produceren logboeken die vele soorten resultaten beschrijven.

### <a name="clean-builds"></a>Schone builds

Nadat u de eerste problemen hebt opgelost die door de hulpprogramma's zijn gerapporteerd, u de extensie configureren om builds op nieuwe problemen te verbreken.Het opzetten van continue integratie bouwt voort op elke pull-aanvraag is eenvoudig.

### <a name="set-it-and-forget-it"></a>Stel het in en vergeet het

Standaard blijven de bouwtaken en -tools up-to-date. Als er een bijgewerkte versie van een hulpprogramma is, hoeft u deze niet te downloaden en te installeren. De uitbreiding zorgt voor de update voor u.

### <a name="under-the-hood"></a>Onderhuids

De bouwtaken van de extensie verbergen de complexiteit van:
  - Hulpprogramma's voor statische beveiliging uitvoeren.
  - De resultaten van logboekbestanden verwerken om een overzichtsrapport te maken of de build te verbreken.

## <a name="microsoft-security-code-analysis-tool-set"></a>Hulpprogrammaset voor Microsoft-beveiligingscodeanalyse

De Microsoft Security Code Analysis-extensie maakt de nieuwste versies van belangrijke analysetools voor u beschikbaar. De extensie bevat zowel door Microsoft beheerde tools als open-source tools.

Deze hulpprogramma's worden automatisch gedownload naar de cloudgehoste agent nadat u de bijbehorende buildtaak hebt gebruikt om de pijplijn te configureren en uit te voeren.

In deze sectie worden de set hulpprogramma's weergegeven die momenteel beschikbaar zijn in de extensie. Kijk uit voor de toevoeging van meer tools. Stuur ons ook uw suggesties voor tools die u wilt dat we toevoegen.

### <a name="anti-malware-scanner"></a>Anti-malwarescanner

De buildtaak anti-malwarescanner is nu opgenomen in de microsoft Security Code Analysis-extensie. Deze taak moet worden uitgevoerd op een buildagent waarop Windows Defender al is geïnstalleerd. Zie voor meer informatie de [website van Windows Defender](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim (BinSkim)

BinSkim is een lichtgewicht scanner (Portable Executable) die compilerinstellingen, linker-instellingen en andere beveiligingsrelevante kenmerken van binaire bestanden valideert. Deze buildtaak biedt een opdrachtregelwrapper rond de consoletoepassing binskim.exe. BinSkim is een open-source tool. Zie [BinSkim op GitHub](https://github.com/Microsoft/binskim)voor meer informatie.

### <a name="credential-scanner"></a>Referentiescanner

Wachtwoorden en andere geheimen opgeslagen in broncode zijn een belangrijk probleem. Credential Scanner is een eigen statische analyse tool die helpt bij het oplossen van dit probleem. De tool detecteert referenties, geheimen, certificaten en andere gevoelige inhoud in uw broncode en uw build-uitvoer.

### <a name="microsoft-security-risk-detection"></a>Detectie van Microsoft-beveiligingsrisico's

Microsoft Security Risk Detection (MSRD) is een cloudservice voor fuzz-tests. Het identificeert exploiteerbare beveiligingsbugs in software. Deze service vereist een apart abonnement en activering. Zie het [MSRD Developer Center](https://docs.microsoft.com/security-risk-detection/)voor meer informatie.

### <a name="roslyn-analyzers"></a>Roslyn Analyzers

Roslyn Analyzers is microsoft's compiler-geïntegreerde tool voor het statisch analyseren van managed C# en Visual Basic code. Zie [Roslyn-gebaseerde analysers](https://docs.microsoft.com/dotnet/standard/analyzers/)voor meer informatie.

### <a name="tslint"></a>TSLint TSLint

TSLint is een uitbreidbaar statische analysetool dat TypeScript-code controleert op leesbaarheid, onderhoud en fouten in functionaliteit. Het wordt breed gedragen door moderne redacteuren en bouwsystemen. U het aanpassen met uw eigen pluisregels, configuraties en formatters. TSLint is een open-source tool. Zie [TSLint op GitHub](https://github.com/palantir/tslint)voor meer informatie.

## <a name="analysis-and-post-processing-of-results"></a>Analyse en nabewerking van de resultaten

De Microsoft Security Code Analysis-extensie heeft ook drie postprocessingtaken. Met deze taken u de resultaten analyseren die worden gevonden door de beveiligingstaken. Wanneer deze taken aan een pijplijn worden toegevoegd, volgen deze taken meestal alle andere gereedschapstaken.

### <a name="publish-security-analysis-logs"></a>Logboeken voor beveiligingsanalyse publiceren

De buildtaak Logboeken voor het bouwen van beveiligingslogboeken behoudt de logboekbestanden van de beveiligingshulpprogramma's die tijdens het bouwen worden uitgevoerd. U deze logboeken lezen voor onderzoek en follow-up.

U de logboekbestanden publiceren naar Azure Artifacts als een .zip-bestand. U ze ook kopiëren naar een toegankelijk bestandsaandeel van uw privé-buildagent.

### <a name="security-report"></a>Beveiligingsrapport

De taak Beveiligingsrapport build onteert de logboekbestanden. Deze bestanden worden gemaakt door de beveiligingstools die tijdens de build worden uitgevoerd. De buildtaak maakt vervolgens één overzichtsrapportbestand. Dit bestand toont alle problemen gevonden door de analyse tools.

U deze taak configureren om resultaten te rapporteren voor specifieke hulpprogramma's of voor alle hulpprogramma's. U ook kiezen welk probleemniveau u wilt rapporteren, zoals alleen fouten of fouten en waarschuwingen.

### <a name="post-analysis-build-break"></a>Post-Analyse (build break)

Met de post-analyse build taak, u injecteren een build break die opzettelijk zorgt ervoor dat een build te mislukken. U injecteert een buildbreak als een of meer analysetools problemen in de code melden.

U deze taak zo configureren dat de build wordt afgebroken voor problemen die worden gevonden door specifieke hulpprogramma's of alle hulpprogramma's. U het ook configureren op basis van de ernst van gevonden problemen, zoals fouten of waarschuwingen.

>[!NOTE]
>Volgens het ontwerp slaagt elke buildtaak als de taak is voltooid. Dit geldt ongeacht of een tool problemen vindt, zodat de build kan worden uitgevoerd tot voltooiing door dat alle hulpprogramma's kunnen worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg onze [Onboarding- en installatiehandleiding](security-code-analysis-onboard.md)voor instructies over het aan boord gaan en installeren van Microsoft Security Code Analysis.

Zie onze [configuratiehandleiding](security-code-analysis-customize.md) of [YAML-configuratiehandleiding](yaml-configuration.md)voor meer informatie over het configureren van de buildtaken.

Als u nog vragen heeft over de extensie en de aangeboden tools, kijk dan op onze [FAQ pagina.](security-code-analysis-faq.md)

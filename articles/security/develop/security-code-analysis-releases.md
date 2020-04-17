---
title: Microsoft Security Code Analysis releases
description: In dit artikel worden aankomende releases voor de Microsoft Security Code Analysis-extensie beschreven
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a07ce7437d664baca0cfdc310dbc2631f41fdbcc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462033"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft Security Code Analysis releases en roadmap

Microsoft Security Code Analysis team in samenwerking met Developer Support is trots om recente en aankomende verbeteringen aan te kondigen aan onze MSCA-extensie. Zie Roadmap hieronder.

![Releases](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-on-april-1-2020"></a>Credential Scanner v2.0: Uitgebracht op 1 april 2020

### <a name="innovations--improvements"></a>Innovaties & verbeteringen

- **Kernmotor**

   - Gemiddelde prestatie-upgrade van 25% met bijna lineaire looptijden
   - Context/evidence based searching en ranking voor meer nauwkeurigheid
   - Verbeteringen in algemene wachtwoorddetecties en bijpassende logica voor voor de hand liggende tijdelijke aanduidingen (bijvoorbeeld fakePassword)

- **Dekking** - Ondersteuning voor meer dan 25 geheime typen, waaronder de volgende topgevraagd:

   - Toegangscode voor fabricaccountcertificaat
   - Clientgeheim/API-sleutel
   - HTTP-autorisatiekoptekst
   - Amazon S3 Client Secret Access Key
   - Azure Active Directory Client Access-token
   - Azure-functiemaster/API-sleutel
   - Power BI-toegangssleutel
   - Wachtwoordpatroon voor Azure Resource Manager-sjabloon

- **Uitvoer**

   - Ondersteuning voor SARIF 2.1- en CSV-bestandsuitvoerbestandsindelingen

## <a name="binskim-v160-to-be-released-on-april-2020"></a>BinSkim v1.6.0: Wordt uitgebracht in april 2020

### <a name="improvements"></a>Verbeteringen

- FUNCTIE: Update naar definitieve SARIF v2 (versie 2.1.16). Dit maakt resultaten caching bij het passeren van --hashes op de command-line, een aanzienlijke prestatieverbetering bij het recursief analyseren van mappen met meerdere kopieën van scandoelen.
- BUG FIX: Fix typo in BA2021. DoNotMarkWritableSectionsAsExecutable output.
- PRESTATIES: Elimineer PDB-laden voor alle niet-gemengde modus voor beheerde assemblages, inclusief IL Library (van tevoren gecompileerd) binaries.
- FALSE NEGATIVE FIX: Controleer of een PDB geplaatst naast een binaire eigenlijk overeenkomt met de binaire onder analyse
- FUNCTIE: Argument --local-symbol-directories opgeven om aanvullende (lokale, niet-symbool-server) PDB-opzoeklocaties op te geven
- FALSE POSITIVE FIX: Skip PDB-driven analysis for the generated .NET core native bootstrap exe (which is not user-controllable code).

## <a name="whats-next-in-fy20"></a>Wat is de volgende stap in FY20?

- Java-beveiligingsanalysetool
- Python-beveiligingsanalyse, hulpprogramma
- ES Lint vervangt TS Lint voor TypeScript en JavaScript

## <a name="next-steps"></a>Volgende stappen

Raadpleeg onze [Onboarding- en installatiehandleiding](security-code-analysis-onboard.md)voor instructies over het aan boord gaan en installeren van Microsoft Security Code Analysis.

Als u meer vragen heeft over de extensie en de aangeboden tools, kijk dan op onze [FAQ-pagina.](security-code-analysis-faq.md)

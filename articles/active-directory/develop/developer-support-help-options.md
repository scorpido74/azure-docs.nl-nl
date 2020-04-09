---
title: Ondersteunings- en helpopties voor Azure AD-app-ontwikkelaars
description: Weet hoe u hulp en ondersteuning krijgen voor ontwikkelingsgerelateerde vragen en problemen bij het maken van toepassingen die integreren met Microsoft-identiteiten (Azure Active Directory en Microsoft-account)
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: ea1afd2ecac8974c0e865e235288da545a9f1244
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885528"
---
# <a name="support-and-help-options-for-developers"></a>Ondersteunings- en Help-opties voor ontwikkelaars

Als u net begint te integreren met Azure Active Directory (Azure AD), Microsoft-identiteiten of Microsoft Graph API, of wanneer u een nieuwe functie voor uw toepassing implementeert, zijn er momenten waarop u hulp van de community moet krijgen of de ondersteuningsopties die u als ontwikkelaar hebt, moet begrijpen. In dit artikel u inzicht krijgen in deze opties, waaronder:

> [!div class="checklist"]
> * Hoe u zoeken of uw vraag niet is beantwoord door de community of dat er al een bestaande documentatie bestaat voor de functie die u probeert te implementeren
> * In sommige gevallen wilt u alleen onze ondersteuningstools gebruiken om u te helpen een specifiek probleem te debuggen
> * Als u het antwoord niet vinden dat u nodig hebt, u een vraag stellen op *Stack Overflow*
> * Als u een probleem vindt met een van onze verificatiebibliotheken, u een *GitHub-probleem aandeorde* doen
> * Tot slot, als u met iemand wilt praten, wilt u misschien een ondersteuningsverzoek openen

## <a name="search"></a>Search

Als u een ontwikkelingsgerelateerde vraag hebt, u het antwoord mogelijk vinden in de documentatie, [GitHub-voorbeelden](https://github.com/azure-samples)of antwoorden op [stackoverflow-vragen.](https://www.stackoverflow.com)

### <a name="scoped-search"></a>Scoped zoeken

Voor snellere resultaten u uw zoekopdracht uitvoeren naar Stack Overflow, de documentatie en de codevoorbeelden met behulp van de volgende query in uw favoriete zoekmachine:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Waar *{Uw zoektermen}* overeenkomen met uw zoekwoorden.

## <a name="use-the-development-support-tools"></a>De hulpprogramma's voor ontwikkelingsondersteuning gebruiken

| Hulpprogramma  | Beschrijving  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Plak een id of toegangstoken om de namen en waarden van de claims te decoderen. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Hulpprogramma waarmee u verzoeken indienen en antwoorden zien tegen de Microsoft Graph API. |

## <a name="post-a-question-to-stack-overflow"></a>Plaats een vraag naar Stack Overflow

Stack Overflow is het voorkeurskanaal voor ontwikkelingsgerelateerde vragen. Hier zijn leden van de ontwikkelaarscommunity en Microsoft-teamleden direct betrokken bij het oplossen van uw problemen.

Als u via zoeken geen antwoord op uw vraag vinden, dient u een nieuwe vraag in bij Stack Overflow. Gebruik een van de volgende tags wanneer u vragen stelt om de community te helpen uw vraag sneller te identificeren en te beantwoorden:

|Component/gebied  | Tags |
|---------|---------|
| ADAL-bibliotheek | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| MSAL-bibliotheek     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| OWIN middleware  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Elk ander gebied met betrekking tot verificatie- of autorisatieonderwerpen | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

De volgende berichten van Stack Overflow bevatten tips over het stellen van vragen en het toevoegen van broncode. Volg deze richtlijnen om de kans voor communityleden om uw vraag snel te beoordelen en erop te reageren te vergroten:

* [Hoe stel ik een goede vraag](https://stackoverflow.com/help/how-to-ask)
* [Een minimaal, volledig en verifieerbaar voorbeeld maken](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Een GitHub-probleem maken

Als u een bug of probleem vindt met betrekking tot onze bibliotheken, u een probleem aandeorde stellen in onze GitHub-repositories. Omdat onze bibliotheken open source zijn, u ook een pull-aanvraag indienen.

Zie het volgende voor een lijst met bibliotheken en hun GitHub-repositories:

* [ADAL-bibliotheken (Azure Active Directory Authentication Library)](../azuread-dev/active-directory-authentication-libraries.md) en GitHub-repositories
* [MSAL-bibliotheken (Microsoft Authentication Library)](reference-v2-libraries.md) en GitHub-repositories

## <a name="open-a-support-request"></a>Een ondersteuningsverzoek openen

Als je met iemand wilt praten, kun je een ondersteuningsverzoek openen. Als u een Azure-klant bent, zijn er verschillende ondersteuningsopties beschikbaar. Zie [deze pagina](https://azure.microsoft.com/support/plans/)om plannen te vergelijken. Ondersteuning voor ontwikkelaars is ook beschikbaar voor Azure-klanten. Zie [deze pagina](https://azure.microsoft.com/support/plans/developer/)voor informatie over het kopen van ondersteuningsplannen voor ontwikkelaars.

* Als u al een Azure Support Plan hebt, [opent u hier een ondersteuningsverzoek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Als u geen Azure-klant bent, u ook een ondersteuningsaanvraag openen bij Microsoft via [onze commerciële ondersteuning.](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)

U ook proberen een [virtuele agent](https://support.microsoft.com/contactus/?ws=support) om ondersteuning te verkrijgen of vragen te stellen.

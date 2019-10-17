---
title: Ondersteunings-en Help-opties voor Azure AD-App-ontwikkel aars | Microsoft Docs
description: Informatie over het verkrijgen van hulp en ondersteuning voor ontwikkel vragen en problemen bij het maken van een toepassing die is geïntegreerd met micro soft-identiteiten (Azure Active Directory en Microsoft-account)
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a160f78f69ed559c576147e9758c837d5ae9b77e
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429977"
---
# <a name="support-and-help-options-for-developers"></a>Ondersteunings- en Help-opties voor ontwikkelaars

Als u net begint met de integratie met Azure Active Directory (Azure AD), micro soft-identiteiten of de Microsoft Graph-API, of wanneer u een nieuwe functie implementeert voor uw toepassing, weet u zeker dat u hulp nodig hebt bij de Community of dat u bekend bent met de ondersteunings opties die u als ontwikkelaar hebt. Dit artikel helpt u bij het begrijpen van deze opties, waaronder:

> [!div class="checklist"]
> * Zoeken of uw vraag niet is beantwoord door de Community of dat er al een bestaande documentatie is voor de functie die u wilt implementeren.
> * In sommige gevallen wilt u de ondersteunings Programma's gebruiken om u te helpen bij het opsporen van een specifiek probleem.
> * Als u het antwoord dat u nodig hebt niet kunt vinden, kunt u een vraag stellen op *stack overflow*
> * Als u een probleem met een van onze verificatie bibliotheken tegen komt, kunt u een *github* -probleem veroorzaken
> * Ten slotte, als u met iemand moet praten, wilt u mogelijk een ondersteunings aanvraag openen

## <a name="search"></a>Search

Als u een vraag hebt over de ontwikkeling, kunt u het antwoord mogelijk vinden in de documentatie, voor [beelden van github](https://github.com/azure-samples)of antwoorden op [stack overflow](https://www.stackoverflow.com) vragen.

### <a name="scoped-search"></a>Zoek opdracht in bereik

Bereik uw zoek opdracht naar Stack Overflow, de documentatie en de code voorbeelden met behulp van de volgende query in uw favoriete zoek machine om sneller resultaten te bereiken:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Waar *{uw zoek termen}* overeenkomen met uw zoek woorden.

## <a name="use-the-development-support-tools"></a>De hulpprogram ma's voor ontwikkelings ondersteuning gebruiken

| Hulpprogramma  | Beschrijving  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Plak een ID of toegangs token om de claim namen en-waarden te decoderen. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Hulp programma waarmee u aanvragen kunt doen en reacties op de Microsoft Graph-API ziet. |

## <a name="post-a-question-to-stack-overflow"></a>Een vraag stellen aan Stack Overflow

Stack Overflow is het voorkeurs kanaal voor vragen met betrekking tot ontwikkeling. Hier zijn leden van de ontwikkelaars community en micro soft-team leden rechtstreeks betrokken bij het helpen om uw problemen op te lossen.

Als u via zoeken geen antwoord op uw vraag kunt vinden, dient u een nieuwe vraag naar Stack Overflow te verzenden. Gebruik een van de volgende tags bij het stellen van vragen om de community te helpen uw vraag sneller te identificeren en te beantwoorden:

|Onderdeel/gebied  | Tags |
|---------|---------|
| ADAL-bibliotheek | [adal](https://stackoverflow.com/questions/tagged/adal) |
| MSAL-bibliotheek     | [msal](https://stackoverflow.com/questions/tagged/msal) |
| OWIN-middleware  | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[Azure-AD-B2B]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure-B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[Azure-AD-B2C]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[micro soft-Graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Elk ander gebied dat betrekking heeft op verificatie-of autorisatie-onderwerpen | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

De volgende berichten van Stack Overflow bevatten tips voor het stellen van vragen en het toevoegen van de bron code. Volg deze richt lijnen om de kans te verg Roten dat leden van de Community uw vraag snel kunnen beoordelen en beantwoorden:

* [Hoe kan ik een goede vraag stellen](https://stackoverflow.com/help/how-to-ask)
* [Een mini maal, volledig en verifieerbaar voor beeld maken](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Een GitHub-probleem maken

Als u een fout of probleem met betrekking tot onze bibliotheken vindt, kunt u een probleem veroorzaken in onze GitHub-opslag plaatsen. Omdat onze bibliotheken open source zijn, kunt u ook een pull-aanvraag indienen.

Voor een lijst met bibliotheken en hun GitHub-opslag plaatsen raadpleegt u het volgende:

* [ADAL](active-directory-authentication-libraries.md) -bibliotheken en github-opslag plaatsen
* [MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md), [MSAL. Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)en [MSAL. obj _c](https://github.com/AzureAD/microsoft-authentication-library-for-objc) libraries en github-opslag plaatsen

## <a name="open-a-support-request"></a>Een ondersteunings aanvraag openen

Als u contact moet opnemen met iemand, kunt u een ondersteunings aanvraag openen. Als u een Azure-klant bent, zijn er verschillende ondersteunings opties beschikbaar. Zie [Deze pagina voor het](https://azure.microsoft.com/support/plans/)vergelijken van plannen. Ondersteuning voor ontwikkel aars is ook beschikbaar voor Azure-klanten. Zie [Deze pagina](https://azure.microsoft.com/support/plans/developer/)voor meer informatie over het kopen van ondersteunings abonnementen voor ontwikkel aars.

* Als u al een ondersteunings abonnement voor Azure hebt, kunt u [hier een ondersteunings aanvraag openen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Als u geen Azure-klant bent, kunt u ook een ondersteunings aanvraag openen met micro soft via [onze commerciële ondersteuning](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

U kunt ook een [virtuele agent](https://support.microsoft.com/contactus/?ws=support) proberen om ondersteuning te verkrijgen of vragen te stellen.

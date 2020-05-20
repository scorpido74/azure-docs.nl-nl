---
title: Samen werken met anderen-QnA Maker
description: ''
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: ef0823fc60424dc861ab70f4112e4689c1b97fb0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665980"
---
# <a name="collaborate-with-other-authors-and-editors"></a>Samen werken met andere auteurs en editors

Samen werken met andere auteurs en editors met op rollen gebaseerd toegangs beheer (RBAC) die zijn geplaatst op uw QnA Maker bron.

## <a name="access-is-provided-on-the-qna-maker-resource"></a>De toegang wordt tot de QnA Maker resource verschaft

Alle machtigingen worden bepaald door de machtigingen die op de QnA Maker bron worden geplaatst. Deze machtigingen worden uitgelijnd op lees-, schrijf-, publicatie-en volledige toegang.

Deze RBAC-functie omvat:
* Azure Active Directory (AAD) is 100% achterwaarts compatibel met verificatie op basis van sleutels voor eigen aren en inzenders. Klanten kunnen verificatie op basis van een sleutel of op RBAC gebaseerde verificatie gebruiken in hun aanvragen.
* U kunt snel auteurs en editors toevoegen aan alle kennis grondslagen in de resource, omdat het besturings element op het niveau van de resource staat en niet op het niveau van de Knowledge Base.

## <a name="access-is-provided-by-a-defined-role"></a>De toegang wordt verkregen door een gedefinieerde rol

[!INCLUDE [RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>Verificatiestroom

In het volgende diagram ziet u de stroom, van het perspectief van de auteur, voor het aanmelden bij de QnA Maker Portal en het gebruik van de ontwerp-Api's.

> [!div class="mx-imgBorder"]
> ![In het volgende diagram ziet u de stroom, van het perspectief van de auteur, voor het aanmelden bij de QnA Maker Portal en het gebruik van de ontwerp-Api's.](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|Stappen|Beschrijving|
|--|--|
|1|Portal verkrijgt token voor QnA Maker bron.|
|2|Portal roept de juiste QnA Maker authoring API (APIM) aan waarbij het token wordt door gegeven in plaats van sleutels.|
|3|QnA Maker-API valideert het token.|
|4 |QnA Maker-API-QnAMaker-service wordt aangeroepen.|

Als u van plan bent om de ontwerp-Api's aan te roepen] (.. /How-To/collaborate-knowledge-base.md), meer informatie over het instellen van verificatie.

## <a name="authenticate-by-qna-maker-portal"></a>Verifiëren door QnA Maker Portal

Als u de QnA Maker Portal ontwerpt en gebruikt, nadat u [de juiste rol aan de resource voor een samen werker hebt toegevoegd](../How-To/collaborate-knowledge-base.md), beheert de QnA Maker-Portal alle toegangs machtigingen.

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>Verifiëren door QnA Maker Api's en Sdk's

Als u de Api's met behulp van REST of de Sdk's wilt ontwerpen en samen werken, moet u [een service-principal maken](../../authentication.md#assign-a-role-to-a-service-principal) om de verificatie te beheren.

## <a name="next-step"></a>Volgende stap

* Een kennis database ontwerpen voor [talen](design-language-culture.md) en voor [client toepassingen](integration-with-other-applications.md)
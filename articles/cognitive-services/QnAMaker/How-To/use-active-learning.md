---
title: Verbeter kennisbank - QnA Maker
description: Verbeter de kwaliteit van je kennisbank met actief leren. Bestaande vragen controleren, accepteren of weigeren, toevoegen zonder bestaande vragen te verwijderen of te wijzigen.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 33b3c547b0aea9a1e235bf8a05d01aa16b468a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071131"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Actief leren gebruiken om uw Knowledge Base te verbeteren

[Met actief leren](../Concepts/active-learning-suggestions.md) u de kwaliteit van uw kennisbasis verbeteren door alternatieve vragen voor te stellen. Gebruikersinzendingen worden in aanmerking genomen en worden weergegeven als suggesties in de lijst met alternatieve vragen. U hebt de flexibiliteit om deze suggesties als alternatieve vragen toe te voegen of ze af te wijzen.

Je kennisbank verandert niet automatisch. Om elke wijziging van kracht te laten worden, moet u de suggesties accepteren. Deze suggesties voegen vragen toe, maar wijzigen of verwijderen bestaande vragen niet.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>Runtime-versie upgraden om actief leren te gebruiken

Active Learning wordt ondersteund in runtime versie 4.4.0 en hoger. Als uw kennisbank is gemaakt op een eerdere versie, [kunt u uw runtime upgraden](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) om deze functie te gebruiken.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Actief leren inschakelen voor alternatieve vragen

Actief leren is standaard uitgeschakeld. Schakel het in om voorgestelde vragen te zien. Nadat u actief leren hebt ingeschakeld, moet u informatie uit de client-app naar QnA Maker verzenden. Zie [Architecturalflow voor het gebruik van GenerateAnswer en Train API's van een bot voor](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)meer informatie.

1. Selecteer **Publiceren** om de kennisbank te publiceren. Actieve leerquery's worden alleen verzameld via het eindpunt voor het voorspellen van de GenerateAnswer API-voorspelling. De query's naar het deelvenster Testen in de QnA Maker-portal hebben geen invloed op actief leren.

1. Als u actief leren wilt inschakelen in de QnA Maker-portal, gaat u naar de rechterbovenhoek, selecteert u uw **naam**, ga naar [**Service-instellingen**](https://www.qnamaker.ai/UserSettings).

    ![Schakel de voorgestelde vraagalternatieven voor actief leren in op de pagina Service-instellingen. Selecteer uw gebruikersnaam in het menu rechtsboven en selecteer Service-instellingen.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Zoek de QnA Maker-service en schakel **Active Learning in.**

    > [!div class="mx-imgBorder"]
    > [![Schakel op de pagina Service-instellingen in op de functie Actief leren. Als u de functie niet inschakelen, moet u mogelijk uw service upgraden.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > De exacte versie van de voorgaande afbeelding wordt alleen als voorbeeld weergegeven. Uw versie kan anders zijn.

    Zodra **Active Learning** is ingeschakeld, stelt de kennisbank op gezette tijden nieuwe vragen voor op basis van door de gebruiker ingediende vragen. U **Actief leren** uitschakelen door de instelling opnieuw te schakelen.

## <a name="review-suggested-alternate-questions"></a>Voorgestelde alternatieve vragen bekijken

[Bekijk alternatieve voorgestelde vragen](improve-knowledge-base.md) op de **pagina Bewerken** van elke kennisbank.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennisdatabase maken](./manage-knowledge-bases.md)
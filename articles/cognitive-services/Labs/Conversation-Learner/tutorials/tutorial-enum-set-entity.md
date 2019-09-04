---
title: Wanneer u ENUM-entiteiten wilt gebruiken en ENTITEITs acties wilt instellen met een Conversation Learner model-Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Leer wanneer het nodig is om ENUM-entiteiten te gebruiken en ENTITEITs acties in te stellen met een Conversation Learner model.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 429570d81f7e15758d8ea60951bb4d01b96f8f2c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256488"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Wanneer u ENUM-entiteiten gebruikt en ENTITEITs acties INSTELT

In deze zelf studie wordt uitgelegd wanneer u ENUM-entiteiten (inventarisatie) en SET_ENTITY-acties moet gebruiken.

## <a name="video"></a>Video

[![Preview-versie van de zelf studie voor entiteiten instellen](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Wat wordt gedekt

In deze zelf studie worden twee nieuwe functies geïntroduceerd. Een nieuw type entiteit met de naam ENUM (kort voor opsomming) en een nieuw type actie, met de naam SET_ENTITY, die kan verwijzen naar een van deze Enum-waarden, en als de naam al aangeeft, wordt de entiteit ingesteld op deze waarde. Zoals hieronder wordt beschreven, worden deze nieuwe functies samen gebruikt en wordt uitgelegd wat ze zijn en hoe u ze hieronder kunt gebruiken. Voordat we de details weer geven, is het belang rijk om te begrijpen welk probleem deze functies kunnen oplossen.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Probleem

Er zijn gevallen in discussies waarbij de betekenis van woorden afhankelijk is van de context.  Normaal gesp roken worden de gelabelde sleutel woorden geleerd en geëxtraheerd met behulp van een language Standing service, maar in deze gevallen is het mogelijk dat deze systemen geen gebruik kunnen maken van voor beelden met een label.

Stel dat u deelneemt aan een gesprek tussen mensen in de buurt en dat u alleen het woord ' ja ' hoort. U weet niet wat het ' ja ' is om te bevestigen of te controleren omdat u de vraag niet hebt gedaan. De vraag wordt gevraagd voordat de context is. Dit is het antwoord. En omdat ' ja ' een veelvoorkomende reactie op veel verschillende vragen is, kan het niet worden geleerd door voor beelden te bieden zoals u dat zou doen met [aangepaste getrainde](04-introduction-to-entities.md) entiteiten. vervolgens zou het label ' ja ' moeten worden weer gegeven als die entiteit.

### <a name="example"></a>Voorbeeld

Laten we verder verduidelijken met het volgende voor beeld:

Tape Als Azure Cognitive Services?
Gebruiker Ja-bot: Bent u tevreden over Ice room?
Gebruiker Ja

In de vorige zelf studies hebben we gekeken naar [aangepaste getrainde](04-introduction-to-entities.md) entiteiten. het is ook mogelijk om een entiteit met de naam "likesCogServices" te maken en de eerste "ja" als deze entiteit te labelen.  Het systeem zou echter ook het tweede ' ja ' labelen. Wanneer we hebben geprobeerd om het label van de tweede ' ja ' te corrigeren op ' likesIceCream ', maken we een conflict van twee dezelfde invoer ' ja ', wat een afwijkend dingen is en die achterblijven.

In deze gevallen moet u ENUM-entiteiten en SET_ENTITY-acties gebruiken.

## <a name="when-to-use-enums-or-set_entity-actions"></a>Wanneer u ENUMs of SET_ENTITY-acties wilt gebruiken

Gebruik deze regels hieronder om te weten wanneer u ENUM-entiteiten en SET_ENTITY-acties moet gebruiken:

- De detectie of instelling van de entiteit is context afhankelijk
- Aantal mogelijke waarden is vast (Ja en Nee is twee waarden)

Met andere woorden, u kunt deze gebruiken voor eventuele gesloten prompts, zoals bevestigings vragen die altijd resulteren in Ja of Nee.

> [!NOTE]
> Er is momenteel een beperking van Maxi maal vijf waarden per Enum-entiteit. Elke waarde gebruikt een van de sleuven in de huidige limiet van 64. Zie [cl-waarden en-grenzen](../cl-values-and-boundaries.md)

Voorbeeld: Tape Is uw bestelling correct?
Gebruiker Ja

Wanneer de mogelijke waarden van de entiteit open-beëindigd zijn en niet zijn opgelost, moet u een alternatieve functie gebruiken, zoals een [verwachte entiteit](05-expected-entity.md).

Voorbeeld: Tape Hoe heet u?
Gebruiker Matt-bot: Wat is uw favoriete kleur?
Gebruiker Zilver

Deze prompts worden beschouwd als open beëindigd omdat ze kunnen worden beantwoord met wille keurige waarden.

## <a name="what"></a>Wat

### <a name="enum-entities"></a>Entiteiten opsommen

ENUM-entiteiten worden net als de andere entiteiten gemaakt. Net als bij een ' programmatische ' entiteit, kunt u geen woorden als deze entiteiten labelen. In plaats daarvan moeten ze worden ingesteld via code-of SET_ENTITY-acties.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Entiteits acties instellen

Zoals hierboven vermeld, worden de acties ' entiteit instellen ' eenvoudigweg ingesteld op een bekende Enum-waarde. U kunt dezelfde resultaten krijgen door een API-call back-actie te maken en met behulp van het geheugen beheer de entiteit in te stellen op een waarde. Bijvoorbeeld `memory.Set(entityName, entityValue)`. Als u deze code wilt schrijven en deze acties wilt maken, worden deze gelastig en moeilijk te beheren, dus Conversation Learner heeft speciale acties om dit werk te vergemakkelijken en deze acties automatisch te genereren wanneer ze worden gebruikt. Als er onafhankelijke acties worden uitgevoerd, blijft de mogelijkheid om deze op te stellen zonder te zijn gekoppeld aan andere acties of code in uw bot.

- Entiteits acties instellen kan alleen worden gemaakt wanneer wordt verwezen naar een waarde van een Enum-entiteit, dus u moet eerst een Enum-entiteit maken.
- Het instellen van entiteits acties is ook ' niet-await ' omdat deze geen zicht bare uitvoer hebben en moeten worden opgevolgd door een actie die de gebruiker kan zien.
- Het instellen van entiteits acties is onveranderlijk omdat u deze niet kunt bewerken nadat ze zijn gemaakt.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Automatisch actie genereren

Als er in uw model een Enum-entiteit bestaat, maakt Conversation Learner tijdelijke acties voor elk van de mogelijke waarden en maakt u deze beschikbaar om te selecteren tijdens de training. Bij selectie wordt de actie automatisch voor u gemaakt.

Als ik bijvoorbeeld een Enum-entiteit Maak met de waarden ' ja ' en ' nee ':

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Zelfs zonder expliciete acties voor deze nieuwe enum te maken, ziet u twee nieuwe acties die beschikbaar zijn tijdens de training:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Een bot maken met behulp van deze nieuwe functies

### <a name="requirements"></a>Vereisten

Voor deze zelf studie moet de bot van de algemene zelf studie worden uitgevoerd

    npm run tutorial-general

We gaan een bot maken voor het simuleren van een snelle voedsel-bestelling. Het heeft discrete waarden voor de grootte van drank en Fries (klein/gemiddeld/groot), en bevestigings vragen met Ja/nee-antwoorden. Beide entiteiten voldoen aan de twee regels hierboven dan context afhankelijke antwoorden en vaste waarden.

### <a name="create-the-model"></a>Het model maken

1. Klik in de Web-UI op importeren
2. Selecteer de zelf studie met de naam ' zelf studie-Enum-set-entity '

Hiermee gaat u naar de pagina model beheer.
U ziet dat het model al een aantal inventarisatie-entiteiten bevat en entiteits acties instellen.

### <a name="create-the-first-dialog"></a>Het eerste dialoog venster maken

1. Klik in het navigatie venster aan de linkerkant op ' dialoog vensters trainen ' en klik vervolgens op de knop Nieuw trainen dialoog venster.
2. Als gebruiker typt u in, "Hallo, ik wil graag een cokes best Ellen en Fries".
3. Klik op de knop Score acties

   > De gebruiker heeft geen grootten voor de drank of het Fries opgegeven. Daarom moeten ze worden gevraagd.

4. Selecteer de actie met reactie: "Welke drank wilt u graag?"
5. Als gebruiker typt u in, "groot"
6. Klik op de knop Score acties
7. Selecteer de actie SET_ENTITY-"drinkSize: LANGE
8. Selecteer de actie met reactie: "Welk formaat Fries wilt u graag?"
9. Als het gebruik type in, moet u ' um ' dit een medium maken.
10. Klik op de knop Score acties
11. Selecteer de actie SET_ENTITY-"friesSize: DRAGER
12. Selecteer de actie met reactie: "Wilt u uw kruiden zien?"
13. Als het gebruik type in, "ja"
14. Klik op de knop Score acties
15. Selecteer de actie SET_ENTITY-"condimentsConfirmation: KLIKT
16. Selecteer de actie met reactie: "OK, ik heb een bestelling voor een groot drank-en gemiddeld Fries. Is dat klopt? '
17. Als het gebruik type in, "ja"
18. Klik op de knop Score acties
19. Selecteer de actie SET_ENTITY-"orderConfirmation: KLIKT
20. Selecteer de actie met reactie: "OK, uw bestel nummer is 58. Een ogen blik geduld...
21. Klik op Opslaan om het dialoog venster te sluiten

U hebt zojuist uw eerste dialoog venster gemaakt met de acties INVENTARISeren en SET_ENTITY. U kunt veel meer combi Naties van de gebruiker om gedeeltelijke informatie op te geven of te experimenteren met andere typen meerkeuze vragen.

> [!NOTE]
> Tijdens de training worden tijdelijke aanduidingen weer geven voor de SET_ENTITY-acties, zoals
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> maar bij het maken van logboek dialoogvensters of het gebruik van geïmplementeerde bots kunnen ze niet worden weer geven.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Alternatieve invoer](./10-alternative-inputs.md)

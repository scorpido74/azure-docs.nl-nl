---
title: Multi-turn gesprekken - QnA Maker
description: Gebruik prompts en context om de meerdere beurten, bekend als multi-turn, voor uw bot van de ene vraag naar de andere te beheren. Multi-turn is de mogelijkheid om een heen-en-weer gesprek waar de vorige vraag context beïnvloedt de volgende vraag en antwoord.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: abdde09fbb1f6b066772366c5cea933824cb5864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220620"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Opvolgprompts gebruiken om meerdere beurten in een gesprek te maken

Gebruik vervolgprompts en context om de meerdere beurten te beheren, bekend als _multi-turn,_ voor uw bot van de ene vraag naar de andere.

Bekijk de volgende demonstratievideo om te zien hoe multi-turn werkt:

[![Multi-turn gesprek in QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Wat is een multi-turn gesprek?

Sommige vragen kunnen niet in één beurt worden beantwoord. Wanneer u uw clienttoepassingsgesprekken (chatbot) ontwerpt, kan een gebruiker een vraag stellen die moet worden gefilterd of verfijnd om het juiste antwoord te bepalen. U maakt deze stroom door de vragen mogelijk door de gebruiker te presenteren met *follow-up prompts*.

Wanneer een gebruiker een vraag stelt, retourneert QnA Maker het antwoord _en_ eventuele vervolgprompts. Met dit antwoord u de vervolgvragen als keuzes presenteren.

> [!CAUTION]
> Multi-turn prompts worden niet uit FAQ-documenten gehaald. Als u multi-turn extractie nodig hebt, verwijdert u de vraagtekens die de QnA-paren als veelgestelde vragen aanwijzen.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Voorbeeld multi-turn gesprek met chat bot

Met multi-turn beheert een chatbot een gesprek met een gebruiker om het uiteindelijke antwoord te bepalen, zoals in de volgende afbeelding wordt weergegeven:

![Een dialoogvenster met meerdere beurten die een gebruiker door een gesprek leiden](../media/conversational-context/conversation-in-bot.png)

In de vorige afbeelding heeft een gebruiker een gesprek gestart door **Mijn account**in te voeren . De kennisbank heeft drie gekoppelde vraag-en-antwoordparen. Om het antwoord te verfijnen, selecteert de gebruiker een van de drie keuzes in de kennisbank. De vraag (#1), heeft drie follow-up prompts, die worden gepresenteerd in de chat bot als drie opties (#2).

Wanneer de gebruiker een optie (#3) selecteert, wordt de volgende lijst met raffinageopties (#4) weergegeven. Deze volgorde gaat verder (#5) totdat de gebruiker het juiste, uiteindelijke antwoord (#6) bepaalt.


### <a name="use-multi-turn-in-a-bot"></a>Multi-turn in een bot gebruiken

Nadat u uw KB hebt gepubliceerd, u de knop **Bot maken** selecteren om uw QnA Maker-bot te implementeren in Azure-botservice. De prompts worden weergegeven in de chatclients die u voor uw bot hebt ingeschakeld.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Een multiturngesprek maken vanuit de structuur van een document

Wanneer u een kennisbank maakt, wordt in de sectie **Uw KB invullen** het selectievakje **Meerdere-draaiextractie inschakelen van URL's, .pdf- of .docx-bestanden** weergegeven.

![Selectievakje voor het inschakelen van multi-turn extractie](../media/conversational-context/enable-multi-turn.png)

Wanneer u deze optie selecteert, haalt QnA Maker de hiërarchie uit die in de documentstructuur aanwezig is. De hiërarchie wordt omgezet in follow-up prompts en de wortel van de hiërarchie dient als de bovenliggende QnA. In sommige documenten heeft de hoofdmap van de hiërarchie geen inhoud die als antwoord kan dienen, u de 'Standaardantwoordtekst' verstrekken die moet worden gebruikt als vervangende antwoordtekst om dergelijke hiërarchieën te extraheren.

De multiturnstructuur kan alleen worden afgeleid uit URL's, PDF-bestanden of DOCX-bestanden. Bekijk voor een voorbeeld van structuur een afbeelding van een [PDF-bestand met Microsoft Surface-gebruiker](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf).

![! [Voorbeeld van structuur in een gebruikershandleiding] (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>Uw eigen multi-turn document bouwen

Als u een document met meerdere beurten maakt, moet u rekening houden met de volgende richtlijnen:

* Gebruik koppen en subkoppen om hiërarchie aan te duiden. U bijvoorbeeld h1 om de bovenliggende QnA en h2 aan te duiden om de QnA aan te duiden die als prompt moet worden genomen. Gebruik kleine kopgrootte om de volgende hiërarchie aan te duiden. Gebruik geen stijl, kleur of een ander mechanisme om structuur in uw document te impliceren, QnA Maker haalt de multi-turn prompts niet uit.

* Het eerste teken van de rubriek moet worden gekapitaliseerd.

* Beëindig een kop niet met `?`een vraagteken.

* U het [voorbeelddocument](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) als voorbeeld gebruiken om uw eigen multiturndocument te maken.

### <a name="adding-files-to-a-multi-turn-kb"></a>Bestanden toevoegen aan een KB met meerdere beurten

Wanneer u een hiërarchisch document toevoegt, bepaalt QnA Maker vervolgprompts uit de structuur om een gespreksstroom te maken.

1. Selecteer in QnA Maker een bestaande kennisbank die is gemaakt met **Multi-turnextractie inschakelen uit URL's, .pdf- of .docx-bestanden.** Ingeschakeld.
1. Ga naar de pagina **Instellingen,** selecteer het bestand of de URL die u wilt toevoegen.
1. **Red en train** de kennisbank.

> [!Caution]
> Ondersteuning voor het gebruik van een geëxporteerd TSV- of XLS-multiturn-kennisbestand als gegevensbron voor een nieuwe of lege kennisbank wordt niet ondersteund. U moet dat bestandstype **importeren** vanaf de pagina **Instellingen** van de QnA Maker-portal om geëxporteerde multi-turnprompts toe te voegen aan een kennisbank.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Maak kennisbank met multi-turn prompts met de API maken

U een kennisaanvraag maken met multi-turn prompts met behulp van de [QnA Maker Create API.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create) De prompts worden `context` toegevoegd in `prompts` de array van de eigenschap.

## <a name="show-questions-and-answers-with-context"></a>Vragen en antwoorden met context weergeven

Verminder de weergegeven vraag-en-antwoordparen tot alleen die met contextuele gesprekken.

Selecteer **Weergaveopties**en selecteer **Vervolgens Context weergeven**. In de lijst worden vraag-en-antwoordparen weergegeven die vervolgaanwijzingen bevatten.

![Vraag-en-antwoordparen filteren op contextuele gesprekken](../media/conversational-context/filter-question-and-answers-by-context.png)

De context met meerdere draaien wordt weergegeven in de eerste kolom.

![! [De kolom 'Context (VOORVERTONING)' ] (.. /media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

In de voorgaande afbeelding geeft **#1** vette tekst in de kolom aan, die de huidige vraag aangeeft. De bovenliggende vraag is het bovenste item in de rij. Alle vragen hieronder zijn de gekoppelde vraag-en-antwoord paren. Deze items zijn selecteerbaar, zodat u onmiddellijk naar de andere contextitems gaan.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Een bestaand vraag-en-antwoordpaar toevoegen als vervolgprompt

De oorspronkelijke vraag, **Mijn account,** heeft follow-up aanwijzingen, zoals **Accounts en aanmelden**.

![De antwoorden en follow-upaanwijzingen voor 'Accounts en aanmelden'](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Voeg een vervolgprompt toe aan een bestaand vraag-en-antwoordpaar dat momenteel niet is gekoppeld. Omdat de vraag niet is gekoppeld aan een vraag-en-antwoordpaar, moet de huidige weergave-instelling worden gewijzigd.

1. Als u een bestaand vraag-en-antwoordpaar als vervolgprompt wilt koppelen, selecteert u de rij voor het vraag- en antwoordpaar. Zoek voor de Surface-handleiding naar **Afmelden** om de lijst te verkleinen.
1. Selecteer in de rij voor **Afmelden**in de kolom **Antwoord** de optie **Vervolgprompt toevoegen**.
1. Voer in de velden in het **pop-upvenster Vervolgprompt** de volgende waarden in:

    |Veld|Waarde|
    |--|--|
    |Weergavetekst|Voer **Het apparaat uitschakelen in**. Dit is aangepaste tekst die wordt weergegeven in de vervolgprompt.|
    |Alleen in context| Schakel dit selectievakje in. Een antwoord wordt alleen geretourneerd als de vraag context aangeeft.|
    |Koppeling naar antwoord|Voer **Het aanmeldingsscherm** gebruiken in om het bestaande vraag- en antwoordpaar te vinden.|


1.  Eén wedstrijd is terug. Selecteer dit antwoord als vervolg en selecteer **Opslaan**.

    ![De pagina 'Vervolgprompt (PREVIEW)'](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Nadat u de vervolgprompt hebt toegevoegd, selecteert u **Opslaan en trainen** in de bovenste navigatienavigatie.

### <a name="edit-the-display-text"></a>De weergavetekst bewerken

Wanneer een vervolgprompt wordt gemaakt en een bestaand vraag-en-antwoordpaar wordt ingevoerd als **koppeling naar antwoord,** u nieuwe **weergavetekst**invoeren. Deze tekst vervangt de bestaande vraag niet en voegt geen nieuwe alternatieve vraag toe. Het staat los van die waarden.

1. Als u de weergavetekst wilt bewerken, zoekt en selecteert u de vraag in het veld **Context.**
1. Selecteer in de rij voor die vraag de vervolgprompt in de antwoordkolom.
1. Selecteer de weergavetekst die u wilt bewerken en selecteer **vervolgens Bewerken**.

    ![De opdracht Bewerken voor de weergavetekst](../media/conversational-context/edit-existing-display-text.png)

1. Wijzig in het **pop-upvenster Vervolgprompt** de bestaande weergavetekst.
1. Wanneer u klaar bent met het bewerken van de weergavetekst, selecteert u **Opslaan**.
1. In de bovenste **navigatiebalk, Opslaan en trainen**.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Een nieuw vraag-en-antwoordpaar toevoegen als vervolgprompt

Wanneer u een nieuw vraag-en-antwoordpaar toevoegt aan de kennisbank, moet elk paar als vervolgprompt aan een bestaande vraag worden gekoppeld.

1. Zoek op de werkbalk kennisbasis naar en selecteer het bestaande vraag-en-antwoordpaar voor **accounts en aanmelden.**

1. Selecteer in de kolom **Antwoord** voor deze vraag de optie **Vervolgprompt toevoegen**.
1. Maak onder **Follow-up prompt (PREVIEW)** een nieuwe follow-upprompt door de volgende waarden in te voeren:

    |Veld|Waarde|
    |--|--|
    |Weergavetekst|*Een Windows-account maken*. De aangepaste tekst die moet worden weergegeven in de vervolgprompt.|
    |Alleen in context|Schakel dit selectievakje in. Dit antwoord wordt alleen geretourneerd als de vraag context aangeeft.|
    |Koppeling naar antwoord|Voer de volgende tekst in als antwoord:<br>* [Maak](https://account.microsoft.com/) een Windows-account met een nieuw of bestaand e-mailaccount*.<br>Wanneer u de database opslaat en traint, wordt deze tekst geconverteerd. |
    |||

    ![Een nieuwe snelle vraag en antwoord maken](../media/conversational-context/create-child-prompt-from-parent.png)


1. Selecteer **Nieuw maken**en selecteer **Opslaan**.

    Met deze actie wordt een nieuw vraag-en-antwoordpaar gemaakt en wordt de geselecteerde vraag als vervolgprompt gekoppeld. De kolom **Context** voor beide vragen geeft een vervolgpromptrelatie aan.

1. Selecteer **Weergaveopties**en selecteer [**Vervolgens Context weergeven (VOORBEELD)**](#show-questions-and-answers-with-context).

    De nieuwe vraag laat zien hoe het is gekoppeld.

    ![Een nieuwe vervolgprompt maken](../media/conversational-context/new-qna-follow-up-prompt.png)

    De bovenliggende vraag geeft een nieuwe vraag weer als een van de keuzes.

    ![! [De kolom Context geeft voor beide vragen een vervolgpromptrelatie aan] (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Nadat u de vervolgprompt hebt toegevoegd, selecteert u **Opslaan en trainen** in de bovenste navigatiebalk.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Multi-turn inschakelen tijdens het testen van vervolgprompts

Wanneer u de vraag test met vervolgprompts in het **deelvenster Testen,** selecteert u **Multi-turn inschakelen**en voert u uw vraag in. Het antwoord bevat de follow-up aanwijzingen.

![Het antwoord omvat de follow-up aanwijzingen](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Als u multi-turn niet inschakelt, wordt het antwoord geretourneerd, maar worden vervolgprompts niet geretourneerd.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Een JSON-verzoek om een eerste antwoord en follow-upprompts terug te sturen

Gebruik het `context` lege object om het antwoord op de vraag van de gebruiker op te vragen en vervolgaanwijzingen op te nemen.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Een JSON-antwoord om een eerste antwoord en follow-upprompts terug te sturen

De vorige sectie vroeg om een antwoord en eventuele follow-up prompts naar **Accounts en aanmelden**. Het antwoord bevat de snelle informatie, die zich bevindt op *antwoorden[0].context*, en de tekst die aan de gebruiker wordt weergegeven.

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

De `prompts` array biedt `displayText` tekst in `qnaId` de eigenschap en de waarde. U deze antwoorden weergeven als de volgende weergegeven opties `qnaId` in de gespreksstroom en de geselecteerde antwoorden in het volgende verzoek terugsturen naar QnA Maker.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Een JSON-verzoek om een niet-eerste antwoord en follow-upprompts terug te sturen

Vul `context` het object op om de vorige context op te nemen.

In de volgende JSON-aanvraag is de huidige vraag *Windows Hello gebruiken om in te loggen* en de vorige vraag was accounts en *aanmelden*.

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
```

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Een JSON-antwoord om een niet-eerste antwoord en follow-upprompts terug te sturen

De QnA Maker _GenerateAnswer_ JSON-reactie bevat de `context` follow-upprompts `answers` in de eigenschap van het eerste item in het object:

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>De kennisbank opvragen met de QnA Maker ID

Als u een aangepaste toepassing bouwt met behulp van multi-turn-functie. In het antwoord van de eerste vraag worden eventuele `qnaId` vervolgaanwijzingen en de bijbehorende vragen geretourneerd. Nu je de ID hebt, kun je dit doorgeven in de aanvraaginstantie van de vervolgprompt. Als de aanvraaginstantie `qnaId`het , e.a. en het contextobject bevat (dat de vorige eigenschappen van QnA Maker bevat), retourneert GenerateAnswer de exacte vraag per ID, in plaats van het classificatiealgoritme te gebruiken om het antwoord te vinden door de vraagtekst.


## <a name="display-order-is-supported-in-the-update-api"></a>Weergavevolgorde wordt ondersteund in de Update-API

De [weergavetekst en weergavevolgorde](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), geretourneerd in het JSON-antwoord, wordt ondersteund voor bewerking door de [Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Multi-turnprompts toevoegen of verwijderen met de Update API

U multi-turn prompts toevoegen of verwijderen met behulp van de [QnA Maker Update API.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)  De prompts worden `context` toegevoegd in `promptsToAdd` de `promptsToDelete` array van de eigenschap en de array.

## <a name="export-knowledge-base-for-version-control"></a>Kennisbank exporteren voor versiebeheer

QnA Maker ondersteunt versiebeheer door multi-turn gespreksstappen op te nemen in het geëxporteerde bestand.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over contextuele gesprekken in dit [dialoogvenster voorbeeld](https://aka.ms/qnamakermultiturnsample) of meer informatie over [conceptueel botontwerp voor multi-turn gesprekken](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Een knowledge base migreren](../Tutorials/migrate-knowledge-base.md)

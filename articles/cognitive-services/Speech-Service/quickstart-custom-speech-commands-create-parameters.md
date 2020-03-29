---
title: 'Snelstart: een aangepaste opdracht maken met parameters (voorbeeld) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: In dit artikel voegt u parameters toe aan een toepassing aangepaste opdrachten.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348532"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Snelstart: een aangepaste opdracht maken met parameters (voorbeeld)

In het [vorige artikel](./quickstart-custom-speech-commands-create-new.md)hebben we een nieuw project voor aangepaste opdrachten gemaakt om te reageren op opdrachten zonder parameters.

In dit artikel zullen we deze toepassing uitbreiden met parameters, zodat het kan omgaan met het in- en uitschakelen van meerdere apparaten.

## <a name="create-parameters"></a>Parameters maken

1. Open het project [dat we eerder hebben gemaakt](./quickstart-custom-speech-commands-create-new.md)
1. Omdat de opdracht nu aan en uit gaat, wijzigt u de naam van de opdracht in 'Uitschakelen'
   - Plaats de plaats boven de naam van de opdracht en selecteer het pictogram bewerken om de naam te wijzigen
1. Een nieuwe parameter maken om aan te geven of de gebruiker het apparaat wil in- of uitschakelen
   - Het `+` pictogram naast de sectie Parameters selecteren

   > [!div class="mx-imgBorder"]
   > ![Parameter maken](media/custom-speech-commands/create-on-off-parameter.png)

   | Instelling            | Voorgestelde waarde     | Beschrijving                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Name               | OnOff               | Een beschrijvende naam voor uw parameter                                                                     |
   | Is wereldwijd          | Ongecontroleerde           | Selectievakje dat aangeeft of een waarde voor deze parameter wereldwijd wordt toegepast op alle opdrachten in het project |
   | Vereist           | Gecontroleerd             | Selectievakje dat aangeeft of een waarde voor deze parameter vereist is voordat de opdracht wordt voltooid          |
   | Antwoordsjabloon  | "- Aan of uit?"      | Een prompt om de waarde van deze parameter te vragen wanneer deze niet bekend is                                       |
   | Type               | Tekenreeks              | Het type parameter, zoals Getal, Tekenreeks of Datumtijd                                               |
   | Configuratie      | Tekenreekslijst         | Voor tekenreeksen beperkt een tekenreekslijst invoer tot een reeks mogelijke waarden                                      |
   | Lijstwaarden voor tekenreeksen | aan, uit             | Voor een parameter String List, de set mogelijke waarden en hun synoniemen                                |

   - Selecteer vervolgens `+` opnieuw het pictogram om een tweede parameter toe te voegen om de naam van de apparaten weer te geven. Voor dit voorbeeld, een tv en een fan

   | Instelling            | Voorgestelde waarde       | Beschrijving                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Name               | OnderwerpApparaat         | Een beschrijvende naam voor uw parameter                                                                     |
   | Is wereldwijd          | Ongecontroleerde             | Selectievakje dat aangeeft of een waarde voor deze parameter wereldwijd wordt toegepast op alle opdrachten in het project |
   | Vereist           | Gecontroleerd               | Selectievakje dat aangeeft of een waarde voor deze parameter vereist is voordat de opdracht wordt voltooid          |
   | Antwoordsjabloon  | "- Welk apparaat?"     | Een prompt om de waarde van deze parameter te vragen wanneer deze niet bekend is                                       |
   | Type               | Tekenreeks                | Het type parameter, zoals Getal, Tekenreeks of Datumtijd                                               |
   | Configuratie      | Tekenreekslijst           | Voor tekenreeksen beperkt een tekenreekslijst invoer tot een reeks mogelijke waarden                                      |
   | Lijstwaarden voor tekenreeksen | tv, ventilator               | Voor een parameter String List, de set mogelijke waarden en hun synoniemen                                |
   | Synoniemen (tv)      | televisie, televisie     | Optionele synoniemen voor elke mogelijke waarde van een tekenreekslijstparameter                                      |

## <a name="add-sample-sentences"></a>Voorbeeldzinnen toevoegen

Met parameters is het handig om voorbeeldzinnen toe te voegen die alle mogelijke combinaties bestrijken. Bijvoorbeeld:

1. Volledige parameterinformatie -`"turn {OnOff} the {SubjectDevice}"`
1. Gedeeltelijke parameterinformatie -`"turn it {OnOff}"`
1. Geen parameterinformatie -`"turn something"`

Met voorbeeldzinnen met verschillende hoeveelheden informatie kan de toepassing Aangepaste opdrachten zowel one-shot resoluties als multi-turn resoluties oplossen met gedeeltelijke informatie.

Met dat in gedachten bewerkt u de voorbeeldzinnen om de parameters te gebruiken zoals hieronder wordt voorgesteld.

> [!TIP]
> Gebruik in de editor Voorbeeldzinnen een krullende beugel om naar uw parameters te verwijzen. - `turn {OnOff} the {SubjectDevice}`Gebruik de voltooiing van tabbladen om te verwijzen naar eerder gemaakte parameters.

> [!div class="mx-imgBorder"]
> ![Voorbeeldzinnen met parameters](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Parameters toevoegen aan de regel Voltooiing

Wijzig de regel Voltooiing die u in [de vorige snelstart](./quickstart-custom-speech-commands-create-new.md)hebt gemaakt:

1. Voeg een nieuwe voorwaarde toe en selecteer De parameter Vereist. Selecteer `OnOff` beide en`SubjectDevice`
1. Bewerk de actie Spraakreactie om te gebruiken `OnOff` en: `SubjectDevice`

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Uitproberen

Open het chatpaneel Testen en probeer een paar interacties.

- Invoer: zet de tv uit
- Output: Ok, het uitschakelen van de tv

- Input: zet de televisie uit
- Output: Ok, het uitschakelen van de tv

- Invoer: schakel het uit
- Uitgang: Welk apparaat?
- Input: de tv
- Output: Ok, het uitschakelen van de tv

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Snelstart: aangepaste opdrachten gebruiken met aangepaste spraak (voorbeeld)](./quickstart-custom-speech-commands-select-custom-voice.md)

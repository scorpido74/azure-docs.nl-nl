---
title: 'Procedure: para meter configureren als entiteit externe entiteiten'
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u een teken reeks parameter configureert om te verwijzen naar de catalogus die wordt weer gegeven via een webeindpunt.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 45dba3b7f46ec558c46b8505da26fd3ef4de4cbc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284180"
---
# <a name="add-configurations-to-commands-parameters"></a>Configuraties toevoegen aan opdrachtparameters

In dit artikel vindt u meer informatie over geavanceerde parameter configuratie, waaronder:

 - Hoe parameter waarden kunnen behoren tot een set die extern is gedefinieerd voor de toepassing Custom commands
 - Validatie componenten toevoegen aan de waarde van de para meters

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:

> [!div class="checklist"]
> * [Procedure: een toepassing maken met eenvoudige opdrachten](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Procedure: para meters toevoegen aan opdrachten](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>Para meter als externe catalogus entiteit configureren

In deze sectie configureert u para meters van het type teken reeks om te verwijzen naar externe catalogi die worden gehost via een webeindpunt. Hierdoor kunt u de externe catalogus onafhankelijk bijwerken zonder wijzigingen aan te brengen in de toepassing aangepaste opdrachten. Deze benadering is handig in gevallen waarin de catalogus vermeldingen groot kunnen zijn in een getal.

Gebruik de para meter **SubjectDevice** uit de opdracht **TurnOnOff** . De huidige configuratie voor deze para meter **accepteert vooraf gedefinieerde invoer van interne catalogus**. Dit verwijst naar een statische lijst met apparaten zoals gedefinieerd in de parameter configuratie. We willen deze inhoud verplaatsen naar een externe gegevens bron die onafhankelijk kan worden bijgewerkt.

Als u dit wilt doen, moet u eerst een nieuw webeind punt toevoegen. Ga naar de sectie **Web-eind punten** in het linkerdeel venster en voeg een nieuw webeind punt toe met de volgende configuratie.

| Instelling | Voorgestelde waarde |
|----|----|
| Naam | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Methode | GET |


Als de voorgestelde waarde voor de URL niet voor u werkt, moet u een eenvoudig webeind punt configureren en hosten die een JSON retourneert dat bestaat uit een lijst met apparaten die kunnen worden beheerd. Het eind punt van het web moet een JSON retour neren met de volgende notatie:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```


Ga vervolgens naar de pagina **SubjectDevice** para meter Settings en wijzig de eigenschappen in het volgende.

| Instelling | Voorgestelde waarde |
| ----| ---- |
| Configuration | Vooraf gedefinieerde invoer accepteren van externe catalogus |                               
| Eind punt van catalogus | getDevices |
| Methode | GET |

Selecteer vervolgens **Opslaan**.

> [!IMPORTANT]
> U ziet geen optie voor het configureren van een para meter voor het accepteren van invoer uit een externe catalogus, tenzij u het webeindpunt hebt ingesteld in het gedeelte **Web endpoint** in het linkerdeel venster.

### <a name="try-it-out"></a>Probeer het eens

Selecteer **trainen** en wacht op voltooiing van de training. Zodra de training is voltooid, selecteert u **testen** en voert u enkele interacties uit.

* Invoer: inschakelen
* Uitvoer: welk apparaat wilt u beheren?
* Invoer: lichten
* Uitvoer: OK, de lampjes inschakelen

> [!NOTE]
> U kunt nu alle apparaten beheren die op het webeindpunt worden gehost. U moet de toepassing nog steeds trainen om de nieuwe wijzigingen te testen en de toepassing opnieuw te publiceren.

## <a name="add-validation-to-parameters"></a>Validatie toevoegen aan para meters

**Validaties** zijn constructies die van toepassing zijn op bepaalde parameter typen, waarmee u beperkingen kunt configureren voor de waarde van de para meter, en vragen om correctie als waarden niet binnen de beperkingen vallen. Ga naar [verwijzingen](./custom-commands-references.md) voor een volledige lijst met parameter typen die de validatie construct uitbreiden

Test de validaties met behulp van de **SetTemperature** -opdracht. Voer de volgende stappen uit om een validatie voor de para meter **Tempe ratuur** toe te voegen.

1. Selecteer de opdracht **SetTemperature** in het linkerdeel venster.
1. Selecteer **Tempe ratuur** in het middelste deel venster.
1. Selecteer **een validatie toevoegen** die aanwezig is in het rechterdeel venster.
1. Configureer in het venster **nieuwe validatie** de optie validatie als volgt en selecteer **maken**.


    | Parameter configuratie | Voorgestelde waarde | Beschrijving |
    | ---- | ---- | ---- |
    | Minimumwaarde | `60` | Voor numerieke para meters kan de minimum waarde van deze para meter aannemen |
    | Maximumwaarde | `80` | Voor numerieke para meters kan de maximum waarde van deze para meter aannemen |
    | Fout bericht |  Eenvoudige editor > eerste variatie >`Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Vragen om een nieuwe waarde te vragen als de validatie is mislukt |

    > [!div class="mx-imgBorder"]
    > ![Validatie van een bereik toevoegen](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>Probeer het eens

1. Selecteer een **trein** pictogram boven in het rechterdeel venster.

1. Zodra de training is voltooid, selecteert u **testen** en voert u enkele interacties uit:

    - Invoer: Stel de Tempe ratuur in op 72 graden
    - Uitvoer: OK, Tempe ratuur instellen op 72 graden
    - Invoer: Stel de Tempe ratuur in op 45 graden
    - Uitvoer: er kan alleen een Tempe ratuur tussen 60 en 80 graden worden ingesteld
    - Invoer: Maak in plaats daarvan 72 graden
    - Uitvoer: OK, Tempe ratuur instellen op 72 graden

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: interactie regels toevoegen](./how-to-custom-commands-add-interaction-rules.md)

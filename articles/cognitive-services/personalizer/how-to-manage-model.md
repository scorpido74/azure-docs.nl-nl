---
title: Model- en leerinstellingen beheren - Personalizer
description: Het machine-geleerde model en de leerinstellingen kunnen worden geëxporteerd voor back-up in uw eigen broncontrolesysteem.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624295"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Model- en leerinstellingen beheren

Het machine-geleerde model en de leerinstellingen kunnen worden geëxporteerd voor back-up in uw eigen broncontrolesysteem.

## <a name="export-the-personalizer-model"></a>Het personalizermodel exporteren

Bekijk in de sectie Resourcebeheer voor **model- en leerinstellingen**de naam van het model en de laatst bijgewerkte datum en exporteerhet huidige model. U de Azure-portal of de Personalizer API's gebruiken om een modelbestand te exporteren voor archiveringsdoeleinden.

![Huidig personalizermodel exporteren](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Gegevens wissen voor uw leerlus

1. Selecteer in de Azure-portal voor uw personalizerbron op de pagina **Model- en leerinstellingen** de optie **Gegevens wissen**.
1. Als u alle gegevens wilt wissen en de leerlus wilt herstellen naar de oorspronkelijke status, schakelt u alle drie de selectievakjes in.

    ![In Azure-portal u gegevens wissen uit personalizerbronnen.](./media/settings/clear-data-from-personalizer-resource.png)

    |Waarde|Doel|
    |--|--|
    |Geregistreerde personalisatie- en beloningsgegevens.|Deze logboekregistratiegegevens worden gebruikt in offline evaluaties. De gegevens wissen als u uw resource opnieuw instelt.|
    |Reset het Personalizer-model.|Dit model verandert op elke omscholing. Deze frequentie van training wordt opgegeven in de frequentie van **het uploadmodel** op de pagina **Configuratie.** |
    |Stel het leerbeleid in op standaard.|Als u het leerbeleid hebt gewijzigd als onderdeel van een offline evaluatie, wordt dit gereset naar het oorspronkelijke leerbeleid.|

1. Selecteer **Geselecteerde gegevens wissen** om het clearingproces te starten. Status wordt gerapporteerd in Azure-meldingen, in de rechterbovenhoek navigatie.

## <a name="import-a-new-learning-policy"></a>Een nieuw leerbeleid importeren

De [leerbeleidsinstellingen](concept-active-learning.md#understand-learning-policy-settings) bepalen de _hyperparameters_ van de modeltraining. Voer een [offline evaluatie](how-to-offline-evaluation.md) uit om een nieuw leerbeleid te vinden.

1. Open de [Azure-portal](https://portal.azure.com)en selecteer uw personalizerbron.
1. Selecteer **Model- en leerinstellingen** in de sectie **Resourcebeheer.**
1. Selecteer de knop **Uploaden** voor de **leerinstellingen importeren** die u hebt gemaakt met de hierboven opgegeven JSON-indeling.

    Wacht op de melding dat het leerbeleid is geüpload.

## <a name="export-a-learning-policy"></a>Een leerbeleid exporteren

1. Open de [Azure-portal](https://portal.azure.com)en selecteer uw personalizerbron.
1. Selecteer **Model- en leerinstellingen** in de sectie **Resourcebeheer.**
1. Selecteer voor de **leerinstellingen importeren** de knop **Leerinstellingen exporteren.** Hiermee wordt `json` het bestand opgeslagen op uw lokale computer.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het beheren van een leerbeleid](how-to-manage-model.md)

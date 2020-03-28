---
title: Gebruik de oplossing voor externe bewaking om apparaatproblemen op te sporen - Azure | Microsoft Documenten
description: In deze zelfstudie leert u hoe u regels en acties kunt gebruiken voor het automatisch detecteren van problemen met apparaten op basis van een drempelwaarde in de externe bewakingsoplossing.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 676f1133a516174478a456a97bc467e7770fe6e7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239516"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Zelfstudie: Problemen detecteren met apparaten die zijn verbonden met uw bewakingsoplossing

In deze zelfstudie configureert u de verbetering voor de externe bewakingsoplossing om problemen met verbonden IoT-apparaten te detecteren. Voor het detecteren van problemen met uw apparaten voegt u regels toe waarmee meldingen in het dashboard van de oplossing worden gegenereerd.

Om u kennis te laten maken met regels en meldingen, maakt de zelfstudie gebruik van een gesimuleerde koeler (chiller). De chiller wordt beheerd door een organisatie met de naam Contoso en is verbonden met de verbetering voor de externe bewakingsoplossing. Contoso heeft al een regel waarmee een kritieke melding wordt gegenereerd wanneer de druk in een chiller 298 PSI overschrijdt. Als operator bij Contoso wilt u chillers met problematische sensoren identificeren door te zoeken naar initiële drukpieken. U kunt dergelijke apparaten identificeren door een regel toe te voegen waarmee een melding wordt gegenereerd wanneer de druk in de chiller hoger wordt dan 150 PSI.

U bent ook gevraagd een kritieke melding voor een chiller te maken wanneer in de laatste vijf minuten de gemiddelde vochtigheid in het apparaat hoger was dan 80% en de temperatuur van het apparaat hoger dan 75 graden Fahrenheit.

In deze zelfstudie hebt u:

>[!div class="checklist"]
> * De regels in uw oplossing weergeven
> * Een regel maken
> * Een regel met meerdere voorwaarden maken
> * Een bestaande rol bewerken
> * Regels in- en uitschakelen

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="review-the-existing-rules"></a>De bestaande regels weergeven

De pagina **Regels** in de oplossingsversneller geeft een lijst van de huidige regels weer:

[![Pagina Regels](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Als u alleen de regels wilt zien die betrekking hebben op chillers, moet u een filter toepassen. U kunt meer informatie over een regel bekijken en deze bewerken wanneer u de regel in de lijst selecteert:

[![Regeldetails weergeven](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Een regel maken

Als u een regel wilt maken waarmee een waarschuwing wordt gegenereerd wanneer de druk in een chiller hoger wordt dan 150 PSI, klikt u op **Nieuwe regel**. Gebruik de volgende waarden om de regel te maken:

| Instelling          | Waarde                                 |
| ---------------- | ------------------------------------- |
| Regelnaam        | Waarschuwing voor chiller                       |
| Beschrijving      | De druk in de chiller heeft 150 PSI overschreden |
| Apparaatgroep     | **Chillers**-apparaatgroep             |
| Berekening      | Direct                               |
| Veld Voorwaarde 1| pressure                              |
| Operator Voorwaarde 1 | Groter dan                      |
| Waarde Voorwaarde 1    | 150                               |
| Ernstniveau  | Waarschuwing                               |

[![Waarschuwingsregel maken](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

Als u wilt de nieuwe regel wilt opslaan, klikt u op **Toepassen**.

U kunt zien wanneer de regel wordt geactiveerd op de pagina **Regels** of op de pagina **Dashboard**:

[![Waarschuwingsregel geactiveerd](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-an-advanced-rule"></a>Een geavanceerde regel maken

Klik op **Nieuwe regel ** als u een regel met meerdere voorwaarden wilt maken die een kritieke melding genereert wanneer in de laatste vijf minuten voor een chiller de gemiddelde vochtigheid hoger is dan 80% en de gemiddelde temperatuur hoger dan 75 graden Fahrenheit. Gebruik de volgende waarden om de regel te maken:

| Instelling          | Waarde                                 |
| ---------------- | ------------------------------------- |
| Regelnaam        | Kritieke vochtigheid en temp van chiller    |
| Beschrijving      | Vochtigheid en temperatuur zijn kritiek |
| Apparaatgroep     | **Chillers**-apparaatgroep             |
| Berekening      | Average                               |
| Periode      | 5                                     |
| Veld Voorwaarde 1| vochtigheid                              |
| Operator Voorwaarde 1 | Groter dan                      |
| Waarde Voorwaarde 1    | 80                                |
| Ernstniveau  | Kritiek                              |

[![Deel één van meerdere voorwaardenregel maken](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

Als u wilt de tweede voorwaarde wilt toevoegen, klikt u op '+ Voorwaarde toevoegen'. Gebruik de volgende waarden voor de nieuwe voorwaarde:

| Instelling          | Waarde                                 |
| ---------------- | ------------------------------------- |
| Veld Voorwaarde 2| temperatuur                           |
| Operator Voorwaarde 2 | Groter dan                      |
| Waarde Voorwaarde 2    | 75                                |

[![Deel twee van de voorwaarderegel maken](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

Als u wilt de nieuwe regel wilt opslaan, klikt u op **Toepassen**.

U kunt zien wanneer de regel wordt geactiveerd op de pagina **Regels** of op de pagina **Dashboard**:

[![Meervoudige voorwaarderegel geactiveerd](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>Een bestaande rol bewerken

Als u een bestaande regel wilt wijzigen, selecteert u de regel in de lijst met regels en klikt u op **Bewerken**:

[![Regel bewerken](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>Een regel uitschakelen

Als u een regel tijdelijk wilt uitschakelen, kunt u dat doen in de lijst met regels. Kies de regel die u wilt uitschakelen en kies **Uitschakelen**. De **Status** van de regel in de lijst verandert om aan te geven dat de regel nu is uitgeschakeld. Een regel die u eerder hebt uitgeschakeld kunt u met behulp van dezelfde procedure weer inschakelen.

[![Regel uitschakelen](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

U kunt meerdere regels tegelijk in- en uitschakelen door meerdere regels in de lijst te selecteren.

## <a name="delete-a-rule"></a>Een regel verwijderen

Als u een regel permanent wilt verwijderen, kunt u deze verwijderen in de lijst met regels. Selecteer de regel die u wilt verwijderen en kies **Verwijderen**.

[![Regel verwijderen](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-expanded.png#lightbox)

Wanneer u hebt bevestigd dat u de regel wilt verwijderen, kunt u meldingen die aan de regel zijn gekoppeld, verwijderen op de pagina **Onderhoud**.

[![Regel verwijderen](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-expanded.png#lightbox)

U kunt slechts één regel tegelijk verwijderen.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de pagina **Regels** in de verbetering voor de externe bewakingsoplossing kunt gebruiken voor het maken en beheren van regels die meldingen in de oplossing activeren. Voor informatie over het gebruik van de oplossingsversneller voor het beheren en configureren van uw verbonden apparaten, gaat u verder met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Apparaten configureren en beheren die zijn verbonden met uw bewakingsoplossing](iot-accelerators-remote-monitoring-manage.md)
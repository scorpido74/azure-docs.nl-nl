---
title: Personalizer configureren
description: Serviceconfiguratie omvat hoe de service beloningen behandelt, hoe vaak de service wordt verkend, hoe vaak het model wordt omgeschoold en hoeveel gegevens worden opgeslagen.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: ac31a9f907defeb44dbd4748a4395d3aec34d30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219355"
---
# <a name="configure-personalizer-learning-loop"></a>Gepersonaliseerde leerlus configureren

Serviceconfiguratie omvat hoe de service beloningen behandelt, hoe vaak de service wordt verkend, hoe vaak het model wordt omgeschoold en hoeveel gegevens worden opgeslagen.

Configureer de leerlus op de **configuratiepagina** in de Azure-portal voor die personalizerbron.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="configure-rewards-for-the-feedback-loop"></a>Beloningen configureren voor de feedbacklus

Configureer de service voor het gebruik van beloningen door uw leerlus. Wijzigingen in de volgende waarden stellen het huidige personalizermodel opnieuw in en worden omscholen met de laatste 2 dagen aan gegevens.

> [!div class="mx-imgBorder"]
> ![De beloningswaarden voor de feedbacklus configureren](media/settings/configure-model-reward-settings.png)

|Waarde|Doel|
|--|--|
|Beloon wachttijd|Hiermee stelt u de duur in waarin Personalizer beloningswaarden verzamelt voor een Rang-gesprek, vanaf het moment dat de Rang-aanroep plaatsvindt. Deze waarde wordt bepaald door te vragen: "Hoe lang moet Personalizer wachten op beloningen oproepen?" Elke beloning die na dit venster aankomt, wordt geregistreerd, maar niet gebruikt om te leren.|
|Standaardbeloning|Als personalizer geen beloningsoproep ontvangt tijdens het wachttijdvenster beloning dat is gekoppeld aan een rangcall, wijst de personalisator de standaardbeloning toe. Standaard en in de meeste scenario's is de standaardbeloning nul (0).|
|Beloningsaggregatie|Als er meerdere beloningen worden ontvangen voor dezelfde Rank API-aanroep, wordt deze aggregatiemethode gebruikt: **som** of **vroegst**. Vroegst kiest de vroegste ontvangen score en gooit de rest weg. Dit is handig als u een unieke beloning wilt onder eventueel dubbele oproepen. |

Nadat u deze waarden hebt gewijzigd, moet u **Opslaan**selecteren.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Verkenning configureren zodat de leerlus zich kan aanpassen

Personalisatie is in staat om nieuwe patronen te ontdekken en zich aan te passen aan veranderingen in het gedrag van gebruikers in de tijd door alternatieven te verkennen in plaats van de voorspelling van het getrainde model te gebruiken. De **waarde verkenning** bepaalt welk percentage rangoproepen wordt beantwoord met verkenning.

Wijzigingen in deze waarde stellen het huidige Personalizer-model opnieuw in en worden omscholen met de laatste 2 dagen aan gegevens.

![De exploratiewaarde bepaalt welk percentage van de rangaanroep wordt beantwoord met exploratie](media/settings/configure-exploration-setting.png)

Nadat u deze waarde hebt gewijzigd, moet u **Opslaan**selecteren.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Modelupdatefrequentie configureren voor modeltraining

De **frequentie van de modelupdate** bepaalt hoe vaak het model wordt getraind.

|Frequentieinstelling|Doel|
|--|--|
|1 minuut|Updatefrequenties van één minuut zijn handig bij **het debuggen van** de code van een toepassing met behulp van Personalizer, het uitvoeren van demo's of het interactief testen van machine learning-aspecten.|
|15 minuten|Hoge modelupdatefrequenties zijn handig voor situaties waarin u wijzigingen in het gedrag van gebruikers op de voet wilt **volgen.** Voorbeelden hiervan zijn sites die draaien op live nieuws, virale inhoud of live productbiedingen. U een frequentie van 15 minuten gebruiken in deze scenario's. |
|1 uur|Voor de meeste use cases is een lagere updatefrequentie effectief.|

![Model update frequentie bepaalt hoe vaak een nieuwe Personalizer model wordt omgeschoold.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Nadat u deze waarde hebt gewijzigd, moet u **Opslaan**selecteren.

## <a name="data-retention"></a>Bewaartijd van gegevens

**Met de bewaartermijn voor gegevens** wordt ingesteld hoeveel dagen Personalizer gegevenslogboeken bijhoudt. Gegevenslogboeken uit het verleden zijn vereist om [offline evaluaties](concepts-offline-evaluation.md)uit te voeren, die worden gebruikt om de effectiviteit van personalizer te meten en het leerbeleid te optimaliseren.

Nadat u deze waarde hebt gewijzigd, moet u **Opslaan**selecteren.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Instellingen met onder meer het opnieuw instellen van het model

De volgende acties omvatten een onmiddellijke omscholing van het model met de laatste 2 dagen van de gegevens.

* Beloning
* Verkennen

Als u al uw gegevens wilt [wissen,](how-to-manage-model.md) gebruikt u de pagina **Model- en leerinstellingen ** om al uw gegevens te wissen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het beheren van uw model](how-to-manage-model.md)

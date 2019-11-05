---
title: Informatie over het versie beheer van de app voor uw Azure IoT Central-apps | Microsoft Docs
description: Herhaal uw Apparaatinstellingen door nieuwe versies te maken en zonder uw live verbonden apparaten te beïnvloeden
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 77137bcc708ee403571c4ca9fc0ee0333c068437
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990323"
---
# <a name="create-a-new-device-template-version-preview-features"></a>Een nieuwe sjabloon versie voor een apparaat maken (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Met Azure IoT Central kunt u IoT-toepassingen snel ontwikkelen. U kunt de ontwerp functie van uw apparaat snel herhalen door de mogelijkheden, weer gaven en aanpassingen van het apparaat toe te voegen, te bewerken of te verwijderen. Wanneer u de sjabloon voor het apparaat hebt gepubliceerd, wordt het hulp model van het apparaat weer gegeven als **gepubliceerd** met vergrendelings pictogrammen naast het model. Als u wijzigingen wilt aanbrengen in het functionaliteits model van het apparaat, moet u een nieuwe versie van de sjabloon voor het apparaat maken. Ondertussen kunnen de Cloud eigenschappen,-aanpassingen en-weer gaven op elk gewenst moment worden bewerkt zonder dat ze de sjabloon voor het apparaat hoeven op te geven. Zodra u een van deze wijzigingen hebt opgeslagen, kunt u de sjabloon voor het apparaat publiceren om de meest recente wijzigingen beschikbaar te maken die de operator kan weer geven in Device Explorer.

> [!NOTE]
> Zie [een sjabloon instellen en beheren](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) voor meer informatie over het maken van een sjabloon voor een apparaat.

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Aanpassingen toevoegen aan de sjabloon voor het apparaat zonder versie beheer

Bepaalde elementen van de mogelijkheden van uw apparaat kunnen worden bewerkt zonder dat ze de sjabloon en interfaces van uw apparaat hoeven te maken. Enkele van deze velden bevatten bijvoorbeeld weergave naam, semantisch type, minimum waarde, maximum waarde, decimaal posities, kleur, eenheid, weergave-eenheid, opmerking en beschrijving. Een van deze aanpassingen toevoegen:

1. Ga naar de pagina met **Apparaatinstellingen** .
1. Selecteer de sjabloon die u wilt aanpassen.
1. Kies het tabblad **aanpassen** .
1. Alle mogelijkheden die zijn gedefinieerd in het mogelijkheidsprofiel, worden hier weer gegeven. Alle velden die u hier kunt bewerken, kunnen worden opgeslagen en gebruikt in uw toepassing, zonder dat u de sjabloon van uw apparaat hoeft te gebruiken. Als er velden zijn die u wilt bewerken die alleen-lezen zijn, moet u de sjabloon van uw apparaat versie om deze te wijzigen. Selecteer een veld dat u wilt bewerken en voer een nieuwe waarde in.
1. Klik op **Opslaan**. Deze waarden overschrijven nu alle items die in de sjabloon voor het eerst zijn opgeslagen en die in de toepassing worden gebruikt.

## <a name="versioning-a-device-template"></a>Versie beheer van een apparaatprofiel

Als u een nieuwe versie van uw apparaatprofiel maakt, wordt er een concept versie van de sjabloon gemaakt waarin het mogelijkheidsprofiel kan worden bewerkt. Gepubliceerde interfaces blijven gepubliceerd totdat ze afzonderlijk zijn geversied. Als u een gepubliceerde interface wilt wijzigen, moet u eerst een nieuwe versie van de sjabloon voor het apparaat maken.

De sjabloon voor het apparaat moet alleen worden genoteerd als u probeert een deel van het mogelijkheidsprofiel te bewerken dat u niet kunt bewerken in de sectie aanpassingen van de sjabloon. 

Als u een sjabloon voor een apparaat wilt versie:

1. Ga naar de pagina met **Apparaatinstellingen** .
1. Selecteer de sjabloon voor het apparaat waarvoor u een versie wilt maken.
1. Klik boven aan de pagina op de knop **versie** en geef de sjabloon een nieuwe naam. We hebben een nieuwe naam voorgesteld die kan worden bewerkt.
1. Klik op **Maken**.
1. Uw sjabloon voor het apparaat bevindt zich nu in de concept modus. U ziet dat uw interfaces nog steeds zijn vergrendeld en moeten afzonderlijk worden bijgewerkt om te worden bewerkt. 

### <a name="versioning-an-interface"></a>Versie beheer van een interface

Met versie beheer van een interface kunt u de mogelijkheden in de interface die u al hebt gemaakt, toevoegen, bijwerken en verwijderen. 

Als u een interface wilt versie:

1. Ga naar de pagina met **Apparaatinstellingen** .
1. Selecteer de Device-sjabloon die u in een concept modus hebt.
1. Selecteer de interface in de gepubliceerde modus die u wilt versie en bewerken.
1. Klik boven aan de interface pagina op de knop **versie** . 
1. Klik op **Maken**.
1. Uw interface bevindt zich nu in de concept modus. U kunt mogelijkheden aan uw interface toevoegen of deze bewerken zonder bestaande aanpassingen en weer gaven te verbreken. 

> [!NOTE]
> Standaard interfaces die zijn gepubliceerd door Azure IoT, kunnen niet worden geversied of bewerkt. Deze standaard interfaces worden gebruikt voor de certificering van apparaten.

> [!NOTE]
> Zodra de interface is gepubliceerd, kunt u deze mogelijkheden niet verwijderen, zelfs niet in een concept modus. Mogelijkheden kunnen alleen worden bewerkt of toegevoegd aan de interface in de concept modus.


## <a name="migrate-a-device-across-device-template-versions"></a>Een apparaat migreren in de sjabloon versies van een apparaat

U kunt meerdere versies van de sjabloon voor het apparaat maken. In de loop van de tijd hebt u meerdere apparaten die zijn verbonden met behulp van deze Apparaatinstellingen. U kunt apparaten van de ene versie van uw apparaatprofiel naar de andere migreren. In de volgende stappen wordt beschreven hoe u een apparaat migreert:

1. Ga naar de pagina **device Explorer** .
1. Selecteer het apparaat dat u naar een andere versie wilt migreren.
1. Kies **migreren**.
1. Selecteer de sjabloon van het-apparaat met het versie nummer waarnaar u het apparaat wilt migreren en kies **migreren**.

![Een apparaat migreren](media/howto-version-device-template-pnp/pick-version.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u versie van Apparaatbeheer kunt gebruiken in uw Azure IoT Central-toepassing, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Telemetrie-regels maken](tutorial-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

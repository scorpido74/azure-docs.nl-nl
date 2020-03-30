---
title: Inzicht in apparaatsjabloonversievoor uw Azure IoT Central-apps | Microsoft Documenten
description: Herhalen over uw apparaatsjablonen door nieuwe versies te maken en zonder dat dit gevolgen heeft voor uw live verbonden apparaten
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bb77d1a09cb2692765c4c834ce617d13465d4d67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157565"
---
# <a name="create-a-new-device-template-version"></a>Een nieuwe apparaatsjabloonversie maken



Azure IoT Central maakt een snelle ontwikkeling van IoT-toepassingen mogelijk. U snel de ontwerpen van uw apparaatsjabloon herhalen door apparaatmogelijkheden, weergaven en aanpassingen toe te voegen, te bewerken of te verwijderen. Nadat u de apparaatsjabloon hebt gepubliceerd, wordt het apparaatcapaciteitsmodel weergegeven als **Gepubliceerd** met vergrendelingspictogrammen naast het model. Als u wijzigingen wilt aanbrengen in het apparaatcapaciteitsmodel, moet u een nieuwe versie van de apparaatsjabloon maken. Ondertussen kunnen de cloudeigenschappen, aanpassingen en weergaven op elk gewenst moment worden bewerkt zonder dat de apparaatsjabloon hoeft te worden aangepast. Zodra u een van deze wijzigingen hebt opgeslagen, u de apparaatsjabloon publiceren om de laatste wijzigingen beschikbaar te maken die de operator kan weergeven in Device Explorer.

> [!NOTE]
> Zie [Een apparaatsjabloon instellen en beheren](howto-set-up-template.md) voor meer informatie over het maken van een apparaatsjabloon

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Aanpassingen toevoegen aan de apparaatsjabloon zonder versiebeheer

Bepaalde elementen van uw apparaatmogelijkheden kunnen worden bewerkt zonder dat u uw apparaatsjabloon en -interfaces hoeft te gebruiken. Sommige van deze velden bevatten bijvoorbeeld weergavenaam, semantisch type, minimumwaarde, maximale waarde, decimalen, kleur, eenheid, weergaveeenheid, opmerking en beschrijving. Ga als een van de volgende aanpassingen met een reeks:

1. Ga naar de pagina **Apparaatsjablonen.**
1. Selecteer de apparaatsjabloon die u wilt aanpassen.
1. Kies het tabblad **Aanpassen.**
1. Alle mogelijkheden die in uw apparaatcapaciteitsmodel zijn gedefinieerd, worden hier weergegeven. Alle velden die u hier bewerken, kunnen worden opgeslagen en gebruikt in uw toepassing, zonder dat u uw apparaatsjabloon hoeft te gebruiken. Als er velden zijn die u wilt bewerken die alleen-lezen zijn, moet u de sjabloon voor het apparaat versien om deze te wijzigen. Selecteer een veld dat u wilt bewerken en voer nieuwe waarden in.
1. Klik op **Opslaan**. Nu zullen deze waarden alles overschrijven dat in eerste instantie in uw apparaatsjabloon is opgeslagen en in de hele toepassing worden gebruikt.

## <a name="versioning-a-device-template"></a>Een apparaatsjabloon versien

Als u een nieuwe versie van uw apparaatsjabloon maakt, wordt een conceptversie van de sjabloon gemaakt waarin het apparaatcapaciteitsmodel kan worden bewerkt. Alle gepubliceerde interfaces blijven gepubliceerd totdat ze individueel zijn geversioneerd. Als u een gepubliceerde interface wilt wijzigen, moet u eerst een nieuwe apparaatsjabloonversie maken.

De apparaatsjabloon mag alleen worden geversioneerd wanneer u een deel van het apparaatcapaciteitsmodel probeert te bewerken dat u niet bewerken in het gedeelte aanpassingen van de apparaatsjabloon. 

Om een apparaatsjabloon te kunnen versien:

1. Ga naar de pagina **Apparaatsjablonen.**
1. Selecteer de apparaatsjabloon die u probeert te gebruiken.
1. Klik op de knop **Versie** boven aan de pagina en geef de sjabloon een nieuwe naam. We hebben een nieuwe naam voor u voorgesteld die kan worden bewerkt.
1. Klik **op Maken**.
1. Nu staat uw apparaatsjabloon in conceptmodus. U ziet dat uw interfaces nog steeds zijn vergrendeld en afzonderlijk moeten worden geversioned om te worden bewerkt. 

### <a name="versioning-an-interface"></a>Een interface versien

Met versioning een interface u de mogelijkheden toevoegen, bijwerken en verwijderen in de interface die u al had gemaakt. 

Om een interface te kunnen versien:

1. Ga naar de pagina **Apparaatsjablonen.**
1. Selecteer de apparaatsjabloon die u in een conceptmodus hebt.
1. Selecteer de interface die zich in de gepubliceerde modus bevindt en die u wilt gebruiken en bewerken.
1. Klik op de knop **Versie** boven aan de interfacepagina. 
1. Klik **op Maken**.
1. Nu is uw interface in conceptmodus. U mogelijkheden toevoegen of bewerken aan uw interface zonder bestaande aanpassingen en weergaven te verbreken. 

> [!NOTE]
> Standaardinterfaces die door Azure IoT worden gepubliceerd, kunnen niet worden geversioned of bewerkt. Deze standaardinterfaces worden gebruikt voor apparaatcertificering.

> [!NOTE]
> Zodra de interface is gepubliceerd, u niet verwijderen van een van de mogelijkheden, zelfs in een concept-modus. Mogelijkheden kunnen alleen worden bewerkt of toegevoegd aan de interface in conceptmodus.


## <a name="migrate-a-device-across-device-template-versions"></a>Een apparaat migreren over apparaatsjabloonversies

U meerdere versies van de apparaatsjabloon maken. Na verloop van tijd hebt u meerdere verbonden apparaten met behulp van deze apparaatsjablonen. U apparaten migreren van de ene versie van uw apparaatsjabloon naar de andere. In de volgende stappen wordt beschreven hoe u een apparaat migreert:

1. Ga naar de pagina **Device Explorer.**
1. Selecteer het apparaat dat u naar een andere versie moet migreren.
1. Kies **Migreren**.
1. Selecteer de apparaatsjabloon met het versienummer waarnaar u het apparaat wilt migreren en kies **Migreren**.

![Een apparaat migreren](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaatsjabloonversies in uw Azure IoT Central-toepassing gebruiken, volgt de volgende stap:

> [!div class="nextstepaction"]
> [Telemetrieregels maken](tutorial-create-telemetry-rules.md)

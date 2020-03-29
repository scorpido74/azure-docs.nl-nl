---
title: Microsoft Translator Hub-werkruimte en -projecten migreren? - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u uw Hub-werkruimte en -projecten migreert naar aangepaste vertaler voor Azure Cognitive Services.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 2fa90a8099778bf37ce8534e968a2b1b4345c2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446787"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Hub-werkruimte en -projecten migreren naar aangepaste vertaler

U uw [Microsoft Translator Hub-werkruimte](https://hub.microsofttranslator.com/) en projecten eenvoudig migreren naar Aangepaste vertaler. Migratie wordt gestart vanuit Microsoft Hub door een werkruimte of project te selecteren, vervolgens een werkruimte te selecteren in Aangepaste vertaler en vervolgens de trainingen te selecteren die u wilt overdragen. Nadat de migratie is gestart, worden de geselecteerde trainingsinstellingen overgedragen met alle relevante documenten. Geïmplementeerde modellen worden getraind en kunnen automatisch worden geïmplementeerd na voltooiing.

Deze acties worden uitgevoerd tijdens de migratie:
* Alle documenten en projectdefinities zullen hun namen overzetten met de toevoeging van "hub_" vooraf aan de naam. Automatisch gegenereerde test- en tuninggegevens krijgen de naam hub_systemtune_\<modelid> of hub_systemtest_\<modelid>.
* Alle trainingen die zich in de geïmplementeerde status bevonden wanneer de migratie plaatsvindt, worden automatisch getraind met behulp van de documenten van de Hub-training. Deze training wordt niet in rekening gebracht voor uw abonnement. Als auto-deploy is geselecteerd voor de migratie, wordt het getrainde model na voltooiing geïmplementeerd. Er worden regelmatig hostingkosten in rekening gebracht.
* Gemigreerde trainings die zich niet in de geïmplementeerde status bevindt, worden in de gemigreerde conceptstatus geplaatst. In deze toestand hebt u de mogelijkheid om een model te trainen met de gemigreerde definitie, maar er zijn regelmatig opleidingskosten van toepassing.
* Op elk moment is de BLEU-score gemigreerd van de Hub-training te vinden op de pagina TrainingDetails van het model in de kop "Bleu score in MT Hub".

> [!Note] 
> Om een training te laten slagen, vereist Custom Translator minimaal 10.000 unieke uitgepakte zinnen. Custom Translator kan geen training uitvoeren met minder dan het [voorgestelde minimum.](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences)

## <a name="find-custom-translator-workspace-id"></a>Werkruimte-id voor aangepaste vertaler zoeken

Als u de werkruimte [Microsoft Translator Hub](https://hub.microsofttranslator.com/) wilt migreren, hebt u de doelwerkruimte-id in Aangepaste vertaler nodig. De doelwerkruimte in Aangepaste vertaler is waar al uw Hub-werkruimten en -projecten naar worden gemigreerd.

U vindt uw doelwerkplek-id op de pagina Aangepaste vertalersinstellingen:

1. Ga naar de pagina 'Instellen' in de portal Aangepaste vertaler.

2. U vindt de werkruimte-id in de sectie Basisgegevens.

    ![Id voor doelwerkruimte zoeken](media/how-to/how-to-find-destination-ws-id.png)

3. Houd uw doel Workspace ID om te verwijzen tijdens het migratieproces.

## <a name="migrate-a-project"></a>Een project migreren

Als u uw projecten selectief wilt migreren, biedt Microsoft Translator Hub u die mogelijkheid.

Een project migreren:

1. Meld u aan bij Microsoft Translator Hub.

2. Ga naar de pagina Projecten.

3. Klik op de koppeling Migreren voor het juiste project.

    ![Migreren vanuit Hub](media/how-to/how-to-migrate-from-hub.png)

4. Bij het indrukken van de migratielink krijgt u een formulier te zien waarmee u:
   * Geef de werkruimte op waarnaar u wilt overstappen op Aangepaste vertaler
   * Geef aan of je alle trainingen met succesvolle trainingen of alleen de ingezette trainingen wilt overdragen. Standaard worden alle succesvolle trainingen overgedragen.
   * Geef aan of u uw trainingsauto wilt inzetten wanneer de training is voltooid. Standaard wordt uw training niet automatisch geïmplementeerd na voltooiing.

5. Klik op 'Aanvraag verzenden'.

## <a name="migrate-a-workspace"></a>Een werkruimte migreren

Naast het migreren van één project, u ook alle projecten migreren met succesvolle trainingen in een werkruimte. Hierdoor wordt elk project in de werkruimte geëvalueerd alsof de migratiekoppeling is ingedrukt. Deze functie is geschikt voor gebruikers met veel projecten die ze allemaal willen migreren naar Aangepaste vertaler met dezelfde instellingen. Een werkonderbreking kan worden gestart vanaf de instellingenpagina van Translator Hub.

Een werkruimte migreren:

1. Meld u aan bij Microsoft Translator Hub.

2. Ga naar de pagina Instellingen.

3. Klik op de pagina 'Instellingen' op 'Werkruimtegegevens migreren naar aangepaste vertaler'.

    ![Migreren vanuit Hub](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Selecteer op de volgende pagina een van de volgende opties:

    a. Alleen geïmplementeerde trainings: als u deze optie selecteert, worden alleen uw geïmplementeerde systemen en gerelateerde documenten gemigreerd.

    b. Alle succesvolle trainingen: Als u deze optie selecteert, worden al uw succesvolle trainingen en gerelateerde documenten gemigreerd.

    c. Voer uw doelwerkplek-id in aangepaste vertaler in.

    ![Migreren vanuit Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5. Klik op Aanvraag verzenden.

## <a name="migration-history"></a>Migratiegeschiedenis

Wanneer u werkruimte- of projectmigratie hebt aangevraagd vanuit Hub, vindt u uw migratiegeschiedenis op de pagina Aangepaste vertalersinstellingen.

Voer de volgende stappen uit om de migratiegeschiedenis te bekijken:

1. Ga naar de pagina 'Instellen' in de portal Aangepaste vertaler.

2. Klik in de sectie Migratiegeschiedenis van de pagina Instellingen op Migratiegeschiedenis.

    ![Migratiegeschiedenis](media/how-to/how-to-migration-history.png)

De pagina Migratiegeschiedenis geeft de volgende informatie weer als overzicht voor elke migratie die u hebt aangevraagd.

1. Gemigreerd door: naam en e-mail van de gebruiker die deze migratieaanvraag heeft ingediend

2. Gemigreerd op: datum- en tijdstempel van de migratie

3. Projecten: Aantal aanvragen de migratie v/s aantal projecten is succesvol gemigreerd.

4. Trainingen: Aantal aangevraagde trainingen voor migratie v/s aantal opleidingen is succesvol gemigreerd.

5. Documenten: het aantal documenten dat wordt aangevraagd voor migratie v/s-aantal documenten is gemigreerd.

    ![Details migratiegeschiedenis](media/how-to/how-to-migration-history-details.png)

Als u meer gedetailleerd migratierapport wilt over uw projecten, trainingen en documenten, hebt u exportgegevens als CSV.

## <a name="implementation-notes"></a>Implementatienotities
* Systemen met taalparen NOG NIET beschikbaar in Custom Translator zijn alleen beschikbaar voor toegang tot gegevens of het uitzetten van de implementatie via Custom Translator. Deze projecten worden gemarkeerd als 'Niet beschikbaar' op de pagina Projecten. Naarmate we nieuwe taalparen met Custom Translator mogelijk maken, zullen de projecten actief worden om te trainen en te implementeren. 
* Het migreren van een project van Hub naar Custom Translator heeft geen invloed op uw Hub-trainingen of -projecten. We verwijderen geen projecten of documenten uit Hub tijdens een migratie en we verwijderen modellen niet.
* U mag slechts één keer migreren per project. Als u een migratie voor een project wilt herhalen, neem dan contact met ons op.
* Custom Translator ondersteunt NMT-taalparen van en naar het Engels. [Bekijk de volledige lijst met ondersteunde talen.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization) Hub vereist geen basislijnmodellen en ondersteunt daarom enkele duizenden talen. U een niet-ondersteund taalpaar migreren, maar we voeren alleen de migratie van documenten en projectdefinities uit. We zullen het nieuwe model niet kunnen trainen. Bovendien worden deze documenten en projecten als inactief weergegeven om aan te geven dat ze op dit moment niet kunnen worden gebruikt. Als er ondersteuning wordt toegevoegd voor deze projecten en/of documenten, worden ze actief en trainbaar.
* Custom Translator ondersteunt momenteel geen enkeleling trainingsgegevens. Net als niet-ondersteunde taalparen u eentalige documenten migreren, maar ze worden weergegeven als inactief totdat eentalige gegevens worden ondersteund.
* Custom Translator vereist 10k parallelle zinnen om te trainen. Microsoft Hub kan trainen op een kleinere set gegevens. Als een opleiding wordt gemigreerd die niet aan deze eis voldoet, wordt deze niet getraind.

## <a name="custom-translator-versus-hub"></a>Aangepaste vertaler versus hub

In deze tabel worden de functies tussen Microsoft Translator Hub en Custom Translator vergeleken.

|   | Hub | Custom Translator |
|:-----|:----:|:----:|
|Status van aanpassingsfunctie   | Algemene beschikbaarheid  | Algemene beschikbaarheid |
| Text API-versie  | V2    | V3  |
| SMT-aanpassing | Ja   | Nee |
| NMT-aanpassing | Nee    | Ja |
| Nieuwe uniforme Spraakservices aanpassing | Nee    | Ja |
| Geen spoor | Ja | Ja |

## <a name="new-languages"></a>Nieuwe talen

Als u een community of organisatie bent die werkt aan het [custommt@microsoft.com](mailto:custommt@microsoft.com) maken van een nieuw taalsysteem voor Microsoft Translator, neem dan contact op voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Train een model.](how-to-train-model.md)
- Gebruik uw geïmplementeerde aangepaste vertaalmodel via [Microsoft Translator Text API V3.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)

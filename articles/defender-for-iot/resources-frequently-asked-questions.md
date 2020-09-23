---
title: Veelgestelde vragen over Defender voor IoT
description: Vind antwoorden op veelgestelde vragen over Azure Defender voor IoT-functies en-services.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 97dd4e13754175e9c81ae308b86faae811e4d554
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936372"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Veelgestelde vragen over Azure Defender voor IoT

In dit artikel vindt u een lijst met veelgestelde vragen en antwoorden over Defender voor IoT.

## <a name="does-azure-provide-support-for-iot-security"></a>Biedt Azure ondersteuning voor IoT-beveiliging?

Azure biedt een geïntegreerde weer gave voor het bewaken en beheren van uw IoT-beveiliging als onderdeel van uw algemene beveiligings oplossing via Azure Security Center. Als u een ontwikkelaar van toepassingen bent, kunt u IoT Hub weer gave gebruiken om uw IoT-toepassings beveiliging te beheren.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Wat is de unieke toegevoegde waarde van Azure voor IoT-beveiliging?

Met Defender voor IoT kunnen bedrijven hun bestaande Cyber-beveiligings weergave uitbreiden naar hun volledige IoT-oplossing. Azure biedt een end-to-end weer gave van uw bedrijfs oplossing, zodat u zakelijke acties en beslissingen kunt nemen op basis van uw bedrijfs beveiligings postuur en verzamelde gegevens. Bij gecombineerde beveiliging met Azure IoT, Azure IoT Edge en Azure Security Center kunt u de gewenste oplossing maken met de beveiliging die u nodig hebt.

## <a name="who-is-defender-for-iot-made-for"></a>Voor wie is Defender voor IoT gemaakt?

Defender voor IoT is geïntegreerd in azure IoT Hub Security en biedt beheer voor de dagelijkse beveiligings activiteiten van de Business Solution. Defender voor IoT is ook geïntegreerd in Azure Security Center mogelijkheden en biedt een geïntegreerde weer gave voor het bewaken en beheren van uw IoT-beveiliging als onderdeel van uw algemene beveiligings oplossing.

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Hoe kan Defender voor IoT worden vergeleken met de competitie?

Hoewel andere oplossingen een reeks mogelijkheden bieden waarmee klanten hun eigen oplossingen kunnen maken, biedt Defender voor IoT een unieke end-to-end IoT-beveiligings oplossing die een brede weer gave biedt over de beveiliging van al uw verwante Azure-resources. Azure maakt snelle implementatie en volledige integratie mogelijk met IoT Hub module apparaatdubbels voor eenvoudige integratie met bestaande hulpprogram ma's voor Apparaatbeheer.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>Moet ik een Azure Security Center klant zijn om deze service te kunnen gebruiken?

Nee, maar dit wordt wel aanbevolen. Zonder Azure Security Center kan Defender voor IoT beperkte verbonden resource gegevens ontvangen en een beperkte analyse van uw potentiële kwets baarheid, bedreigingen en mogelijke aanvallen bieden.

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Moet ik een Azure IoT-klant zijn?

Ja. Defender voor IoT is afhankelijk van Azure IoT-connectiviteit en-infra structuur.

## <a name="do-i-have-to-install-an-agent"></a>Moet ik een agent installeren?

Het installeren van de agent op uw IoT-apparaten is niet verplicht om Defender in te scha kelen voor IoT. U kunt kiezen uit de volgende drie opties en zo verschillende niveaus van beveiligings bewaking en beheer mogelijkheden verkrijgen, afhankelijk van uw selectie:

1. Installeer de Defender voor IoT-beveiligings agent met of zonder wijzigingen. Deze optie biedt het hoogste niveau van verbeterde beveiligings inzichten in het gedrag en de toegang tot het apparaat.

1. Maak uw eigen agent en implementeer het Defender voor IoT-beveiligings bericht schema. Met deze optie wordt het gebruik van Defender voor IoT-analyse hulpprogramma's boven op de beveiligings agent van het apparaat ingeschakeld.

1. Geen beveiligings agent geïnstalleerd op uw IoT-apparaten. Met deze optie wordt IoT Hub communicatie bewaking ingeschakeld, met beperkte beveiligings bewaking en beheer mogelijkheden.

## <a name="what-does-the-defender-for-iot-agent-do"></a>Wat doet de agent voor de Defender voor IoT?

Defender voor IoT-agent biedt een bedreigings dekking op apparaatniveau voor apparaatconfiguratie, gedrag en toegang (door de configuratie te scannen), &-verbinding te verwerken. De Defender voor IoT-beveiligings agent scant geen bedrijfsgerelateerde gegevens of activiteiten.

## <a name="where-can-i-get-the-defender-for-iot-security-agent"></a>Waar kan ik de Defender voor IoT-beveiligings agent krijgen?

De beveiligings agent voor Defender voor IoT is open source en beschikbaar op GitHub in 32 bits en 64-bits Windows-en Linux-versies: https://github.com/Azure/Azure-IoT-Security .

## <a name="where-does-the-defender-for-iot-agent-get-installed"></a>Waar wordt de Defender voor IoT-agent geïnstalleerd?

Gedetailleerde informatie over de installatie en implementatie van de agent vindt u in GitHub: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Wat zijn de afhankelijkheden en vereisten van de agent?

Defender voor IoT ondersteunt een groot aantal verschillende platformen. Zie [ondersteunde platformen](how-to-deploy-agent.md) voor het controleren van de ondersteuning voor uw specifieke apparaten.

## <a name="which-data-is-collected-by-the-agent"></a>Welke gegevens worden door de agent verzameld?

Connectiviteit, toegang, firewall configuratie, lijst met processen & de basis lijn van het besturings systeem worden verzameld door de agent.

## <a name="how-much-data-will-the-agent-generate"></a>Hoeveel gegevens worden er door de agent gegenereerd?

Het genereren van agent gegevens wordt aangestuurd door apparaat, toepassing, connectiviteits type en configuratie van de klanten agent. Als gevolg van de hoge variabiliteit tussen apparaten en IoT-oplossingen, raden we u aan eerst de agent in een test omgeving of test instelling te implementeren om de specifieke configuratie die aan uw behoeften voldoet, te observeren, te leren en te bepalen, terwijl u de hoeveelheid gegenereerde gegevens meet. Na het starten van de service biedt de agent van de Defender voor IoT operationele aanbevelingen voor het optimaliseren van de door Voer van de agent om u te helpen bij het configureren en aanpassen van het proces.

## <a name="how-can-i-control-my-billing"></a>Hoe kan ik mijn facturering beheren?

Defender voor IoT biedt Configureer bare scans van agents, gegevens buffers en de mogelijkheid om aangepaste waarschuwingen te maken waarmee de hoeveelheid gegevens die door de agent wordt gegenereerd, wordt verg root of verkleind.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Gebruiken de agent berichten een quotum van IoT Hub?

Ja. De door agent verzonden gegevens worden meegeteld in uw IoT Hub-quotum.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Wat nu? Ik heb een agent geïnstalleerd en geen activiteiten of logboeken weer geven...

1. Controleer of het [agent type past bij het aangewezen OS-platform van uw apparaat](how-to-deploy-agent.md)

1. Controleer of de [agent wordt uitgevoerd op het apparaat](how-to-agent-configuration.md).

1. Controleer of de [service is ingeschakeld](quickstart-onboard-iot-hub.md) voor de **beveiliging** van uw IOT hub.

1. Controleer of het apparaat is [geconfigureerd in IOT hub met de Defender voor IOT-module](quickstart-create-security-twin.md).

Als de activiteiten of Logboeken nog steeds niet beschikbaar zijn, neemt u contact op met uw Defender voor IoT-partner voor meer hulp.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Wat gebeurt er als de Internet verbinding niet meer werkt?

De agent blijft gegevens uitvoeren en opslaan, zolang het apparaat wordt uitgevoerd. Gegevens worden opgeslagen in de cache voor beveiligings berichten volgens de grootte configuratie. Wanneer het apparaat de connectiviteit verkrijgt, wordt het verzenden van beveiligings berichten hervat.

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Als het apparaat opnieuw is opgestart, wordt de beveiligings agent zelf hersteld?

De beveiligings agent is zodanig ontworpen dat deze automatisch opnieuw wordt uitgevoerd wanneer het apparaat opnieuw wordt opgestart.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Kan de agent invloed hebben op de prestaties van het apparaat of andere geïnstalleerde software?

De agent verbruikt machine resources als elke andere toepassing/elk ander proces en mag geen normale activiteit van het apparaat verstoren. Resource verbruik op het apparaat waarop de agent wordt uitgevoerd, is gekoppeld aan de installatie en configuratie. We raden u aan uw agent configuratie te testen in een opgenomen omgeving, samen met interoperabiliteit met uw andere IoT-toepassingen en-functionaliteit, voordat u probeert te implementeren in een productie omgeving.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Ik maak onderhoud op het apparaat. Kan ik de agent uitschakelen?

De agent kan niet worden uitgeschakeld.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Is er een manier om te testen of de agent correct werkt?

Als de agent niet meer communiceert of geen beveiligings berichten verzendt, wordt een melding op het **apparaat wordt** gegenereerd.

## <a name="can-i-create-my-own-alerts"></a>Kan ik mijn eigen waarschuwingen maken?

Ja. U kunt een aangepaste waarschuwing instellen voor een vooraf vastgestelde set gedrag, zoals IP-adres en open poorten. Zie [aangepaste waarschuwingen maken](quickstart-create-custom-alerts.md) voor meer informatie over aangepaste waarschuwingen en hoe u deze kunt maken.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Waar kan ik logboeken zien? Kan ik logboeken aanpassen?

- Waarschuwingen en aanbevelingen weer geven met behulp van uw verbonden Log Analytics-werk ruimte. Opslag grootte en duur in de werk ruimte configureren.

- Onbewerkte gegevens van uw beveiligings agent kunnen ook worden opgeslagen in uw Log Analytics-account. Overweeg grootte, duur, opslag vereisten en gekoppelde kosten voordat u de configuratie van deze optie wijzigt.

## <a name="why-should-i-add-defender-for-iot-to-the-module-identity-what-is-it-used-for"></a>Waarom moet ik Defender voor IoT toevoegen aan de module-identiteit? Wat wordt er gebruikt voor?

De module Defender voor IoT wordt gebruikt voor het configureren en beheren van agents.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over hoe u aan de slag kunt gaan met Defender voor IoT:

- Lees het [overzicht](overview.md) van Defender voor IOT
- De vereisten voor de [service](service-prerequisites.md) controleren
- Meer informatie over hoe u aan de [slag kunt gaan](getting-started.md)
- Informatie [over Defender voor IOT-beveiligings waarschuwingen](concept-security-alerts.md)

---
title: Veelgestelde vragen over FHIR Services in azure-Azure API voor FHIR
description: Krijg antwoorden op veelgestelde vragen over de Azure API voor FHIR, zoals de opslag locatie van gegevens achter FHIR Api's en versie ondersteuning.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: af891935fd474e6f1f83ff1c2ce56ef71cd065c6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536722"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Veelgestelde vragen over de Azure API voor FHIR

## <a name="azure-api-for-fhir"></a>Azure-API voor FHIR

### <a name="what-is-fhir"></a>Wat is FHIR?
De snelle interoperabiliteits bronnen van de gezondheids zorg (FHIR-uitgesp roken "vuur") is een interoperabiliteits norm die is bedoeld om de uitwisseling van gezondheids zorg tussen verschillende Health-systemen mogelijk te maken. Deze standaard is ontwikkeld door de HL7-organisatie en wordt aangenomen door organisaties in de gezondheids zorg over de hele wereld. De meest recente versie van FHIR beschikbaar is R4 (Release 4). De Azure API voor FHIR ondersteunt R4 en biedt ook ondersteuning voor de vorige versie van STU3 (standaard voor proef versie 3). Ga naar [HL7.org](http://hl7.org/fhir/summary.html)voor meer informatie over FHIR.

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Bevinden de gegevens zich achter de FHIR-Api's die zijn opgeslagen in azure?

Ja, de gegevens worden opgeslagen in beheerde data bases in Azure. De Azure API voor FHIR biedt geen directe toegang tot het onderliggende gegevens archief.

### <a name="what-identity-provider-do-you-support"></a>Welke id-provider ondersteunt u?

We ondersteunen momenteel Microsoft Azure Active Directory als id-provider.

### <a name="what-fhir-version-do-you-support"></a>Welke FHIR-versie wordt ondersteund?

We ondersteunen versies 4.0.0 en 3.0.1 op zowel de Azure API voor FHIR (PaaS) als de FHIR-server voor Azure (open source).

Zie [ondersteunde functies](fhir-features-supported.md)voor meer informatie. Meer informatie over wat er is gewijzigd tussen versies in de [versie geschiedenis van HL7 FHIR](https://hl7.org/fhir/R4/history.html).

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Wat is het verschil tussen de open-source micro soft FHIR-server voor Azure en de Azure API voor FHIR?

De Azure API voor FHIR is een gehoste en beheerde versie van de open-source micro soft FHIR-server voor Azure. In de beheerde service biedt micro soft alle onderhoud en updates. 

Wanneer u FHIR server voor Azure gebruikt, hebt u rechtstreeks toegang tot de onderliggende services. Maar u bent ook verantwoordelijk voor het onderhouden en bijwerken van de server en alle vereiste nalevings werkzaamheden als u PHI-gegevens opslaat.

Vanuit een ontwikkelings standpunt wordt elke functie eerst geïmplementeerd op de open-source micro soft FHIR-server voor Azure. Zodra de app is gevalideerd in open source, wordt deze vrijgegeven aan de PaaS Azure API voor de FHIR-oplossing. De tijd tussen de release in open source en PaaS is afhankelijk van de complexiteit van de functie en andere prioriteiten voor het schema. 

### <a name="what-is-smart-on-fhir"></a>Wat is slim op FHIR?

SLIMME (Vervang bare medische toepassingen en herbruikbare technologie) op FHIR is een set open specificaties voor het integreren van partner toepassingen met FHIR-servers en andere Health IT-systemen, zoals elektronische status records en informatie uitwisselingen van de status. Door een slimme on FHIR-toepassing te maken, kunt u ervoor zorgen dat uw toepassing toegankelijk is en kan worden gebruikt door een verdwaald van verschillende systemen.
Verificatie en Azure API voor FHIR. Ga voor meer informatie naar Smart [Health](https://smarthealthit.org/).


## <a name="iot-connector-preview"></a>IoT-connector (preview-versie)

### <a name="what-is-iomt"></a>Wat is IoMT?
IoMT staat voor Internet of medische dingen en is een categorie van IoT-apparaten die status-en Wellness-gegevens vastleggen en uitwisselen met andere IT-systemen in de gezondheids zorg via een netwerk. Enkele voor beelden van IoMT-apparaten zijn: fitness-en klinisch Wearables, bewakings Sens oren, traceerers voor activiteiten, Point of Care-kiosken, of zelfs een slimme Pill.

### <a name="how-many-iot-connectors-do-i-need"></a>Hoeveel IoT-connectors heb ik nodig?
Eén IoT-connector kan worden gebruikt om gegevens op te nemen uit een groot aantal verschillende typen apparaten. U kunt echter nog steeds kiezen om verschillende connectors te gebruiken om de volgende redenen:
- **Scale**: voor de open bare preview is de resource capaciteit van de IOT-connector vast en verwacht een door Voer van ongeveer 200 berichten per seconde. U kunt meer IoT-connectors toevoegen als er een hogere door voer nodig is.
- **Apparaattype**: u kunt een afzonderlijke IOT-connector instellen voor elk type IoMT-apparaat dat u hebt om redenen om Apparaatbeheer te beheren.

### <a name="is-there-a-limit-on-number-of-iot-connectors-during-public-preview"></a>Geldt er een limiet voor het aantal IoT-connectors tijdens de open bare preview?
Ja, u kunt Maxi maal twee IoT-connectors per abonnement maken terwijl de functie in open bare preview is. Deze limiet bestaat om onverwachte kosten te voor komen terwijl de functie gratis beschikbaar is tijdens de preview-versie. Op aanvraag kan deze limiet worden verhoogd tot Maxi maal vijf IoT-connectors.

### <a name="what-azure-regions-iot-connector-feature-is-available-during-public-preview"></a>Welke Azure-regio's IoT-connector functie is beschikbaar tijdens de open bare preview-versie?
IoT-connector is beschikbaar in alle Azure-regio's waar Azure API voor FHIR beschikbaar is.

### <a name="can-i-configure-scaling-capacity-for-iot-connector"></a>Kan ik schaal capaciteit voor IoT-connector configureren?
Omdat IoT connector gratis is tijdens de open bare preview, is de capaciteit van de schaal vast en beperkt. De configuratie van IoT-connector die beschikbaar is in open bare Preview wordt verwacht een door Voer van ongeveer 200 berichten per seconde te bieden. Een vorm van resource capaciteits configuratie wordt beschikbaar gesteld in algemene Beschik baarheid (GA).

### <a name="what-fhir-version-does-iot-connector-support"></a>Welke FHIR-versie ondersteunt IoT-connector?
IoT-connector ondersteunt momenteel alleen FHIR versie R4. Deze functie is daarom alleen zichtbaar voor de R4-instanties van de Azure API voor FHIR en micro soft is op dit moment niet van plan om versie STU3 te ondersteunen.

### <a name="why-cant-i-install-iot-connector-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Waarom kan ik IoT-connector niet installeren als privé-verbinding is ingeschakeld op de Azure-API voor FHIR?
IoT-connector biedt op dit moment geen ondersteuning voor persoonlijke koppelings mogelijkheden. Als u een persoonlijke koppeling hebt ingeschakeld op de Azure-API voor FHIR, kunt u echter geen IoT-connector installeren en vice versa. Deze beperking is naar verwachting verdwenen als IoT-connector beschikbaar is voor algemene Beschik baarheid (GA).

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-iot-connector-feature-of-azure-api-for-fhir-service"></a>Wat is het verschil tussen de open-source IoMT FHIR-connector voor de Azure-en IoT-connector functie van de Azure API for FHIR-service?
IoT-connector is een gehoste en beheerde versie van de open-source IoMT FHIR-connector voor Azure. In de beheerde service biedt micro soft alle onderhoud en updates.

Wanneer u IoMT FHIR-connector voor Azure uitvoert, hebt u rechtstreeks toegang tot de onderliggende resources. Maar u bent ook verantwoordelijk voor het onderhouden en bijwerken van de server en alle vereiste nalevings werkzaamheden als u PHI-gegevens opslaat.

Vanuit een ontwikkelings standpunt wordt elke functie voor het eerst geïmplementeerd op de open-source IoMT FHIR-connector voor Azure. Zodra de app is gevalideerd in open source, wordt deze vrijgegeven aan de PaaS IoT-connector functie van de Azure API for FHIR-service. De tijd tussen de release in open source en PaaS is afhankelijk van de complexiteit van de functie en andere prioriteiten voor de weg-toewijzing.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u enkele veelgestelde vragen over de Azure API voor FHIR gelezen. Meer informatie over de ondersteunde functies in FHIR server voor Azure:
 
>[!div class="nextstepaction"]
>[Ondersteunde FHIR-functies](fhir-features-supported.md)
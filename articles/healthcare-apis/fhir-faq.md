---
title: Veelgestelde vragen over FHIR Services in azure-Azure API voor FHIR
description: Krijg antwoorden op veelgestelde vragen over de Azure API voor FHIR, zoals de opslag locatie van gegevens achter FHIR Api's en versie ondersteuning.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 35b59fb0583911b5b9faee96276d1bb09a8d6679
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269706"
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

### <a name="can-i-create-a-custom-fhir-resource"></a>Kan ik een aangepaste FHIR-resource maken?

Er zijn geen aangepaste FHIR-resources toegestaan. Als u een aangepaste FHIR-resource nodig hebt, kunt u een aangepaste resource maken boven op de [basis bron](http://www.hl7.org/fhir/basic.html) met uitbrei dingen. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Worden [extensies](https://www.hl7.org/fhir/extensibility.html) ondersteund op de Azure API voor FHIR?

U kunt alle geldige FHIR JSON-gegevens naar de server laden. Als u de structuur definitie wilt opslaan die de uitbrei ding definieert, kunt u deze opslaan als een structuur definitie resource. Op dit moment kunt u niet zoeken op uitbrei dingen.

### <a name="what-is-the-limit-on-_count"></a>Wat is de limiet voor _count?

De huidige limiet voor aantal is 100.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Zijn er beperkingen voor de export functionaliteit van de groep?

Voor het exporteren van de groep exporteert u alleen de opgenomen verwijzingen van de groep, niet alle kenmerken van de [groeps resource](https://www.hl7.org/fhir/group.html).

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>Kan ik een bundel voor FHIR in de Azure-API plaatsen?

We ondersteunen momenteel het boeken van [batch bundels](https://www.hl7.org/fhir/valueset-bundle-type.html) , maar bieden geen ondersteuning voor het boeken van transactie bundels in de Azure API voor FHIR. U kunt de open-source FHIR-server die wordt ondersteund door SQL gebruiken voor het boeken van transactie bundels.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Hoe krijg ik alle resources voor één patiënt in de Azure API voor FHIR?

Het [zoeken naar compartimenten](https://www.hl7.org/fhir/compartmentdefinition.html) wordt ondersteund in de Azure API voor FHIR. Zo kunt u alle resources met betrekking tot een specifieke patiënt ophalen. Houd er rekening mee dat de afdeling nu alle resources bevat die aan de patiënt zijn gerelateerd, maar niet de patiënt zelf, dus u moet ook zoeken naar de patiënt als u de patiënt resource in uw resultaten nodig hebt.

Enkele voor beelden hiervan zijn:

* Ontvang een patiënt/<id>/*
* Patiënt/ <id> /OBSERVATION ophalen
* Bent u de <id> /Observation? code = 8302-2

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Waar kan ik enkele voor beelden zien van het gebruik van de Azure API voor FHIR binnen een werk stroom?

We hebben een verzameling referentie architecturen die beschikbaar zijn op de GitHub-pagina van de [status architectuur](https://github.com/microsoft/health-architectures).

## <a name="azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (preview)

### <a name="what-is-iomt"></a>Wat is IoMT?
IoMT staat voor Internet of medische dingen en is een categorie van IoT-apparaten die status-en Wellness-gegevens vastleggen en uitwisselen met andere IT-systemen in de gezondheids zorg via een netwerk. IoMT-apparaten zijn bijvoorbeeld wearables voor fitness en klinische toepassingen, bewakingssensoren, activiteitstrackers, zorgkiosken of zelfs slimme pillen.

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>Hoeveel Azure IoT connector voor FHIR (preview) heb ik nodig?
Eén Azure IoT-connector voor FHIR * kan worden gebruikt om gegevens op te nemen uit een groot aantal verschillende typen apparaten. U kunt echter nog steeds kiezen om verschillende connectors te gebruiken om de volgende redenen:
- **Scale**: voor een open bare preview is de Azure IOT-connector voor de resource capaciteit van FHIR vast en verwacht om een door Voer van ongeveer 200 berichten per seconde te bieden. U kunt meer Azure IoT-connector voor FHIR toevoegen als een hogere door Voer is vereist.
- **Apparaattype**: u kunt een afzonderlijke Azure IOT-connector voor FHIR instellen voor elk type IoMT-apparaat dat u hebt om redenen om Apparaatbeheer te beheren.

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>Geldt er een limiet voor het aantal Azure IoT-connector voor FHIR (preview) tijdens de open bare preview-versie?
Ja, u kunt slechts twee Azure IoT-connector maken voor FHIR per abonnement terwijl de functie in open bare preview is. Deze limiet bestaat om onverwachte kosten te voor komen terwijl de functie gratis beschikbaar is tijdens de preview-versie. Op aanvraag kan deze limiet worden verhoogd tot Maxi maal vijf Azure IoT-connector voor FHIR.

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>Wat is de Azure-regio Azure IoT connector voor FHIR (preview) beschikbaar tijdens de open bare preview?
Azure IoT connector voor FHIR is beschikbaar in alle Azure-regio's waar Azure API voor FHIR beschikbaar is.

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Kan ik schaal capaciteit configureren voor Azure IoT connector voor FHIR (preview)?
Omdat Azure IoT connector voor FHIR gratis is tijdens de open bare preview, is de capaciteit van de schaal vast en beperkt. De Azure IoT-connector voor FHIR-configuratie die beschikbaar is in open bare Preview wordt verwacht een door Voer van ongeveer 200 berichten per seconde te bieden. Een vorm van resource capaciteits configuratie wordt beschikbaar gesteld in algemene Beschik baarheid (GA).

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>Welke FHIR-versie biedt Azure IoT connector voor FHIR-ondersteuning (preview)?
Azure IoT connector voor FHIR ondersteunt momenteel alleen FHIR versie R4. Deze functie is daarom alleen zichtbaar voor de R4-instanties van de Azure API voor FHIR en micro soft is op dit moment niet van plan om versie STU3 te ondersteunen.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Waarom kan ik de Azure IoT-connector voor FHIR (preview) niet installeren als persoonlijke koppeling is ingeschakeld op de Azure API voor FHIR?
Azure IoT connector voor FHIR biedt op dit moment geen ondersteuning voor persoonlijke koppelings mogelijkheden. Als u een persoonlijke koppeling hebt ingeschakeld in azure API voor FHIR, kunt u de Azure IoT-connector niet installeren voor FHIR en vice versa. Deze beperking wordt verwacht als de Azure IoT-connector voor FHIR beschikbaar is voor algemene Beschik baarheid (GA).

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>Wat is het verschil tussen de open-source IoMT FHIR-connector voor de Azure-en Azure IoT-connector voor de FHIR-functie (preview) van de Azure API for FHIR-service?
Azure IoT connector voor FHIR is een gehoste en beheerde versie van de open-source IoMT FHIR-connector voor Azure. In de beheerde service biedt micro soft alle onderhoud en updates.

Wanneer u IoMT FHIR-connector voor Azure uitvoert, hebt u rechtstreeks toegang tot de onderliggende resources. Maar u bent ook verantwoordelijk voor het onderhouden en bijwerken van de server en alle vereiste nalevings werkzaamheden als u PHI-gegevens opslaat.

Vanuit een ontwikkelings standpunt wordt elke functie voor het eerst geïmplementeerd op de open-source IoMT FHIR-connector voor Azure. Zodra de app is gevalideerd in open source, wordt deze vrijgegeven aan de PaaS Azure IoT-connector voor de FHIR-functie van de Azure API for FHIR-service. De tijd tussen de release in open source en PaaS is afhankelijk van de complexiteit van de functie en andere prioriteiten voor de weg-toewijzing.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u enkele veelgestelde vragen over de Azure API voor FHIR gelezen. Meer informatie over de ondersteunde functies in FHIR server voor Azure:
 
>[!div class="nextstepaction"]
>[Ondersteunde FHIR-functies](fhir-features-supported.md)

*In Azure Portal wordt Azure IoT Connector for FHIR aangeduid als IoT Connector (preview).

FHIR is het gedeponeerde handelsmerk van HL7 en wordt gebruikt met de toestemming van HL7.
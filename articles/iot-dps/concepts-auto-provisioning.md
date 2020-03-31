---
title: IoT Hub Device Provisioning Service - Auto-provisioning concepten
description: Dit artikel biedt een conceptueel overzicht van de fasen van automatische inrichting van apparaten, met behulp van IoT Device Provisioning Service (DPS), IoT Hub en client-SDK's.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c94fa6b851dfc9923628a738a15f7c245204f73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975326"
---
# <a name="auto-provisioning-concepts"></a>Concepten voor automatisch inrichten

Zoals beschreven in het [overzicht,](about-iot-dps.md)is de Device Provisioning Service een helperservice die just-in-time inrichten van apparaten naar een IoT-hub mogelijk maakt, zonder menselijke tussenkomst. Na een succesvolle inrichting maken apparaten rechtstreeks verbinding met hun aangewezen IoT-hub. Dit proces wordt auto-provisioning genoemd en biedt een kant-en-klare registratie en initiële configuratie-ervaring voor apparaten.

## <a name="overview"></a>Overzicht

Azure IoT-automatische inrichting kan in drie fasen worden opgesplitst:

1. **Serviceconfiguratie** - een eenmalige configuratie van de exemplaren Azure IoT Hub en IoT Hub Device Provisioning Service, waarbij deze worden vastgesteld en er een koppeling tussen wordt gemaakt.

   > [!NOTE]
   > Ongeacht de grootte van uw IoT-oplossing, zelfs als u van plan bent om miljoenen apparaten te ondersteunen, is dit een **eenmalige configuratie.**

2. **Apparaatinschrijving** - het proces om de instantie Device Provisioning Service bewust te maken van de apparaten die in de toekomst proberen te registreren. [De inschrijving](concepts-service.md#enrollment) wordt bereikt door apparaatidentiteitsgegevens in de inrichtingsservice te configureren, als een 'individuele inschrijving' voor één apparaat of een 'groepsinschrijving' voor meerdere apparaten. De identiteit is gebaseerd op het [attestmechanisme](concepts-security.md#attestation-mechanism) waarvoor het apparaat is ontworpen, waardoor de inrichtingsdienst tijdens de registratie kan getuigen van de echtheid van het apparaat:

   - **TPM**: geconfigureerd als een "individuele inschrijving", de identiteit van het apparaat is gebaseerd op de TPM-registratie-ID en de openbare goedkeuringssleutel. Gezien het feit dat TPM een [specificatie](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)is, verwacht de service alleen te bevestigen volgens de specificatie, ongeacht de TPM-implementatie (hardware of software). Zie [Apparaatinrichting: identiteitsattestation met TPM](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) voor meer informatie over tpm-gebaseerde attestation. 

   - **X509**: geconfigureerd als een "individuele inschrijving" of "groepsinschrijving", de identiteit van het apparaat is gebaseerd op een X.509 digitaal certificaat, dat wordt geüpload naar de inschrijving als een .pem of .cer-bestand.

   > [!IMPORTANT]  
   > Hoewel dit geen voorwaarde is voor het gebruik van Device Provisioning Services, raden we uw apparaat ten zeerste aan een Hardware Security Module (HSM) te gebruiken om gevoelige apparaatidentiteitsgegevens op te slaan, zoals sleutels en X.509-certificaten.

3. **Apparaatregistratie en -configuratie** - gestart bij het opstarten door registratiesoftware, die is gebouwd met behulp van een DEVICE Provisioning Service-client SDK die geschikt is voor het apparaat en het attestmechanisme. De software maakt een verbinding met de inrichtingsservice voor verificatie van het apparaat en de daaropvolgende registratie in de IoT Hub. Bij succesvolle registratie wordt het apparaat voorzien van zijn unieke iot-hub-apparaat-id en verbindingsinformatie, waardoor het de oorspronkelijke configuratie kan trekken en het telemetrieproces kan starten. In productieomgevingen kan deze fase weken of maanden na de vorige twee fasen plaatsvinden.

## <a name="roles-and-operations"></a>Rollen en bewerkingen

De fasen besproken in de vorige sectie kan overspannen weken of maanden, als gevolg van de productie realiteit, zoals productietijd, scheepvaart, douaneproces, enz. Bovendien kunnen ze activiteiten over meerdere rollen omvatten, gezien de verschillende betrokken entiteiten. In deze sectie wordt dieper gekeken naar de verschillende rollen en bewerkingen met betrekking tot elke fase en wordt vervolgens de stroom in een sequentiediagram geïllustreerd. 

Automatische inrichting stelt ook eisen aan de fabrikant van het apparaat, specifiek voor het inschakelen van het attestmechanisme. Productiewerkzaamheden kunnen ook onafhankelijk van de timing van de automatische inrichtingsfasen plaatsvinden, vooral in gevallen waarin nieuwe apparaten worden aangeschaft nadat de automatische inrichting al is vastgesteld.

Een reeks Quickstarts zijn voorzien in de inhoudsopgave aan de linkerkant, om automatisch inrichten uit te leggen door middel van hands-on ervaring. Om het leerproces te vergemakkelijken/vereenvoudigen, wordt software gebruikt om een fysiek apparaat voor inschrijving en registratie te simuleren. Voor sommige Quickstarts moet u bewerkingen uitvoeren voor meerdere rollen, waaronder bewerkingen voor niet-bestaande rollen, vanwege de gesimuleerde aard van de Quickstarts.

| Rol | Bewerking | Beschrijving |
|------| --------- | ------------|
| Fabrikant | Identiteit en registratie-URL coderen | Op basis van het gebruikte attestmechanisme is de fabrikant verantwoordelijk voor het coderen van de identiteitsgegevens van het apparaat en de URL voor de registratie van de Apparaatprovisioning-service.<br><br>**Snelstart:** omdat het apparaat wordt gesimuleerd, is er geen rol van de fabrikant. Zie de rol Ontwikkelaars voor meer informatie over hoe u aan deze informatie komt, die wordt gebruikt bij het coderen van een voorbeeldregistratietoepassing. |
| | Apparaatidentiteit opgeven | Als opdrachtgever van de identiteitsgegevens van het apparaat is de fabrikant verantwoordelijk voor het communiceren ervan aan de operator (of een aangewezen agent) of het rechtstreeks inschrijven bij de dienst voor het inrichten van apparaten via API's.<br><br>**Snelstart:** omdat het apparaat wordt gesimuleerd, is er geen rol van de fabrikant. Zie de functie Operator voor meer informatie over hoe u de identiteit van het apparaat krijgt, die wordt gebruikt om een gesimuleerd apparaat in te schrijven in uw apparaatvoorzieningsservice-instantie. |
| Operator | Automatische inrichting configureren | Deze bewerking komt overeen met de eerste fase van automatische inrichting.<br><br>**Snelstart:** U voert de operatorrol uit en configureert de device provisioning service en IoT Hub-exemplaren in uw Azure-abonnement. |
|  | Identiteit van het apparaat inschrijven | Deze bewerking komt overeen met de tweede fase van automatische inrichting.<br><br>**Snelstart:** u voert de operatorrol uit en schrijft uw gesimuleerde apparaat in in uw instantie Device Provisioning Service. De identiteit van het apparaat wordt bepaald door de attestmethode die wordt gesimuleerd in de Quickstart (TPM of X.509). Zie de rol Ontwikkelaar voor attestationdetails. |
| Dienst voor het inrichten van apparaten,<br>IoT Hub | \<alle bewerkingen\> | Voor zowel een productie-implementatie met fysieke apparaten als Quickstarts met gesimuleerde apparaten, worden deze rollen uitgevoerd via de IoT-services die u configureert in uw Azure-abonnement. De rollen/bewerkingen werken precies hetzelfde, omdat de IoT-services onverschillig staan tegenover het inrichten van fysieke versus gesimuleerde apparaten. |
| Developer | Registratiesoftware voor het bouwen/implementeren van | Deze bewerking komt overeen met de derde fase van automatische inrichting. De ontwikkelaar is verantwoordelijk voor het bouwen en implementeren van de registratiesoftware op het apparaat, met behulp van de juiste SDK.<br><br>**Snelstart:** De voorbeeldregistratietoepassing die u bouwt, simuleert een echt apparaat, voor uw platform/taal naar keuze, dat op uw werkstation wordt uitgevoerd (in plaats van het te implementeren op een fysiek apparaat). De registratietoepassing voert dezelfde bewerkingen uit als een bewerking die is geïmplementeerd op een fysiek apparaat. U geeft de attestationmethode (TPM- of X.509-certificaat) op, plus de registratie-URL en de 'ID-scope' van uw apparaatinrichtingsservice-exemplaar. De identiteit van het apparaat wordt bepaald door de SDK-attestation-logica tijdens runtime, op basis van de methode die u opgeeft: <ul><li>**TPM attest** - uw ontwikkelingswerkstation draait een [TPM simulator applicatie](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Eenmaal uitgevoerd, wordt een aparte toepassing gebruikt om de "Endorsement Key" en "Registration ID" van de TPM te extraheren voor gebruik bij het inschrijven van de apparaatidentiteit. De SDK-attestation-logica gebruikt de simulator ook tijdens de registratie om een ondertekend SAS-token te presenteren voor verificatie en inschrijvingsverificatie.</li><li>**X509-attest** - u gebruikt een tool om een certificaat te [genereren.](how-to-use-sdk-tools.md#x509-certificate-generator) Nadat u bent gegenereerd, maakt u het certificaatbestand dat nodig is voor gebruik bij inschrijving. De SDK-attestlogica gebruikt het certificaat ook tijdens de registratie om te presenteren voor verificatie en inschrijvingsverificatie.</li></ul> |
| Apparaat | Opstarten en registreren | Deze bewerking komt overeen met de derde fase van automatische inrichting, vervuld door de apparaatregistratiesoftware die door de ontwikkelaar is gebouwd. Zie de rol Ontwikkelaars voor meer informatie. Bij de eerste boot: <ol><li>De toepassing maakt verbinding met de instantie Device Provisioning Service, volgens de globale URL en service "ID Scope" die tijdens de ontwikkeling is opgegeven.</li><li>Eenmaal aangesloten, wordt het apparaat geverifieerd tegen de attestmethode en identiteit die tijdens de inschrijving zijn opgegeven.</li><li>Eenmaal geverifieerd, wordt het apparaat geregistreerd bij de IoT Hub-instantie die is opgegeven door het inrichtingsservice-exemplaar.</li><li>Na succesvolle registratie worden een uniek apparaat-ID en IoT Hub-eindpunt teruggestuurd naar de registratietoepassing voor communicatie met IoT Hub.</li><li> Van daaruit kan het apparaat de oorspronkelijke dubbele status van [het apparaat](~/articles/iot-hub/iot-hub-devguide-device-twins.md) voor configuratie naar beneden halen en beginnen met het proces van het rapporteren van telemetriegegevens.</li></ol>**Snelstart:** omdat het apparaat wordt gesimuleerd, wordt de registratiesoftware uitgevoerd op uw ontwikkelingswerkstation.|

In het volgende diagram worden de rollen en volgorde van bewerkingen tijdens het automatisch inrichten van het apparaat samengevat:
<br><br>
[![Volgorde voor automatisch inrichten voor een apparaat](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Optioneel kan de fabrikant ook de bewerking 'Identiteit van het enroll-apparaat' uitvoeren met behulp van Device Provisioning Service API's (in plaats van via de Operator). Zie de [Zero touch-apparaatregistratie met Azure IoT-video](https://youtu.be/cSbDRNg72cU?t=2460) (vanaf 41:00 uur) voor een gedetailleerde bespreking van deze volgorde en meer.

## <a name="roles-and-azure-accounts"></a>Rollen en Azure-accounts

Hoe elke rol wordt toegewezen aan een Azure-account is scenario-afhankelijk, en er zijn nogal wat scenario's die kunnen worden betrokken. De onderstaande algemene patronen moeten helpen om een algemeen inzicht te geven in hoe rollen over het algemeen worden toegewezen aan een Azure-account.

#### <a name="chip-manufacturer-provides-security-services"></a>Chipfabrikant levert beveiligingsdiensten

In dit scenario beheert de fabrikant de beveiliging voor level-one-klanten. Dit scenario kan de voorkeur hebben van deze level-one-klanten, omdat ze geen gedetailleerde beveiliging hoeven te beheren. 

De fabrikant introduceert beveiliging in Hardware Security Modules (HSMs). Deze beveiliging kan bestaan uit het verkrijgen van sleutels, certificaten, etc. van potentiële klanten die al DPS-exemplaren en inschrijvingsgroepen hebben ingesteld. De fabrikant kan deze beveiligingsinformatie ook voor zijn klanten genereren.

In dit scenario zijn er mogelijk twee Azure-accounts bij betrokken:

- **Account #1:** waarschijnlijk tot op zekere hoogte gedeeld over de functies van de operator en ontwikkelaar. Deze partij kan de HSM-chips bij de fabrikant kopen. Deze chips worden gericht op DPS-exemplaren die zijn gekoppeld aan de account#1. Met DPS-inschrijvingen kan deze partij apparaten leasen aan meerdere niveau-twee-klanten door de instellingen voor apparaatinschrijving in DPS opnieuw te configureren. Deze partij kan ook IoT-hubs toegewezen voor de eindgebruiker backend systemen om te communiceren met om toegang te krijgen tot apparaat telemetrie etc. In dit laatste geval is een tweede rekening mogelijk niet nodig.

- **Account #2**: Eindgebruikers, level-two-klanten kunnen hun eigen IoT-hubs hebben. De partij die is gekoppeld aan Account #1 alleen gehuurde apparaten naar de juiste hub in dit account. Voor deze configuratie moet DPS- en IoT-hubs worden gekoppeld aan Azure-accounts, wat kan worden gedaan met Azure Resource Manager-sjablonen.

#### <a name="all-in-one-oem"></a>Alles-in-één OEM

De fabrikant zou een "All-in-one OEM" waar slechts een enkele fabrikant account nodig zou zijn. De fabrikant verzorgt de beveiliging en inrichting van end-to-end.

De fabrikant kan een cloudtoepassing leveren aan klanten die apparaten kopen. Deze toepassing zou interface met de IoT Hub toegewezen door de fabrikant.

Automaten of geautomatiseerde koffieautomaten zijn voorbeelden van dit scenario.




## <a name="next-steps"></a>Volgende stappen

U het nuttig vinden om dit artikel als referentiepunt te bookmarken, terwijl u zich een weg baant door de bijbehorende snelstarts voor automatisch inrichten. 

Begin met het voltooien van een Quickstart 'Automatisch inrichten instellen' die het beste past bij uw voorkeur voor beheergereedschap, die u door de fase 'Serviceconfiguratie' leidt:

- [Automatische inrichting instellen met Azure CLI](quick-setup-auto-provision-cli.md)
- [Automatische inrichting instellen met behulp van de Azure-portal](quick-setup-auto-provision.md)
- [Automatische inrichting instellen met behulp van een resourcemanagersjabloon](quick-setup-auto-provision-rm.md)

Ga vervolgens verder met een Quickstart 'Automatisch inrichten van een gesimuleerd apparaat' dat past bij uw apparaatattestmechanisme en de sdk/taalvoorkeur voor apparaatinrichting. In deze Quickstart loopt u door de fasen 'Apparaatregistratie' en 'Apparaatregistratie en -configuratie': 

|  | Gesimuleerd apparaatattestmechanisme | Sdk/taal snel starten |  |
|--|--|--|--|
|  | Trusted Platform Module (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C #](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | X.509-certificaat | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C #](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |





---
title: Overzicht van Azure IoT Hub X.509 CA-beveiliging | Microsoft Documenten
description: Overzicht - hoe u apparaten verifieert naar IoT Hub met behulp van X.509-certificaatautoriteiten.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3d02d3573902964a8549fa0eeb1f4f1471de1752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284510"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Apparaatverificatie met behulp van X. 509 CA-certificaten

In dit artikel wordt beschreven hoe u CA-certificaten (X.509 Certificate Authority) gebruiken om apparaten die iot-hub verbinden, te verifiëren.  In dit artikel leert u:

* Een X.509 CA-certificaat aanvragen
* Het X.509 CA-certificaat registreren bij IoT Hub
* Apparaten ondertekenen met X.509 CA-certificaten
* Hoe apparaten die zijn ondertekend met X.509 CA worden geverifieerd

## <a name="overview"></a>Overzicht

Met de x.509 CA-functie apparaatverificatie naar IoT Hub worden gemaakt met behulp van een Certificaatautoriteit (CA). Het vereenvoudigt het initiële apparaatinschrijvingsproces en de logistiek van de supply chain tijdens de productie van apparaten. [Meer informatie in dit scenarioartikel over de waarde van het gebruik van X.509 CA-certificaten](iot-hub-x509ca-concept.md) voor apparaatverificatie.  We raden u aan dit scenarioartikel te lezen voordat u verdergaat, omdat dit verklaart waarom de volgende stappen bestaan.

## <a name="prerequisite"></a>Vereiste

Als u de X.509 CA-functie gebruikt, heeft u een IoT Hub-account.  [Meer informatie over het maken van een IoT Hub-exemplaar](quickstart-send-telemetry-dotnet.md) als u er nog geen hebt.

## <a name="how-to-get-an-x509-ca-certificate"></a>Een X.509 CA-certificaat aanvragen

Het X.509 CA-certificaat staat bovenaan de certificatenketen voor elk van uw apparaten.  U er een kopen of maken, afhankelijk van hoe u het wilt gebruiken.

Voor de productieomgeving raden we u aan een X.509 CA-certificaat aan te schaffen bij een openbare basiscertificaatinstantie. De aankoop van een CA-certificaat heeft het voordeel dat de basis-CA optreedt als een vertrouwde derde partij om in te staan voor de legitimiteit van uw apparaten. Overweeg deze optie als u van plan bent uw apparaten deel uit te laten maken van een open IoT-netwerk waar ze naar verwachting communiceren met producten of services van derden.

U ook een zelfondertekende X.509 CA maken voor experimenten of voor gebruik in gesloten IoT-netwerken.

Ongeacht hoe u uw X.509 CA-certificaat verkrijgt, zorg ervoor dat u de bijbehorende privésleutel te allen tijde geheim en beschermd houdt.  Dit is nodig voor het vertrouwen in de X.509 CA-verificatie.

Meer informatie over het [maken van een zelfondertekend CA-certificaat,](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)dat u gebruiken voor experimenten in deze functiebeschrijving.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Apparaten aanmelden bij de vertrouwensketen van het certificaat

De eigenaar van een X.509 CA-certificaat kan cryptografisch een tussenliggende CA ondertekenen die op zijn beurt een andere tussenliggende CA kan ondertekenen, enzovoort, totdat de laatste tussentijdse CA dit proces beëindigt door een apparaat te ondertekenen. Het resultaat is een trapsgewijze keten van certificaten die bekend staat als een certificaatketen van vertrouwen. In het echte leven speelt dit zich af als een delegatie van vertrouwen naar het ondertekenen van apparaten. Deze delegatie is belangrijk omdat het een cryptografisch variabele keten van bewaring vaststelt en het delen van ondertekeningssleutels vermijdt.

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

Het apparaatcertificaat (ook wel een bladcertificaat genoemd) moet de *onderwerpnaam* hebben ingesteld op de **apparaat-id** die is gebruikt bij het registreren van het IoT-apparaat in de Azure IoT Hub. Deze instelling is vereist voor verificatie.

Lees hier hoe [u een certificaatketen](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) maakt zoals die is gedaan bij het ondertekenen van apparaten.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Het X.509 CA-certificaat registreren bij IoT Hub

Registreer uw X.509 CA-certificaat op IoT Hub, waar het wordt gebruikt om uw apparaten te verifiëren tijdens registratie en verbinding.  Het registreren van het X.509 CA-certificaat is een proces in twee stappen dat bestaat uit het uploaden van certificaatbestanden en het bewijs van bezit.

Het uploadproces houdt in dat u een bestand uploadt dat uw certificaat bevat.  Dit bestand mag nooit privésleutels bevatten.

Het bewijs van bezit stap omvat een cryptografische uitdaging en reactieproces tussen u en IoT Hub.  Aangezien de inhoud van digitale certificaten openbaar is en dus vatbaar is voor afluisteren, wil IoT Hub nagaan of u echt eigenaar bent van het CA-certificaat.  Dit doet u door een willekeurige uitdaging te genereren die u moet ondertekenen met de bijbehorende privésleutel van het CA-certificaat.  Als u de private key geheim en beschermd hield zoals eerder geadviseerd, dan alleen u zult beschikken over de kennis om deze stap te voltooien. Geheimhouding van privésleutels is de bron van vertrouwen in deze methode.  Nadat u de uitdaging hebt ondertekend, voltooit u deze stap door een bestand met de resultaten te uploaden.

Meer informatie over het [registreren van uw CA-certificaat](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Een apparaat maken op IoT Hub

Om apparaatimitatie uit te sluiten, vereist IoT Hub dat u het laat weten welke apparaten u verwachten.  Dit doe je door een apparaatinvoer te maken in het apparaatregister van de IoT Hub.  Dit proces wordt geautomatiseerd bij het gebruik van de IoT Hub [Device Provisioning Service.](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/) 

Lees hier hoe u [handmatig een apparaat maakt in IoT Hub.](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)

Maak een X.509-apparaat voor uw IoT-hub

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Authenticerende apparaten die zijn ondertekend met X.509 CA-certificaten

Met X.509 CA certificaat geregistreerd en apparaten aangemeld bij een certificaat keten van vertrouwen, wat overblijft is apparaat authenticatie wanneer het apparaat verbinding maakt, zelfs voor de eerste keer.  Wanneer een x.509 CA-ondertekend apparaat verbinding maakt, wordt de certificaatketen geüpload voor validatie. De keten bevat alle tussentijdse CA- en apparaatcertificaten.  Met deze informatie verifieert IoT Hub het apparaat in een proces in twee stappen.  IoT Hub valideert de certificaatketen cryptografisch voor interne consistentie en geeft vervolgens een proof-of-possession-uitdaging uit aan het apparaat.  IoT Hub verklaart het apparaat authentiek op een succesvolle proof-of-possession reactie van het apparaat.  In deze verklaring wordt ervan uitgegaan dat de privésleutel van het apparaat is beveiligd en dat alleen het apparaat met succes op deze uitdaging kan reageren.  We raden u aan veilige chips zoals Hardware Secure Modules (HSM) in apparaten te gebruiken om privésleutels te beschermen.

Een succesvolle apparaatverbinding met IoT Hub voltooit het verificatieproces en is ook indicatief voor een goede installatie.

Meer informatie hier hoe u [deze stap voor apparaatverbinding voltooien.](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [de waarde van X.509 CA-verificatie](iot-hub-x509ca-concept.md) in IoT.

Aan de slag met de Service [voor het inrichten van IoT-hubapparaten](https://docs.microsoft.com/azure/iot-dps/).

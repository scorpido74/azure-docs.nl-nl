---
title: Uw IoT Plug and Play Preview-apparaat certificeren | Microsoft Documenten
description: In deze zelfstudie wordt beschreven hoe u uw productgegevens toevoegt aan de azure certified voor IoT-apparaatcatalogus, uw apparaat aansluit op de Azure IoT-certificeringsservice en vervolgens de IoT Plug and Play-certificeringstests uitvoert.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550159"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Zelfstudie: Uw IoT Plug and Play Preview-apparaat certificeren

Als u een IoT Plug and Play Preview-apparaat wilt publiceren in de [azure certified voor IoT-apparaatcatalogus,](https://aka.ms/iotdevcat)moet het een reeks certificeringstests doorstaan. Gebruik de [Azure Certified for IoT-portal](https://aka.ms/ACFI) om uw apparaat in te dienen voor certificering. De [Azure IoT-certificeringsservice](https://aka.ms/azure-iot-aics) voert de certificeringstests uit.

In deze zelfstudie leert u:

> [!div class="checklist"]
> * Wat zijn de IoT Plug and Play-certificeringsvereisten?
> * Hoe voeg je je productnaam en informatie toe aan de portal.
> * Hoe maak je verbinding en ontdek IoT Plug and Play interfaces.
> * Hoe u IoT Plug and Play-interfaces bekijkt en certificeringstests uitvoert.
> * Het gecertificeerde IoT Plug and Play-apparaat publiceren in de catalogus.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Een Microsoft Partner Center-account.
* De Microsoft Partner Network ID.

Zie [Aan boord van de Azure Certified for IoT-portal voor](howto-onboard-portal.md) meer informatie.

## <a name="certification-requirements"></a>Vereisten voor certificering

Om uw IoT Plug and Play-apparaat te certificeren, moet uw apparaat aan de volgende vereisten voldoen:

* De code van uw IoT Plug and Play-apparaat moet op uw apparaat zijn geïnstalleerd.
* Uw IoT Plug and Play-apparaatcode is gebouwd met de Azure IoT SDK.
* Uw apparaatcode moet de [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md)ondersteunen.
* Uw apparaatcode moet de [apparaatinformatie-interface](concepts-common-interfaces.md)implementeren.
* Het capaciteitsmodel en de apparaatcode werken met IoT Central.

Alle apparaten die momenteel in de catalogus staan, worden beschouwd als vooraf gecertificeerde IoT Plug and Play-apparaten. Het garandeert niet de kwaliteit en naleving van het eindproduct van IoT Plug and Play softwarecomponenten zoals SDK en de Digital Twin Definition Language.

Alle vooraf gecertificeerde IoT Plug and Play-apparaten moeten opnieuw certificeren op basis van de algemene beschikbaarheid van IoT Plug and Play op basis van de definitieve versie van certificeringsvereisten en softwarecomponenten van Microsoft.

## <a name="add-product-name"></a>Productnaam toevoegen

**Product** is een vriendelijke naam voor een productmodel dat een klant kan kopen. Ga als lid van het artikel om een product toe te voegen:

1. Meld u aan bij de [Azure Certified for IoT-portal.](https://aka.ms/ACFI)
1. Ga naar de pagina **Producten** in de portal in het linkermenu en selecteer **+ Nieuw**.
1. Voer uw vriendelijke productnaam in en selecteer **Maken**. De hier ingevoerde naam verschilt van de naam die in de apparaatcatalogus wordt weergegeven.

## <a name="add-product-information"></a>Productinformatie toevoegen

Nadat u het product in de portal hebt gemaakt, wordt het product weergegeven op de pagina **Producten.** Ga als u de productinformatie toevoegen:

1. Selecteer de gemaakte productkoppeling op **de** productpagina in de productkolom. De staat moet in conceptstaat zijn.
1. Selecteer de koppeling **Bewerken** naast de kop **Productinformatie.** Voer informatie over uw product in op de pagina met productinformatie en zorg ervoor dat u alle vereiste velden invult.
1. Selecteer **Opslaan**. De knop **Opslaan** wordt alleen weergegeven wanneer u alle vereiste velden hebt voltooid. Als de velden onvolledig zijn, wordt op de knop **Opslaan en later voltooid.**
1. Bekijk de inhoud die u hebt ingevoerd. Vul alle vereiste velden in om het apparaat naar de apparaatcatalogus te publiceren. U altijd teruggaan om de productinformatie op de pagina productdetails aan te brengen door op de koppeling **bewerken** naast de kop **Productinformatie** te klikken.

## <a name="connect-and-discover-interfaces"></a>Interfaces verbinden en ontdekken

Als u de certificeringstests wilt uitvoeren, sluit u uw apparaat aan op de [Azure IoT-certificeringsservice](https://aka.ms/azure-iot-aics) (AICS) die beschikbaar is in de portal.

Deze stappen zijn een eenmalige stap voor het uitvoeren van certificeringstests en het is niet nodig om de code van uw productapparaat te wijzigen. Volg de volgende stappen om verbinding te maken met AICS:

1. Meld u aan bij de portal met uw Partner Center-account.
1. Klik op **Connect + test** om de certificeringsstroom te starten.
1. Kies de [verificatiemethode](../iot-dps/concepts-security.md#attestation-mechanism) om uw apparaat in te richten op AICS met de [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
   * Als u een [X.509-certificaat](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)gebruikt, uploadt u het gegenereerde X.509-certificaat. Misschien wilt u de voorbeeldcode bekijken die laat zien hoe u X.509-certificaten gebruikt: [C,](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c) [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Als u een [symmetrische sleutel](../iot-dps/concepts-symmetric-key-attestation.md)gebruikt, kopieert en plakt u de symmetrische toets in de apparaatcode.
   * TPM-verificatiemethode wordt op dit moment niet ondersteund.
1. Kopieer en plak de volgende gegenereerde id's in de apparaatcode.
   * [Registratie-id](../iot-dps/use-hsm-with-sdk.md)
   * [DPS-ID](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [DPS-eindpunt](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Wanneer uw apparaatcode is gebouwd en geïmplementeerd op het apparaat, selecteert u **Verbinding maken** om de IoT Plug and Play-interfaces te ontdekken.
1. Wanneer de verbinding met AICS succesvol is, selecteert u **Volgende** om de IoT Plug and Play-interfaces te bekijken.

## <a name="run-tests-and-publish-the-device"></a>Tests uitvoeren en het apparaat publiceren

Op de beoordelingspagina u de gedetecteerde IoT Plug and Play-interfaces bekijken. Gebruik deze pagina om de primitieven die in de interfaceweergave zijn geïmplementeerd, correct te controleren. U ook de locatie van de interface controleren.

1. Zorg ervoor dat laadvermogeningangen worden ingevoerd voor de vereiste velden. Deze velden bevatten payload-informatie voor de opdracht primitief voor de opgegeven interface.
1. Wanneer u alle vereiste gegevens hebt ingevoerd, selecteert u **Volgende**.
1. Als u de tests voor de geïmplementeerde IoT Plug and Play-interfaces wilt uitvoeren, selecteert u **Tests uitvoeren**.
1. Alle tests worden automatisch uitgevoerd. Als een test mislukt, selecteert u **Logboeken weergeven** om de foutberichten van AICS en de ruwe telemetrie te bekijken die naar Azure IoT Hub worden verzonden.
1. Als u de certificeringstests wilt voltooien, selecteert u **Voltooien**.
1. Publiceer het gecertificeerde IoT Plug and Play-apparaat in de catalogus. Als u het gecertificeerde apparaat aan de catalogus wilt toevoegen, selecteert u **Toevoegen aan catalogus** op de werkbalk. Als de **catalogus Toevoegen aan** wordt vergrijsd, betekent dit dat de productinformatie onvolledig is of dat de tests zijn mislukt. 
1. Selecteer de koppeling 'GECERTIFICEERD EN IN DE CATALOGUS' om uw gepubliceerde apparaat in de apparaatcatalogus weer te geven.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over het certificeren van IoT Plug and Play-apparaat, is de voorgestelde volgende stap om meer te weten te komen over het beheren van capaciteitsmodellen:

> [!div class="nextstepaction"]
> [Modellen beheren](./howto-manage-models.md)

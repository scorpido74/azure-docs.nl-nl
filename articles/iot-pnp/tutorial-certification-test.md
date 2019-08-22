---
title: Uw IoT Plug en Play-preview-apparaat certificeren | Microsoft Docs
description: In deze zelf studie wordt beschreven hoe u uw product gegevens toevoegt aan de catalogus met Azure Certified for IoT-apparaten, uw apparaat verbindt met de Azure IoT-certificerings service en vervolgens de IoT-Plug en Play certificerings tests uitvoert.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 06/21/2019
ms.openlocfilehash: 1516a132372a81d06d82de2409c48220f27b8d87
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878080"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Zelfstudie: Uw IoT Plug en Play-preview-apparaat certificeren

Voor het publiceren van een IoT Plug en Play preview-apparaat in de [catalogus met Azure Certified for IOT-apparaten](https://aka.ms/iotdevcat)moet er een set certificerings tests worden door gegeven. Gebruik de [Azure Certified voor IOT](https://aka.ms/ACFI) -Portal om uw apparaat in te dienen voor certificering. De [Azure IOT-certificerings service](https://aka.ms/azure-iot-aics) voert de certificerings tests uit.

In deze zelf studie leert u het volgende:

> [!div class="checklist"]
> * Wat zijn de vereisten voor IoT Plug en Play-certificering.
> * Hoe u uw product naam en-gegevens toevoegt aan de portal.
> * Verbinding maken en IoT Plug en Play-interfaces ontdekken.
> * Hoe u IoT-Plug en Play interfaces kunt controleren en certificerings tests kunt uitvoeren.
> * De gecertificeerde IoT Plug en Play-apparaat publiceren naar de catalogus.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Een micro soft Partner Center-account.
* De Microsoft Partner Network-ID.

Zie [How to unboard to the Azure Certified for IOT](howto-onboard-portal.md) Portal (Engelstalig) voor meer informatie.

## <a name="certification-requirements"></a>Certificerings vereisten

Als u uw IoT Plug en Play-apparaat wilt certificeren, moet uw apparaat voldoen aan de volgende vereisten:

* Uw IoT Plug en Play-apparaatcode moet op uw apparaat zijn geïnstalleerd.
* Uw IoT Plug en Play-apparaatcode zijn gebouwd met de Azure IoT SDK.
* Uw apparaatcode moet de [Azure-IOT hub Device Provisioning Service](../iot-dps/about-iot-dps.md)ondersteunen.
* Op de apparaatcode moet de [Interface](concepts-common-interfaces.md)voor de apparaatgegevens worden geïmplementeerd.
* Het bekwaamheids model en de apparaatcode werken met IoT Central.

Alle apparaten die zich momenteel in de catalogus bevinden, worden beschouwd als vooraf gecertificeerde IoT Plug en Play-apparaten. Het biedt geen garantie voor de kwaliteit en naleving van het uiteindelijke product van IoT-Plug en Play software onderdelen zoals SDK en de taal van de digitale dubbele definitie.

Alle vooraf gecertificeerde IoT Plug en Play-apparaten moeten opnieuw worden gecertificeerd bij de algemene Beschik baarheid van IoT Plug en Play op basis van de definitieve versie van de certificerings vereisten en software onderdelen van micro soft.

## <a name="add-product-name"></a>Product naam toevoegen

**Product** is een beschrijvende naam voor een product model dat een klant kan kopen. Een product toevoegen:

1. Meld u aan bij de [Azure Certified voor IOT](https://aka.ms/ACFI) -Portal.
1. Ga in het menu links naar de pagina **producten** van de portal en selecteer **+ Nieuw**.
1. Voer uw beschrijvende product naam in en selecteer **maken**. De naam die u hier opgeeft, wijkt af van de naam die wordt weer gegeven in de catalogus met apparaten.

## <a name="add-product-information"></a>Product informatie toevoegen

Nadat het product in de portal is gemaakt, wordt het product weer gegeven op de pagina **producten** . De product gegevens toevoegen:

1. Selecteer de gemaakte product koppeling die is gevonden op de **product** pagina in de kolom product. De status moet de status concept hebben.
1. Selecteer de koppeling **bewerken** naast **product informatie** . Voer informatie over uw product in op de pagina product informatie en zorg ervoor dat u alle vereiste velden hebt voltooid.
1. Selecteer **Opslaan**. De knop **Opslaan** wordt alleen weer gegeven wanneer u alle vereiste velden hebt voltooid. Als de velden onvolledig zijn, verschijnt de knop **opslaan en later volt ooien**.
1. Controleer de inhoud die u hebt ingevoerd. Vul alle vereiste velden in om het apparaat naar de catalogus van het apparaat te publiceren. U kunt altijd teruggaan om wijzigingen aan te brengen in de product informatie op de pagina product details door te klikken op de koppeling **bewerken** naast **product informatie** .

## <a name="connect-and-discover-interfaces"></a>Interfaces verbinden en detecteren

Als u de certificerings tests wilt uitvoeren, moet u uw apparaat verbinden met de [Azure IOT-certificerings service](https://aka.ms/azure-iot-aics) (AICS) die beschikbaar is in de portal.

Deze stappen zijn één tijd stap voor het uitvoeren van certificerings tests en het is niet nodig om de code van uw product apparaat te wijzigen. Volg deze stappen om verbinding te maken met AICS:

1. Meld u aan bij de portal met uw partner centrum-account.
1. Klik op **verbinding + testen** om de certificerings stroom te starten.
1. Kies de [verificatie methode](../iot-dps/concepts-security.md#attestation-mechanism) om uw apparaat in te RICHTEN op AICS met behulp van de [Azure-IOT hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
   * Als u een [x. 509-certificaat](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)gebruikt, uploadt u het gegenereerde x. 509-certificaat. U kunt de voorbeeld code bekijken waarin wordt getoond hoe u X. 509-certificaten gebruikt: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Als u een symmetrische [sleutel](../iot-dps/concepts-symmetric-key-attestation.md)gebruikt, kopieert en plakt u de symmetrische sleutel in de code van uw apparaat.
   * De TPM-verificatie methode wordt op dit moment niet ondersteund.
1. Kopieer de volgende gegenereerde Id's en plak ze in de code van uw apparaat.
   * [Registratie-ID](../iot-dps/use-hsm-with-sdk.md)
   * [DPS-ID](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [DPS-eind punt](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Wanneer uw apparaatcode is gebouwd en geïmplementeerd op het apparaat, selecteert u **verbinding maken** om de IOT Plug en Play-interfaces te ontdekken.
1. Wanneer de verbinding met AICS is geslaagd, selecteert u **volgende** om de IOT Plug en Play-interfaces te controleren.

## <a name="run-tests-and-publish-the-device"></a>Tests uitvoeren en het apparaat publiceren

Op de pagina controleren kunt u de gedetecteerde IoT-Plug en Play interfaces bekijken. Op deze pagina kunt u controleren of de primitieven die in de interface worden geïmplementeerd, correct worden weer gegeven. U kunt ook de locatie van de interface controleren.

1. Zorg ervoor dat Payload-invoer is ingevoerd voor de vereiste velden. Deze velden bevatten Payload-informatie voor de opdracht primitief voor de opgegeven interface.
1. Wanneer u alle vereiste gegevens hebt opgegeven, selecteert u **volgende**.
1. Als u de tests voor de geïmplementeerde IoT Plug en Play-interfaces wilt uitvoeren, selecteert u **tests uitvoeren**.
1. Alle tests worden automatisch uitgevoerd. Als een test mislukt, selecteert u **Logboeken weer geven** om de fout berichten van AICS en de onbewerkte telemetrie te bekijken die naar Azure IOT hub worden verzonden.
1. Selecteer **volt ooien**om de certificerings tests te volt ooien.
1. Publiceer het gecertificeerde IoT Plug en Play-apparaat naar de catalogus. Selecteer **toevoegen aan catalogus** op de werk balk om het gecertificeerde apparaat toe te voegen aan de catalogus. Als de **toevoegen aan catalogus** grijs wordt weer gegeven, betekent dit dat de product gegevens onvolledig zijn of dat de tests zijn mislukt. 
1. Selecteer de koppeling ' gecertificeerd en IN de catalogus ' om uw gepubliceerde apparaat weer te geven in in de catalogus met apparaten.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over het certificeren van IoT Plug en Play-apparaat, is de voorgestelde volgende stap meer informatie over het beheren van mogelijkheden modellen:

> [!div class="nextstepaction"]
> [Modellen beheren](./howto-manage-models.md)

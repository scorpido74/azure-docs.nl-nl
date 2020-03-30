---
title: Serviceconcepten in Azure IoT Hub Device Provisioning Service | Microsoft Documenten
description: Beschrijft serviceprovisioningconcepten die specifiek zijn voor apparaten met de DPS (Device Provisioning Service) en IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285212"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT Hub Device Provisioning Service-concepten

IoT Hub Device Provisioning Service is een helperservice voor IoT Hub die u gebruikt om zero-touch apparaatinrichting te configureren op een bepaalde IoT-hub. Met de Service Voor het inrichten van apparaten u miljoenen apparaten [automatisch inrichten](concepts-auto-provisioning.md) op een veilige en schaalbare manier.

Apparaatinrichting is een proces met twee delen. Het eerste deel is het tot stand brengen van de eerste verbinding tussen het apparaat en de IoT-oplossing door het apparaat *te registreren.* Het tweede deel is het toepassen van de juiste *configuratie* op het apparaat op basis van de specifieke vereisten van de oplossing. Zodra beide stappen zijn voltooid, is het apparaat volledig *ingericht.* De Device Provisioning Service automatiseert beide stappen en biedt zo een naadloze ervaring voor de inrichting van het apparaat.

Dit artikel geeft een overzicht van de inrichtingsconcepten die het meest van toepassing zijn op het beheer van de *dienst.* Dit artikel is het meest relevant voor persona's die betrokken zijn bij de [cloudsetupstap](about-iot-dps.md#cloud-setup-step) om een apparaat klaar te maken voor implementatie.

## <a name="service-operations-endpoint"></a>Eindpunt van servicebewerkingen

Het eindpunt voor servicebewerkingen is het eindpunt voor het beheren van de service-instellingen en het onderhouden van de inschrijvingslijst. Dit eindpunt wordt alleen gebruikt door de servicebeheerder; het wordt niet gebruikt door apparaten.

## <a name="device-provisioning-endpoint"></a>Eindpunt voor het inrichten van apparaten

Het apparaatinrichtingseindpunt is het enige eindpunt dat alle apparaten gebruiken voor automatische inrichting. De URL is hetzelfde voor alle inrichtingsservice-exemplaren, om te voorkomen dat apparaten opnieuw moeten worden geflitst met nieuwe verbindingsinformatie in supply chain-scenario's. De ID-scope zorgt voor tenantisolatie.

## <a name="linked-iot-hubs"></a>Gekoppelde IoT-hubs

De Service Voor het inrichten van apparaten kan alleen apparaten inrichten op IoT-hubs die eraan zijn gekoppeld. Door een IoT-hub te koppelen aan een instantie van de apparaatinrichtingsservice, worden de lees-/schrijfmachtigingen van de service gekoppeld aan het apparaatregister van de IoT-hub; met de koppeling kan een apparaatinrichtingsservice een apparaat-id registreren en de eerste configuratie instellen in de apparaatkoppeling. Gekoppelde IoT-hubs bevinden zich mogelijk in een Azure-regio. U hubs in andere abonnementen koppelen aan uw inprovisioningsservice.

## <a name="allocation-policy"></a>Toewijzingsbeleid

De instelling op serviceniveau die bepaalt hoe de apparaatinrichtingsservice apparaten toewijst aan een IoT-hub. Er worden drie soorten toewijzingsbeleid ondersteund:

* **Gelijkmatig gewogen verdeling**: gekoppelde IoT-hubs hebben evenwaarschijnlijk apparaten die op hen zijn ingericht. De standaardinstelling. Als u apparaten voor slechts één IoT-hub inricht, kunt u deze instelling bewaren.

* **Laagste latentie:** apparaten zijn ingericht voor een IoT-hub met de laagste latentie op het apparaat. Als meerdere gekoppelde IoT-hubs dezelfde laagste latentie zouden bieden, heeft de inrichtingsservice apparaten in die hubs

* **Statische configuratie via de inschrijvingslijst:** specificatie van de gewenste IoT-hub in de inschrijvingslijst heeft voorrang op het toewijzingsbeleid op serviceniveau.

## <a name="enrollment"></a>Registratie

Een inschrijving is de registratie van apparaten of groepen apparaten die zich kunnen registreren via automatische inrichting. De inschrijvingsrecord bevat informatie over het apparaat of de groep apparaten, waaronder:
- het [attestmechanisme](concepts-security.md#attestation-mechanism) dat door het apparaat wordt gebruikt
- de optionele initiële gewenste configuratie
- gewenste IoT-hub
- de gewenste apparaat-ID

Er zijn twee soorten inschrijvingen die worden ondersteund door de Apparaatinrichtingsservice:

### <a name="enrollment-group"></a>Inschrijvingsgroep

Een inschrijvingsgroep is een groep apparaten die een specifiek attestmechanisme delen. Inschrijvingsgroepen ondersteunen zowel X.509 als symmetrisch. Alle apparaten in de X.509-inschrijvingsgroep presenteren X.509-certificaten die zijn ondertekend door dezelfde root- of intermediate Certificate Authority (CA). Elk apparaat in de symmetrische sleutelinschrijvingsgroep presenteert SAS-tokens die zijn afgeleid van de symmetrische sleutel van de groep. De naam van de inschrijvingsgroep en de certificaatnaam moeten alfanumeriek, kleine letters zijn en kunnen afbreekstreepjes bevatten.

> [!TIP]
> We raden u aan een inschrijvingsgroep te gebruiken voor een groot aantal apparaten die een gewenste initiële configuratie delen, of voor apparaten die allemaal naar dezelfde tenant gaan.

### <a name="individual-enrollment"></a>Individuele inschrijving

Een individuele inschrijving is een vermelding voor één apparaat dat zich kan registreren. Individuele inschrijvingen kunnen X.509 leaf-certificaten of SAS-tokens (van een fysieke of virtuele TPM) gebruiken als attestmechanismen. De registratie-id in een individuele inschrijving is alfanumeriek, kleine letters en kan koppeltekens bevatten. Afzonderlijke inschrijvingen hebben mogelijk de gewenste apparaat-id voor IoT Hub die is opgegeven.

> [!TIP]
> We raden u aan afzonderlijke inschrijvingen te gebruiken voor apparaten waarvoor unieke initiële configuraties nodig zijn, of voor apparaten die alleen kunnen verifiëren met SAS-tokens via TPM-attest.

## <a name="registration"></a>Registratie

Een registratie is de registratie van een apparaat dat met succes een IoT-hub registreert/instelt via de Service Voor het inrichten van apparaten. Registratierecords worden automatisch gemaakt; ze kunnen worden verwijderd, maar ze kunnen niet worden bijgewerkt.

## <a name="operations"></a>Bewerkingen

Bewerkingen zijn de factureringseenheid van de Service Apparaatinrichting. Één verrichting is de succesvolle voltooiing van één instructie aan de dienst. Bewerkingen omvatten apparaatregistraties en herregistraties; bewerkingen bevatten ook wijzigingen aan de servicezijde, zoals het toevoegen van inschrijvingslijstvermeldingen en het bijwerken van inschrijvingslijstvermeldingen.

---
title: Service concepten in azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Hierin worden de concepten van service-inrichting beschreven die specifiek zijn voor apparaten met de Device Provisioning Service (DPS) en IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79285212"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT Hub Device Provisioning Service concepten

IoT Hub Device Provisioning Service is een Helper-service voor IoT Hub die u gebruikt om het inrichten van Zero-Touch-apparaten te configureren voor een opgegeven IoT-hub. Met de Device Provisioning Service kunt u miljoenen apparaten op een veilige en schaal bare manier [automatisch inrichten](concepts-auto-provisioning.md) .

Het inrichten van apparaten is een proces dat uit twee delen bestaat. In het eerste deel wordt de eerste verbinding tot stand gebracht tussen het apparaat en de IoT-oplossing door het apparaat te *registreren* . Het tweede deel past de juiste *configuratie* op het apparaat toe op basis van de specifieke vereisten van de oplossing. Wanneer beide stappen zijn uitgevoerd, is het apparaat volledig *ingericht*. De Device Provisioning Service automatiseert beide stappen en biedt zo een naadloze ervaring voor de inrichting van het apparaat.

Dit artikel bevat een overzicht van de belangrijkste concepten die van toepassing zijn op het beheer van de *service*. Dit artikel is het meest relevant voor personen die deel uitmaken van de stap voor de installatie van de [Cloud](about-iot-dps.md#cloud-setup-step) van het ophalen van een apparaat dat gereed is voor implementatie.

## <a name="service-operations-endpoint"></a>Service-eind punt

Het eind punt van de service bewerkingen is het eind punt voor het beheren van de service-instellingen en het onderhouden van de registratie lijst. Dit eind punt wordt alleen gebruikt door de service beheerder. het wordt niet gebruikt door apparaten.

## <a name="device-provisioning-endpoint"></a>Device Provisioning-eind punt

Het apparaat voor het inrichten van apparaten is het enige eind punt dat alle apparaten gebruiken voor automatische inrichting. De URL is hetzelfde voor alle inrichtings service-instanties, om te voor komen dat apparaten opnieuw moeten worden geflashen met nieuwe verbindings gegevens in de toeleverings keten scenario's. Het ID-bereik zorgt voor isolatie van de Tenant.

## <a name="linked-iot-hubs"></a>Gekoppelde IoT-hubs

De Device Provisioning Service kan alleen apparaten inrichten voor IoT-hubs die eraan zijn gekoppeld. Als u een IoT-hub koppelt aan een exemplaar van de Device Provisioning Service, worden de machtigingen lezen/schrijven van de service voor het Device-REGI ster van de IoT-hub geboden. met de koppeling kan een Device Provisioning-Service een apparaat-ID registreren en de eerste configuratie in het apparaat configureren. Gekoppelde IoT-hubs kunnen zich in elke Azure-regio bevinden. U kunt hubs in andere abonnementen koppelen aan uw inrichtings service.

## <a name="allocation-policy"></a>Toewijzings beleid

De instelling op service niveau die bepaalt hoe Device Provisioning Service apparaten toewijst aan een IoT-hub. Er worden drie soorten toewijzingsbeleid ondersteund:

* **Evenredig gewogen distributie**: gekoppelde IOT-hubs zijn even waarschijnlijk dat apparaten worden ingericht. De standaard instelling. Als u apparaten voor slechts één IoT-hub inricht, kunt u deze instelling bewaren.

* **Laagste latentie**: apparaten worden ingericht naar een IOT-hub met de laagste latentie voor het apparaat. Als meerdere gekoppelde IoT-hubs dezelfde laagste latentie geven, wordt de inrichtings service hashes van apparaten via deze hubs

* **Statische configuratie via de registratie lijst**: specificatie van de gewenste IOT-hub in de registratie lijst heeft voor rang op het toewijzings beleid op service niveau.

## <a name="enrollment"></a>Inschrijving

Een inschrijving is de record met apparaten of groepen apparaten die door automatische inrichting kunnen worden geregistreerd. De registratie record bevat informatie over het apparaat of de groep apparaten, waaronder:
- het [Attestation-mechanisme](concepts-security.md#attestation-mechanism) dat wordt gebruikt door het apparaat
- de optionele eerste gewenste configuratie
- gewenste IoT-hub
- de gewenste apparaat-ID

Er worden twee soorten inschrijvingen ondersteund door de Device Provisioning Service:

### <a name="enrollment-group"></a>Registratie groep

Een registratie groep is een groep apparaten die een specifiek Attestation-mechanisme delen. Registratie groepen ondersteunen zowel X. 509 als symmetrisch. Alle apparaten in de X. 509-registratie groep bevatten X. 509-certificaten die zijn ondertekend door dezelfde basis of tussenliggende certificerings instantie (CA). Elk apparaat in de groep voor de symmetrische sleutel registratie bevat SAS-tokens die zijn afgeleid van de groeps symmetrische sleutel. De naam van de registratie groep en de certificaat naam moeten alfanumerieke tekens, kleine letters en afbreek streepjes bevatten.

> [!TIP]
> U kunt het beste een registratie groep gebruiken voor een groot aantal apparaten die een gewenste initiële configuratie delen, of voor apparaten die allemaal naar dezelfde Tenant gaan.

### <a name="individual-enrollment"></a>Individuele inschrijving

Een afzonderlijke inschrijving is een vermelding voor één apparaat dat kan worden geregistreerd. Afzonderlijke inschrijvingen kunnen X. 509 Leaf-certificaten of SAS-tokens (van een fysieke of virtuele TPM) gebruiken als Attestation-mechanismen. De registratie-ID in een afzonderlijke inschrijving is alfanumeriek, in kleine letters en kan afbreek streepjes bevatten. Afzonderlijke inschrijvingen hebben mogelijk de gewenste apparaat-id voor IoT Hub die is opgegeven.

> [!TIP]
> We raden u aan om afzonderlijke inschrijvingen te gebruiken voor apparaten die unieke initiële configuraties vereisen, of voor apparaten die alleen kunnen worden geverifieerd met SAS-tokens via TPM-Attestation.

## <a name="registration"></a>Registratie

Een registratie is de record van een apparaat dat is geregistreerd/ingericht naar een IoT Hub via de Device Provisioning Service. Registratie records worden automatisch gemaakt. ze kunnen worden verwijderd, maar kunnen niet worden bijgewerkt.

## <a name="operations"></a>Bewerkingen

Bewerkingen zijn de facturerings eenheid van de Device Provisioning Service. Eén bewerking is het volt ooien van een instructie naar de service. Bewerkingen omvatten apparaatregistratie en nieuwe registraties; bewerkingen omvatten ook wijzigingen aan de service zijde, zoals het toevoegen van inschrijvings lijst vermeldingen en het bijwerken van inschrijvings lijst vermeldingen.

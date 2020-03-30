---
title: Functies voor klantgegevensaanvragen voor Azure DPS-apparaten
description: Voor apparaten die worden beheerd in DE Azure Device Provisioning Service (DPS) die persoonlijk zijn, wordt in dit artikel beheerders weergegeven hoe ze persoonlijke gegevens kunnen exporteren of verwijderen.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 1dcf1b9f62f94b8f75ef2fe77f3e237a387c53eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890655"
---
# <a name="summary-of-customer-data-request-features"></a>Overzicht van de functies voor het aanvragen van klantgegevens

De Azure IoT Hub Device Provisioning Service is een REST API-gebaseerde cloudservice gericht op zakelijke klanten die naadloze, geautomatiseerde zero-touch inrichting van apparaten naar Azure IoT Hub mogelijk maakt met beveiliging die begint bij het apparaat en eindigt met de cloud.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Afzonderlijke apparaten krijgen een registratie-id en een apparaat-id toegewezen van een tenantbeheerder. Gegevens van en over deze apparaten zijn gebaseerd op deze id's. Microsoft heeft geen informatie en heeft geen toegang tot gegevens die correlatie van deze apparaten met een individu mogelijk maken.

Veel van de apparaten die worden beheerd in Device Provisioning Service zijn geen persoonlijke apparaten, bijvoorbeeld een kantoorthermostaat of fabrieksrobot. Klanten kunnen sommige apparaten echter persoonlijk identificeerbaar achten en naar eigen inzicht hun eigen methoden voor het bijhouden van activa of inventarishouden die apparaten aan individuen koppelen. Device Provisioning Service beheert en bewaart alle gegevens die aan apparaten zijn gekoppeld alsof het persoonlijke gegevens zijn.

Tenantbeheerders kunnen de Azure-portal of de REST-API's van de service gebruiken om informatieaanvragen te vervullen door gegevens te exporteren of te verwijderen die zijn gekoppeld aan een apparaat-id of registratie-id.

> [!NOTE]
> Apparaten die zijn ingericht in Azure IoT Hub via Device Provisioning Service, bevatten extra gegevens die zijn opgeslagen in de Azure IoT Hub-service. Bekijk de [referentiedocumentatie van Azure IoT Hub](../iot-hub/iot-hub-customer-data-requests.md) om een volledige aanvraag voor een bepaald apparaat te voltooien.

## <a name="deleting-customer-data"></a>Klantgegevens verwijderen

Device Provisioning Service slaat inschrijvingen en registratierecords op. Inschrijvingen bevatten informatie over apparaten die mogen worden ingericht en registratierecords geven aan welke apparaten het inrichtingsproces al hebben doorlopen.

Tenantbeheerders kunnen inschrijvingen uit de Azure-portal verwijderen en hiermee worden ook alle bijbehorende registratierecords verwijderd.

Zie [Apparaatinschrijvingen beheren](how-to-manage-enrollments.md)voor meer informatie .

Het is ook mogelijk om verwijderbewerkingen uit te voeren voor inschrijvingen en registratierecords met REST API's:

* Als u inschrijvingsgegevens voor één apparaat wilt verwijderen, u [Apparaatinschrijving gebruiken - Verwijderen](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Als u inschrijvingsgegevens voor een groep apparaten wilt verwijderen, u [Apparaatinschrijvingsgroep gebruiken - Verwijderen](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Als u informatie wilt verwijderen over apparaten die zijn ingericht, u [registratiestatus - Registratiestatus verwijderen gebruiken.](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate)

## <a name="exporting-customer-data"></a>Klantgegevens exporteren

Device Provisioning Service slaat inschrijvingen en registratierecords op. Inschrijvingen bevatten informatie over apparaten die mogen worden ingericht en registratierecords geven aan welke apparaten het inrichtingsproces al hebben doorlopen.

Tenantbeheerders kunnen inschrijvingen en registratierecords bekijken via de Azure-portal en deze exporteren met kopiëren en plakken.

Zie Inschrijvingen voor apparaten beheren voor meer informatie over het beheren van [inschrijvingen.](how-to-manage-enrollments.md)

Het is ook mogelijk om exportbewerkingen uit te voeren voor inschrijvingen en registratierecords met REST API's:

* Als u inschrijvingsgegevens voor één apparaat wilt exporteren, u Apparaatinschrijving gebruiken [- Opvragen.](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment)
* Als u inschrijvingsgegevens voor een groep apparaten wilt exporteren, u [apparaatinschrijvingsgroep](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup)gebruiken - Opstappen.
* Als u informatie wilt exporteren over apparaten die al zijn ingericht, u [registratiestatus gebruiken - Registratiestatus verzamelen](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> Wanneer u de bedrijfsservices van Microsoft gebruikt, genereert Microsoft bepaalde informatie, bekend als door het systeem gegenereerde logboeken. Sommige door het Apparaat provisioning Service-systeem gegenereerde logboeken zijn niet toegankelijk of exporteerbaar door tenantbeheerders. Deze logboeken vormen feitelijke acties die binnen de service worden uitgevoerd en diagnostische gegevens met betrekking tot individuele apparaten.

## <a name="links-to-additional-documentation"></a>Koppelingen naar aanvullende documentatie

De volledige documentatie voor api's [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps)voor apparaatinrichtingsservice bevindt zich op .

Functies voor [klantgegevensaanvragen van](../iot-hub/iot-hub-customer-data-requests.md)Azure IoT Hub .
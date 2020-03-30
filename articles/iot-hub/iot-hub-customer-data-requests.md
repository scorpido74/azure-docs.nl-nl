---
title: Verzoeken om klantgegevens voor Azure IoT Hub-apparaten
description: De meeste apparaten die worden beheerd in Azure IoT Hub zijn niet persoonlijk, maar sommige wel. In dit artikel wordt gesproken over beheerders die persoonlijke gegevens van een apparaat kunnen exporteren of verwijderen.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: a05fbf6e1908f88014cd8da99fafb875de033f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499315"
---
# <a name="summary-of-customer-data-request-features"></a>Overzicht van de functies voor het aanvragen van klantgegevens

De Azure IoT Hub is een REST API-gebaseerde cloudservice gericht op zakelijke klanten die veilige, bidirectionele communicatie tussen miljoenen apparaten en een gepartitioneerde Azure-service mogelijk maakt.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Afzonderlijke apparaten krijgen een apparaat-id (apparaat-id) toegewezen door een tenantbeheerder. Apparaatgegevens zijn gebaseerd op de toegewezen apparaat-id. Microsoft houdt geen informatie bij en heeft geen toegang tot gegevens waarmee apparaat-id's kunnen worden gesommeerd aan de correlatie van de gebruiker.

Veel van de apparaten die in Azure IoT Hub worden beheerd, zijn geen persoonlijke apparaten, bijvoorbeeld een kantoorthermostaat of fabrieksrobot. Klanten kunnen sommige apparaten echter persoonlijk identificeerbaar achten en naar eigen inzicht hun eigen methoden voor het bijhouden van activa of inventarishouden die apparaten aan individuen koppelen. Azure IoT Hub beheert en bewaart alle gegevens die aan apparaten zijn gekoppeld alsof het persoonlijke gegevens zijn.

Tenantbeheerders kunnen de Azure-portal of de REST-API's van de service gebruiken om informatieaanvragen te vervullen door gegevens te exporteren of te verwijderen die zijn gekoppeld aan een apparaat-id.

Als u de routeringsfunctie van de Azure IoT Hub-service gebruikt om apparaatberichten door te sturen naar andere services, moeten gegevensaanvragen worden uitgevoerd door de tenantbeheerder voor elk routeringseindpunt om een volledige aanvraag voor een bepaald apparaat te voltooien. Zie voor meer informatie de referentiedocumentatie voor elk eindpunt. Zie [Referentie - IoT Hub-eindpunten](iot-hub-devguide-endpoints.md)voor meer informatie over ondersteunde eindpunten.

Als u de integratiefunctie Azure Event Grid van de Azure IoT Hub-service gebruikt, moeten gegevensaanvragen worden uitgevoerd door de tenantbeheerder voor elke abonnee van deze gebeurtenissen. Zie [Reageren op IoT Hub-gebeurtenissen met gebeurtenisraster voor](iot-hub-event-grid.md)meer informatie .

Als u de Azure Monitor-integratiefunctie van de Azure IoT Hub-service gebruikt om diagnostische logboeken te maken, moeten gegevensaanvragen door de tenantbeheerder worden uitgevoerd tegen de opgeslagen logboeken. Zie [De status van Azure IoT Hub controleren](iot-hub-monitor-resource-health.md)voor meer informatie.

## <a name="deleting-customer-data"></a>Klantgegevens verwijderen

Beheerders van tenant's kunnen het IE-apparatenblad van de Azure IoT Hub-extensie in de Azure-portal gebruiken om een apparaat te verwijderen, waarmee de gegevens die aan dat apparaat zijn gekoppeld, worden verwijderd.

Het is ook mogelijk om verwijderbewerkingen uit te voeren voor apparaten met REST API's. Zie [Service - Apparaat verwijderen voor](/rest/api/iothub/service/registrymanager/deletedevice)meer informatie.

## <a name="exporting-customer-data"></a>Klantgegevens exporteren

Tenantbeheerders kunnen kopiëren en plakken gebruiken in het deelvenster IoT-apparaten van de Azure IoT Hub-extensie in de Azure-portal om gegevens die aan een apparaat zijn gekoppeld, te exporteren.

Het is ook mogelijk om exportbewerkingen uit te voeren voor apparaten met REST API's. Zie [Service - Apparaat opvragen](/rest/api/iothub/service/registrymanager/getdevice)voor meer informatie.

> [!NOTE]
> Wanneer u de bedrijfsservices van Microsoft gebruikt, genereert Microsoft bepaalde informatie, bekend als door het systeem gegenereerde logboeken. Sommige azure IoT Hub-systeemlogboeken zijn niet toegankelijk of exporteerbaar door tenantbeheerders. Deze logboeken vormen feitelijke acties die binnen de service worden uitgevoerd en diagnostische gegevens met betrekking tot individuele apparaten.

## <a name="links-to-additional-documentation"></a>Koppelingen naar aanvullende documentatie

Volledige documentatie voor Azure IoT Hub Service API's bevindt zich op [IoT Hub Service API's.](https://docs.microsoft.com/rest/api/iothub/service/configuration)

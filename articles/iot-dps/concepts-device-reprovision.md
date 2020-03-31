---
title: Azure IoT Hub Device Provisioning Service - Apparaatconcepten
description: Beschrijft concepten voor het opnieuw inrichten van apparaten voor de DPS (Azure IoT Hub Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2bf369b784cddf307abc59d2b8766fc8a87e0985
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975343"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Concepten voor het opnieuw inrichten van IoT Hub Device

Tijdens de levenscyclus van een IoT-oplossing is het gebruikelijk om apparaten tussen IoT-hubs te verplaatsen. De redenen voor deze verhuizing kunnen de volgende scenario's zijn:

* **Geolocatie / GeoLatentie:** Als een apparaat tussen locaties beweegt, wordt de netwerklatentie verbeterd door het apparaat te laten migreren naar een nauwere IoT-hub.

* **Multi-tenancy**: Een apparaat kan binnen dezelfde IoT-oplossing worden gebruikt en opnieuw worden toegewezen aan een nieuwe klant of klantsite. Deze nieuwe klant kan worden bediend met behulp van een andere IoT-hub.

* **Wijziging van de oplossing:** een apparaat kan worden verplaatst naar een nieuwe of bijgewerkte IoT-oplossing. Voor deze hertoewijzing moet het apparaat mogelijk communiceren met een nieuwe IoT-hub die is verbonden met andere back-endcomponenten.

* **Quarantaine:** vergelijkbaar met een oplossingswijziging. Een apparaat dat defect, gecompromitteerd of verouderd is, kan opnieuw worden toegewezen aan een IoT-hub die alleen kan worden bijgewerkt en weer in overeenstemming kan worden. Zodra het apparaat goed functioneert, wordt het vervolgens teruggemigreerd naar de hoofdhub.

Ondersteuning opnieuw inrichten binnen de Service Voor het inrichten van apparaten is voor deze behoeften. Apparaten kunnen automatisch opnieuw worden toegewezen aan nieuwe IoT-hubs op basis van het herinrichtingsbeleid dat is geconfigureerd bij de inschrijvingsvermelding van het apparaat.

## <a name="device-state-data"></a>Gegevens over apparaatstatus

Gegevens over de apparaatstatus zijn samengesteld uit de [apparaattweeling](../iot-hub/iot-hub-devguide-device-twins.md) en apparaatmogelijkheden. Deze gegevens worden opgeslagen in de instantie Device Provisioning Service en de IoT-hub waaraan een apparaat is toegewezen.

![Inrichten bij de dienst voor inrichting van apparaten](./media/concepts-device-reprovisioning/dps-provisioning.png)

Wanneer een apparaat in eerste instantie is ingericht met een apparaatinrichtingsservice, worden de volgende stappen uitgevoerd:

1. Het apparaat stuurt een inrichtingsverzoek naar een apparaatinrichtingsservice-exemplaar. De serviceinstantie verifieert de apparaatidentiteit op basis van een inschrijvingsvermelding en maakt de eerste configuratie van de apparaatstatusgegevens. De service-instantie wijst het apparaat toe aan een IoT-hub op basis van de inschrijvingsconfiguratie en retourneert die Toewijzing van de IoT-hub naar het apparaat.

2. De instantie voor het inrichten van de service geeft een kopie van de oorspronkelijke apparaatstatusgegevens aan de toegewezen IoT-hub. Het apparaat maakt verbinding met de toegewezen IoT-hub en begint met bewerkingen.

Na verloop van tijd kunnen de gegevens van de apparaatstatus op de IoT-hub worden bijgewerkt door [apparaatbewerkingen](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) en [back-endbewerkingen](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). De initiële apparaatstatusstatusgegevens die zijn opgeslagen in de instantie Apparaatvoorzieningsservice blijft onaangeroerd. Deze onaangeroerde apparaatstatusgegevens zijn de eerste configuratie.

![Inrichten bij de dienst voor inrichting van apparaten](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Afhankelijk van het scenario kan het nodig zijn om de apparaatstatus die op de vorige IoT-hub is bijgewerkt naar de nieuwe IoT-hub te migreren naar de nieuwe IoT-hub, aangezien een apparaat tussen IoT-hubs wordt verplaatst. Deze migratie wordt ondersteund door het opnieuw inrichten van beleid in de Apparaatinrichtingsservice.

## <a name="reprovisioning-policies"></a>Beleid voor het opnieuw inrichten

Afhankelijk van het scenario stuurt een apparaat meestal een verzoek naar een inrichtingsserviceinstantie bij opnieuw opstarten. Het ondersteunt ook een methode om handmatig te activeren provisioning on demand. Het herinderingsbeleid voor een inschrijvingsitem bepaalt hoe de serviceinstantie voor het inrichten van apparaten met deze inrichtingsaanvragen omgaat. Het beleid bepaalt ook of apparaatstatusgegevens moeten worden gemigreerd tijdens het opnieuw inrichten. Hetzelfde beleid is beschikbaar voor afzonderlijke inschrijvingen en inschrijvingsgroepen:

* **Gegevens opnieuw inrichten en migreren:** dit beleid is de standaardinstelling voor nieuwe inschrijvingsitems. Dit beleid onderneemt actie wanneer apparaten die zijn gekoppeld aan het inschrijvingsitem een nieuwe aanvraag indienen (1). Afhankelijk van de configuratie van de inschrijvingsinvoer kan het apparaat opnieuw worden toegewezen aan een andere IoT-hub. Als het apparaat IoT-hubs wijzigt, wordt de apparaatregistratie met de oorspronkelijke IoT-hub verwijderd. De bijgewerkte informatie over de status van het apparaat van die oorspronkelijke IoT-hub wordt gemigreerd naar de nieuwe IoT-hub (2). Tijdens de migratie wordt de status van het apparaat gerapporteerd als **Toewijzen**.

    ![Inrichten bij de dienst voor inrichting van apparaten](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Opnieuw inrichten en opnieuw instellen op initiële config**: Dit beleid onderneemt actie wanneer apparaten die zijn gekoppeld aan de inschrijvingsvermelding een nieuwe inrichtingsaanvraag indienen (1). Afhankelijk van de configuratie van de inschrijvingsinvoer kan het apparaat opnieuw worden toegewezen aan een andere IoT-hub. Als het apparaat IoT-hubs wijzigt, wordt de apparaatregistratie met de oorspronkelijke IoT-hub verwijderd. De eerste configuratiegegevens die de inrichtingsservice-instantie heeft ontvangen toen het apparaat werd ingericht, worden verstrekt aan de nieuwe IoT-hub (2). Tijdens de migratie wordt de status van het apparaat gerapporteerd als **Toewijzen**.

    Dit beleid wordt vaak gebruikt voor een fabrieksreset zonder IoT-hubs te wijzigen.

    ![Inrichten bij de dienst voor inrichting van apparaten](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nooit opnieuw inrichten**: Het apparaat wordt nooit opnieuw toegewezen aan een andere hub. Dit beleid is bedoeld voor het beheren van backwards compatibility.

### <a name="managing-backwards-compatibility"></a>Achterwaartse compatibiliteit beheren

Vóór september 2018 hadden apparaattoewijzingen aan IoT-hubs een plakkerig gedrag. Wanneer een apparaat het inrichtingsproces heeft doorlopen, wordt het alleen toegewezen aan dezelfde IoT-hub.

Voor oplossingen die afhankelijk zijn van dit gedrag, bevat de inrichtingsservice achterwaartse compatibiliteit. Dit gedrag wordt momenteel gehandhaafd voor apparaten volgens de volgende criteria:

1. De apparaten maken verbinding met een API-versie voordat u de ondersteuning voor native reprovisioning in de Service voor apparaatinrichting beschikbaar maakt. Raadpleeg de onderstaande API-tabel.

2. De inschrijvingsvermelding voor de apparaten heeft geen reprovisioning-beleid.

Deze compatibiliteit zorgt ervoor dat eerder geïmplementeerde apparaten hetzelfde gedrag vertonen als tijdens de eerste tests. Als u het vorige gedrag wilt behouden, slaat u een herinrichtingsbeleid niet op voor deze inschrijvingen. Als een herinrichtingsbeleid is ingesteld, heeft het herinrichtende beleid voorrang op het gedrag. Door het reprovisioning-beleid voorrang te geven, kunnen klanten het gedrag van apparaten bijwerken zonder het apparaat opnieuw te hoeven aaien.

In het volgende stroomdiagram u weergeven wanneer het gedrag aanwezig is:

![achterwaartse compatibiliteitsstroomdiagram](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

In de volgende tabel worden de API-versies weergegeven vóór de beschikbaarheid van native reprovisioning-ondersteuning in de service voor apparaatinrichting:

| REST API | C SDK | Python SDK |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 en eerder](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 en eerder](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 en eerder](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 of eerder](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 of eerder](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 of eerder](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Deze waarden en links zullen waarschijnlijk veranderen. Dit is slechts een tijdelijke aanduiding poging om te bepalen waar de versies kunnen worden bepaald door een klant en wat de verwachte versies zullen zijn.

## <a name="next-steps"></a>Volgende stappen

* [Apparaten opnieuw inrichten](how-to-reprovision.md)

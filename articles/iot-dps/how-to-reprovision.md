---
title: Apparaten opnieuw inrichten in Azure IoT Hub Device Provisioning Service
description: Meer informatie over het opnieuw inrichten van apparaten met uw DPS-exemplaar (Device Provisioning Service) en waarom u dit mogelijk moet doen.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0ded494debab19daa15a953715b1ab7b0b10ad18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974901"
---
# <a name="how-to-reprovision-devices"></a>Apparaten opnieuw inrichten

Tijdens de levenscyclus van een IoT-oplossing is het gebruikelijk om apparaten tussen IoT-hubs te verplaatsen. De redenen voor deze verhuizing kunnen de volgende scenario's zijn:

* **Geolocatie:** Als een apparaat tussen locaties beweegt, wordt de netwerklatentie verbeterd door het apparaat dichter bij elke locatie te laten migreren naar een IoT-hub.

* **Multi-tenancy**: Een apparaat kan worden gebruikt binnen dezelfde IoT-oplossing, maar, toegewezen of verhuurd aan een nieuwe klant of klantsite. Deze nieuwe klant kan worden bediend met behulp van een andere IoT-hub.

* **Wijziging van de oplossing:** een apparaat kan worden verplaatst naar een nieuwe of bijgewerkte IoT-oplossing. Deze hertoewijzing kan vereisen dat het apparaat communiceert met een nieuwe IoT-hub die is verbonden met andere backendcomponenten. 

* **Quarantaine:** vergelijkbaar met een oplossingswijziging. Een apparaat dat defect, gecompromitteerd of verouderd is, kan worden toegewezen aan een IoT-hub waar het alleen maar kan updaten en weer in overeenstemming is. Zodra het apparaat goed functioneert, wordt het vervolgens teruggemigreerd naar de hoofdhub.

Zie [IoT Hub Device reprovisioning concepts](concepts-device-reprovision.md)voor meer een gedetailleerder overzicht van het opnieuw inrichten van iot Hub Device.


## <a name="configure-the-enrollment-allocation-policy"></a>Het beleid voor inschrijvingstoewijzing configureren

Het toewijzingsbeleid bepaalt hoe de apparaten die aan de inschrijving zijn gekoppeld, worden toegewezen aan een IoT-hub zodra deze opnieuw zijn ingericht.

In de volgende stappen configureert u het toewijzingsbeleid voor de inschrijving van een apparaat:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar de instantie Device Provisioning Service.

2. Klik op Inschrijvingen beheren en klik op de **inschrijvingsgroep**of individuele inschrijving die u wilt configureren voor opnieuw inrichten. 

3. Selecteer **onder Selecteer hoe u apparaten aan hubs wilt toewijzen,** een van de volgende toewijzingsbeleidsregels:

    * **Laagste latentie**: Dit beleid wijst apparaten toe aan de gekoppelde IoT-hub die resulteert in de laagste latentiecommunicatie tussen apparaat en IoT-hub. Met deze optie kan het apparaat communiceren met de dichtstbijzijnde IoT-hub op basis van locatie. 
    
    * **Gelijkmatiggewogen verdeling:** Dit beleid verdeelt apparaten over de gekoppelde IoT-hubs op basis van het toewijzingsgewicht dat is toegewezen aan elke gekoppelde IoT-hub. Met dit beleid u saldoapparaten laden in een groep gekoppelde hubs op basis van de toewijzingsgewichten die op die hubs zijn ingesteld. Als u apparaten inricht op slechts één IoT-hub, raden we deze instelling aan. Dit is de standaardinstelling. 
    
    * **Statische configuratie:** voor dit beleid is een gewenste IoT-hub vereist die wordt vermeld in de inschrijvingsvermelding voor een apparaat dat moet worden ingericht. Met dit beleid u één specifieke IoT-hub aanwijzen waaraan u apparaten wilt toewijzen.

4. Selecteer **onder De IoT-hubs selecteren waaraan deze groep kan worden toegewezen,** selecteer de gekoppelde IoT-hubs die u wilt opnemen in uw toewijzingsbeleid. Voeg optioneel een nieuwe gekoppelde Iot-hub toe met de **knop Koppeling een nieuwe IoT Hub-knop.**

    Met het beleid voor toewijzing **met laagste latentie** worden de hubs die u selecteert, opgenomen in de latentie-evaluatie om de dichtstbijzijnde hub voor apparaattoewijzing te bepalen.

    Met het **beleid voor gelijkmatig gewogen distributietoewijzing** worden apparaten in balans gebracht in de hubs die u selecteert op basis van hun geconfigureerde toewijzingsgewichten en hun huidige apparaatbelasting.

    Selecteer met het beleid **voor locatie van statische configuratie** de IoT-hub waaraan apparaten zijn toegewezen.

4. Klik **op Opslaan**of ga naar de volgende sectie om het inrichtingsbeleid in te stellen.

    ![Beleid voor inschrijvingstoewijzing selecteren](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Het inrichtingsbeleid instellen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar de instantie Device Provisioning Service.

2. Klik op Inschrijvingen beheren en klik op de **inschrijvingsgroep**of individuele inschrijving die u wilt configureren voor opnieuw inrichten.

3. Kies **onder Selecteer hoe u wilt dat apparaatgegevens worden verwerkt bij hervoorziening naar een andere IoT-hub,** een van de volgende opties voor het opnieuw inrichten:

    * **Gegevens opnieuw inrichten en migreren:** Dit beleid onderneemt actie wanneer apparaten die zijn gekoppeld aan de inschrijvingsvermelding een nieuwe inrichtingsaanvraag indienen. Afhankelijk van de configuratie van de inschrijvingsinvoer kan het apparaat opnieuw worden toegewezen aan een andere IoT-hub. Als het apparaat IoT-hubs wijzigt, wordt de apparaatregistratie met de oorspronkelijke IoT-hub verwijderd. Alle informatie over apparaatstatus van die oorspronkelijke IoT-hub wordt gemigreerd naar de nieuwe IoT-hub. Tijdens de migratie wordt de status van het apparaat gerapporteerd **als**

    * **Opnieuw inrichten en opnieuw instellen op initiële config:** Dit beleid onderneemt actie wanneer apparaten die zijn gekoppeld aan de inschrijvingsvermelding een nieuwe inrichtingsaanvraag indienen. Afhankelijk van de configuratie van de inschrijvingsinvoer kan het apparaat opnieuw worden toegewezen aan een andere IoT-hub. Als het apparaat IoT-hubs wijzigt, wordt de apparaatregistratie met de oorspronkelijke IoT-hub verwijderd. De eerste configuratiegegevens die de inrichtingsservice-instantie heeft ontvangen toen het apparaat werd ingericht, worden verstrekt aan de nieuwe IoT-hub. Tijdens de migratie wordt de status van het apparaat gerapporteerd als **Toewijzen**.

4. Klik **op Opslaan** om de herinrichting van het apparaat in te schakelen op basis van uw wijzigingen.

    ![Beleid voor inschrijvingstoewijzing selecteren](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Een inrichtingsverzoek verzenden vanaf het apparaat

Om apparaten opnieuw in te richten op basis van de configuratiewijzigingen die in de voorgaande secties zijn aangebracht, moeten deze apparaten om herinrichten verzoeken. 

Hoe vaak een apparaat een inrichtingsaanvraag indient, is afhankelijk van het scenario. Het is echter raadzaam om uw apparaten te programmeren om een inrichtingsverzoek te verzenden naar een inrichtingsservice-instantie bij opnieuw opstarten, en een [methode](../iot-hub/iot-hub-devguide-direct-methods.md) te ondersteunen om handmatig provisioning on demand te activeren. Inrichten kan ook worden geactiveerd door het instellen van een [gewenste eigenschap](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

Het herinrichtingsbeleid voor een inschrijvingsitem bepaalt hoe de serviceinstantie voor apparaatinrichting omgaat met deze inrichtingsaanvragen en of apparaatstatusgegevens moeten worden gemigreerd tijdens het opnieuw inrichten. Hetzelfde beleid is beschikbaar voor afzonderlijke inschrijvingen en inschrijvingsgroepen:

Zie Bijvoorbeeld code voor het verzenden van inrichtingsverzoeken van een apparaat tijdens een opstartreeks, zie [Het automatisch inrichten van een gesimuleerd apparaat](quick-create-simulated-device.md).


## <a name="next-steps"></a>Volgende stappen

- Zie Concepten voor het opnieuw inrichten van IoT Hub Device voor meer informatie over het opnieuw [inrichten van iot-hubapparaten](concepts-device-reprovision.md) 
- Zie [Apparaten deprovisionen die voorheen automatisch zijn ingericht](how-to-unprovision-devices.md) voor meer deprovisioning. 












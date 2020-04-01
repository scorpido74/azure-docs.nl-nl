---
title: Een app voor continue patiëntbewaking maken met Azure IoT Central | Microsoft Documenten
description: Leer een continue toepassing voor patiëntbewaking bouwen met Azure IoT Central-toepassingssjablonen.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 35ac39109bfcb4dc63b738c947d2ad8caf8ac0a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77021284"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Zelfstudie: Een sjabloon voor continue patiëntbewaking implementeren en doorlopen



In deze zelfstudie ziet u als oplossingsbouwer hoe u aan de slag gaan door een iot central-sjabloon voor continue patiëntbewaking te implementeren. U leert hoe u de sjabloon implementeert, wat er uit het vak is opgenomen en wat u vervolgens doen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toepassingssjabloon maken
> * De toepassingssjabloon doorlopen

## <a name="create-an-application-template"></a>Een toepassingssjabloon maken

Navigeer naar de [website azure IoT Central-toepassingsbeheer](https://apps.azureiotcentral.com/). Selecteer **Bouwen** op de navigatiebalk aan de linkerkant en klik op het tabblad **Gezondheidszorg.** 

>[!div class="mx-imgBorder"] 
>![App manager Healthcare](media/app-manager-health.png)

Klik **op** de knop App maken om te beginnen met het maken van uw toepassing en meld u vervolgens aan met een persoonlijk, werk- of schoolaccount van Microsoft. Het brengt u naar de **nieuwe toepassingspagina.**

![Toepassing Gezondheidszorg maken](media/app-manager-health-create.png)

![Factureringsgegevens voor toepassinggezondheidszorg maken](media/app-manager-health-create-billinginfo.png)

Ga als u uw toepassing maken:

1. Azure IoT Central stelt automatisch een toepassingsnaam voor op basis van de sjabloon die u hebt geselecteerd. U deze naam accepteren of uw eigen vriendelijke toepassingsnaam invoeren, zoals **Continue patiëntbewaking.** Azure IoT Central genereert ook een uniek URL-voorvoegsel voor u op basis van de naam van de toepassing. Je bent vrij om dit URL-voorvoegsel te wijzigen in iets dat meer gedenkwaardig is als je wilt.

2. U selecteren of u de toepassing wilt maken met behulp van het *gratis* prijsplan of een van de *standaardprijsplannen.* Toepassingen die u maakt met behulp van het gratis abonnement zijn gratis voor zeven dagen voordat ze verlopen en staan maximaal vijf gratis apparaten toe. U een toepassing op elk gewenst moment van het gratis abonnement naar een standaardprijsplan verplaatsen voordat deze verloopt. Als u het gratis abonnement kiest, moet u uw contactgegevens invoeren en kiezen of u informatie en tips van Microsoft wilt ontvangen. Toepassingen die u maakt met behulp van een standaardabonnement ondersteunen maximaal twee gratis apparaten en vereisen dat u uw Azure-abonnementsgegevens invoert voor facturering.

3. Selecteer **Maken** onder aan de pagina om uw toepassing te implementeren.

## <a name="walk-through-the-application-template"></a>De toepassingssjabloon doorlopen

### <a name="dashboards"></a>Dashboards

Nadat u de app-sjabloon hebt geïmplementeerd, landt u eerst op het **lamna-dashboard voor patiëntbewaking.** Lamna Healthcare is een fictief ziekenhuissysteem dat twee ziekenhuizen bevat: Woodgrove Hospital en Burkville Hospital. Op dit operatordashboard voor Het Ziekenhuis van Woodgrove ziet u informatie en telemetrie over de apparaten in deze sjabloon, samen met een reeks opdrachten, taken en acties die u uitvoeren. Vanuit het dashboard u:

* Bekijk de telemetrie en eigenschappen van het apparaat, zoals het **batterijniveau** van uw apparaat of de **verbindingsstatus.**

* Bekijk de **plattegrond** en locatie van het Smart Vitals Patch-apparaat.

* **Heringericht van** de Smart Vitals Patch voor een nieuwe patiënt.

* Zie een voorbeeld van een **provider dashboard** dat een ziekenhuis zorg team kan zien om hun patiënten te volgen.

* Wijzig de **patiëntstatus** van uw apparaat om aan te geven of het apparaat wordt gebruikt voor een scenario op afstand of op afstand.

>[!div class="mx-imgBorder"] 
>![Lamna in-patient](media/lamna-in-patient.png)

U ook op **Ga naar het dashboard van externe patiënten** klikken om het tweede bedieningsdashboard te zien dat wordt gebruikt voor het Burkville-ziekenhuis. Dit dashboard bevat een vergelijkbare set acties, telemetrie en informatie. Bovendien u zien dat meerdere apparaten worden gebruikt en hebben de mogelijkheid om **de firmware bij** te werken op elk.

>[!div class="mx-imgBorder"] 
>![Lamna afstandsbediening](media/lamna-remote.png)

Op beide dashboards u altijd terugkoppelen naar deze documentatie.

### <a name="device-templates"></a>Apparaatsjablonen

Als u op het tabblad **Apparaatsjablonen** klikt, ziet u dat er twee verschillende apparaattypen zijn die deel uitmaken van de sjabloon:

* **Smart Vitals Patch**: Dit apparaat vertegenwoordigt een patch die verschillende soorten vitale functies meet. Het kan worden gebruikt voor het monitoren van patiënten in en buiten het ziekenhuis. Als u op de sjabloon klikt, ziet u dat de patch naast het verzenden van apparaatgegevens zoals batterijniveau en apparaattemperatuur, ook gezondheidsgegevens van patiënten, zoals de ademhalingssnelheid en bloeddruk, verzendt.

* **Smart Knee Brace**: Dit apparaat vertegenwoordigt een kniebrace die patiënten kunnen gebruiken bij het herstellen van een knieprothese operatie. Als u op deze sjabloon klikt, ziet u naast apparaatgegevens ook mogelijkheden zoals bewegingsbereik en versnelling.

>[!div class="mx-imgBorder"] 
>![Sjabloon voor smart vitals-patchapparaten](media/smart-vitals-device-template.png)

Als u op het tabblad **Apparaatgroepen** klikt, ziet u ook dat voor deze apparaatsjablonen automatisch apparaatgroepen voor hen zijn gemaakt.

### <a name="rules"></a>Regels

Wanneer u naar het tabblad Regels springt, ziet u drie regels die in de toepassingssjabloon bestaan:

* **Brace temperatuur hoog**: Deze regel wordt geactiveerd wanneer de temperatuur&deg;van het apparaat van de Smart Knee Brace groter is dan 95 F over een periode van 5 minuten. U deze regel gebruiken om de patiënt en het zorgteam te waarschuwen en het apparaat op afstand af te koelen.

* **Val gedetecteerd**: Deze regel wordt geactiveerd als een val van een patiënt wordt gedetecteerd. U deze regel gebruiken om een actie te configureren om een operationeel team in te zetten om de gevallen patiënt bij te staan.

* **Patch batterij bijna leeg**: Deze regel wordt geactiveerd wanneer het batterijniveau op het apparaat onder de 10% komt. U deze regel gebruiken om een melding aan de patiënt te activeren om hun apparaat op te laden.

>[!div class="mx-imgBorder"] 
>![Brace temperatuur hoge regel](media/brace-temp-rule.png)

### <a name="devices"></a>Apparaten

Klik op het tabblad **Apparaten** en selecteer een instantie van de **Smart Knee Brace**. U ziet dat er drie weergaven zijn om informatie te verkennen over het specifieke apparaat dat u hebt geselecteerd. Deze weergaven worden gemaakt en gepubliceerd bij het bouwen van de apparaatsjabloon voor uw apparaat, wat betekent dat ze consistent zijn op alle apparaten die u verbindt of simuleert.

De **dashboardweergave** geeft een overzicht van telemetrie en eigenschappen die afkomstig zijn van het apparaat dat operatorgeoriënteerd is.

Op het tabblad **Eigenschappen** u cloud-eigenschappen bewerken en eigenschappen van het lees-/schrijfapparaat lezen/schrijven.

Op het tabblad **Opdrachten** u opdrachten uitvoeren die zijn gemodelleerd als onderdeel van uw apparaatsjabloon.

>[!div class="mx-imgBorder"] 
>![Kniebrace weergaven](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet blijft gebruiken, verwijdert u de toepassing door **naar Beheer > toepassingsinstellingen** te gaan en klikt u op **Verwijderen**.

>[!div class="mx-imgBorder"] 
>![App verwijderen](media/admin-delete.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het maken van een providerdashboard dat verbinding maakt met uw IoT Central-toepassing.

> [!div class="nextstepaction"]
> [Een providerdashboard maken](howto-health-data-triage.md)
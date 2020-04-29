---
title: Een voortdurende app voor het controleren van patiënten maken met Azure IoT Central | Microsoft Docs
description: Leer hoe u een continue patiënten-bewakings toepassing maakt met behulp van Azure IoT Central-toepassings sjablonen.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 35ac39109bfcb4dc63b738c947d2ad8caf8ac0a6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77021284"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Zelf studie: een sjabloon voor het controleren van doorlopende patiënten implementeren en bewaken



In deze zelf studie ziet u, als een oplossings functie, hoe u aan de slag kunt gaan door een IoT Central continue patiënten-toepassings sjabloon te implementeren. U leert hoe u de sjabloon implementeert, wat is opgenomen in het vak en wat u nu kunt doen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toepassings sjabloon maken
> * De toepassings sjabloon door lopen

## <a name="create-an-application-template"></a>Een toepassings sjabloon maken

Ga naar de [website van Azure IOT Central Application Manager](https://apps.azureiotcentral.com/). Selecteer **samen stellen** in de navigatie balk aan de linkerkant en klik vervolgens op het tabblad **gezondheids zorg** . 

>[!div class="mx-imgBorder"] 
>![Gezondheids zorg voor app manager](media/app-manager-health.png)

Klik op de knop **app maken** om de toepassing te gaan maken en meld u aan met een persoonlijk, werk-of school account van micro soft. Hiermee gaat u naar de pagina **nieuwe toepassing** .

![Gezondheids zorg voor de toepassing maken](media/app-manager-health-create.png)

![Facturerings gegevens voor de betalings zorg van de toepassing maken](media/app-manager-health-create-billinginfo.png)

Uw toepassing maken:

1. In azure IoT Central wordt automatisch een toepassings naam voorgesteld op basis van de sjabloon die u hebt geselecteerd. U kunt deze naam accepteren of uw eigen beschrijvende toepassings naam invoeren, zoals een **voortdurende patiënt bewaking**. Azure IoT Central genereert ook een uniek URL-voor voegsel voor u op basis van de naam van de toepassing. Als u wilt, kunt u dit URL-voor voegsel wijzigen in iets eenvoudiger te onthouden.

2. U kunt kiezen of u de toepassing wilt maken met behulp van het *gratis* prijs plan of een van de *standaard* prijzen plannen. Toepassingen die u maakt met behulp van het gratis abonnement, zijn zeven dagen beschikbaar voordat ze verlopen en bieden Maxi maal vijf gratis apparaten. U kunt een toepassing verplaatsen van het gratis abonnement naar een Standard-prijs plan op elk moment voordat het verloopt. Als u kiest voor het gratis abonnement, moet u uw contact gegevens invoeren en kiezen of u informatie en tips van micro soft wilt ontvangen. Toepassingen die u maakt met behulp van een Standard-abonnement ondersteunen Maxi maal twee gratis apparaten en u moet uw Azure-abonnements gegevens invoeren voor facturering.

3. Selecteer onder aan de pagina **maken** om uw toepassing te implementeren.

## <a name="walk-through-the-application-template"></a>De toepassings sjabloon door lopen

### <a name="dashboards"></a>Dashboards

Na de implementatie van de app-sjabloon gaat u eerst naar het **Lamna in de gaten**houden. Lamna gezondheids zorg is een fictief ziekenhuis systeem dat twee zieken huizen bevat: Woodgrove ziekenhuis en Burkville zieken huis. Op dit operator dashboard voor Woodgrove zieken huizen ziet u informatie en telemetrie over de apparaten in deze sjabloon, samen met een set opdrachten, taken en acties die u kunt uitvoeren. Vanuit het dash board kunt u het volgende doen:

* Zie telemetrie van apparaten en eigenschappen, zoals het **accu niveau** van het apparaat of de **connectiviteits** status.

* Bekijk het **vloer plan** en de locatie van het Smart vitale patch-apparaat.

* De Smart vitale patch opnieuw **inrichten** voor een nieuwe patiënt.

* Bekijk een voor beeld van een **provider dashboard** dat een ziekenhuis Care team kan zien om hun patiënten bij te houden.

* Wijzig de **status** van de patiënt van uw apparaat om aan te geven of het apparaat wordt gebruikt voor een in-patiënten of extern scenario.

>[!div class="mx-imgBorder"] 
>![Lamna in-patiënten](media/lamna-in-patient.png)

U kunt ook klikken op **naar het dash board van de externe patiënt** om het tweede operator dashboard te zien dat voor Burkville ziekenhuis wordt gebruikt. Dit dash board bevat een soort gelijke set acties, telemetrie en informatie. Bovendien ziet u dat er meerdere apparaten worden gebruikt en de mogelijkheid hebben om **de firmware** op elk apparaat bij te werken.

>[!div class="mx-imgBorder"] 
>![Lamna extern](media/lamna-remote.png)

Op beide Dash boards kunt u altijd een koppeling naar deze documentatie maken.

### <a name="device-templates"></a>Apparaatinstellingen

Als u op het tabblad **device templates** klikt, ziet u dat er twee verschillende typen apparaten zijn die deel uitmaken van de sjabloon:

* **Smart vitale patch**: dit apparaat vertegenwoordigt een patch die verschillende soorten vitale tekens meet. Het kan worden gebruikt voor het bewaken van patiënten in en buiten het zieken huis. Als u op de sjabloon klikt, ziet u dat naast het verzenden van apparaatgegevens, zoals het accu niveau en de Tempe ratuur van het apparaat, ook de status gegevens van patiënten, zoals het Ademhalings tempo en de bloed druk, worden verzonden.

* **Krul haak**: dit apparaat vertegenwoordigt een knie accolade die patiënten mogelijk gebruiken bij het herstellen van een chirurgische. Als u op deze sjabloon klikt, worden de mogelijkheden, zoals het aantal bewegingen en de versnelling, weer gegeven naast de apparaatgegevens.

>[!div class="mx-imgBorder"] 
>![Sjabloon Smart vitale patch-apparaat](media/smart-vitals-device-template.png)

Als u op het tabblad **Apparaatgroepen** klikt, ziet u ook dat voor deze Apparaatinstellingen automatisch apparaatgroepen worden gemaakt.

### <a name="rules"></a>Regels

Wanneer u naar het tabblad regels gaat, ziet u drie regels die voor komen in de toepassings sjabloon:

* **Hoge beugel-Tempe ratuur**: deze regel wordt geactiveerd wanneer de apparaats temperatuur van de slimme knie accolade groter is&deg;dan 95 F over een periode van vijf minuten. U kunt deze regel gebruiken om het patiënten-en Care team te waarschuwen en het apparaat op afstand af te koelen.

* **Herfst gedetecteerd**: deze regel wordt geactiveerd als er een patiënt wordt gedetecteerd. U kunt deze regel gebruiken om een actie te configureren voor het implementeren van een operationeel team om de patiënt te helpen die zich heeft voordoen.

* **Accu laag patch**: deze regel wordt geactiveerd wanneer het accu niveau op het apparaat lager is dan 10%. U kunt deze regel gebruiken om een melding naar de patiënt te activeren om het apparaat in rekening te brengen.

>[!div class="mx-imgBorder"] 
>![Hoge regel voor haak-Tempe ratuur](media/brace-temp-rule.png)

### <a name="devices"></a>Apparaten

Klik op het tabblad **apparaten** en selecteer vervolgens een exemplaar van de **slimme knie accolade**. U ziet dat er drie weer gaven zijn om informatie over het geselecteerde apparaat dat u hebt geselecteerd, te verkennen. Deze weer gaven worden gemaakt en gepubliceerd bij het bouwen van de sjabloon voor het apparaat voor uw apparaat. Dit betekent dat ze consistent zijn op alle apparaten die u verbindt of simuleert.

In de **Dashboard** weergave wordt een overzicht gegeven van de telemetrie en de eigenschappen die afkomstig zijn van het apparaat waarop de operator georiënteerd is.

Op het tabblad **Eigenschappen** kunt u de eigenschappen van de Cloud en het lezen/schrijven van apparaten bewerken.

Op het tabblad **opdrachten** kunt u opdrachten uitvoeren die zijn gemodelleerd als onderdeel van de sjabloon voor het apparaat.

>[!div class="mx-imgBorder"] 
>![Weer gaven voor knie accolades](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u de toepassing door naar **beheer > toepassings instellingen** te gaan en op **verwijderen**te klikken.

>[!div class="mx-imgBorder"] 
>![App verwijderen](media/admin-delete.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het maken van een provider dashboard dat verbinding maakt met uw IoT Central-toepassing.

> [!div class="nextstepaction"]
> [Een provider dashboard bouwen](howto-health-data-triage.md)
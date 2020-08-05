---
title: Een app voor continue patiëntbewaking maken met Azure IoT Central | Microsoft Docs
description: Leer hoe u een toepassing voor continue patiëntbewaking maakt met een Azure IoT Central-toepassingssjabloon.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 704c56745ad89e9ed2f79e8a863f1d0bc9845bf9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001822"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Zelfstudie: Een sjabloon voor het controleren van een continue patiëntbewaking implementeren en bewaken



In deze zelfstudie ziet u, als een bouwer van oplossingen, hoe u aan de slag gaat met het implementeren van een IoT Cental-toepassingssjabloon voor continue patiëntbewaking. U leert hoe u de sjabloon implementeert, wat is opgenomen uit het vak en wat u nu kunt doen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toepassingssjabloon maken
> * De toepassingssjabloon doorlopen

## <a name="create-an-application-template"></a>Een toepassingssjabloon maken

Navigeer naar de [​​Toepassingsbeheerwebsite van Azure IoT Central](https://apps.azureiotcentral.com/). Selecteer **Bouwen** in de navigatie balk aan de linkerkant en klik vervolgens op het tabblad **Gezondheidszorg**. 

>[!div class="mx-imgBorder"] 
>![App-beheerder Gezondheidszorg](media/app-manager-health.png)

Klik op de knop **App maken** om de toepassing te maken en meld u aan met een persoonlijk, werk- of schoolaccount van Microsoft. Hiermee gaat u naar de pagina **Nieuwe toepassing**.

![Toepassing Gezondheidszorg maken](media/app-manager-health-create.png)

![Toepassing Gezondheidszorg factureringsgegevens maken](media/app-manager-health-create-billinginfo.png)

Om uw toepassing te maken doet u het volgende:

1. In Azure IoT Central wordt automatisch een toepassingsnaam voorgesteld op basis van de sjabloon die u hebt geselecteerd. U kunt deze naam accepteren of uw eigen beschrijvende toepassingsnaam invoeren, zoals **Continue patiëntbewaking**. Azure IoT Central genereert ook een uniek URL-voorvoegsel voor u op basis van de naam van de toepassing. U kunt dit URL-voorvoegsel wijzigen in iets dat gemakkelijker te onthouden is als u dat wilt.

2. U kunt kiezen of u de toepassing wilt maken met behulp van het *gratis* abonnement of een van de *standaard*-abonnementen. Toepassingen die u maakt met het gratis abonnement zijn gratis gedurende zeven dagen voordat ze verlopen en ondersteunen maximaal vijf gratis apparaten. U kunt een toepassing op elk moment van het gratis abonnement naar een standaard-abonnement verplaatsen voordat het verloopt. Als u kiest voor het gratis abonnement, moet u uw contactgegevens invoeren en kiezen of u informatie en tips van Microsoft wilt ontvangen. Toepassingen die u maakt met behulp van een standaard-abonnement ondersteunen maximaal twee gratis apparaten en u moet uw Azure-abonnementsgegevens invoeren voor facturering.

3. Selecteer **Maken** onder aan de pagina om uw toepassing te implementeren.

## <a name="walk-through-the-application-template"></a>De toepassingssjabloon doorlopen

### <a name="dashboards"></a>Dashboards

Nadat u de app-sjabloon hebt geïmplementeerd, gaat u eerst naar het **Lamna-dashboard voor controleren van opgenomen patiënten**. Lamna Gezondheidszorg is een fictief ziekenhuis systeem dat twee ziekenhuizen bevat: Ziekenhuis Woodgrove en Ziekenhuis Burkville. Op dit operatordashboard voor Ziekenhuis Woodgrove ziet u informatie en telemetrie over de apparaten in deze sjabloon, samen met een set opdrachten, taken en acties die u kunt uitvoeren. Vanuit het dashboard kunt u het volgende doen:

* Telemetrie en eigenschappen van apparaten zien, zoals het **accuniveau** van het apparaat of de **connectiviteitsstatus**.

* Bekijk de **plattegrond** en de locatie van het Smart Vitals Patch-apparaat.

* De Smart Vitals Patch **opnieuw inrichten** voor een nieuwe patiënt.

* Bekijk een voorbeeld van een **provider-dashboard** dat een zorgteam in een ziekenhuis kan zien om hun patiënten te volgen.

* Wijzig de **patiëntstatus** van het apparaat om aan te geven of het apparaat wordt gebruikt voor een extern scenario of een scenario met een opgenomen patiënt.

>[!div class="mx-imgBorder"] 
>![Lamna opgenomen patiënt](media/lamna-in-patient.png)

U kunt ook klikken op **Ga naar het dashboard voor externe patiënten** om het tweede operatordashboard weer te geven dat wordt gebruikt voor Ziekenhuis Burkville. Dit dashboard bevat een soortgelijke set acties, telemetrie en informatie. Daarnaast ziet u dat er meerdere apparaten worden gebruikt en de mogelijkheid hebben om **de firmware bij te werken** op elk systeem.

>[!div class="mx-imgBorder"] 
>![Lamna extern](media/lamna-remote.png)

Op beide dashboards kunt u altijd een koppeling naar deze documentatie maken.

### <a name="device-templates"></a>Apparaatsjablonen

Als u op het tabblad **Apparaatsjablonen** klikt, ziet u dat er twee verschillende typen apparaten zijn die deel uitmaken van de sjabloon:

* **Smart Vitals Patch**: Dit apparaat vertegenwoordigt een patch die verschillende soorten vitale functies meet. Het kan worden gebruikt voor het controleren van patiënten in en buiten het ziekenhuis. Als u op de sjabloon klikt, ziet u dat naast het verzenden van apparaatgegevens, zoals het accuniveau en de temperatuur van het apparaat, de patch ook de statusgegevens van patiënten, zoals het ademhalingstempo en de bloeddruk, verzendt.

* **Smart Knee Brace**: Dit apparaat vertegenwoordigt een kniebrace die patiënten mogelijk gebruiken bij het herstellen van een knievervangende operatie. Als u op deze sjabloon klikt, worden de mogelijkheden, zoals het aantal bewegingen en de versnelling, weergegeven naast de apparaatgegevens.

>[!div class="mx-imgBorder"] 
>![Smart Vitals Patch-apparaatsjabloon](media/smart-vitals-device-template.png)

### <a name="device-groups"></a>Apparaatgroepen 
Met apparaatgroepen kunt u een set apparaten logisch groeperen om vervolgens bulksgewijs query's of bewerkingen uit te voeren. 

Als u op het tabblad apparaatgroepen klikt, ziet u dat er een aantal standaard apparaatgroepen is gemaakt voor elk van de apparaatsjablonen in de toepassing. U zult merken dat we ook twee extra voorbeelden van apparaatgroepen hebben gemaakt met de naam 'Apparaten inrichten' en 'Apparaten met verouderde firmware.' Deze voorbeeldapparaatgroepen worden gebruikt als invoer voor het uitvoeren van enkele [Taken](#jobs).

### <a name="rules"></a>Regels

Wanneer u naar het tabblad regels gaat, ziet u drie regels die voorkomen in de toepassingssjabloon:

* **Hoogste temperatuur brace**: Deze regel wordt geactiveerd wanneer de apparaatstemperatuur van de Smart Knee Brace groter is dan 95&deg;F over een periode van vijf minuten. U kunt deze regel gebruiken om het patiënten- en zorgteam te waarschuwen en het apparaat op afstand af te koelen.

* **Val gedetecteerd**: Deze regel wordt geactiveerd als er wordt gedetecteerd dat een patiënt is gevallen. U kunt deze regel gebruiken om een actie te configureren voor het implementeren van een operationeel team om de patiënt te helpen die zijn gevallen.

* **Accu van patch laag**: Deze regel wordt geactiveerd wanneer het accuniveau op het apparaat lager is dan 10%. U kunt deze regel gebruiken om een melding naar de patiënt te activeren om het apparaat op te laden.

>[!div class="mx-imgBorder"] 
>![Hoogste temperatuur brace regel](media/brace-temp-rule.png)

### <a name="jobs"></a>Taken

Met taken kunt u bulkbewerkingen uitvoeren op een set apparaten, met behulp van [apparaatgroepen](#device-groups) als invoer. We hebben de toepassingssjabloon uitgerust met twee voorbeeldtaken die een oplossingsoperator mogelijk moet uitvoeren op een bepaald moment van de levenscyclus van apparaten:
* **Firmware van kniebrace bijwerken**: Met deze taak vindt u apparaten in de apparaatgroep 'apparaten met verouderde firmware' en voert u een opdracht uit om die apparaten bij te werken naar de meest recente firmwareversie van de kniebrace. In deze voorbeeldtaak wordt ervan uitgegaan dat de apparaten de mogelijkheid hebben om een bijwerkopdracht te ontvangen en de firmwarebestanden rechtstreeks op te halen uit de cloud.  

* **Apparaten opnieuw inrichten**: Als u een set apparaten hebt die recent zijn teruggestuurd naar het ziekenhuis en opnieuw moeten worden ingericht voor de volgende set patiënten, kunt u deze taak uitvoeren om uw inrichtingsapparaten bulksgewijs bij te werken. In dit geval nemen we alle apparaten van een apparaatgroep met de naam 'apparaten inrichten' op en voeren we een opdracht uit om ze te kunnen inrichten. 

### <a name="devices"></a>Apparaten

Klik op het tabblad **Apparaten** en selecteer vervolgens een exemplaar van de **Smart Knee Brace**. U ziet dat er drie weergaven zijn om informatie over het geselecteerde apparaat dat u hebt geselecteerd, te verkennen. Deze weergaven worden gemaakt en gepubliceerd bij het bouwen van de apparaatsjabloon voor uw apparaat. Dit betekent dat ze consistent zijn op alle apparaten die u verbindt of simuleert.

De weergave **Dashboard** bevat een overzicht van de telemetrie en eigenschappen die afkomstig zijn van het apparaat waarop de operator georiënteerd is.

Op het tabblad **Eigenschappen** kunt u eigenschappen van de cloud bewerken en apparaateigenschappen lezen/schrijven.

Met het tabblad **Opdrachten** tabblad kunt u opdrachten uitvoeren die zijn gemodelleerd als onderdeel van de sjabloon voor het apparaat.

>[!div class="mx-imgBorder"] 
>![Weergaven voor kniebrace](media/knee-brace-dashboard.png)

### <a name="data-export"></a>Gegevensexport

Met gegevensexport kunt u de gegevens van uw IoT Central-apparaat continu exporteren naar andere Azure-Services, waaronder de [Azure API for FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir).

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u de toepassing door **Beheer > Toepassingsinstellingen** te bezoeken en klikt u op **Verwijderen**.

>[!div class="mx-imgBorder"] 
>![App verwijderen](media/admin-delete.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het maken van een provider-dashboard dat verbinding maakt met uw IoT Central-toepassing.

> [!div class="nextstepaction"]
> [Een provider-dashboard bouwen](howto-health-data-triage.md)
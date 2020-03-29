---
title: De Connected Factory-oplossing aanpassen - Azure | Microsoft Documenten
description: Een beschrijving van hoe u het gedrag van de Connected Factory-oplossingsversneller aanpassen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 6062f8b3992732e0e0f9bbdae9549e69c393f4ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67080491"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Aanpassen hoe de Connected Factory-oplossing gegevens van uw OPC UA-servers weergeeft

De Connected Factory-oplossing verzamelt en toont gegevens van de OPC UA-servers die met de oplossing zijn verbonden. U in uw oplossing door de OPC UA-servers bladeren en opdrachten verzenden. Zie de [veelgestelde vragen](iot-accelerators-faq-cf.md)over verbonden fabriek voor meer informatie over OPC UA.

Voorbeelden van geaggregeerde gegevens in de oplossing zijn de Algemene Apparatuurefficiëntie (OEE) en Key Performance Indicators (KPI's) die u in het dashboard op de fabrieks-, lijn- en stationsniveaus bekijken. De volgende screenshot toont de OEE en KPI waarden voor de **Assemblage** station, op **Productielijn 1**, in de fabriek in **München:**

![Voorbeeld van OEE- en KPI-waarden in de oplossing][img-oee-kpi]

Met de oplossing u gedetailleerde informatie bekijken van specifieke gegevensitems van de OPC UA-servers, *stations*genaamd. De volgende schermafbeelding toont plots van het aantal gefabriceerde items van een specifiek station:

![Percelen van het aantal vervaardigde artikelen][img-manufactured-items]

Als u op een van de grafieken klikt, u de gegevens verder verkennen met behulp van Time Series Insights (TSI):

![Gegevens verkennen met behulp van inzichten uit de time-serie][img-tsi]

In dit artikel wordt beschreven:

- Hoe de gegevens beschikbaar worden gesteld aan de verschillende standpunten in de oplossing.
- Hoe u de manier waarop de oplossing de gegevens weergeeft, aanpassen.

## <a name="data-sources"></a>Gegevensbronnen

De Connected Factory-oplossing geeft gegevens weer van de OPC UA-servers die met de oplossing zijn verbonden. De standaardinstallatie omvat verschillende OPC UA-servers die een fabriekssimulatie uitvoeren. U uw eigen OPC UA-servers die [verbinding maken via een gateway][lnk-connect-cf] toevoegen aan uw oplossing.

U bladeren door de gegevensitems die een verbonden OPC UA-server naar uw oplossing in het dashboard kan verzenden:

1. Kies **Browser** om naar de **OPC UA-serverweergave** selecteren:

    ![Navigeren naar de opc-serverweergave selecteren][img-select-server]

1. Selecteer een server en klik op **Verbinding maken**. Klik **op Doorgaan** wanneer de beveiligingswaarschuwing wordt weergegeven.

    > [!NOTE]
    > Deze waarschuwing wordt slechts één keer voor elke server weergegeven en vormt een vertrouwensrelatie tussen het oplossingsdashboard en de server.

1. U nu bladeren door de gegevensitems die de server naar de oplossing kan verzenden. Items die naar de oplossing worden verzonden, hebben een vinkje:

    ![Gepubliceerde objecten][img-published]

1. Als u *een beheerder in* de oplossing bent, u ervoor kiezen een gegevensitem te publiceren om het beschikbaar te maken in de Connected Factory-oplossing. Als beheerder u ook de waarde van gegevensitems en aanroepmethoden in de OPC UA-server wijzigen.

## <a name="map-the-data"></a>De gegevens in kaart brengen

De Connected Factory-oplossing brengt de gepubliceerde gegevensitems van de OPC UA-server in kaart en aggregaat naar de verschillende weergaven in de oplossing. De Connected Factory-oplossing wordt geïmplementeerd in uw Azure-account wanneer u de oplossing indient. Een JSON-bestand in de Visual Studio Connected Factory-oplossing slaat deze kaartinformatie op. U dit JSON-configuratiebestand bekijken en wijzigen in de Connected Factory Visual Studio-oplossing. U de oplossing opnieuw implementeren nadat u een wijziging hebt gewijzigd.

U het configuratiebestand gebruiken om:

- Bewerk de bestaande gesimuleerde fabrieken, productielijnen en stations.
- Kaart gegevens van echte OPC UA-servers die u met de oplossing maakt.

Zie Hoe u [de oplossingsversneller van Connected Factory configureert ](iot-accelerators-connected-factory-configure.md)voor meer informatie over het toewijzen en aggregeren van de gegevens om aan uw specifieke vereisten te voldoen.

## <a name="deploy-the-changes"></a>De wijzigingen implementeren

Wanneer u klaar bent met het aanbrengen van wijzigingen in het bestand **ContosoTopologyDescription.json,** moet u de oplossing Verbonden fabriek opnieuw implementeren in uw Azure-account.

De **azure-iot-connected-factory** repository bevat een **build.ps1** PowerShell script dat u gebruiken om de oplossing te herbouwen en te implementeren.

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen over de Connected Factory-oplossingsversneller:

* [Machtigingen op de site van de azureiotsolutions.com][lnk-permissions]
* [Veelgestelde vragen over verbonden fabriek](iot-accelerators-faq-cf.md)
* [Veelgestelde vragen][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md
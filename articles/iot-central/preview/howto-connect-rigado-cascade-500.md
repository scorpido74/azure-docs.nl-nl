---
title: Verbinding maken met een Rigado Cascade 500 in azure IoT Central | Microsoft Docs
description: Meer informatie over hoe u een Rigado trapsgewijs 500-gateway apparaat verbindt met uw IoT Central-toepassing.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 164fc4eef245a2b9ea610179c3cf2e78b7b1b389
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895688"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Een Rigado-trapsgewijs 500-gateway apparaat verbinden met uw Azure IoT Central-toepassing


In dit artikel wordt beschreven hoe u als opbouw functie voor oplossingen een Rigado Cascade 500-gateway apparaat kunt verbinden met uw Microsoft Azure IoT Central toepassing. 

## <a name="what-is-cascade-500"></a>Wat is trapsgewijs 500?

Cascade 500 IoT-gateway is een hardware-aanbieding van Rigado die deel uitmaakt van hun trapsgewijze Edge-as-a-service-oplossing. Het biedt commerciële IoT-project-en product teams met flexibele Edge computing kracht, een robuuste, container toepassings omgeving en een groot aantal connectiviteits opties voor draadloze apparaten, waaronder Bluetooth 5, LTE, & Wi-Fi.

Cascade 500 is vooraf gecertificeerd voor Azure IoT Plug en Play (PnP) zodat onze oplossingen bouwen het apparaat eenvoudig kunnen opheffen in hun end-to-end-oplossingen. Met de trapsgewijze gateway kunt u draadloos verbinding maken met een aantal Sens oren die op het gateway apparaat nabij zijn. Deze Sens oren kunnen worden geboardd naar IoT Central via het gateway apparaat.

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u de volgende resources nodig:

* Een Rigado-trapsgewijs 500-apparaat. Ga naar [Rigado](https://www.rigado.com/)voor meer informatie.
* Een Azure IoT Central-toepassing die is gemaakt op basis van een van de preview-toepassings sjablonen. Zie voor meer informatie de [nieuwe toepassing maken](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Een apparaatsjabloon toevoegen

Als u een trapsgewijs 500-gateway apparaat wilt toevoegen aan uw Azure IoT Central-toepassings exemplaar, moet u een bijbehorende apparaatprofiel in uw toepassing configureren.

Een sjabloon voor een trapsgewijs 500-apparaat toevoegen: 

1. Ga naar het tabblad ***device templates*** in het linkerdeel venster en selecteer **+ Nieuw**: ![nieuwe sjabloon voor het apparaat maken](./media/howto-connect-rigado-cascade500/device-template-new.png)
1. De pagina bevat een optie voor het ***maken van een aangepaste sjabloon*** of ***het gebruiken van een vooraf geconfigureerde apparaatprofiel***
1. Selecteer de sjabloon C500 in de lijst met vooraf geconfigureerde Apparaatinstellingen, zoals hieronder wordt weer gegeven: ![Selecteer C500 Device Temp late](./media/howto-connect-rigado-cascade500/device-template-preconfigured.png)
1. Selecteer ***volgende: aanpassen*** om door te gaan naar de volgende stap. 
1. Selecteer in het volgende scherm de optie ***maken*** om de C500-apparaatprofiel voor te bereiden in uw IOT Central-toepassing.

## <a name="retrieve-application-connection-details"></a>Verbindings Details van toepassing ophalen

U moet nu de **scope-id** en de **primaire sleutel** voor uw Azure IOT Central-toepassing ophalen om verbinding te kunnen maken met het trapsgewijs 500-apparaat. 

1. Ga in het linkerdeel venster naar **beheer** en klik op **verbinding met apparaat**. 
2. Noteer de **bereik-id** voor uw IOT Central-toepassing.
![app-bereik-ID](./media/howto-connect-rigado-cascade500/app-scope-id.png)
3. Klik nu op **sleutels weer geven** en noteer de **primaire sleutel**
![primaire sleutel](./media/howto-connect-rigado-cascade500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Contact opnemen met Rigado om verbinding te maken met de gateway 

Als u het trapsgewijze 500-apparaat wilt verbinden met uw IoT Central-toepassing, moet u contact opnemen met Rigado en de details van de toepassings verbinding opgeven in de bovenstaande stappen. 

Zodra het apparaat is verbonden met internet, kan Rigado via een beveiligd kanaal een configuratie-update naar het trapsgewijs 500-gateway apparaat pushen. 

Met deze update worden de IoT Central verbindings Details toegepast op het trapsgewijs 500-apparaat en wordt het weer gegeven in de lijst met apparaten. 

![Primaire sleutel](./media/howto-connect-rigado-cascade500/devices-list-c500.png)  

U bent nu klaar om uw C500-apparaat in uw IoT Central-toepassing te gebruiken!

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een Rigado-trapsgewijs 500 verbindt met uw Azure IoT Central-toepassing, is de voorgestelde volgende stap informatie over het [maken van een in-Store Analytics-toepassing](../retail/tutorial-in-store-analytics-create-app-pnp.md) om een end-to-end-oplossing te bouwen. 
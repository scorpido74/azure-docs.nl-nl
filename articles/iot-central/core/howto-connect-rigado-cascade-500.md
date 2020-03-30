---
title: Een Rigado Cascade 500 aansluiten in Azure IoT Central | Microsoft Documenten
description: Meer informatie over het aansluiten van een Rigado Cascade 500-gateway-apparaat op uw IoT Central-toepassing.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: d52366684d772f91b53a1ab385b51ae4f11f0a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158338"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Een Rigado Cascade 500-gatewayapparaat verbinden met uw Azure IoT Central-toepassing


In dit artikel wordt beschreven hoe u als oplossingsbouwer een Rigado Cascade 500-gateway-apparaat verbinden met uw Microsoft Azure IoT Central-toepassing. 

## <a name="what-is-cascade-500"></a>Wat is Cascade 500?

Cascade 500 IoT-gateway is een hardwareaanbod van Rigado dat is opgenomen als onderdeel van hun Cascade Edge-as-a-Service-oplossing. Het biedt commerciële IoT-project- en productteams flexibele edge computing-kracht, een robuuste containeromgeving en een breed scala aan opties voor draadloze apparaatconnectiviteit, waaronder Bluetooth 5, LTE, & Wi-Fi.

Cascade 500 is vooraf gecertificeerd voor Azure IoT Plug and Play (preview), zodat onze oplossingsbouwers eenvoudig aan boord kunnen gaan van het apparaat in hun end-to-end-oplossingen. Met de Cascade-gateway u draadloos verbinding maken met verschillende sensoren voor conditiebewaking die zich in de nabijheid van het gateway-apparaat bevinden. Deze sensoren kunnen via het gateway-apparaat in IoT Central worden aangesloten.

## <a name="prerequisites"></a>Vereisten
Als u deze handleiding wilt doorlopen, hebt u de volgende bronnen nodig:

* Een Rigado Cascade 500 apparaat. Voor meer informatie u terecht op [Rigado](https://www.rigado.com/).
* Een Azure IoT Central-toepassing. Zie het maken [van een nieuwe toepassing voor](./quick-deploy-iot-central.md)meer informatie.

## <a name="add-a-device-template"></a>Een apparaatsjabloon toevoegen

Als u een Cascade 500-gatewayapparaat wilt inbouwen in uw Azure IoT Central-toepassingsexemplaar, moet u een bijbehorende apparaatsjabloon in uw toepassing configureren.

Ga als lid van de knop om een trapsgewijze 500-apparaatsjabloon toe te voegen: 

1. Navigeer naar het tabblad ***Apparaatsjablonen*** in het ![linkerdeelvenster en selecteer **+ Nieuw:** Nieuwe apparaatsjabloon maken](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. De pagina geeft u de optie om ***een aangepaste sjabloon*** te maken of een vooraf ***geconfigureerde apparaatsjabloon te gebruiken***
1. Selecteer de c500-apparaatsjabloon in de lijst met vooraf ![geconfigureerde apparaatsjablonen zoals hieronder weergegeven: C500-apparaatsjabloon selecteren](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Selecteer ***Volgende: Aanpassen*** om door te gaan naar de volgende stap. 
1. Selecteer in het volgende scherm ***Maken*** om de c500-apparaatsjabloon aan boord te nemen in uw IoT Central-toepassing.

## <a name="retrieve-application-connection-details"></a>Details van toepassingsverbinding ophalen

U moet nu de **scope-id** en **primaire sleutel** voor uw Azure IoT Central-toepassing ophalen om het Cascade 500-apparaat aan te sluiten. 

1. Navigeer naar **Beheer** in het linkerdeelvenster en klik op **Apparaatverbinding**. 
2. Noteer de **Scope ID** voor uw IoT Central-toepassing.
![App-scope-id](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Klik nu op **Toetsen weergeven** en noteer de primaire toets primaire toets van **de primaire toets**
![](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Neem contact op met Rigado om de gateway aan te sluiten 

Om het Cascade 500-apparaat aan te sluiten op uw IoT Central-toepassing, moet u contact opnemen met Rigado en hen de details van de toepassingsverbinding van de bovenstaande stappen verstrekken. 

Zodra het apparaat is aangesloten op het internet, rigado in staat zal zijn om naar beneden te duwen een configuratie-update naar beneden naar de Cascade 500 gateway-apparaat via een veilig kanaal. 

Deze update past de iot-centrale verbindingsgegevens toe op het Cascade 500-apparaat en wordt weergegeven in de lijst met apparaten. 

![Primaire sleutel](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

U bent nu klaar om uw C500-apparaat te gebruiken in uw IoT Central-toepassing!

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een Rigado Cascade 500 aansluiten op uw Azure IoT Central-toepassing, is de voorgestelde volgende stap om te leren hoe u [een in-store analytics-toepassing maken](../retail/tutorial-in-store-analytics-create-app-pnp.md) om een end-to-end-oplossing te bouwen. 
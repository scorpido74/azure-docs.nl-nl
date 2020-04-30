---
title: 'Quick Start: een beveiligings module maken, twee'
description: In deze Quick Start leert u hoe u een Azure Security Center maakt voor IoT-module, die u kunt gebruiken met Azure Security Center voor IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: e9c4470ea16f1840274a6fa8613822b20d6772b3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310864"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Quick Start: een azureiotsecurity-module maken dubbele

In deze Quick Start wordt uitgelegd hoe u een afzonderlijke _azureiotsecurity_ -module maakt voor nieuwe apparaten of een batch module apparaatdubbels maakt voor alle apparaten in een IOT hub.

## <a name="understanding-azureiotsecurity-module-twins"></a>Informatie over azureiotsecurity-module apparaatdubbels

Voor IoT-oplossingen die zijn ingebouwd in azure, speelt apparaatdubbels een belang rijke rol in Apparaatbeheer en proces automatisering.

Azure Security Center voor IoT biedt volledige integratie met uw bestaande IoT Device Management-platform, zodat u de beveiligings status van uw apparaat kunt beheren en ook de mogelijkheden van bestaande apparaten beheert.
Azure Security Center voor IoT-integratie wordt bereikt door gebruik te maken van het IoT Hub dubbele mechanisme.

Zie [IOT hub module apparaatdubbels](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) voor meer informatie over het algemene concept van module Apparaatdubbels in azure IOT hub.

Azure Security Center voor IoT maakt gebruik van het dubbel mechanisme van de module en onderhoudt een beveiligings module met de naam _azureiotsecurity_ voor elk van uw apparaten.

De beveiligings module heeft betrekking op alle informatie die relevant is voor de beveiliging van apparaten voor elk apparaat.

Als u optimaal gebruik wilt maken van Azure Security Center voor IoT-functies, moet u deze beveiligings module apparaatdubbels voor elk apparaat in de service maken, configureren en gebruiken.

## <a name="create-azureiotsecurity-module-twin"></a>Azureiotsecurity-module maken dubbele

_azureiotsecurity_ module apparaatdubbels kan op twee manieren worden gemaakt:

1. [Module batch script](https://aka.ms/iot-security-github-create-module) : maakt automatisch een module voor nieuwe apparaten of apparaten zonder een module die de standaard configuratie gebruikt.
1. Hand matig bewerken van elke module dubbele afzonderlijk met specifieke configuraties voor elk apparaat.

>[!NOTE]
> Als u de batch-methode gebruikt, wordt de bestaande azureiotsecurity-module apparaatdubbels niet overschreven. Met de batch-methode maakt u alleen nieuwe module apparaatdubbels voor apparaten die nog geen beveiligings module hebben.

Zie [agent configuratie](how-to-agent-configuration.md) voor meer informatie over het wijzigen of wijzigen van de configuratie van een bestaande module, twee.

Gebruik de volgende instructies om hand matig een nieuwe _azureiotsecurity_ -module te maken voor een apparaat:

1. Zoek en selecteer in uw IoT Hub het apparaat waarvoor u een beveiligings module wilt maken.
1. Klik op uw apparaat en vervolgens op **module identiteit toevoegen**.
1. Voer **azureiotsecurity**in het veld **naam van module-id** in.

1. Klik op **Opslaan**.

## <a name="verify-creation-of-a-module-twin"></a>Het maken van een module twee controleren

Controleren of er sprake is van een beveiligings module voor een specifiek apparaat:

1. Selecteer in uw Azure IoT Hub **IOT-apparaten** in het menu **Explorers** .
1. Voer de apparaat-ID in of selecteer een optie in het **veld query apparaat** en klik op **query apparaten**.
    ![Apparaten opvragen](./media/quickstart/verify-security-module-twin.png)
1. Selecteer het apparaat of dubbel klik erop om de pagina met details van het apparaat te openen.
1. Selecteer het menu **module-identiteiten** en bevestig dat de module **azureiotsecurity** aanwezig is in de lijst met module-identiteiten die zijn gekoppeld aan het apparaat.
    ![Modules die zijn gekoppeld aan een apparaat](./media/quickstart/verify-security-module-twin-3.png)

Zie [agent configuratie](how-to-agent-configuration.md)voor meer informatie over het aanpassen van eigenschappen van Azure Security Center voor IOT-module apparaatdubbels.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het configureren van aangepaste waarschuwingen...

> [!div class="nextstepaction"]
> [Aangepaste waarschuwingen configureren](quickstart-create-custom-alerts.md)

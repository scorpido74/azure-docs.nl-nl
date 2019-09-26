---
title: Azure Security Center inschakelen voor IoT-service in IoT Hub | Microsoft Docs
description: Meer informatie over het inschakelen van Azure Security Center voor IoT-service in uw IoT Hub.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3d9c5352a90d5bcacbaf27b7b62be61fc404e87a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299493"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Quickstart: Onboard Azure Security Center voor IoT-service in IoT Hub

Dit artikel bevat een uitleg over het inschakelen van de Azure Security Center voor IoT-service op uw bestaande IoT Hub. Als u momenteel geen IoT Hub hebt, raadpleegt u [een IOT hub maken met behulp van de Azure Portal](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) om aan de slag te gaan. 

> [!NOTE]
> Azure Security Center voor IoT biedt momenteel alleen ondersteuning voor IoT-hubs met Standard-laag.
> Azure Security Center voor IoT is een oplossing voor één hub. Als u meerdere hubs nodig hebt, zijn er meerdere Azure Security Center voor IoT-oplossingen vereist. 

## <a name="prerequisites-for-enabling-the-service"></a>Vereisten voor het inschakelen van de service

- Log Analytics-werkruimte
  - Twee soorten informatie worden standaard opgeslagen in uw Log Analytics-werk ruimte door Azure Security Center voor IoT. **beveiligings waarschuwingen** en **aanbevelingen**. 
  - U kunt ervoor kiezen om opslag ruimte toe te voegen voor een extra gegevens type, **onbewerkte gebeurtenissen**. Houd er rekening mee dat bij het opslaan van **onbewerkte gebeurtenissen** in log Analytics extra opslag kosten worden uitgevoerd. 
- IoT Hub (Standard-laag)
- Aan alle [service vereisten](service-prerequisites.md) voldoen 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Azure Security Center voor IoT op uw IoT Hub inschakelen 

Ga als volgt te werk om beveiliging in te scha kelen op uw IoT Hub: 

1. Open uw **IOT hub** in azure Portal. 
1. Klik in het menu **beveiliging** op **uw IOT-oplossing beveiligen**
1. Schakel het selectie vakje inschakelen als standaard **in** . 
1. Selecteer uw log Analytics-werk ruimte.
1. Geef uw Log Analytics werkruimte Details op. 
   - Kies voor het inschakelen van een **dubbele verzameling** door de **dubbele verzameling** **in-/uitschakelen te**laten staan.
   - Kies voor het opslaan van **onbewerkte gebeurtenissen** naast de standaard gegevens typen opslag door de **beveiligings gebeurtenissen voor onbewerkte apparaten** in log Analytics op te slaan. Wijzig de **Onbewerkte gebeurtenis** **in**-/uitschakelen. 
    
1. Klik op **Opslaan**. 

Gefeliciteerd! Het inschakelen van Azure Security Center voor IoT op uw IoT Hub is voltooid. 

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om uw oplossing te configureren...

> [!div class="nextstepaction"]
> [Uw oplossing configureren](quickstart-configure-your-solution.md)



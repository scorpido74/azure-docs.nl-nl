---
title: Onboarded Sens oren weer geven en beheren in azure Defender voor IoT
description: In dit artikel wordt beschreven hoe u uitbestede Sens oren kunt weer geven en verwijderen, evenals nieuwe activerings bestanden voor voorbereide Sens oren in azure Defender voor IoT.
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6aec19d413e1730bc1599f6cbac1c62e9b304ecd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094403"
---
# <a name="view-and-manage-onboarded-sensors"></a>Onboarded Sens oren weer geven en beheren

In dit artikel wordt beschreven hoe u onboarded Sens oren kunt weer geven en verwijderen, en hoe u nieuwe activerings bestanden voor voorbereide Sens oren downloadt.

## <a name="update-sensor-management-mode"></a>Sensor beheer modus bijwerken

U kunt de modus die uw sensor beheert, bijwerken. Wanneer u dit doet, moet u de huidige sensor verwijderen van de pagina sensor management en een nieuw activerings bestand uploaden.

- **Werk in de in de Cloud beheerde modus in plaats van de lokaal beheerde modus**: werk het activerings bestand voor uw lokaal beheerde sensor bij met een activerings bestand voor een door de Cloud beheerde sensor. Na opnieuw activeren worden sensor detecties weer gegeven in zowel de sensor als Azure Defender voor IoT-Portal. Nadat het reactiverings bestand is geüpload, worden de nieuwe gedetecteerde waarschuwings gegevens verzonden naar Azure.

- **Werk in de lokaal beheerde modus in plaats van de in de Cloud beheerde modus**: werk het activerings bestand voor een door de Cloud beheerde sensor bij met een activerings bestand voor een lokaal beheerde sensor. Na opnieuw activeren worden sensor detectie gegevens alleen weer gegeven in de sensor.

- **Koppel de sensor aan een nieuwe IOT hub**: u kunt uw sensor koppelen aan een nieuwe IOT hub. U doet dit door opnieuw te registreren en vervolgens een nieuw activerings bestand te uploaden.

**De sensor opnieuw activeren:**

1. Ga naar de pagina Azure Defender voor IoT, **sensor Management** .

2. Selecteer de sensor waarvoor u een nieuw activerings bestand wilt uploaden.

3. Deze verwijderen.

4. Onboarding van de sensor opnieuw vanaf de pagina **onboarding** in de nieuwe modus of met een nieuwe IOT hub.

5. Down load het activerings bestand op de pagina **activerings bestand downloaden** .

6. Meld u aan bij de Azure Defender voor IoT-sensor console.

7. Selecteer in de sensor console de optie **systeem instellingen** en selecteer vervolgens opnieuw **activeren**.

   ![Scherm opname van weer gave opnieuw activeren](media/updates/image14.png)

8. Selecteer **uploaden** en selecteer het bestand dat u hebt opgeslagen.

9. Selecteer **Activate**.
 
## <a name="delete-sensors"></a>Sens oren verwijderen

Als u een door de Cloud beheerde sensor verwijdert, worden er geen gegevens verzonden naar de IoT Hub.

Verwijder lokaal beheerde Sens oren wanneer u niet meer werkt.

**Sens oren verwijderen:**

1. Ga naar de beheer pagina van de Azure Defender voor IoT- **sensor** .

2. Selecteer de Sens oren die u wilt verwijderen.

3. Selecteer **sensor verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over configuratie opties gaat u naar de hand leiding voor het configureren van de module.
> [!div class="nextstepaction"]
> [Hand leiding voor module configuratie](./how-to-agent-configuration.md)

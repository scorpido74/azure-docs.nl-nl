---
title: Werk stromen bouwen met de IoT Central-connector in Azure Logic Apps | Microsoft Docs
description: Gebruik de IoT Central-connector in Azure Logic Apps om werk stromen te activeren en apparaten in werk stromen te maken, bij te werken en te verwijderen.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 2269ede83ad64172e316a208871ab65053b2d10e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951586"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Werk stromen bouwen met de IoT Central-connector in Azure Logic Apps

*Dit onderwerp is van toepassing op bouwers en beheerders.*

Gebruik Azure Logic Apps om geautomatiseerde schaal bare werk stromen te bouwen die apps en gegevens integreren in Cloud Services en on-premises systemen. Azure Logic Apps heeft veel connectors in veel Azure-Services, micro soft-Services en andere SaaS-producten (Software-as-A-Service). Met behulp van de IoT Central-connector in Azure Logic Apps kunt u werk stromen activeren wanneer een regel wordt geactiveerd in IoT Central. U kunt ook de acties in de IoT Central-connector gebruiken om een apparaat te maken, de eigenschappen en instellingen van het apparaat bij te werken of een apparaat te verwijderen.

U kunt de IoT Central-connector gebruiken in Microsoft Flow. Zowel Azure Logic Apps als Microsoft Flow zijn ontwerper-eerste integratie Services, maar doel gericht op verschillende doel groepen. Flow biedt een wille keurige Office-werk stroom voor het bouwen van de zakelijke werk stromen die ze nodig hebben. Logic Apps biedt IT-professionals de mogelijkheid om de integraties te bouwen die ze nodig hebben om gegevens te verbinden. Vergelijk [hier](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs)de stroom en Logic apps. Meer informatie over het maken van werk stromen met de IoT Central-connector in Microsoft Flow [hier](howto-add-microsoft-flow.md).

## <a name="prerequisites"></a>Vereisten

- Een betalen naar gebruik-toepassing
- Een Azure-account en-abonnement voor het maken en beheren van logische apps

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Een werk stroom activeren wanneer een regel wordt geactiveerd

In deze sectie wordt beschreven hoe u een bericht in micro soft teams plaatst wanneer een regel wordt geactiveerd. U kunt uw werk stroom configureren voor het gebruik van andere connectors om zaken te doen, zoals het verzenden van een gebeurtenis naar uw event hub, het maken van een nieuw Azure DevOps-werk item of het invoegen van een nieuwe rij in SQL Server.

1. Begin met [het maken van een regel in IOT Central](howto-create-telemetry-rules.md). Nadat u de regel voorwaarden hebt opgeslagen, selecteert u de **Azure Logic apps** tegel als een nieuwe actie. Selecteer **maken in azure Portal**. U wordt naar de Azure Portal geleid om een nieuwe logische app te maken. Mogelijk moet u zich aanmelden bij uw Azure-account.

1. Voer de vereiste gegevens in om een nieuwe logische app te maken. U kunt een Azure-abonnement kiezen om uw nieuwe logische app in te richten. Het hoeft niet hetzelfde abonnement te zijn als uw IoT Central-app is gemaakt in. Selecteer **Maken**.

    ![Een logische app maken in Azure Portal](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. Nadat de logische app is gemaakt, wordt u automatisch naar de Logic Apps Designer genavigeerd. Selecteer **lege logische app**. 

    ![Een lege, logische app maken](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. Zoek in de ontwerp functie naar IOT Central en kies de trigger **Wanneer een regel wordt geactiveerd** . Meld u aan bij de connector met het account waarmee u zich aanmeldt bij uw IoT Central-app met.

    ![Aanmelden bij IoT Central connector](./media/howto-build-azure-logic-apps/addtrigger.png)

1. Nadat het aanmelden is geslaagd, worden de velden weer gegeven. Selecteer de **toepassing** en **regel** in de vervolg keuzelijsten.

    ![Toepassing en regel kiezen](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Voeg een nieuwe actie toe. Zoek naar **post-bericht teams** en kies **een bericht verzenden** van de micro soft teams-connector. Meld u aan bij de connector met het account dat u gebruikt in micro soft teams.

1. Kies in de actie het **team** en het **kanaal**. Vul in het veld **bericht de tekst** in waarin elk bericht moet worden gemeld. U kunt *dynamische inhoud* van uw IOT Central regel toevoegen, waarbij u belang rijke informatie, zoals de apparaatnaam en de tijds tempel, aan uw melding door gegeven.
    > [!NOTE]
    > Selecteer in het venster dynamische inhoud **meer tekst weer geven** om meet-en eigenschaps waarden op te halen waarmee de regel wordt geactiveerd.

    ![Bewerkings actie logische app met dynamisch deel venster openen](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. Wanneer u klaar bent met het bewerken van uw actie, selecteert u **Opslaan**.

1. Als u teruggaat naar uw IoT Central-app, ziet u dat deze regel een Azure Logic Apps actie in het gebied acties heeft.

U kunt altijd beginnen met het bouwen van een werk stroom met behulp van de IoT Central-connector in Logic Apps in azure Portal. Vervolgens kunt u kiezen welke IoT Central-app en met welke regel u verbinding wilt maken. het werkt op dezelfde manier. U hoeft niet telkens op de pagina IoT Central regels te beginnen.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Een apparaat in een werk stroom maken, bijwerken en verwijderen

Wanneer u een werk stroom in uw logische app bouwt, kunt u een actie toevoegen met behulp van de IoT Central-connector. De beschik bare acties zijn het **maken van een apparaat**, het **bijwerken van een apparaat**en **het verwijderen van een apparaat**.

> [!NOTE]
> Voor het **bijwerken van een apparaat** en **het verwijderen van een apparaat**hebt u een id nodig van het bestaande apparaat dat u wilt bijwerken of verwijderen. U kunt de ID van het IoT Central apparaat ophalen in de **device Explorer**

![Apparaat-ID apparaat Verkenner IoT Central](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Microsoft Flow kunt gebruiken om werk stromen te maken, is de voorgestelde volgende stap het [beheren van apparaten](howto-manage-devices.md).

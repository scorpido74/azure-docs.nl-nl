---
title: De Azure IoT Central-connector gebruiken in Microsoft Flow | Microsoft Docs
description: Gebruik de IoT Central-connector in Microsoft Flow om werk stromen te activeren en apparaten te maken, op te halen, bij te werken, te verwijderen en opdrachten uit te voeren in werk stromen.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: f1912b90df75f3d85f6ba5d730c723cf42803237
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930378"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Werk stromen bouwen met de IoT Central-connector in Microsoft Flow

*Dit onderwerp is van toepassing op bouwers en beheerders.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Gebruik Microsoft Flow om werk stromen te automatiseren voor de vele toepassingen en services waarmee zakelijke gebruikers gebruikmaken. Met behulp van de IoT Central-connector in Microsoft Flow kunt u werk stromen activeren wanneer een regel wordt geactiveerd in IoT Central. In een werk stroom die wordt geactiveerd door IoT Central of een andere toepassing, kunt u de acties in de IoT Central-connector gebruiken voor het volgende:
- Een apparaat maken
- Apparaatgegevens ophalen
- De eigenschappen en instellingen van een apparaat bijwerken
- Een opdracht uitvoeren op een apparaat
- Een apparaat verwijderen

Bekijk [deze Microsoft flow sjablonen](https://aka.ms/iotcentralflowtemplates) waarmee u IOT Central kunt verbinden met andere services, zoals mobiele meldingen en micro soft-teams.

## <a name="prerequisites"></a>Vereisten

- Een betalen naar gebruik-toepassing
- Een persoonlijk of werk-of school account van micro soft om Microsoft Flow te gebruiken ([meer informatie over Microsoft flow-abonnementen](https://aka.ms/microsoftflowplans))
- Een werk-of school account voor het gebruik van de Azure IoT Central-connector

## <a name="trigger-a-workflow"></a>Een werk stroom activeren

In deze sectie wordt beschreven hoe u een mobiele melding activeert in de mobiele flow-app wanneer een regel wordt geactiveerd in IoT Central. U kunt deze volledige werk stroom binnen uw IoT Central-app maken met behulp van de Inge sloten Microsoft Flow Designer.

1. Begin met [het maken van een regel in IOT Central](howto-create-telemetry-rules.md). Nadat u de regel voorwaarden hebt opgeslagen, selecteert u de **Microsoft flow actie** als een nieuwe actie. Er wordt een dialoog venster geopend waarin u uw werk stroom kunt configureren. Het IoT Central gebruikers account waarmee u bent aangemeld, wordt gebruikt om u aan te melden bij Microsoft Flow.

    ![Een nieuwe Microsoft Flow actie maken](media/howto-add-microsoft-flow/createflowaction.png)

1. Er wordt een lijst met werk stromen weer geven waartoe u toegang hebt en die zijn gekoppeld aan deze IoT Central regel. Klik op **sjablonen verkennen** of **nieuwe > maken op basis van sjabloon** en u kunt kiezen uit een van de beschik bare sjablonen. 

    ![Beschik bare Microsoft Flow sjablonen](media/howto-add-microsoft-flow/flowtemplates1.png)

1. U wordt gevraagd om u aan te melden bij de connectors in de sjabloon die u hebt gekozen. 

    > [!NOTE]
    > Als u de Azure IoT Central-connector wilt gebruiken, moet u zich aanmelden met een Azure Active Directory-account (werk-of school account). Een persoonlijk account, zoals abc@outlook.com of abc@live.com, wordt niet ondersteund door de Azure IoT Central-connector.

    Zodra u bent aangemeld bij de connectors, gaat u in Designer om uw werk stroom te bouwen. De werk stroom bevat een IoT Central trigger waarbij uw toepassing en regel al zijn ingevuld.

1. U kunt de werk stroom aanpassen door de gegevens aan te passen die zijn door gegeven aan de actie en nieuwe acties toe te voegen. In dit voor beeld is de actie **meldingen-ik wil een mobiele melding ontvangen**. U kunt *dynamische inhoud* van uw IOT Central regel toevoegen, waarbij u belang rijke informatie, zoals de apparaatnaam en de tijds tempel, aan uw melding door gegeven.

    > [!NOTE]
    > Selecteer in het venster dynamische inhoud **meer tekst weer geven** om meet-en eigenschaps waarden op te halen waarmee de regel wordt geactiveerd.

    ![Bewerking voor stroom bewerking met dynamisch deel venster openen](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Wanneer u klaar bent met het bewerken van uw actie, selecteert u **Opslaan**. U wordt omgeleid naar de overzichts pagina van uw werk stroom. Hier kunt u de uitvoerings geschiedenis bekijken en delen met andere collega's.

    > [!NOTE]
    > Als u wilt dat andere gebruikers in uw IoT Central-app deze regel kunnen bewerken, moet u deze met hen delen in Microsoft Flow. Voeg hun Microsoft Flow-accounts toe als eigen aren in uw werk stroom.

1. Als u teruggaat naar uw IoT Central-app, ziet u dat deze regel een Microsoft Flow actie in het gebied acties heeft.

U kunt ook rechtstreeks vanuit Microsoft Flow werk stromen bouwen met behulp van de IoT Central-connector. Vervolgens kunt u kiezen met welke IoT Central-app verbinding moet worden gemaakt.

## <a name="create-a-device-in-a-workflow"></a>Een apparaat in een werk stroom maken

In deze sectie wordt beschreven hoe u een nieuw apparaat maakt in IoT Central op het pushen van een knop op een mobiel apparaat met behulp van de mobiele app van Microsoft Flow. U kunt deze actie in stroom gebruiken om ERP-systemen zoals Dynamics te integreren met IoT Central door een nieuw apparaat te maken wanneer een apparaat elders wordt toegevoegd.

1. Begin met het maken van een lege werk stroom in Microsoft Flow.

1. Zoek naar de **knop stroom voor mobiel** als een trigger.

1. Voeg in deze trigger een tekst invoer met de **naam apparaatnaam**toe. Wijzig de naam van de beschrijving om **de apparaatnaam van het nieuwe apparaat in te voeren**.

1. Voeg een nieuwe actie toe. Zoek naar de **Azure-IOT Central: een actie voor een apparaat maken** .

1. Kies uw toepassing en kies een sjabloon voor het maken van een apparaat in de vervolg keuzelijsten. U ziet de actie uitbreiden om alle eigenschappen en instellingen van het apparaat weer te geven.

1. Selecteer het veld met de naam van het apparaat. Kies **apparaatnaam**in het deel venster dynamische inhoud. Deze waarde wordt door gegeven van de invoer die de gebruiker via de mobiele app invult, en is de naam van het nieuwe apparaat in IoT Central. In dit voor beeld is het enige vereiste veld de naam van het apparaat, aangegeven door het rode sterretje. Een andere apparaatprofiel kan meerdere vereiste velden bevatten die moeten worden ingevuld om een nieuw apparaat te maken.

    ![Dynamisch deel venster stroom actie apparaat maken](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. Beschrijving Vul andere velden in zoals u ziet voor het maken van nieuwe apparaten.

1. Sla tot slot uw werk stroom op.

1. Probeer uw werk stroom in de mobiele app van Microsoft Flow. Ga naar het tabblad **knoppen** in de app. U ziet de knop-> een nieuwe werk stroom voor apparaten maken. Voer de naam van het nieuwe apparaat in en kijk hoe dit in IoT Central wordt weer gegeven.

    ![Scherm opname van mobiel apparaat voor apparaten maken](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Een apparaat bijwerken in een werk stroom

In deze sectie wordt beschreven hoe u apparaatinstellingen en eigenschappen bijwerkt in IoT Central op het pushen van een knop op een mobiel apparaat met behulp van de mobiele app van Microsoft Flow.

1. Begin met het maken van een lege werk stroom in Microsoft Flow.

1. Zoek naar de **knop stroom voor mobiel** als een trigger.

1. In deze trigger voegt u een invoer toe, zoals een **locatie** tekst invoer die overeenkomt met een instelling of eigenschap die u wilt wijzigen. Wijzig de beschrijvende tekst.

1. Voeg een nieuwe actie toe. Zoek naar de **Azure-IOT Central-een actie van een apparaat bijwerken** .

1. Kies uw toepassing in de vervolg keuzelijst. U hebt nu een ID nodig van het bestaande apparaat dat u wilt bijwerken. 

    > [!NOTE] 
    > **U moet de id in de URL gebruiken** op de pagina Details van apparaat van het apparaat dat u wilt bijwerken. De apparaat-ID die wordt weer gegeven in de lijst met apparaten van apparaat Verkenner is niet het recht om te gebruiken in Microsoft Flow.

    ![IoT Central-ID van URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. U kunt de naam van het apparaat bijwerken. Als u de eigenschappen en instellingen van het apparaat wilt bijwerken, moet u de apparaatprofiel selecteren van het apparaat dat u wilt bijwerken in de vervolg keuzelijst van de sjabloon voor het **apparaat** . De tegel actie wordt uitgebreid om alle eigenschappen en instellingen die u kunt bijwerken weer te geven.

    ![Werk stroom van stroom update apparaat](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Selecteer alle eigenschappen en instellingen die u wilt bijwerken. Kies in het deel venster dynamische inhoud de overeenkomende invoer van de trigger. In dit voor beeld wordt de locatie waarde door gegeven om de locatie-eigenschap van het apparaat bij te werken.

1. Sla tot slot uw werk stroom op.

1. Probeer uw werk stroom in de mobiele app van Microsoft Flow. Ga naar het tabblad **knoppen** in de app. U ziet de knop-> een werk stroom van een apparaat bijwerken. Voer de invoer in en controleer of het apparaat wordt bijgewerkt in IoT Central!

## <a name="get-device-information-in-a-workflow"></a>Apparaatgegevens in een werk stroom ophalen

U kunt apparaatgegevens ophalen met behulp van de **Azure-IOT Central: een actie van een apparaat ophalen** . 
> [!NOTE] 
> **U moet de id in de URL gebruiken** op de pagina Details van apparaat van het apparaat dat u wilt bijwerken. De apparaat-ID die wordt weer gegeven in de lijst met apparaten van apparaat Verkenner is niet het recht om te gebruiken in Microsoft Flow.

U kunt informatie, zoals de apparaatnaam, de naam van de apparaatnaam, waarden van eigenschappen en instellingen, ophalen om latere acties in uw werk stroom door te geven. Hier volgt een voor beeld van een werk stroom die de waarde van de eigenschap klant naam van een apparaat doorgeeft aan micro soft teams.

   ![Werk stroom apparaat stroom ophalen](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Een opdracht uitvoeren op een apparaat in een werk stroom
U kunt een opdracht uitvoeren op een apparaat dat is opgegeven met de ID met behulp van de **Azure IOT Central-een opdracht actie uitvoeren** . 

> [!NOTE] 
> **U moet de id in de URL gebruiken** op de pagina Details van apparaat van het apparaat dat u wilt bijwerken. De apparaat-ID die wordt weer gegeven in de lijst met apparaten van apparaat Verkenner is niet het recht om te gebruiken in Microsoft Flow.
    
U kunt de opdracht die moet worden uitgevoerd en de para meters van de opdracht door geven via deze actie. Hier volgt een voor beeld van een werk stroom die een opdracht voor het opnieuw opstarten van een apparaat uitvoert vanaf een knop in de mobiele app van Microsoft Flow.

   ![Werk stroom apparaat stroom ophalen](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Een apparaat in een werk stroom verwijderen

U kunt een apparaat verwijderen op basis van de ID met behulp van de actie **Azure IOT Central-een apparaat verwijderen** . 
> [!NOTE] 
> **U moet de id in de URL gebruiken** op de pagina Details van apparaat van het apparaat dat u wilt bijwerken. De apparaat-ID die wordt weer gegeven in de lijst met apparaten van apparaat Verkenner is niet het recht om te gebruiken in Microsoft Flow.

Hier volgt een voor beeld van een werk stroom waarmee een apparaat wordt verwijderd bij het pushen van een knop in de mobiele app van Microsoft Flow.

   ![Werk stroom apparaat verwijderen stroom](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen ondervindt bij het maken van een verbinding met de Azure IoT Central-connector, kunt u aan de hand van een aantal tips.

1. Persoonlijke micro soft-accounts (zoals @hotmail.com, @live.com, @outlook.com domeinen) worden op dit moment niet ondersteund. U moet een werk-of school account voor Azure Active Directory (AD) gebruiken.

2. Als u de IoT Central-connector in Microsoft Flow wilt gebruiken, moet u ten minste één keer zijn aangemeld bij de IoT Central-toepassing. Anders wordt de toepassing niet weer gegeven in de vervolg keuzelijst van de toepassing.

3. Als er een fout optreedt tijdens het gebruik van een Azure AD-account, probeert u Windows Power shell te openen en voert u de volgende Commandlets uit als beheerder.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Microsoft Flow kunt gebruiken om werk stromen te maken, is de voorgestelde volgende stap het [beheren van apparaten](howto-manage-devices.md).


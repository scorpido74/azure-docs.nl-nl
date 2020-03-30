---
title: De Azure IoT Central UI aanpassen | Microsoft Documenten
description: Het thema aanpassen en koppelingen voor uw centrale Azure IoT-toepassing helpen
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8f76f143b6c6a26b88b78e20d8d5d8ae1ae48553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157981"
---
# <a name="customize-the-azure-iot-central-ui"></a>De Azure IoT Central-gebruikersinterface aanpassen

In dit artikel wordt beschreven hoe u als beheerder de gebruikersinterface van uw toepassing aanpassen door aangepaste thema's toe te passen en de helpkoppelingen aan te passen die verwijzen naar uw eigen aangepaste helpbronnen. 



In de volgende schermafbeelding wordt een pagina weergegeven met het standaardthema:

![Standaard IoT Centraal-thema](./media/howto-customize-ui/standard-ui.png)

In de volgende schermafbeelding wordt een pagina weergegeven met behulp van een aangepaste schermafbeelding met de aangepaste ui-elementen gemarkeerd:

![Aangepast IoT-centraal thema](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Thema maken

Als u een aangepast thema wilt maken, navigeert u naar de pagina **Uw toepassing aanpassen** in de sectie **Beheer:**

![IoT Centrale thema's](./media/howto-customize-ui/themes.png)

Op deze pagina u de volgende aspecten van uw toepassing aanpassen:

### <a name="application-logo"></a>Toepassingslogo

Een PNG-afbeelding, niet groter dan 1 MB, met een transparante achtergrond. Dit logo wordt links weergegeven op de titelbalk van de IoT Central-toepassing.

Als uw logoafbeelding de naam van uw toepassing bevat, u de tekst van de toepassingsnaam verbergen. Zie [Uw toepassing beheren](howto-administer.md#change-application-name-and-url)voor meer informatie.

### <a name="browser-icon-favicon"></a>Pictogram Browser (favicon)

Een PNG-afbeelding, niet groter dan 32 x 32 pixels, met een transparante achtergrond. Een webbrowser kan deze afbeelding gebruiken in de adresbalk, geschiedenis, bladwijzers en het tabblad browser.

### <a name="browser-colors"></a>Browserkleuren

U de kleur van de paginakoptekst en de kleur die wordt gebruikt voor accentknoppen en andere hooglichten wijzigen. Gebruik een kleurwaarde van zes `##ff6347`tekens in het formaat . Zie [HTML-kleuren](https://www.w3schools.com/html/html_colors.asp)voor meer informatie over **kleurnotatie van HEX-waarde** .

> [!NOTE]
> U altijd terugkeren naar de standaardopties op de **pagina Uw toepassing aanpassen.**

### <a name="changes-for-operators"></a>Wijzigingen voor exploitanten

Als een beheerder een aangepast thema maakt, kunnen operators en andere gebruikers van uw toepassing niet langer een thema kiezen in **Instellingen**.

## <a name="replace-help-links"></a>Help-koppelingen vervangen

Als u uw operators en andere gebruikers aangepaste help-informatie wilt verstrekken, u de koppelingen in het **Help-menu** van toepassingen wijzigen.

Als u de helpkoppelingen wilt wijzigen, navigeert u naar de **helppagina aanpassen** in de sectie **Beheer:**

![IoT Central help-koppelingen aanpassen](./media/howto-customize-ui/help-links.png)

U ook nieuwe items toevoegen aan het Helpmenu en standaardvermeldingen verwijderen:

![Aangepaste IoT Central-hulp](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> U altijd terugkeren naar de standaardhelpkoppelingen op de **helppagina aanpassen.**

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u de gebruikersinterface in uw IoT Central-toepassing aanpassen, volgen hier enkele voorgestelde volgende stappen:

- [Uw toepassing beheren](./howto-administer.md)
- [Tegels toevoegen aan uw dashboard](howto-add-tiles-to-your-dashboard.md)
---
title: Toepassings sjablonen gebruiken in azure IoT Central | Microsoft Docs
description: Als operator kunt u de apparaatsets gebruiken in uw Azure IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d682c49aa833b9e11dbbddc5e9f6afd52cbb6e84
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952873"
---
# <a name="use-application-templates"></a>Toepassingssjablonen gebruiken

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In dit artikel wordt beschreven hoe u als oplossings Manager toepassings sjablonen kunt maken en gebruiken.

Wanneer u een Azure IoT Central-toepassing maakt, hebt u de keuze uit ingebouwde voorbeeld sjablonen. U kunt ook uw eigen toepassings sjablonen maken op basis van bestaande IoT Central toepassingen. U kunt vervolgens uw eigen toepassings sjablonen gebruiken wanneer u nieuwe toepassingen maakt.

Wanneer u een toepassings sjabloon maakt, worden de volgende items uit uw bestaande toepassing opgenomen:

- Het standaard toepassings dashboard, met inbegrip van de indeling van het dash board en alle tegels die u hebt gedefinieerd.
- Apparaatinstellingen, waaronder metingen, instellingen, eigenschappen, opdrachten en dash board.
- Wetgeving. Alle regel definities zijn opgenomen. Acties, met uitzonde ring van e-mail acties, worden echter niet meegenomen.
- Apparaatsets, met inbegrip van de voor waarden en dash boards.

> [!WARNING]
> Als een dash board tegels bevat waarin informatie over specifieke apparaten wordt weer gegeven, worden in die tegels **de aangevraagde bron niet** in de nieuwe toepassing gevonden. U moet deze tegels opnieuw configureren om informatie weer te geven over apparaten in de nieuwe toepassing.

Wanneer u een toepassings sjabloon maakt, bevat deze geen de volgende items:

- Apparaten
- Gebruikers
- Taak definities
- Definities voor continue gegevens export

Voeg deze items hand matig toe aan alle toepassingen die zijn gemaakt op basis van een toepassings sjabloon.

## <a name="create-an-application-template"></a>Een toepassings sjabloon maken

Een toepassings sjabloon maken op basis van een bestaande IoT Central-toepassing:

1. Ga naar de sectie **beheer** in uw toepassing.
1. Selecteer **toepassings sjabloon exporteren**.
1. Voer op de pagina **toepassings sjabloon exporteren** een naam en een beschrijving in voor uw sjabloon.
1. Selecteer de knop **exporteren** om de toepassings sjabloon te maken. U kunt nu de **deel bare koppeling** kopiëren waarmee iemand een nieuwe toepassing kan maken op basis van de sjabloon:

![Een toepassings sjabloon maken](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Een toepassings sjabloon gebruiken

Als u een toepassings sjabloon wilt gebruiken om een nieuwe IoT Central-toepassing te maken, hebt u een eerder gemaakte **deel bare koppeling**nodig. Plak de **koppeling** die u wilt delen in de adres balk van uw browser. De pagina **een toepassing maken** wordt weer gegeven als u uw aangepaste toepassings sjabloon hebt geselecteerd:

![Een toepassing maken op basis van een sjabloon](media/howto-use-app-templates/create-app.png)

Selecteer uw betalings plan en vul de andere velden in het formulier in. Selecteer vervolgens **maken** om een nieuwe IOT Central-toepassing te maken op basis van de toepassings sjabloon.

## <a name="manage-application-templates"></a>Toepassings sjablonen beheren

Op de pagina **exporteren van toepassings sjablonen** kunt u de toepassings sjabloon verwijderen of bijwerken.

Als u een toepassings sjabloon verwijdert, kunt u de eerder gegenereerde koppeling niet meer gebruiken om nieuwe toepassingen te maken.

Als u uw toepassings sjabloon wilt bijwerken, wijzigt u de naam of beschrijving van de sjabloon op de pagina **export van toepassings sjabloon** . Selecteer vervolgens de knop **exporteren** opnieuw. Met deze actie wordt een nieuwe **deel bare koppeling** gegenereerd en wordt een vorige **deel bare koppelings** -URL ongeldig gemaakt.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u toepassings sjablonen kunt gebruiken, is de voorgestelde volgende stap informatie over het [beheren van IOT Central van de Azure Portal](howto-manage-iot-central-from-portal.md)

---
title: Uw factuur beheren en converteren van het gratis prijs plan in azure IoT Central toepassing | Microsoft Docs
description: Als beheerder leert u hoe u uw factuur kunt beheren en hoe u het gratis prijs plan kunt verplaatsen naar een Standard-prijs plan in uw Azure IoT Central-toepassing
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 58503784f28a3ba0a6290a2209ce9fdccc14b4e4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023766"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Uw factuur beheren in een IoT Central-toepassing

In dit artikel wordt beschreven hoe u, als beheerder, uw factuur kunt beheren in azure IoT Central-toepassing in de sectie beheer. U leert hoe u uw toepassing kunt verplaatsen van het gratis prijs plan naar een Standard-prijs plan en hoe u uw prijs plan bijwerkt of downgradet.

Als u de sectie **beheer** wilt openen en gebruiken, moet u de rol *beheerder* hebben of een *aangepaste* gebruikersrol hebben waarmee u factuur kunt bekijken voor een Azure IOT Central-toepassing. Als u een Azure IoT Central-toepassing maakt, wordt u automatisch toegewezen aan **de beheerdersrol** voor die toepassing.

## <a name="move-from-free-to-standard-pricing-plan"></a>Verplaatsen van gratis naar Standard-prijs plan

- Toepassingen die gebruikmaken van het gratis prijs plan zijn zeven dagen beschikbaar voordat ze verlopen. Om verlies van gegevens te voor komen, kunt u ze op elk gewenst moment naar een Standard-prijs plan verplaatsen voordat ze verlopen.
- Toepassingen die gebruikmaken van een Standard-prijs plan worden per apparaat in rekening gebracht, met de eerste twee gratis apparaten per toepassing.

Lees meer over prijzen op de [prijzenpagina van IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

In het gedeelte prijzen kunt u uw toepassing verplaatsen van het gratis naar een standaard prijs plan.

Voer de volgende stappen uit om dit self-service proces te volt ooien:

1. Ga naar de pagina met **prijzen** in de sectie **beheer** .

    ![Status van de proef versie](media/howto-view-bill/freetrialbilling.png)

1. Selecteer **converteren naar een betaald abonnement**.

    ![Proef versie converteren](media/howto-view-bill/convert.png)

1. Selecteer de juiste Azure Active Directory en vervolgens het Azure-abonnement dat u wilt gebruiken voor uw toepassing die gebruikmaakt van een betaald abonnement.

1. Nadat u **converteren**hebt geselecteerd, maakt uw toepassing nu gebruik van een betaald abonnement en wordt u gefactureerd.

> [!Note]
> Standaard wordt u geconverteerd naar een prijs plan van *Standard 2* .

## <a name="how-to-change-your-application-pricing-plan"></a>Het prijs plan van uw toepassing wijzigen

Toepassingen die gebruikmaken van een Standard-prijs plan worden per apparaat in rekening gebracht, met de eerste twee gratis apparaten per toepassing.

In het gedeelte prijzen kunt u uw Azure IoT-prijs plan op elk gewenst moment bijwerken of verlagen.

1. Ga naar de pagina met **prijzen** in de sectie **beheer** .

    ![Status van de proef versie](media/howto-view-bill/pricing.png)

1. Selecteer het **plan** en klik op **Opslaan** om te upgraden of Down graden.

## <a name="view-your-bill"></a>Uw factuur weergeven

1. Selecteer de juiste Azure Active Directory en vervolgens het Azure-abonnement dat u wilt gebruiken voor uw toepassing die gebruikmaakt van een betaald abonnement.

1. Nadat u **converteren**hebt geselecteerd, maakt uw toepassing nu gebruik van een betaald abonnement en wordt u gefactureerd.

> [!Note]
> Standaard wordt u geconverteerd naar een prijs plan van *Standard 2* .

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw factuur kunt beheren in azure IoT Central-toepassing, is de voorgestelde volgende stap meer informatie over het aanpassen van de [gebruikers interface](howto-customize-ui.md) van de toepassing in azure IOT Central.
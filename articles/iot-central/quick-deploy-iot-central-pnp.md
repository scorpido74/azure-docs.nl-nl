---
title: Een Azure IoT Central-toepassing maken | Microsoft Docs
description: Maak een nieuwe Azure IoT Central-toepassing. Maak een proefversie of betalen per gebruik-toepassing met behulp van een toepassingssjabloon.
author: viv-liu
ms.author: viviali
ms.date: 06/07/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 72d02cecf37c631e6f8097b220848425c5e1fe9c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719122"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Een Azure IoT Central-toepassing maken (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

In deze Quick start ziet u hoe u een Azure IoT Central-toepassing maakt die gebruikmaakt van preview-functies zoals IoT Plug en Play.

> [!WARNING]
> De IoT Plug en Play-mogelijkheden in azure IoT Central zijn momenteel beschikbaar als open bare preview. Gebruik geen IoT Plug en Play ingeschakelde IoT Central toepassing voor werk belastingen voor de productie. Voor productie omgevingen wordt een IoT Central-toepassing gebruikt die is gemaakt op basis van een huidige, algemeen beschik bare, toepassings sjabloon.

## <a name="create-an-application"></a>Een toepassing maken

Ga naar de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) . Meld u vervolgens aan met een persoonlijk, werk-of school account van micro soft.

Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing**. Met deze koppeling gaat u naar de pagina **een toepassing maken** .

![Een toepassings pagina maken in azure IoT Central](media/quick-deploy-iot-central-pnp/iotcentralcreate-pnp.png)

Voor het maken van een nieuwe Azure IoT Central-toepassing die preview-functies zoals IoT Plug en Play bevat:

1. Kies een betalingsplan:
   - **Proef** toepassingen zijn zeven dagen beschikbaar voordat ze verlopen. Ze kunnen op elk gewenst moment worden geconverteerd naar **betalen per gebruik** voordat ze verlopen. Als u een **proef** toepassing maakt, moet u uw contact gegevens invoeren en kiezen of u informatie en tips van micro soft wilt ontvangen.
   - Voor **betalen naar gebruik** -toepassingen worden per apparaat in rekening gebracht, met de eerste vijf apparaten gratis. Als u een **betalen per gebruik** -toepassing maakt, moet u uw *adres lijst*, het *Azure-abonnement*en de *regio*selecteren:
        - *Directory* is de Azure Active Directory (AD) voor het maken van uw toepassing. Deze bevat gebruikers-id's, referenties en andere organisatiegegevens. Als u geen Azure AD hebt, wordt er een voor u gemaakt wanneer u een Azure-abonnement maakt.
        - Als u een *Azure-abonnement* hebt, kunt u instanties van Azure-services maken. IoT Central zorgt ervoor dat resources in uw abonnement worden ingericht. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-aanmeldingspagina](https://aka.ms/createazuresubscription). Nadat u het Azure-abonnement hebt gemaakt, gaat u terug naar de pagina **een toepassing maken** . Uw nieuwe abonnement wordt weergegeven in de vervolgkeuzelijst.**Azure-abonnement**.
        - *Regio* is de fysieke locatie waar u de toepassing wilt maken. Normaal gesp roken moet u de regio kiezen die zich het dichtst bij uw apparaten bevindt voor optimale prestaties. Tijdens de open bare preview zijn de enige beschik bare regio's voor een **Preview-toepassing** **Europa-Noord** en **centrale VS**. Wanneer u een regio hebt gekozen, kunt u uw toepassing niet meer naar een andere regio verplaatsen.

        Lees meer over prijzen op de [prijzenpagina van IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Selecteer de sjabloon **voor beeld van toepassing** . Een toepassingssjabloon kan vooraf gedefinieerde items bevatten, zoals apparaatsjablonen en dashboards om u op weg te helpen.

1. In azure IoT Central wordt automatisch een toepassings naam voorgesteld op basis van de toepassings sjabloon die u hebt geselecteerd. U kunt deze naam accepteren of uw eigen beschrijvende toepassings naam invoeren, zoals **Contoso IOT**. Azure IoT Central genereert ook een uniek URL-voor voegsel, op basis van de naam van de toepassing. Als u wilt, kunt u dit URL-voor voegsel wijzigen in iets eenvoudiger te onthouden.

1. Vul in stap 1 de aanvullende informatie in die vereist is voor het betalings schema dat u eerder hebt geselecteerd.

1. Selecteer **Maken** onderaan de pagina.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een IoT Central-toepassing gemaakt die gebruikmaakt van de preview-functies. Dit is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Een nieuw apparaattype definiëren in uw Azure IoT Central-toepassing](./tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

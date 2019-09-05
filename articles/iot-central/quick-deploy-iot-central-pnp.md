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
ms.openlocfilehash: 9d242c48068e96498a811f52dbc599abd32bc936
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383008"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Een Azure IoT Central-toepassing maken (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

In deze Quick start ziet u hoe u een Azure IoT Central-toepassing maakt die gebruikmaakt van preview-functies zoals IoT Plug en Play.

> [!WARNING]
> De IoT Plug en Play-mogelijkheden in azure IoT Central zijn momenteel beschikbaar als open bare preview. Gebruik geen IoT Plug en Play ingeschakelde IoT Central toepassing voor werk belastingen voor de productie. Voor productie omgevingen wordt een IoT Central-toepassing gebruikt die is gemaakt op basis van een huidige, algemeen beschik bare, toepassings sjabloon.

## <a name="create-an-application"></a>Een app maken

Ga naar de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) . Meld u vervolgens aan met een persoonlijk of werk-of school account van micro soft.

Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing**. Met deze koppeling gaat u naar de pagina **een toepassing maken** .

![Een toepassings pagina maken in azure IoT Central](media/quick-deploy-iot-central-pnp/iotcentralcreate.png)

Voor het maken van een nieuwe Azure IoT Central-toepassing die preview-functies zoals IoT Plug en Play bevat:

1. Kies een betalingsplan:
   - **Proef** toepassingen zijn zeven dagen beschikbaar voordat ze verlopen. Voordat ze verlopen, kunnen ze op elk gewenst moment worden geconverteerd naar betalen per gebruik. Als u een **proef** toepassing maakt, moet u uw contact gegevens invoeren en kiezen of u informatie en tips van micro soft wilt ontvangen.
   - Voor **betalen naar gebruik** -toepassingen worden per apparaat in rekening gebracht, met de eerste vijf apparaten gratis. Als u een **betalen per gebruik** -toepassing maakt, moet u uw *Directory*, *Azure-abonnement*en *regio*selecteren:
      - *Directory* is de Azure Active Directory (AD) voor het maken van uw toepassing. Deze bevat gebruikers-id's, referenties en andere organisatiegegevens. Als u geen Azure AD hebt, wordt er een voor u gemaakt wanneer u een Azure-abonnement maakt.
      - Als u een *Azure-abonnement* hebt, kunt u instanties van Azure-services maken. IoT Central zorgt ervoor dat resources in uw abonnement worden ingericht. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-aanmeldingspagina](https://aka.ms/createazuresubscription). Nadat u het Azure-abonnement hebt gemaakt, gaat u terug naar de pagina **Toepassing maken**. Uw nieuwe abonnement wordt weergegeven in de vervolgkeuzelijst.**Azure-abonnement**.
      - *Regio* is de fysieke locatie waar u de toepassing wilt maken. Tijdens de open bare preview zijn de enige beschik bare regio's voor een **Preview-toepassing** **Europa-Noord** en **centrale VS**.

      Lees meer over prijzen op de [prijzenpagina van IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Kies een beschrijvende toepassingsnaam, zoals **Contoso IoT**. Azure IoT Central genereert een uniek URL-voorvoegsel voor u. U kunt dit URL-voorvoegsel wijzigen in iets dat gemakkelijker te onthouden is.

1. Kies de sjabloon **voor beeld van toepassing** . Een toepassingssjabloon kan vooraf gedefinieerde items bevatten, zoals apparaatsjablonen en dashboards om u op weg te helpen.

1. Selecteer **Maken** onderaan de pagina.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een IoT Central-toepassing gemaakt die gebruikmaakt van de preview-functies. De voorgestelde volgende stap is:

> [!div class="nextstepaction"]
> [Een nieuw apparaattype definiëren in uw Azure IoT Central-toepassing](./tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

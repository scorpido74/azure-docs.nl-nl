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
ms.openlocfilehash: 05b761dbf9f58f6afca6459e28d2fecca9aa7b06
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878304"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Een Azure IoT Central-toepassing maken (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

In deze Quick start ziet u hoe u een Azure IoT Central-toepassing maakt die gebruikmaakt van preview-functies zoals IoT Plug en Play.

> [!WARNING]
> De IoT Plug en Play-mogelijkheden in azure IoT Central zijn momenteel beschikbaar als open bare preview. Gebruik geen IoT Plug en Play ingeschakelde IoT Central toepassing voor werk belastingen voor de productie. Voor productie omgevingen wordt een IoT Central-toepassing gebruikt die is gemaakt op basis van een huidige, algemeen beschik bare, toepassings sjabloon.

## <a name="create-an-application"></a>Een app maken

Navigeer naar de pagina [​​Toepassingsbeheer ](https://aka.ms/iotcentral) van Azure IoT Central. Meld u vervolgens aan met een persoonlijk of werk-of school account van micro soft.

Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing**. Met deze koppeling gaat u naar de pagina **een toepassing maken** .

![Een toepassings pagina maken in azure IoT Central](media/quick-deploy-iot-central-pnp/iotcentralcreate.png)

Voor het maken van een nieuwe Azure IoT Central-toepassing die preview-functies zoals IoT Plug en Play bevat:

1. Kies een betalingsplan:
   - **Proef** toepassingen zijn zeven dagen beschikbaar voordat ze verlopen. Voordat ze verlopen, kunnen ze op elk gewenst moment worden geconverteerd naar betalen per gebruik. Als u een **proef** toepassing maakt, moet u uw contact gegevens invoeren en kiezen of u informatie en tips van micro soft wilt ontvangen.
   - Voor **betalen naar gebruik** -toepassingen worden per apparaat in rekening gebracht, met de eerste vijf apparaten gratis. Als u een **betalen per gebruik** -toepassing maakt, moet u uw *Directory*, *Azure-abonnement*en *regio*selecteren:
      - *Directory* is de Azure Active Directory (AD) voor het maken van uw toepassing. Deze bevat gebruikers-id's, referenties en andere organisatiegegevens. Als u geen Azure AD hebt, wordt er een voor u gemaakt wanneer u een Azure-abonnement maakt.
      - Als u een *Azure-abonnement* hebt, kunt u instanties van Azure-services maken. IoT Central zorgt ervoor dat resources in uw abonnement worden ingericht. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-aanmeldingspagina](https://aka.ms/createazuresubscription). Nadat u het Azure-abonnement hebt gemaakt, gaat u terug naar de pagina **Toepassing maken**. Uw nieuwe abonnement wordt weergegeven in de vervolgkeuzelijst.**Azure-abonnement**.
      - *Regio* is de fysieke locatie waar u de toepassing wilt maken. Normaal gesp roken kiest u de regio die zich het dichtst in de buurt bevindt van uw apparaten om optimaal te pres teren. U kunt de regio's bekijken waarin Azure IoT Central beschikbaar is op de pagina [beschik bare producten per regio](https://azure.microsoft.com/regions/services/) . Wanneer u een regio hebt gekozen, kunt u de toepassing later niet verplaatsen naar een andere regio.

      Lees meer over prijzen op de [prijzenpagina van IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Kies een beschrijvende toepassingsnaam, zoals **Contoso IoT**. Azure IoT Central genereert een uniek URL-voorvoegsel voor u. U kunt dit URL-voorvoegsel wijzigen in iets dat gemakkelijker te onthouden is.

1. Kies de sjabloon **voor beeld van toepassing** . Een toepassingssjabloon kan vooraf gedefinieerde items bevatten, zoals apparaatsjablonen en dashboards om u op weg te helpen.

1. Selecteer **Maken** onderaan de pagina.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een IoT Central-toepassing gemaakt die gebruikmaakt van de preview-functies. De voorgestelde volgende stap is:

> [!div class="nextstepaction"]
> [Volg een rondleiding door IoT Central](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

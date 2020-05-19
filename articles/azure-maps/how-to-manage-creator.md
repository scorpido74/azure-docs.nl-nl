---
title: Azure Maps Maker beheren
description: In dit artikel leert u hoe u Azure Maps Maker beheert.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 945c7035c2eeada3fea358489460f80c40438950
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598309"
---
# <a name="manage-azure-maps-creator"></a>Azure Maps Maker beheren

Met Azure Maps Maker kunt u persoonlijke gegevens voor een binnenste kaart maken. Met de Azure Maps-API en de module over kaarten kunt u interactieve en dynamische kaart webtoepassingen voor het binnenste Web ontwikkelen. Op dit moment is de Maker alleen beschikbaar in de Verenigde Staten met de prijs categorie S1.

In dit artikel worden de stappen beschreven voor het maken en verwijderen van een Creator-resource in een Azure Maps-account.

## <a name="create-creator-resource"></a>Creator-resource maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)

2. Selecteer uw Azure Maps-account. Als u uw Azure Maps-account niet kunt zien onder de **recente resources**, gaat u naar het menu Azure Portal. Selecteer **Alle resources**. Zoek en selecteer uw Azure Maps-account.

    ![Start pagina van Azure Maps Portal](./media/how-to-manage-creator/select-maps-account.png)

3. Als u zich op de pagina Azure Maps-account bevindt, gaat u naar de optie **overzicht** onder **Maker**. Klik op **maken** om een Azure Maps Creator-resource te maken.

    ![Azure Maps Creator-pagina maken](./media/how-to-manage-creator/creator-blade-settings.png)

4. Voer de naam en locatie in voor de bron van de maker. Momenteel wordt de Maker alleen ondersteund in de Verenigde Staten. Klik op **Controleren + maken**.

   ![Informatie pagina voor de account van de Creator invoeren](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Controleer uw instellingen en klik op **maken**.

    ![Pagina instellingen van de Creator-account bevestigen](./media/how-to-manage-creator/creator-create.PNG)

6. Wanneer de implementatie is voltooid, ziet u een pagina met een geslaagde of fout melding.

   ![Pagina status van implementatie van resource](./media/how-to-manage-creator/creator-resource-created.png)

7. Klik op **Ga naar resource**. Op de pagina Maker resource weergave ziet u de status van uw Creator-resource en de gekozen demografische regio.

    ![Pagina status van de maker](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >Op de pagina Creator-resource kunt u teruggaan naar het Azure Maps account waarmee het deel uitmaakt door te klikken op Azure Maps-account.

## <a name="delete-creator-resource"></a>Creator-resource verwijderen

Als u de Creator-resource wilt verwijderen, gaat u naar uw Azure Maps-account. Selecteer **overzicht** onder **Maker**. Klik op de knop **Verwijderen**.

>[!WARNING]
>Wanneer u de maker-resource van uw Azure Maps-account verwijdert, verwijdert u ook de gegevens sets, tilesets en functie statesets die zijn gemaakt met behulp van Creator Services.

![De pagina Maker met de knop verwijderen](./media/how-to-manage-creator/creator-delete.png)

Klik op de knop **verwijderen** en typ de naam van de maker om de verwijdering te bevestigen. Zodra de resource is verwijderd, ziet u een bevestigings pagina, zoals in de onderstaande afbeelding:

![Pagina Maker met bevestiging van verwijderen](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>Verificatie

De maker neemt de instellingen van Azure Maps Access Control (IAM) over. Alle API-aanroepen voor gegevens toegang moeten met verificatie-en autorisatie regels worden verzonden.

De gebruiks gegevens van de maker zijn opgenomen in uw Azure Maps-gebruiks diagrammen en het activiteiten logboek.  Zie [verificatie beheren in azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)voor meer informatie.

## <a name="access-to-creator-services"></a>Toegang tot Creator-Services

De Creator-Services zijn alleen toegankelijk vanaf de locatie die is geselecteerd tijdens het maken. Als aanroepen naar Creator-services van buiten de geselecteerde locatie worden gedaan, wordt er een fout bericht van de gebruiker weer gegeven. Als u aanroepen buiten de geselecteerde locatie wilt maken, moet de service-URL het geografische voor voegsel voor de geselecteerde locaties bevatten. Als er bijvoorbeeld een Maker wordt gemaakt in de Verenigde Staten, moeten alle aanroepen naar de conversie service worden ingediend bij `us.atlas.microsoft.com/conversion/convert` .

Daarnaast moeten alle gegevens die in de Maker worden geïmporteerd, worden geüpload naar dezelfde geografische locatie als de maker-resource. Als de maker bijvoorbeeld is ingericht in de Verenigde Staten, moeten alle onbewerkte gegevens worden geüpload via `us.atlas.microsoft.com/mapData/upload` .

## <a name="next-steps"></a>Volgende stappen

Inleiding tot Maker voor een binnenste toewijzing:

> [!div class="nextstepaction"]
> [Gegevens uploaden](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Gegevens conversie](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Sets](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tegelset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Functie status ingesteld](creator-indoor-maps.md#feature-statesets)

Meer informatie over het gebruik van de maker voor het weer geven van binnenste kaarten in uw toepassing:

> [!div class="nextstepaction"]
> [Zelf studie Azure Maps Maker](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamische opmaak van kaarten in een binnenshuis](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [De module binnenste kaarten gebruiken](how-to-use-indoor-module.md)
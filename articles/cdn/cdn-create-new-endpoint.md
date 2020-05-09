---
title: 'Quick Start: een Azure CDN profiel en eind punt maken'
description: In deze quickstart ziet u hoe u Azure CDN inschakelt door een nieuw CDN-profiel en CDN-eindpunt te maken.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: af90166b688dee104e7bda18a88a2fe7c98f657b
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996282"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Snelstart: Een Azure CDN-profiel en een eindpunt maken

In deze Quick Start schakelt u Azure Content Delivery Network (CDN) in door een nieuw CDN-profiel te maken. Dit is een verzameling van een of meer CDN-eind punten. Nadat u een profiel en een eindpunt hebt gemaakt, kunt u beginnen met het leveren van inhoud aan uw klanten.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Een Azure Storage account met de naam *cdnstorageacct123*, dat u gebruikt voor de hostnaam van de oorsprong. Als u deze vereiste wilt volt ooien, raadpleegt u [een Azure Storage-account integreren met Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw Azure-account.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Nieuwe CDN-eindpunten maken

Nadat u een CDN-profiel hebt gemaakt, gebruikt u dit om een eind punt te maken.

1. Selecteer in het dashboard het CDN-profiel dat u hebt gemaakt in het Azure Portal. Als u het niet kunt vinden, kunt u de resource groep openen waarin u deze hebt gemaakt, of de zoek balk boven in de portal gebruiken, de profiel naam invoeren en het profiel selecteren in de resultaten.
   
1. Selecteer op de pagina CDN-profiel **+ eind punt**.
   
    ![CDN-profiel](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    Het deelvenster **Een eindpunt toevoegen** wordt weergegeven.

3. Voer de volgende instellings waarden in:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Naam** | Voer *CDN-endpoint-123* in voor de hostnaam van uw eind punt. Deze naam moet wereld wijd uniek zijn in Azure. Als deze al in gebruik is, voert u een andere naam in. Deze naam wordt gebruikt om toegang te krijgen tot uw resources in de cache op het endpoint van het domein _ &lt;-name&gt;_. azureedge.net.|
    | **Oorsprongtype** | Selecteer **Opslag**. | 
    | **Hostnaam van oorsprong** | Selecteer de hostnaam van het Azure Storage account dat u gebruikt in de vervolg keuzelijst, zoals *cdnstorageacct123.blob.core.Windows.net*. |
    | **Pad voor de oorsprong** | Leeg laten. |
    | **Host-header van oorsprong** | Wijzig de standaard waarde (dit is de hostnaam voor het opslag account). |  
    | **Protocol** | Laat de standaard geselecteerde opties **HTTP** en **HTTPS** staan. |
    | **Poort van oorsprong** | Laat de standaard gegenereerde poortwaardes staan. | 
    | **Geoptimaliseerd voor** | Laat de standaardselectie **Algemene weboverdracht** staan. |

    ![Deelvenster Eindpunt toevoegen](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. Selecteer **Toevoegen** om het nieuwe eindpunt te maken. Zodra het eindpunt is gemaakt, wordt deze weergegeven in de lijst met eindpunten voor het profiel.
    
   ![CDN-eindpunt](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Hoe lang het duurt voordat het eind punt wordt door gegeven, is afhankelijk van de prijs categorie die is geselecteerd bij het maken van het profiel. **Standaard Akamai** is doorgaans binnen één minuut, **Standard van micro soft** in 10 minuten en **Standard verizon** en **Premium Verizon** binnen 90 minuten.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u een CDN-profiel en een eindpunt in een resourcegroep gemaakt. Sla deze resources op als u naar [Vervolgstappen](#next-steps) wilt gaan en informatie wilt krijgen over het toevoegen van een aangepast domein aan uw eindpunt. Als u echter denkt deze resources in de toekomst niet meer nodig te hebben, kunt u ze verwijderen door de resourcegroep te verwijderen. Op deze manier voorkomt u bijkomende kosten:

1. Selecteer **resource groepen** in het menu aan de linkerkant in het Azure Portal en selecteer vervolgens **CDNQuickstart-RG**.

2. Selecteer op de pagina **resource groep** de optie **resource groep verwijderen**, Voer *CDNQuickstart-RG* in het tekstvak in en selecteer vervolgens **verwijderen**. Met deze actie verwijdert u de resource groep, het profiel en het eind punt dat u in deze Quick Start hebt gemaakt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: een CDN gebruiken om statische inhoud van een web-app te server](cdn-add-to-web-app.md)

> [!div class="nextstepaction"]
> [Zelfstudie: Een aangepast domein toevoegen aan uw Azure CDN-eindpunt](cdn-map-content-to-custom-domain.md)

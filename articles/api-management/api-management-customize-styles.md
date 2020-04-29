---
title: Pagina stijl aanpassen op API Management verouderde ontwikkelaars Portal
titleSuffix: Azure API Management
description: Volg de stappen van deze snelstart voor het aanpassen van de stijl van de elementen in de ontwikkelaarsportal van Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 664686511df8f310295a9f6ed6bc689b3a999544
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75430740"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>De stijl van de pagina's van de ontwikkelaars portal aanpassen

Er zijn drie veelgebruikte manieren om de ontwikkelaars Portal in azure API Management aan te passen:
 
* [De inhoud van statische pagina's en pagina-indelingselementen bewerken](api-management-modify-content-layout.md)
* De stijlen bijwerken die worden gebruikt voor pagina-elementen in de ontwikkelaarsportal (uitgelegd in deze handleiding)
* [De sjablonen bewerken die worden gebruikt voor pagina's die worden gegenereerd door de portal](api-management-developer-portal-templates.md) (bijvoorbeeld API-documenten, producten, gebruikersverificatie, enzovoort)

In dit artikel leert u hoe u de stijl van elementen kunt aanpassen op pagina's van de verouderde **ontwikkelaars** Portal en hoe u de wijzigingen weergeeft.

![stijl aanpassen](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Vereisten

+ Leer de [terminologie van Azure API Management](api-management-terminology.md).
+ Lees de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
+ Voltooi tevens de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>De ontwikkelaars portal aanpassen

1. Selecteer **Overzicht**.
2. Klik op de knop **ontwikkelaars Portal (verouderd)** boven in het venster **overzicht** .
3. In de linkerbovenhoek van het scherm ziet u een pictogram dat bestaat uit twee schilderkwasten. Beweeg de muisaanwijzer over dit pictogram om het menu Portalaanpassing te openen.

    ![stijl aanpassen](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Selecteer **Stijlen** in het menu om het deelvenster Stijl aanpassen te openen.

    Alle elementen die u kunt aanpassen met behulp van **Stijlen** worden weergegeven op de pagina
5. Voer 'headings-color' in het veld **Wijzigen van de waarden om de weergave van het ontwikkelaarsportal aan te passen:** in.

    Het ** \@element kop-Color** wordt weer gegeven op de pagina. Deze variabele bepaalt de kleur van de tekst.

    ![stijl aanpassen](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Klik op het veld voor de ** \@variabele koppen-kleur** . 
    
    De vervolgkeuzelijst Kleurenkiezer wordt geopend.
7. Selecteer een nieuwe kleur uit de vervolgkeuzelijst Kleurenkiezer.

    > [!TIP]
    > Een realtime-voorbeeld is beschikbaar voor alle wijzigingen. Er verschijnt een voortgangsindicator aan de bovenkant van het deelvenster Aanpassen. Na een paar seconden verandert de koptekst naar de zojuist geselecteerde kleur.

8. Selecteer **Publiceren** linksonder in het menu van het deelvenster Aanpassen.
9. Selecteer **Aanpassingen publiceren** om de wijzigingen openbaar beschikbaar te maken.

## <a name="view-your-change"></a>Uw wijziging bekijken

1. Navigeer naar de ontwikkelaars Portal.
2. Hier ziet u de wijziging die u hebt aangebracht.

## <a name="next-steps"></a>Volgende stappen

Misschien wilt u wel weten hoe u [de Azure API Management-ontwikkelaarsportal aanpast met behulp van sjablonen](api-management-developer-portal-templates.md).
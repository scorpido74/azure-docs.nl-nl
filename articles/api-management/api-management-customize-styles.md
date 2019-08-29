---
title: De paginastijl aanpassen in de ontwikkelaarsportal van Azure API Management | Microsoft Docs
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
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 047e724fe3e1c2e4738e5964326bf7719281f4af
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073700"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>De stijl van de pagina's van de ontwikkelaarsportal aanpassen

Er zijn drie gebruikelijke manieren waarop u de ontwikkelaarsportal in Azure API Management kunt aanpassen:
 
* [De inhoud van statische pagina's en pagina-indelingselementen bewerken](api-management-modify-content-layout.md)
* De stijlen bijwerken die worden gebruikt voor pagina-elementen in de ontwikkelaarsportal (uitgelegd in deze handleiding)
* [De sjablonen bewerken die worden gebruikt voor pagina's die worden gegenereerd door de portal](api-management-developer-portal-templates.md) (bijvoorbeeld API-documenten, producten, gebruikersverificatie, enzovoort)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Aanpassen van de stijl van elementen op pagina's van de **ontwikkelaars**portal
> * Uw wijziging bekijken

![stijl aanpassen](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>Vereisten

+ Informatie over de [terminologie van Azure API Management](api-management-terminology.md).
+ Voltooi de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
+ Voltooi ook de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="customize-the-developer-portal"></a>De ontwikkelaarsportal aanpassen

1. Selecteer **Overzicht**.
2. Klik op de knop **Ontwikkelaarsportal** bovenaan het venster **Overzicht**. U kunt ook klikken op de **Ontwikkelaarsportal URL**-link.
3. In de linkerbovenhoek van het scherm ziet u een pictogram dat bestaat uit twee schilderkwasten. Beweeg de muisaanwijzer over dit pictogram om het menu Portalaanpassing te openen.

    ![Stijl aanpassen](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Selecteer **Stijlen** in het menu om het deelvenster Stijl aanpassen te openen.

    Alle elementen die u kunt aanpassen met behulp van **Stijlen** worden weergegeven op de pagina
5. Voer 'headings-color' in het veld **Wijzigen van de waarden om de weergave van het ontwikkelaarsportal aan te passen:** in.

    Het element kop-Color wordt weer gegeven op de pagina.  **\@** Deze variabele bepaalt de kleur van de tekst.

    ![stijl aanpassen](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Klik op het veld voor de  **\@variabele koppen-kleur** . 
    
    De vervolgkeuzelijst Kleurenkiezer wordt geopend.
7. Selecteer een nieuwe kleur uit de vervolgkeuzelijst Kleurenkiezer.

    > [!TIP]
    > Een realtime-voorbeeld is beschikbaar voor alle wijzigingen. Er verschijnt een voortgangsindicator aan de bovenkant van het deelvenster Aanpassen. Na een paar seconden verandert de koptekst naar de zojuist geselecteerde kleur.

8. Selecteer **Publiceren** linksonder in het menu van het deelvenster Aanpassen.
9. Selecteer **Aanpassingen publiceren** om de wijzigingen openbaar beschikbaar te maken.

## <a name="view-your-change"></a>Uw wijziging bekijken

1. Navigeer naar de ontwikkelaarsportal.
2. Hier ziet u de wijziging die u hebt aangebracht.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Aanpassen van de stijl van elementen op pagina's van de **ontwikkelaars**portal
> * Uw wijziging bekijken

Misschien wilt u wel weten hoe u [de Azure API Management-ontwikkelaarsportal aanpast met behulp van sjablonen](api-management-developer-portal-templates.md).
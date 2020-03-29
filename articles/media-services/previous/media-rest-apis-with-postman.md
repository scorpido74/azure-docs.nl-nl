---
title: Postman configureren voor API-aanroepen van Azure Media Services REST
description: In dit artikel wordt beschreven hoe u Postman for Media Services REST API-aanroepen configureert.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 11c9c26e7c0f36e1e3dba732e90a6aef95e6ee14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76694987"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Postman configureren voor V2 REST API-aanroepen  

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

In deze zelfstudie ziet u hoe u **Postman configureert,** zodat deze kan worden gebruikt om API's voor Azure Media Services (AMS) REST te bellen. In de zelfstudie ziet u hoe u omgevingen en verzamelingsbestanden importeert in **Postman.** De verzameling bevat gegroepeerde definities van HTTP-aanvragen die API's voor Azure Media Services (AMS) REST aanroepen. Het omgevingsbestand bevat variabelen die worden gebruikt door de verzameling.

Deze omgeving en verzameling wordt gebruikt in artikelen die laten zien hoe u verschillende taken uitvoeren met AZURE Media Services REST API's.

## <a name="prerequisites"></a>Vereisten

- Installeer de [Postman](https://www.getpostman.com/) REST-client als u de REST-API's wilt uitvoeren die in een aantal AMS REST-zelfstudies worden weergegeven. 

    We gebruiken **Postman** maar elk ander REST-hulpprogramma is hiervoor geschikt. Andere alternatieven zijn: **Visual Studio Code** met de REST plugin of **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>De omgeving configureren 

1. Maak een .json-bestand met de omgevingsvariabelen die worden gebruikt in AMS-zelfstudies. Geef het bestand een naam (bijvoorbeeld **AzureMediaServices.postman_environment.json).** Open het bestand en plak de code die de postman-omgeving definieert in [deze codevermelding.](postman-environment.md) 
2. Open de **Postman**.
3. Selecteer rechts van het scherm de optie **Manage environment**.

    ![Bestand uploaden](./media/media-services-rest-upload-files/postman-create-env.png)
4. Klik in het dialoogvenster **Manage environment** op **Import**.
5. Blader door het **azureMediaServices.postman_environment.json-bestand.**
6. De **AzureMedia-omgeving** wordt toegevoegd.
7. Sluit het dialoogvenster.
8. Selecteer de **AzureMedia-omgeving.**

    ![Bestand uploaden](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>De collectie configureren

1. Maak een .json-bestand met de **Postman-verzameling** met alle bewerkingen die nodig zijn om een bestand naar Media Services te uploaden. Geef het bestand een naam (bijvoorbeeld **AzureMediaServicesOperations.postman_collection.json).** Open het bestand en plak de code die de **postmanverzameling** definieert in [deze codevermelding.](postman-collection.md)
2. Klik op **Import** om het verzamelingbestand te importeren.
3. Kies het **bestand AzureMediaServicesOperations.postman_collection.json.**

    ![Bestand uploaden](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk het artikel [over uploadassets.](media-services-rest-upload-files.md)  

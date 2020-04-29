---
title: Postman configureren voor Azure Media Services REST API-aanroepen
description: In dit artikel wordt beschreven hoe u na het configureren van Postman voor Media Services REST API-aanroepen.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76694987"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Postman configureren voor Media Services v2-REST API-aanroepen  

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

In deze zelf studie wordt uitgelegd hoe u **postman** zo configureert dat deze kan worden gebruikt om Azure Media Services-rest API'S (AMS) aan te roepen. In de zelf studie ziet u hoe u omgevings-en verzamelings bestanden importeert in een **bericht**. De verzameling bevat gegroepeerde definities van HTTP-aanvragen die de REST-Api's van Azure Media Services (AMS) aanroepen. Het omgevingsbestand bevat variabelen die worden gebruikt door de verzameling.

Deze omgeving en verzameling wordt in artikelen gebruikt die laten zien hoe u verschillende taken met Azure Media Services REST-Api's kunt bezorgen.

## <a name="prerequisites"></a>Vereisten

- Installeer de [Postman](https://www.getpostman.com/) REST-client als u de REST-API's wilt uitvoeren die in een aantal AMS REST-zelfstudies worden weergegeven. 

    We gebruiken **Postman** maar elk ander REST-hulpprogramma is hiervoor geschikt. Andere alternatieven zijn: **Visual Studio code** met de rest-invoeg toepassing of **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>De omgeving configureren 

1. Maak een JSON-bestand dat de omgevings variabelen bevat die in AMS-zelf studies worden gebruikt. Noem het bestand (bijvoorbeeld **AzureMediaServices. postman_environment. json**). Open het bestand en plak de code die de Postman-omgeving in de [code vermelding](postman-environment.md)definieert. 
2. Open de **Postman**.
3. Selecteer rechts van het scherm de optie **Manage environment**.

    ![Bestand uploaden](./media/media-services-rest-upload-files/postman-create-env.png)
4. Klik in het dialoogvenster **Manage environment** op **Import**.
5. Blader en selecteer het bestand **AzureMediaServices. postman_environment. json** .
6. De **Media** -omgeving wordt toegevoegd.
7. Sluit het dialoogvenster.
8. Selecteer de **Media** -omgeving.

    ![Bestand uploaden](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>De collectie configureren

1. Maak een JSON-bestand dat de **postman** -verzameling bevat met alle bewerkingen die nodig zijn om een bestand te uploaden naar Media Services. Noem het bestand (bijvoorbeeld **AzureMediaServicesOperations. postman_collection. json**). Open het bestand en plak de code die de **postman** -verzameling definieert vanuit [deze code lijst](postman-collection.md).
2. Klik op **Import** om het verzamelingbestand te importeren.
3. Kies het bestand **AzureMediaServicesOperations. postman_collection. json** .

    ![Bestand uploaden](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk het artikel [Upload assets](media-services-rest-upload-files.md) .  

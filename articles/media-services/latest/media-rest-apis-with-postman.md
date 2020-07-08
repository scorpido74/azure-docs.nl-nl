---
title: Postman configureren voor Azure Media Services v3-REST API-aanroepen
description: In dit artikel wordt beschreven hoe u postman zo configureert dat deze kan worden gebruikt voor het aanroepen van Azure Media Services-REST Api's (AMS).
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
ms.date: 12/05/2019
ms.author: juliako
ms.openlocfilehash: 872dad95fc5b536c51e251612f40439da020a059
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75779634"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Postman configureren voor Media Services v3-REST API-aanroepen

In dit artikel wordt beschreven hoe u **postman** zo configureert dat deze kan worden gebruikt voor het aanroepen van Azure Media Services-rest API'S (AMS). In dit artikel wordt beschreven hoe u omgevings-en verzamelings bestanden importeert in een **bericht**. De verzameling bevat gegroepeerde definities van HTTP-aanvragen die de REST-Api's van Azure Media Services (AMS) aanroepen. Het omgevingsbestand bevat variabelen die worden gebruikt door de verzameling.

Controleer voordat u begint met het ontwikkelen [met behulp van Media Services v3-api's](media-services-apis-overview.md).

## <a name="prerequisites"></a>Vereisten

- [Maak een Media Services-account](create-account-cli-how-to.md). Zorg ervoor dat u de naam van de resource groep en de naam van het Media Services account vergeet. 
- Gegevens ophalen die nodig zijn voor [toegang tot api's](access-api-cli-how-to.md)
- Installeer de [Postman](https://www.getpostman.com/) REST-client als u de REST-API's wilt uitvoeren die in een aantal AMS REST-zelfstudies worden weergegeven. 

    We gebruiken **Postman** maar elk ander REST-hulpprogramma is hiervoor geschikt. Andere alternatieven zijn: **Visual Studio code** met de rest-invoeg toepassing of **Telerik Fiddler**. 

> [!IMPORTANT]
> Bekijk [naam conventies](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Postman-bestanden downloaden

Kloon een GitHub-opslagplaats die de Postman verzameling en -omgevingsbestanden bevat.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Postman configureren

### <a name="configure-the-environment"></a>De omgeving configureren 

1. Open de **postman** -app.
2. Selecteer rechts van het scherm de optie **Manage environment**.

    ![Omgeving beheren](./media/develop-with-postman/postman-import-env.png)
4. Klik in het dialoogvenster **Manage environment** op **Import**.
2. Blader naar het bestand `Azure Media Service v3 Environment.postman_environment.json` dat is gedownload toen u `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` kloonde.
6. De omgeving **Azure Media Service v3 Environment** is toegevoegd.

    > [!Note]
    > Werk de toegangsvariabelen bij met waarden die u hebt gekregen in de sectie **Toegang krijgen tot de Media Services API** hierboven.

7. Dubbelklik op het geselecteerde bestand en voer de waarden in die u hebt verkregen door de stappen voor het verkrijgen van toegang tot API's te volgen.
8. Sluit het dialoogvenster.
9. Selecteer de omgeving **Azure Media Service v3 Environment** in de vervolgkeuzelijst.

    ![Omgeving kiezen](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>De collectie configureren

1. Klik op **Import** om het verzamelingbestand te importeren.
1. Blader naar het bestand `Media Services v3.postman_collection.json` dat is gedownload toen u `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` kloonde
3. Kies het bestand **Media Services v3.postman_collection.json**.

    ![Een bestand importeren](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Azure AD-token verkrijgen 

Voordat u begint met het bewerken van AMS v3-resources, moet u Azure AD-token voor Service-Principal-verificatie ophalen en instellen.

1. Selecteer in het linkerdeel venster van de Postman-app ' stap 1: AAD-verificatie Token ophalen '.
2. Selecteer vervolgens Get Azure AD Token for Service Principal Authentication.
3. Druk op **Verzenden**.

    De volgende **POST**-bewerking wordt verzonden.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Het antwoord bevat de token en stelt de omgevingsvariabele AccessToken in op de tokenwaarde.  

    ![AAD-token verkrijgen](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Problemen oplossen 

* Als uw toepassing mislukt met ' HTTP 504: time-out van gateway ', moet u ervoor zorgen dat de variabele locatie niet expliciet is ingesteld op een andere waarde dan de verwachte locatie van het Media Services-account. 
* Als u een fout bericht ' account niet gevonden ' krijgt, controleert u ook of de eigenschap location in het JSON-bericht van de hoofd tekst is ingesteld op de locatie waar het Media Services-account zich bevindt. 

## <a name="see-also"></a>Zie tevens

- [Bestanden uploaden naar een Media Services-account - REST](upload-files-rest-how-to.md)
- [Filters maken met Media Services - REST](filters-dynamic-manifest-rest-howto.md)
- [REST API op basis van Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Volgende stappen

- [Streamen van bestanden met rest](stream-files-tutorial-with-rest.md).  
- [Zelf studie: een extern bestand coderen op basis van URL en de video-REST streamen](stream-files-tutorial-with-rest.md)

---
title: Postman configureren voor V3 REST API-aanroepen van Azure Media Services
description: In dit artikel ziet u hoe u Postman configureert, zodat deze kan worden gebruikt om API's voor Azure Media Services (AMS) TE bellen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779634"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Postman configureren voor V3 REST API-aanroepen

In dit artikel ziet u hoe u **Postman configureert,** zodat deze kan worden gebruikt om API's voor Azure Media Services (AMS) TE bellen. Het artikel laat zien hoe u omgeving- en verzamelingsbestanden importeert in **Postman.** De verzameling bevat gegroepeerde definities van HTTP-aanvragen die API's voor Azure Media Services (AMS) REST aanroepen. Het omgevingsbestand bevat variabelen die worden gebruikt door de verzameling.

Voordat u begint met het ontwikkelen, review [Ontwikkelen met Media Services v3 API's](media-services-apis-overview.md).

## <a name="prerequisites"></a>Vereisten

- [Een Azure Media Services-account maken](create-account-cli-how-to.md). Zorg ervoor dat u de naam van de brongroep en de naam van het Media Services-account onthoudt. 
- Informatie krijgen die nodig is om toegang te [krijgen tot API's](access-api-cli-how-to.md)
- Installeer de [Postman](https://www.getpostman.com/) REST-client als u de REST-API's wilt uitvoeren die in een aantal AMS REST-zelfstudies worden weergegeven. 

    We gebruiken **Postman** maar elk ander REST-hulpprogramma is hiervoor geschikt. Andere alternatieven zijn: **Visual Studio Code** met de REST plugin of **Telerik Fiddler**. 

> [!IMPORTANT]
> [Naamgevingsconventies bekijken](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Postman-bestanden downloaden

Kloon een GitHub-opslagplaats die de Postman verzameling en -omgevingsbestanden bevat.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Postman configureren

### <a name="configure-the-environment"></a>De omgeving configureren 

1. Open de **Postbode-app.**
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

Voordat u AMS v3-bronnen gaat manipuleren, moet u Azure AD-token voor serviceprincipal-verificatie opvragen en instellen.

1. Selecteer in het linkervenster van de Postman-app de optie 'Stap 1: Ontvang AAD Auth-token'.
2. Selecteer vervolgens Get Azure AD Token for Service Principal Authentication.
3. Druk op **Verzenden**.

    De volgende **POST**-bewerking wordt verzonden.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Het antwoord bevat de token en stelt de omgevingsvariabele AccessToken in op de tokenwaarde.  

    ![AAD-token verkrijgen](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Problemen oplossen 

* Als uw toepassing mislukt met 'HTTP 504: Gateway Timeout', moet u ervoor zorgen dat de locatievariabele niet expliciet is ingesteld op een andere waarde dan de verwachte locatie van het Media Services-account. 
* Als u een fout als 'account niet gevonden' wordt, controleert u ook of de locatieeigenschap in het JSON-bericht van de hoofdtekst is ingesteld op de locatie waarin het Media Services-account zich bevindt. 

## <a name="see-also"></a>Zie ook

- [Bestanden uploaden naar een Media Services-account - REST](upload-files-rest-how-to.md)
- [Filters maken met Media Services - REST](filters-dynamic-manifest-rest-howto.md)
- [REST API op basis van Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Volgende stappen

- [Bestanden streamen met REST](stream-files-tutorial-with-rest.md).  
- [Zelfstudie: Een extern bestand coderen op basis van URL en de video streamen - REST](stream-files-tutorial-with-rest.md)

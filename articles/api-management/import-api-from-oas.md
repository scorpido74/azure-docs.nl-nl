---
title: Een OpenAPI-specificatie importeren met Azure Portal | Microsoft Docs
description: Informatie over het importeren van een OpenAPI-specificatie met API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: apimpm
ms.openlocfilehash: 57803ec9889cb6a19dae6d6d1070d8381577aff0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77468385"
---
# <a name="import-an-openapi-specification"></a>Een OpenAPI-specificatie importeren

In dit artikel ziet u hoe u een back-end-API met de naam OpenAPI-specificatie kunt importeren die zich bevindt in https://conferenceapi.azurewebsites.net?format=json. Deze back-end-API wordt geleverd door Microsoft en gehost in Azure. In het artikel wordt ook uitgelegd hoe u de APIM-API kunt testen.

> [!IMPORTANT]
> Zie dit [document](https://azure.microsoft.com/blog/announcing-the-preview-of-openapi-specification-v3-support-in-azure-api-management/) voor belangrijke informatie en tips die betrekking hebben op OpenAPI importeren.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een back-end-API met de naam OpenAPI-specificatie importeren
> * De API testen in Azure Portal
> * De API testen in de ontwikkelaarsportal

## <a name="prerequisites"></a>Vereisten

De volgende quickstart voltooien: [een azure API-beheerexemplaar maken](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Een back-end-API importeren en publiceren

1. Selecteer **API's** bij **API MANAGEMENT**.
2. Selecteer **OpenAPI-specificatie** in de lijst **Een nieuwe API toevoegen**.

    ![OpenAPI-specificatie](./media/import-api-from-oas/oas-api.png)
3. Voer de juiste instellingen in. U kunt tijdens het maken alle API-waarden instellen. U kunt ook bepaalde waarden later instellen door naar het tabblad **Instellingen** te gaan. <br/> Als u op **Tab** drukt, worden sommige of alle velden ingevuld met de gegevens van de opgegeven back-end-service.

    ![Een API maken](./media/api-management-get-started/create-api.png)

    |Instelling|Waarde|Beschrijving|
    |---|---|---|
    |**OpenAPI-specificatie**|https://conferenceapi.azurewebsites.net?format=json|Verwijst naar de service waarmee de API wordt geïmplementeerd. API Management stuurt aanvragen door naar dit adres.|
    |**Weergavenaam**|*Demo Conference API*|Als u op Tab drukt nadat u de service-URL hebt ingevoerd, wordt dit veld ingevuld met APIM op basis van de inhoud van het JSON-bestand. <br/>Deze naam wordt weergegeven in de ontwikkelaarsportal.|
    |**Naam**|*demo-conference-api*|Biedt een unieke naam voor de API. <br/>Als u op Tab drukt nadat u de service-URL hebt ingevoerd, wordt dit veld ingevuld met APIM op basis van de inhoud van het JSON-bestand.|
    |**Beschrijving**|Geef een optionele beschrijving van de API.|Als u op Tab drukt nadat u de service-URL hebt ingevoerd, wordt dit veld ingevuld met APIM op basis van de inhoud van het JSON-bestand.|
    |**API-URL-achtervoegsel**|*conference*|Het achtervoegsel wordt toegevoegd aan de basis-URL voor de API Management-service. In API Management worden API's herkend aan hun achtervoegsel en daarom moet het achtervoegsel uniek zijn voor elke API voor een bepaalde uitgever.|
    |**URL-schema**|*HTTPS*|Bepaalt welke protocollen kunnen worden gebruikt om toegang te krijgen tot de API. |
    |**Producten**|*Onbeperkt*| Publiceer de API door deze aan een product te koppelen. Typ eventueel de productnaam als u deze nieuwe API aan een product wilt toevoegen. Deze stap kan meerdere keren worden herhaald om de API toe te voegen aan meerdere producten.<br/>Producten zijn koppelingen van een of meer API's. U kunt een aantal API's opnemen en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ontwikkelaars zich abonneren, ontvangen ze een abonnementssleutel die toegang biedt tot elke API in het betreffende product. Als u de APIM-abonnementssleutel hebt gemaakt, bent u al een beheerder en bent u standaard geabonneerd op elk product.<br/> Standaard wordt elke API Management-instantie geleverd met twee voorbeeldproducten: **Starter** en **Onbeperkt**. |

4. Selecteer **Maken**.

> [!NOTE]
> De api-importbeperkingen worden gedocumenteerd in [een ander artikel.](api-management-api-import-restrictions.md)

## <a name="test-the-new-api-in-the-azure-portal"></a>De nieuwe API testen in de Azure-portal

![API-kaart testen](./media/api-management-get-started/01-import-first-api-01.png)

Bewerkingen kunnen rechtstreeks vanuit Azure Portal worden aangeroepen. Dit is een handige manier om de bewerkingen van een API te bekijken en te testen.

1. Selecteer de API die u in de vorige stap hebt gemaakt (op het tabblad **API's**).
2. Druk op het tabblad **Testen**.
3. Klik op **GetSpeakers**. Op de pagina worden velden weergegeven voor queryparameters (maar in dit geval zijn er geen queryparameters) en headers. Een van de headers is Ocp-Apim-Subscription-Key voor de abonnementssleutel van het product dat is gekoppeld aan deze API. De sleutel wordt automatisch ingevuld.
4. Druk op **Verzenden**.

    Back-end reageert met **200 OK** en enkele gegevens.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een gepubliceerde API transformeren en beveiligen](transform-api.md)

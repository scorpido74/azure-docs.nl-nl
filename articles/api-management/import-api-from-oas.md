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
ms.openlocfilehash: b96cfe9813eef9caf1f1f21e43470a23c7032cb1
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072127"
---
# <a name="import-an-openapi-specification"></a>Een OpenAPI-specificatie importeren

In dit artikel ziet u hoe u een back-end-API met de naam OpenAPI-specificatie kunt importeren die zich bevindt in https://conferenceapi.azurewebsites.net?format=json. Deze back-end-API wordt geleverd door Microsoft en gehost in Azure. In het artikel wordt ook uitgelegd hoe u de APIM-API kunt testen.

> [!IMPORTANT]
> Zie dit [document](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) voor belangrijke informatie en tips die betrekking hebben op OpenAPI importeren.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een back-end-API met de naam OpenAPI-specificatie importeren
> * De API testen in Azure Portal
> * De API testen in de ontwikkelaarsportal

## <a name="prerequisites"></a>Vereisten

Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Een back-end-API importeren en publiceren

1. Selecteer **API's** bij **API MANAGEMENT**.
2. Selecteer **OpenAPI-specificatie** in de lijst **Een nieuwe API toevoegen**.
    ![OpenAPI-specificatie](./media/import-api-from-oas/oas-api.png)
3. Voer de juiste instellingen in. U kunt tijdens het maken alle API-waarden instellen. U kunt ook bepaalde waarden later instellen door naar het tabblad **Instellingen** te gaan. <br/> Als u op **Tab** drukt, worden sommige of alle velden ingevuld met de gegevens van de opgegeven back-end-service.

    ![Een API maken](./media/api-management-get-started/create-api.png)

    |Instelling|Value|Description|
    |---|---|---|
    |**OpenAPI-specificatie**|https://conferenceapi.azurewebsites.net?format=json|Verwijst naar de service waarmee de API wordt geïmplementeerd. API Management stuurt aanvragen door naar dit adres.|
    |**Weergavenaam**|*Demo Conference API*|Als u op Tab drukt nadat u de service-URL hebt ingevoerd, wordt dit veld ingevuld met APIM op basis van de inhoud van het JSON-bestand. <br/>Deze naam wordt weergegeven in de ontwikkelaarsportal.|
    |**Name**|*demo-conference-api*|Biedt een unieke naam voor de API. <br/>Als u op Tab drukt nadat u de service-URL hebt ingevoerd, wordt dit veld ingevuld met APIM op basis van de inhoud van het JSON-bestand.|
    |**Beschrijving**|Geef een optionele beschrijving van de API.|Als u op Tab drukt nadat u de service-URL hebt ingevoerd, wordt dit veld ingevuld met APIM op basis van de inhoud van het JSON-bestand.|
    |**API-URL-achtervoegsel**|*conference*|Het achtervoegsel wordt toegevoegd aan de basis-URL voor de API Management-service. In API Management worden API's herkend aan hun achtervoegsel en daarom moet het achtervoegsel uniek zijn voor elke API voor een bepaalde uitgever.|
    |**URL-schema**|*HTTPS*|Bepaalt welke protocollen kunnen worden gebruikt om toegang te krijgen tot de API. |
    |**Producten**|*Onbeperkt*| Publiceer de API door deze aan een product te koppelen. Typ eventueel de productnaam als u deze nieuwe API aan een product wilt toevoegen. Deze stap kan meerdere keren worden herhaald om de API toe te voegen aan meerdere producten.<br/>Producten zijn koppelingen van een of meer API's. U kunt een aantal API's opnemen en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ontwikkelaars zich abonneren, ontvangen ze een abonnementssleutel die toegang biedt tot elke API in het betreffende product. Als u de APIM-abonnementssleutel hebt gemaakt, bent u al een beheerder en bent u standaard geabonneerd op elk product.<br/> Standaard wordt elk API Management-exemplaar geleverd met twee voorbeeldproducten: **Starter** en **Onbeperkt**. |

4. Selecteer **Maken**.

> [!NOTE]
> De beperkingen voor het importeren van API'S worden beschreven in [een ander artikel](api-management-api-import-restrictions.md).

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>De nieuwe APIM API testen in Azure Portal

Bewerkingen kunnen rechtstreeks vanuit Azure Portal worden aangeroepen. Dit is een handige manier om de bewerkingen van een API te bekijken en te testen.

![API testen](./media/api-management-get-started/01-import-first-api-01.png)

1. Selecteer de API die u in de vorige stap hebt gemaakt.
2. Druk op het tabblad **Testen**.
3. Klik op **GetSpeakers**.

    Op de pagina worden velden weergegeven voor queryparameters, maar in dit geval zijn er geen queryparameters. Op de pagina worden ook velden weergegeven voor de headers. Een van de headers is Ocp-Apim-Subscription-Key voor de abonnementssleutel van het product dat is gekoppeld aan deze API. Als u de APIM-instantie hebt gemaakt, bent u al een beheerder en wordt de sleutel automatisch ingevoerd.
4. Druk op **Verzenden**.

    Back-end reageert met **200 OK** en enkele gegevens.

## <a name="call-operation"> </a>Een bewerking aanroepen vanuit de ontwikkelaarsportal

Bewerkingen kunnen ook worden aangeroepen vanuit de **ontwikkelaarsportal** om API's te testen.

1. Selecteer de API die u hebt gemaakt in de stap voor het importeren en publiceren van een back-end-API.
2. Selecteer de **ontwikkelaarsportal**.

    ![Testen in ontwikkelaarsportal](./media/api-management-get-started/developer-portal.png)

    De site voor de ontwikkelaarsportal wordt geopend.
3. Selecteer **API**.
4. Selecteer **Demo Conference API**.
5. Klik op **GetSpeakers**.

    Op de pagina worden velden weergegeven voor queryparameters, maar in dit geval zijn er geen queryparameters. Op de pagina worden ook velden weergegeven voor de headers. Een van de headers is Ocp-Apim-Subscription-Key voor de abonnementssleutel van het product dat is gekoppeld aan deze API. Als u de APIM-instantie hebt gemaakt, bent u al een beheerder en wordt de sleutel automatisch ingevoerd.
6. Druk op **Proberen**.
7. Druk op **Verzenden**.

    Nadat een bewerking is aangeroepen, worden in de ontwikkelaarsportal de **antwoordstatus**, de **antwoordheaders** en eventuele **antwoordinhoud** weergegeven.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een gepubliceerde API transformeren en beveiligen](transform-api.md)

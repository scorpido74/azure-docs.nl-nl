---
title: Uw eerste API importeren en publiceren in Azure API Management | Microsoft Docs
description: Informatie over het importeren en publiceren van uw eerste API met API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: apimpm
ms.openlocfilehash: bae762b4603b2f5f80447a16671fed4e37e62b95
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108536"
---
# <a name="import-and-publish-your-first-api"></a>Uw eerste API importeren en publiceren 

In deze zelfstudie ziet u hoe u een back-end-API met de naam OpenAPI-specificatie kunt importeren die zich bevindt in https://conferenceapi.azurewebsites.net?format=json. Deze back-end-API wordt geleverd met Microsoft en gehost in Azure. 

Zodra de back-end-API is geïmporteerd in API Management (APIM), wordt de APIM-API een facade voor de back-end-API. Op het moment dat u de back-end-API importeert, zijn de bron-API en de APIM API identiek. In APIM kunt u de facade naar wens aanpassen zonder de back-end-API te wijzigen. Zie [Transform and protect your API](transform-api.md) (Uw API transformeren en beveiligen) voor meer informatie. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw eerste API importeren
> * De API testen in Azure Portal
> * De API testen in de ontwikkelaarsportal

![Nieuwe API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Vereisten

+ Leer de [terminologie van Azure API Management](api-management-terminology.md).
+ Lees de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Een API importeren en publiceren

In deze sectie ziet u hoe u een back-end-API met de naam OpenAPI-specificatie kunt importeren en publiceren.
 
1. Selecteer **API's** bij **API MANAGEMENT**.
2. Selecteer **OpenAPI-specificatie** uit de lijst en klik op **Volledig** in het pop-upvenster.

    ![Een API maken](./media/api-management-get-started/create-api.png)

    U kunt de API-waarden tijdens het maken of later instellen door naar het tabblad **instellingen** te gaan. De rode ster naast een veld geeft aan dat het veld vereist is.

    Gebruik de waarden uit de onderstaande tabel om uw eerste API te maken.

    | Instelling                   | Waarde                                              | Beschrijving                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    |---------------------------|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **OpenAPI-specificatie** | https://conferenceapi.azurewebsites.net?format=json | Verwijst naar de service waarmee de API wordt geïmplementeerd. API Management stuurt aanvragen door naar dit adres.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
    | **Weergavenaam**          | *Demo Conference API*                              | Als u op Tab drukt nadat u de service-URL hebt ingevoerd, wordt dit veld ingevuld met APIM op basis van de inhoud van het JSON-bestand. <br/>Deze naam wordt weergegeven in de ontwikkelaarsportal.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Name**                  | *demo-conference-api*                              | Biedt een unieke naam voor de API. <br/>Als u op Tab drukt nadat u de service-URL hebt ingevoerd, wordt dit veld ingevuld met APIM op basis van de inhoud van het JSON-bestand.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Beschrijving**           | Geef een optionele beschrijving van de API.        | Als u op Tab drukt nadat u de service-URL hebt ingevoerd, wordt dit veld ingevuld met APIM op basis van de inhoud van het JSON-bestand.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
    | **URL-schema**            | *HTTPS*                                            | Bepaalt welke protocollen kunnen worden gebruikt om toegang te krijgen tot de API.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | **API-URL-achtervoegsel**        | *conference*                                       | Het achtervoegsel wordt toegevoegd aan de basis-URL voor de API Management-service. In API Management worden API's herkend aan hun achtervoegsel en daarom moet het achtervoegsel uniek zijn voor elke API voor een bepaalde uitgever.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Producten**              | *Onbeperkt*                                        | Producten zijn koppelingen van een of meer API's. U kunt een aantal API's opnemen in een product en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. <br/>U publiceert de API door deze te koppelen aan een product (in dit voorbeeld *Onbeperkt*). Als u deze nieuwe API wilt toevoegen aan een product, typt u de productnaam (u kunt dit ook later doen op de pagina **Instellingen**). Deze stap kan meerdere keren worden herhaald om de API toe te voegen aan meerdere producten.<br/>Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ontwikkelaars zich abonneren, ontvangen ze een abonnementssleutel die toegang biedt tot elke API in het betreffende product. <br/> Als u de APIM-instantie hebt gemaakt, bent u al een beheerder en bent u geabonneerd op elk product.<br/> Standaard wordt elke API Management-instantie geleverd met twee voorbeeldproducten: **Starter** en **Onbeperkt**. |
    | **Tags**                  |                                                    | Tags voor het ordenen van API's. Tags kunnen worden gebruikt voor zoeken, groeperen of filteren.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Versie van deze API?**     |                                                    | Zie [Publish multiple versions of your API](api-management-get-started-publish-versions.md) (Meerdere versies van uw API publiceren) voor meer informatie over versiebeheer                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

    >[!NOTE]
    > Als u de API wilt publiceren, moet u deze koppelen aan een product. U kunt dit doen op de pagina **Instellingen**.

3. Selecteer **Maken**.

> [!TIP]
> Zie de [lijst met bekende problemen en beperkingen](api-management-api-import-restrictions.md) als u problemen ondervindt met het importeren van uw eigen API-definitie.

## <a name="test-the-new-api-in-the-azure-portal"></a>De nieuwe API in het Azure Portal testen

![API-kaart testen](./media/api-management-get-started/01-import-first-api-01.png)

Bewerkingen kunnen rechtstreeks vanuit Azure Portal worden aangeroepen. Dit is een handige manier om de bewerkingen van een API te bekijken en te testen.

1. Selecteer de API die u in de vorige stap hebt gemaakt (op het tabblad **API's**).
2. Druk op het tabblad **Testen**.
3. Klik op **GetSpeakers**. Op de pagina worden velden weergegeven voor queryparameters (maar in dit geval zijn er geen queryparameters) en headers. Een van de headers is Ocp-Apim-Subscription-Key voor de abonnementssleutel van het product dat is gekoppeld aan deze API. De sleutel wordt automatisch ingevuld.
4. Druk op **Verzenden**.

    Back-end reageert met **200 OK** en enkele gegevens.

## <a name="next-steps"> </a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Uw eerste API importeren
> * De API testen in Azure Portal

Ga door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Een product maken en publiceren](api-management-howto-add-products.md)

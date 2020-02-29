---
title: Uw eerste API importeren en publiceren in azure API Management
description: Meer informatie over het importeren van een OpenAPI-specificatie-API in azure API Management en het testen van uw API in de Azure Portal.
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
ms.date: 02/27/2020
ms.author: apimpm
ms.openlocfilehash: 886063dcf886d79ac960814f20b3789e8e3b6839
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163480"
---
# <a name="import-and-publish-your-first-api"></a>Uw eerste API importeren en publiceren 

In deze zelf studie ziet u hoe u een back-end-API van OpenAPI-specificatie in JSON-indeling importeert in azure API Management. Micro soft biedt de back-end-API en host deze op Azure op [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json).

Nadat u de back-end-API hebt geïmporteerd in API Management, wordt uw API Management-API een gevel voor de back-end-API. U kunt de gevel aanpassen aan uw behoeften in API Management zonder de back-end-API te raken. Zie [Transform and protect your API](transform-api.md) (Uw API transformeren en beveiligen) voor meer informatie. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een API importeren in API Management
> * De API testen in Azure Portal

![Nieuwe API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Vereisten

- Inzicht krijgen in de [terminologie van Azure API Management](api-management-terminology.md).
- [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Een back-end-API importeren en publiceren

In deze sectie wordt beschreven hoe u een back-end-API voor OpenAPI-specificatie importeert en publiceert.
 
1. Selecteer in de linkernavigatiebalk van uw API Management-exemplaar **api's** in het gedeelte **API Management** .
1. Selecteer de tegel **OpenAPI** en selecteer vervolgens **volledig** in het pop-upvenster.
1. Gebruik in het scherm **maken van OpenAPI-specificatie** de waarden uit de volgende tabel om uw API te maken.
   
   Een rode ster naast een veld op het formulier geeft aan dat het veld vereist is. U kunt de API-waarden tijdens het maken of later instellen door naar het tabblad **instellingen** te gaan. 
   
   ![Een API maken](./media/api-management-import-and-publish/create-api.png)
   
   |Instelling|Waarde|Beschrijving|
   |-------|-----|-----------|
   |**OpenAPI-specificatie**|*https:\//conferenceapi.azurewebsites.net? format = json*|De service die de API implementeert. API Management stuurt aanvragen door naar dit adres.|
   |**Weergavenaam**|Nadat u de voor gaande service-URL hebt opgegeven, API Management dit veld invullen op basis van de JSON.|De naam die wordt weer gegeven in de ontwikkelaars Portal.|
   |**Naam**|Nadat u de voor gaande service-URL hebt opgegeven, API Management dit veld invullen op basis van de JSON.|Een unieke naam voor de API.|
   |**Beschrijving**|Nadat u de voor gaande service-URL hebt opgegeven, API Management dit veld invullen op basis van de JSON.|Een optionele beschrijving van de API.|
   |**URL-schema**|**HTTPS**|Welke protocollen kunnen worden gebruikt om toegang te krijgen tot de API.|
   |**API-URL-achtervoegsel**|*conference*|Het achtervoegsel dat is toegevoegd aan de basis-URL voor de API Management-service. API Management onderscheidt Api's met hun achtervoegsel, dus het achtervoegsel moet uniek zijn voor elke API voor een bepaalde uitgever.|
   |**Producten**|**Onbeperkt**|Koppeling van een of meer Api's. Elk API Management-exemplaar wordt geleverd met twee voorbeeld producten: **starter** en **onbeperkt**. U publiceert een API door de API te koppelen aan een product, **onbeperkt** in dit voor beeld.<br/>U kunt meerdere Api's in een product toevoegen en deze aanbieden aan ontwikkel aars via de ontwikkelaars Portal. Als u deze API wilt toevoegen aan een ander product, typt of selecteert u de product naam. Herhaal deze stap om de API toe te voegen aan meerdere producten. U kunt later ook Api's aan producten toevoegen op de pagina **instellingen** .<br/>Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ze zich abonneren, krijgen ze een abonnements sleutel die geschikt is voor elke API in dat product. <br/>Als u de API Management instantie hebt gemaakt, bent u al een beheerder en bent u geabonneerd op elk product in het exemplaar.|
   |**Tags**| |Tags voor het organiseren van Api's voor zoeken, groeperen en filteren.|
   |**Versie van deze API?**|Selecteren of selectie opheffen|Zie [meerdere versies van uw API publiceren](api-management-get-started-publish-versions.md)voor meer informatie over versie beheer.|
   
   > [!NOTE]
   > Als u de API wilt publiceren, moet u deze koppelen aan een product. U kunt dit doen op de pagina **instellingen** .
   
1. Selecteer **Maken**.

Zie de [lijst met bekende problemen en beperkingen](api-management-api-import-restrictions.md)als u problemen hebt met het importeren van een API-definitie.

## <a name="test-the-new-api-in-the-azure-portal"></a>De nieuwe API in het Azure Portal testen

U kunt API-bewerkingen rechtstreeks aanroepen vanuit de Azure Portal. Dit biedt een handige manier om de bewerkingen te bekijken en te testen.

1. Selecteer in de linkernavigatiebalk van uw API Management-exemplaar **api's** in het gedeelte **API Management** en selecteer vervolgens **demo conferentie-API**.
1. Selecteer het tabblad **testen** en selecteer vervolgens **GetSpeakers**. De pagina bevat **query parameters** en **kopteksten**, indien van toepassing. De **OCP-APIM-Subscription-Key** wordt automatisch ingevuld voor de abonnements sleutel die is gekoppeld aan deze API.
1. Selecteer **Verzenden**.
   
   ![API-kaart testen](./media/api-management-import-and-publish/01-import-first-api-01.png)
   
   Back-end reageert met **200 OK** en enkele gegevens.

## <a name="next-steps"> </a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Uw eerste API importeren
> * De API testen in Azure Portal

Ga naar de volgende zelf studie voor meer informatie over het maken en publiceren van een product:

> [!div class="nextstepaction"]
> [Een product maken en publiceren](api-management-howto-add-products.md)

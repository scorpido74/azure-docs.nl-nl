---
title: Uw eerste API importeren en publiceren in Azure API Management
description: Meer informatie over het importeren van een OpenAPI Specification API in Azure API Management en het testen van uw API in de Azure-portal.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78163480"
---
# <a name="import-and-publish-your-first-api"></a>Uw eerste API importeren en publiceren 

In deze zelfstudie ziet u hoe u een OpenAPI Specification backend API importeert in JSON-indeling in Azure API Management. Microsoft biedt de backend-API en [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json)host deze op Azure op .

Zodra u de backend API in API Management importeert, wordt uw API Management API een gevel voor de backend API. U de gevel aanpassen aan uw behoeften in API Management zonder de backend API aan te raken. Zie [Transform and protect your API](transform-api.md) (Uw API transformeren en beveiligen) voor meer informatie. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een API importeren in API-beheer
> * De API testen in Azure Portal

![Nieuwe API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Vereisten

- Informatie over [de terminologie van Azure API Management](api-management-terminology.md).
- [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>Een API importeren en publiceren

In deze sectie ziet u hoe u een OpenAPI Specification backend API importeert en publiceert.
 
1. Selecteer **API's** in de sectie **API-beheer** in de linkernavigatie van uw API-beheerexemplaar.
1. Selecteer de **tegel OpenAPI** en selecteer **Volledig** op het pop-upscherm.
1. Gebruik in het scherm **Maken vanuit OpenAPI-specificatie** de waarden uit de volgende tabel om uw API te maken.
   
   Een rode ster naast een veld op het formulier geeft aan dat het veld vereist is. U API-waarden instellen tijdens het maken of later door naar het tabblad **Instellingen** te gaan. 
   
   ![Een API maken](./media/api-management-import-and-publish/create-api.png)
   
   |Instelling|Waarde|Beschrijving|
   |-------|-----|-----------|
   |**OpenAPI-specificatie**|*https:\//conferenceapi.azurewebsites.net?format=json*|De service die de API implementeert. API Management stuurt aanvragen door naar dit adres.|
   |**Weergavenaam**|Nadat u de vorige service-URL hebt ingevoerd, vult API Management dit veld in op basis van de JSON.|De naam die wordt weergegeven in de ontwikkelaarsportal.|
   |**Naam**|Nadat u de vorige service-URL hebt ingevoerd, vult API Management dit veld in op basis van de JSON.|Een unieke naam voor de API.|
   |**Beschrijving**|Nadat u de vorige service-URL hebt ingevoerd, vult API Management dit veld in op basis van de JSON.|Een optionele beschrijving van de API.|
   |**URL-schema**|**HTTPS**|Welke protocollen kunnen worden gebruikt om toegang te krijgen tot de API.|
   |**API-URL-achtervoegsel**|*conference*|Het achtervoegsel is toegevoegd aan de basis-URL voor de API-beheerservice. API-beheer onderscheidt API's door hun achtervoegsel, dus het achtervoegsel moet uniek zijn voor elke API voor een bepaalde uitgever.|
   |**Producten**|**Onbeperkt**|Vereniging van een of meer API's. Elke API Management-instantie wordt geleverd met twee voorbeeldproducten: **Starter** en **Unlimited.** U publiceert een API door de API te koppelen aan een product, **Unlimited** in dit voorbeeld.<br/>U verschillende API's in een product opnemen en deze aanbieden aan ontwikkelaars via de ontwikkelaarsportal. Als u deze API aan een ander product wilt toevoegen, typt u of selecteert u de productnaam. Herhaal deze stap om de API aan meerdere producten toe te voegen. U api's later ook toevoegen aan producten vanaf de pagina **Instellingen.**<br/>Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ze zich abonneren, krijgen ze een abonnementssleutel die goed is voor elke API in dat product. <br/>Als u de instantie API-beheer hebt gemaakt, bent u al beheerder, dus u bent geabonneerd op elk product in de instantie.|
   |**Tags**| |Tags voor het organiseren van API's voor zoeken, groeperen of filteren.|
   |**Versie van deze API?**|De selectie selecteren of deselecteren|Zie Meerdere versies van [uw API publiceren](api-management-get-started-publish-versions.md)voor meer informatie over versiebeheer.|
   
   > [!NOTE]
   > Als u de API wilt publiceren, moet u deze koppelen aan een product. Dat kan op de pagina **Instellingen.**
   
1. Selecteer **Maken**.

Als u problemen ondervindt bij het importeren van een API-definitie, raadpleegt u de [lijst met bekende problemen en beperkingen.](api-management-api-import-restrictions.md)

## <a name="test-the-new-api-in-the-azure-portal"></a>De nieuwe API testen in de Azure-portal

U API-bewerkingen rechtstreeks vanuit de Azure-portal aanroepen, wat een handige manier biedt om de bewerkingen te bekijken en te testen.

1. Selecteer **API's** in de sectie **API-beheer** in de linkernavigatie van uw API-beheerinstantie en selecteer vervolgens **Demo Conference API**.
1. Selecteer het tabblad **Testen** en selecteer **Ophaalluidsprekers**. De pagina toont **queryparameters** en **eventuele kopteksten.** De **Ocp-Apim-Subscription-Key** wordt automatisch ingevuld voor de abonnementssleutel die aan deze API is gekoppeld.
1. Selecteer **Verzenden**.
   
   ![API-kaart testen](./media/api-management-import-and-publish/01-import-first-api-01.png)
   
   Back-end reageert met **200 OK** en enkele gegevens.

## <a name="next-steps"></a><a name="next-steps"> </a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Uw eerste API importeren
> * De API testen in Azure Portal

Ga naar de volgende zelfstudie om te leren hoe u een product maken en publiceren:

> [!div class="nextstepaction"]
> [Een product maken en publiceren](api-management-howto-add-products.md)

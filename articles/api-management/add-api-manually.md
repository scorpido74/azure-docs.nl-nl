---
title: Een API handmatig toevoegen met Azure Portal | Microsoft Docs
description: Deze zelfstudie laat u zien hoe u een API Management (APIM) moet gebruiken om handmatig een API toe te voegen.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/27/2018
ms.author: apimpm
ms.openlocfilehash: 5440333360549c5df2da57c97b24dcc77436ba4b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072703"
---
# <a name="add-an-api-manually"></a>Handmatig een API toevoegen

In de stappen in dit artikel ziet u hoe u de Azure Portal kunt gebruiken om hand matig een API toe te voegen aan het API Management-exemplaar (APIM). Een veelvoorkomend scenario wanneer u een lege API wilt maken en het handmatig wilt definiëren om een gesimuleerde API te maken. Zie voor meer informatie over het simuleren van een API [Gesimuleerde API-antwoorden](mock-api-responses.md).

Als u een bestaande API wilt importeren, zie de sectie [Verwante onderwerpen](#related-topics).

In dit artikel maken we een lege API en geven [httpbin.org](https://httpbin.org) (een openbare testservice) op als back-end-API.

## <a name="prerequisites"></a>Vereisten

Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Een API maken

1. Selecteer **API's** bij **API MANAGEMENT**.
2. Selecteer **+ API toevoegen** in het linkermenu.
3. Selecteer **Lege API** uit de lijst.

    ![Lege API](media/add-api-manually/blank-api.png)
4. Voer de instellingen voor de API in.

    |**Name**|**Waarde**|**Beschrijving**|
    |---|---|---|
    |**Weergavenaam**|*Lege API*|Deze naam wordt weergegeven in de ontwikkelaarsportal.|
    |**Name**|*lege API*|Biedt een unieke naam voor de API.|
    |**Webservice-URL** (optioneel)|*https://httpbin.org*| Als u een API wilt simuleren, kan het zijn dat u niets invoert. <br/>In dit geval voeren we [https://httpbin.org](https://httpbin.org) in. Dit is een openbare testservice. <br/>Als u een API wilt importeren die automatisch is toegewezen aan een back-end, lees dan een van de onderwerpen in de sectie [Verwante onderwerpen](#related-topics).|
    |**URL-schema**|*HTTPs*|In dit geval geven we een beveiligde HTTPS APIM toegang tot de back-end, ondanks dat de back-end niet-beveiligde HTTP-toegang heeft. <br/>Dit soort scenario (HTTPS naar HTTP) wordt HTTPS-beëindiging genoemd. U kunt dit doen als uw API binnen een virtueel netwerk bestaat (waarvan u weet dat de toegang is beveiligd, zelfs als HTTPS wordt niet gebruikt). <br/>U kunt "HTTPS-beëindiging" op een aantal CPU-cycli gebruiken om op te slaan.|
    |**URL-achtervoegsel**|*hbin*| Het achtervoegsel is een naam die deze specifieke API in dit APIM-exemplaar identificeert. Hij moet uniek zijn in dit APIM-exemplaar.|
    |**Producten**|*Onbeperkt*|Publiceer de API door deze aan een product te koppelen. Als u wilt dat de API wordt gepubliceerd en beschikbaar is voor ontwikkelaars, kunt u deze toevoegen aan een product. U kunt dit doen tijdens het maken van de API of het later instellen.<br/><br/>Producten zijn koppelingen van een of meer API's. U kunt een aantal API's opnemen en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. <br/>Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ontwikkelaars zich abonneren, ontvangen ze een abonnementssleutel die toegang biedt tot elke API in het betreffende product. Als u de APIM-abonnementssleutel hebt gemaakt, bent u al een beheerder en bent u standaard geabonneerd op elk product.<br/><br/> Standaard wordt elk API Management-exemplaar geleverd met twee voorbeeldproducten: **Starter** en **Onbeperkt**.| 
5. Selecteer **Maken**.

U hebt op dit moment geen bewerkingen in APIM die zijn toegewezen aan de bewerkingen in uw back-end-API. Als u een bewerking aanroept die beschikbaar is gesteld via de back-end maar niet via de APIM, krijgt u een **404**.

>[!NOTE] 
> Standaard zal de APIM geen bewerkingen blootstellen totdat u ze accepteert wanneer u een API toevoegt, zelfs als deze is verbonden met bepaalde back-endservice. Om een bewerking van uw back-end-service goed te keuren, maakt u een APIM-bewerking die is toegewezen aan de back-end-bewerking.

## <a name="add-and-test-an-operation"></a>Toevoegen en testen van een bewerking

In deze sectie wordt beschreven hoe u een bewerking "/get" toevoegt om ze te kunnen toewijzen aan de back-end-bewerking "http://httpbin.org/get".

### <a name="add-an-operation"></a>Een bewerking toevoegen

1. Selecteer de API die u in de vorige stap hebt gemaakt.
2. Klik op **+ Bewerking toevoegen**.
3. Selecteer **GET** in de **URL** en voer " */get*" in de resource in.
4. Voer "*FetchData*" in als **Weergavenaam**.
5. Selecteer **Opslaan**.

### <a name="test-an-operation"></a>Een bewerking testen

Test de functie in Azure Portal. U kunt het ook testen in de **Ontwikkelaarsportal**.

1. Selecteer het tabblad **Testen**.
2. Selecteer **FetchData**.
3. Druk op **Verzenden**.

Het antwoord dat de "http://httpbin.org/get"-bewerking genereert, wordt weergegeven. Als u uw bewerkingen wilt transformeren, gaat u naar [Uw API transformeren en beschermen](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Een bewerking met parameters toevoegen en testen

In deze sectie wordt beschreven hoe u een bewerking toevoegt die een parameter heeft. In dit geval, wijst u de bewerking toe aan "http://httpbin.org/status/200".

### <a name="add-the-operation"></a>Voeg de bewerking toe

1. Selecteer de API die u in de vorige stap hebt gemaakt.
2. Klik op **+ Bewerking toevoegen**.
3. Selecteer **GET** in de **URL** en voer " */status/{code}* " in de resource in. Desgewenst kunt u sommige gegevens die zijn gekoppeld aan deze parameter opgeven. Voer bijvoorbeeld "*getal*" in bij **TYPE**, "*200*" (standaard) bij **VALUES**.
4. Voer "GetStatus" in bij **Weergavenaam**.
5. Selecteer **Opslaan**.

### <a name="test-the-operation"></a>Test de bewerking 

Test de functie in Azure Portal.  U kunt het ook testen in de **Ontwikkelaarsportal**.

1. Selecteer het tabblad **Testen**.
2. Selecteer **GetStatus**. De waarde is standaard ingesteld op "*200*". U kunt deze wijzigen als u andere waarden wilt testen. Typ bijvoorbeeld "*418*".
3. Druk op **Verzenden**.

    Het antwoord dat de "http://httpbin.org/status/200"-bewerking genereert, wordt weergegeven. Als u uw bewerkingen wilt transformeren, gaat u naar [Uw API transformeren en beschermen](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een gepubliceerde API transformeren en beveiligen](transform-api.md)

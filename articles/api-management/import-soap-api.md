---
title: SOAP API importeren met behulp van Azure Portal | Microsoft Docs
description: Informatie over het importeren van SOAP API met API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 22b7b79426829b57f15263f7792dca6596c6641f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073243"
---
# <a name="import-soap-api"></a>SOAP API importeren

Dit artikel laat zien hoe u een standaard XML-representatie van een SOAP API moet importeren. In het artikel wordt ook uitgelegd hoe u de APIM-API kunt testen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * SOAP API importeren
> * De API testen in Azure Portal
> * De API testen in de ontwikkelaarsportal

## <a name="prerequisites"></a>Vereisten

Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Een back-end-API importeren en publiceren

1. Selecteer **API's** bij **API MANAGEMENT**.
2. Selecteer **WSDL** uit de lijst **Nieuwe API toevoegen**.

    ![SOAP API](./media/import-soap-api/wsdl-api.png)
3. Geef in de **WSDL-specificatie** de URL op waar de SOAP API is opgeslagen.
4. Het **SOAP pass-through**-keuzerondje is standaard geselecteerd. Met deze selectie zal de API worden weergegeven als SOAP. De consument moet SOAP-regels gebruiken. Als u van de API een REST API wilt maken, volg dan de stappen in [Import a SOAP API and convert it to REST](restify-soap-api.md) (Een SOAP API importeren en converteren naar REST).

    ![Pass-through](./media/import-soap-api/pass-through.png)
5. Druk op tab.

    De volgende velden worden gevuld met de informatie uit de SOAP-API: Weergave naam, naam en beschrijving.
6. Voeg een achtervoegsel toe van de URL voor de API. Het achtervoegsel is een naam die deze specifieke API in dit APIM-exemplaar identificeert. Hij moet uniek zijn in dit APIM-exemplaar.
9. Publiceer de API door deze aan een product te koppelen. In dit geval wordt het product *onbeperkt* gebruikt.  Als u wilt dat de API wordt gepubliceerd en beschikbaar is voor ontwikkelaars, kunt u deze toevoegen aan een product. U kunt dit doen tijdens het maken van de API of het later instellen.

    Producten zijn koppelingen van een of meer API's. U kunt een aantal API's opnemen en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ontwikkelaars zich abonneren, ontvangen ze een abonnementssleutel die toegang biedt tot elke API in het betreffende product. Als u de APIM-abonnementssleutel hebt gemaakt, bent u al een beheerder en bent u standaard geabonneerd op elk product.

    Standaard wordt elk API Management-exemplaar geleverd met twee voorbeeldproducten:

    * **Starter**
    * **Onbeperkt**   
10. Selecteer **Maken**.

### <a name="test-the-new-apim-api-in-the-administrative-portal"></a>De nieuwe APIM API testen in de administratieve portal

Bewerkingen kunnen rechtstreeks vanuit de administratieve portal worden aangeroepen. Dit is een handige manier om de bewerkingen van een API te bekijken en te testen.  

1. Selecteer de API die u in de vorige stap hebt gemaakt.
2. Druk op het tabblad **Testen**.
3. Selecteer een willekeurige bewerking.

    De pagina geeft velden weer voor queryparameters en velden voor de headers. Een van de headers is Ocp-Apim-Subscription-Key voor de abonnementssleutel van het product dat is gekoppeld aan deze API. Als u de APIM-instantie hebt gemaakt, bent u al een beheerder en wordt de sleutel automatisch ingevoerd. 
1. Druk op **Verzenden**.

    Back-end reageert met **200 OK** en enkele gegevens.

### <a name="call-operation"> </a>Een bewerking aanroepen vanuit de ontwikkelaarsportal

Bewerkingen kunnen ook worden aangeroepen vanuit de **ontwikkelaarsportal** om API's te testen. 

1. Selecteer de API die u hebt gemaakt in de stap voor het importeren en publiceren van een back-end-API.
2. Selecteer de **ontwikkelaarsportal**.

    De site voor de ontwikkelaarsportal wordt geopend.
3. Selecteer de **API** die u hebt gemaakt.
4. Klik op de bewerking die u wilt testen.
5. Druk op **Proberen**.
6. Druk op **Verzenden**.
    
    Nadat een bewerking is aangeroepen, worden in de ontwikkelaarsportal de **antwoordstatus**, de **antwoordheaders** en eventuele **antwoordinhoud** weergegeven.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een gepubliceerde API transformeren en beveiligen](transform-api.md)
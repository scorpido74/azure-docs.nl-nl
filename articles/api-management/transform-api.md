---
title: Uw API transformeren en beveiligen met Azure API Management | Microsoft Docs
description: Informatie over het beveiligen van uw API met beleidsregels voor quota en (frequentie)beperking.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/26/2019
ms.author: apimpm
ms.openlocfilehash: 5dec08bd4bc0a63a419d2bdc63383348a69b02db
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70067472"
---
# <a name="transform-and-protect-your-api"></a>Uw API transformeren en beveiligen

In de zelfstudie ziet u hoe u uw API kunt transformeren zodat deze geen persoonlijke back-endgegevens vrijgeeft. U kunt bijvoorbeeld de gegevens verbergen over de technologiestack die op de back-end wordt uitgevoerd. U kunt ook de oorspronkelijke URL's verbergen die worden weergegeven in de hoofdtekst van het HTTP-antwoord van de API, en deze in plaats hiervan omleiden naar de APIM-gateway.

In deze zelfstudie kunt u ook zien hoe eenvoudig het is om beveiliging toe te voegen voor uw back-end-API door de frequentielimiet te configureren met Azure API Management. U kunt bijvoorbeeld het aantal aanroepen voor de API beperken, zodat deze niet wordt overbelast door ontwikkelaars. Zie [API Management-beleid](api-management-policies.md) voor meer informatie

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> -   Een API transformeren om antwoordheaders te verwijderen
> -   Oorspronkelijke URL's in de hoofdtekst van het API-antwoord vervangen door APIM-gateway-URL's
> -   Een API beveiligen door beleid voor frequentielimieten toe te voegen
> -   De transformaties testen

![Beleidsregels](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>Vereisten

-   Leer de [terminologie van Azure API Management](api-management-terminology.md).
-   Inzicht in het [beleidsconcept in Azure API Management](api-management-howto-policies.md).
-   Lees de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
-   Voltooi tevens de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Een API transformeren om antwoordheaders te verwijderen

In deze sectie wordt beschreven hoe u de HTTP-headers kunt verbergen die u niet wilt weergeven aan gebruikers. In dit voorbeeld worden de volgende headers in het HTTP-antwoord verwijderd:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Het oorspronkelijke antwoord testen

Het oorspronkelijke antwoord zien:

1. Selecteer in het APIM-service-exemplaar **API's** (onder **API MANAGEMENT**).
2. Klik in de API-lijst op **Demo Conference API**.
3. Klik bovenaan het scherm op het tabblad **Testen**.
4. Selecteer de bewerking **GetSpeakers**.
5. Klik onderaan het scherm op de knop **Verzenden**.

Het oorspronkelijke antwoord moet er als volgt uitzien:

![Beleidsregels](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Transformatiebeleid instellen

![Beleid voor uitgaande verwerking instellen](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png)

1. Selecteer **Demo Conference API**.
2. Selecteer boven in het scherm het tabblad **Ontwerp**.
3. Selecteer **Alle bewerkingen**.
4. Klik in de sectie **Uitgaande verwerking** op het pictogram **</>**.
5. Plaats de cursor ** &lt;&gt; ** in het uitgaande element.
6. Klik in het rechtervenster onder **Transformatiebeleid** twee keer op **+ HTTP-header instellen** (om twee beleidsfragmenten in te voegen).

   ![Beleidsregels](./media/transform-api/transform-api.png)

7. Wijzig ** \<** uw uitgaande>code om er als volgt uit te zien:

       <set-header name="X-Powered-By" exists-action="delete" />
       <set-header name="X-AspNet-Version" exists-action="delete" />

   ![Beleidsregels](./media/transform-api/set-policy.png)

8. Klik op de knop **Opslaan**.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Oorspronkelijke URL's in de hoofdtekst van het API-antwoord vervangen door APIM-gateway-URL's

In deze sectie ziet u hoe u de oorspronkelijke URL's kunt verbergen die worden weergegeven in de hoofdtekst van het HTTP-antwoord van de API, en deze in plaats hiervan kunt omleiden naar de APIM-gateway.

### <a name="test-the-original-response"></a>Het oorspronkelijke antwoord testen

Het oorspronkelijke antwoord zien:

1. Selecteer **Demo Conference API**.
2. Klik bovenaan het scherm op het tabblad **Testen**.
3. Selecteer de bewerking **GetSpeakers**.
4. Klik onderaan het scherm op de knop **Verzenden**.

    Zoals u kunt zien, ziet het oorspronkelijke antwoord er als volgt uit:

    ![Beleidsregels](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Transformatiebeleid instellen

1.  Selecteer **Demo Conference API**.
2.  Selecteer **Alle bewerkingen**.
3.  Selecteer boven in het scherm het tabblad **Ontwerp**.
4.  Klik in de sectie **Uitgaande verwerking** op het pictogram **</>**.
5.  Plaats de cursor ** &lt;&gt; ** in het uitgaande element.
6.  Klik in het rechtervenster onder **Transformatiebeleid** twee keer op **+ Tekenreeks in hoofdtekst zoeken en vervangen**.
7.  Wijzig de **find-and-replace**-code (in het **\<uitgaande element\>**) om de URL te vervangen zodat deze overeenkomt met de APIM-gateway. Bijvoorbeeld:

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Een API beveiligen door beleid voor frequentielimieten toe te voegen

In deze sectie wordt beschreven hoe u beveiliging voor uw back-end-API kunt toevoegen door frequentielimieten te configureren. U kunt bijvoorbeeld het aantal aanroepen voor de API beperken, zodat deze niet wordt overbelast door ontwikkelaars. In dit voorbeeld wordt de limiet ingesteld op 3 aanroepen per 15 seconden voor elk abonnement-id. Na 15 seconden kan een ontwikkelaar opnieuw proberen de API aan te roepen.

![Beleid voor binnenkomende verwerking instellen](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png)

1.  Selecteer **Demo Conference API**.
2.  Selecteer **Alle bewerkingen**.
3.  Selecteer boven in het scherm het tabblad **Ontwerp**.
4.  Klik in de sectie **Binnenkomende verwerking** op het pictogram **</>**.
5.  Plaats de cursor in het ** &lt;binnenkomende&gt; ** element.
6.  Klik in het rechtervenster onder **Toegang tot beperkingsbeleid** op **+ Aantal oproepen per sleutel beperken**.
7.  Wijzig uw **rate-limit-by-key**-code (in het **\<inkomende\>** element) in de volgende code:

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>De transformaties testen

Als u nu naar de code in de code-editor kijkt, ziet uw beleid er als volgt uit:

    <policies>
        <inbound>
            <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <set-header name="X-Powered-By" exists-action="delete" />
            <set-header name="X-AspNet-Version" exists-action="delete" />
            <find-and-replace from="://conferenceapi.azurewebsites.net:443" to="://apiphany.azure-api.net/conference"/>
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

In de rest van deze sectie worden de beleidstransformaties getest die u in dit artikel hebt ingesteld.

### <a name="test-the-stripped-response-headers"></a>De verwijderde anwoordheaders testen

1. Selecteer **Demo Conference API**.
2. Selecteer het tabblad **Testen**.
3. Selecteer de bewerking **GetSpeakers**.
4. Druk op **Verzenden**.

    Zoals u ziet, zijn de headers verwijderd:

    ![Beleidsregels](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>De vervangen URL testen

1. Selecteer **Demo Conference API**.
2. Selecteer het tabblad **Testen**.
3. Selecteer de bewerking **GetSpeakers**.
4. Druk op **Verzenden**.

    Zoals u ziet, is de URL vervangen.

    ![Beleidsregels](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>De frequentielimiet testen

1. Selecteer **Demo Conference API**.
2. Selecteer het tabblad **Testen**.
3. Selecteer de bewerking **GetSpeakers**.
4. Druk drie keer achter elkaar op **Verzenden**.

    Nadat de aanvraag 3 keer is verzonden, ontvangt u het antwoord **429 Te veel aanvragen**.

5. Wacht ongeveer 15 seconden en druk opnieuw op **Verzenden**. Deze keer ontvangt u, als het goed is, het antwoord **200 OK**.

    ![Beperking](./media/transform-api/test-throttling.png)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
>
> -   Een API transformeren om antwoordheaders te verwijderen
> -   Oorspronkelijke URL's in de hoofdtekst van het API-antwoord vervangen door APIM-gateway-URL's
> -   Een API beveiligen door beleid voor frequentielimieten toe te voegen
> -   De transformaties testen

Ga door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Uw API controleren](api-management-howto-use-azure-monitor.md)

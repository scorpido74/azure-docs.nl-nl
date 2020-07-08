---
title: Api's beveiligen met verificatie op basis van client certificaten in API Management
titleSuffix: Azure API Management
description: Meer informatie over het beveiligen van de toegang tot Api's met behulp van client certificaten
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76713148"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API's beveiligen met behulp van verificatie via clientcertificaten in API Management

API Management biedt de mogelijkheid om de toegang tot Api's (client naar API Management) te beveiligen met behulp van client certificaten. U kunt het inkomende certificaat valideren en certificaat eigenschappen controleren op basis van de gewenste waarden met behulp van beleids expressies.

Voor informatie over het beveiligen van de toegang tot de back-end-service van een API met behulp van client certificaten (API Management naar back-end), raadpleegt u [back-end-services beveiligen met verificatie op basis van client certificaten](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Als u client certificaten wilt ontvangen en controleren via HTTP/2 in de lagen ontwikkelaar, Basic, Standard of Premium, moet u de instelling ' onderhandelen over client certificaat ' op de Blade ' aangepaste domeinen ' inschakelen, zoals hieronder wordt weer gegeven.

![Onderhandelen over client certificaat](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Als u client certificaten wilt ontvangen en verifiëren in de laag verbruik, moet u de instelling client certificaat aanvragen inschakelen op de Blade aangepaste domeinen, zoals hieronder wordt weer gegeven.

![Client certificaat aanvragen](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>De uitgever en het onderwerp controleren

Onder beleids regels kunnen worden geconfigureerd om de uitgever en het onderwerp van een client certificaat te controleren:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Als u het controleren van de lijst met ingetrokken certificaten wilt uitschakelen, gebruikt u `context.Request.Certificate.VerifyNoRevocation()` in plaats van `context.Request.Certificate.Verify()` .
> Als het client certificaat zelfondertekend is, moeten de basis-(of tussenliggende) CA-certificaten worden [geüpload](api-management-howto-ca-certificates.md) naar API Management for `context.Request.Certificate.Verify()` en `context.Request.Certificate.VerifyNoRevocation()` to work.

## <a name="checking-the-thumbprint"></a>De vinger afdruk controleren

Onder beleids regels kunnen worden geconfigureerd om de vinger afdruk van een client certificaat te controleren:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Als u het controleren van de lijst met ingetrokken certificaten wilt uitschakelen, gebruikt u `context.Request.Certificate.VerifyNoRevocation()` in plaats van `context.Request.Certificate.Verify()` .
> Als het client certificaat zelfondertekend is, moeten de basis-(of tussenliggende) CA-certificaten worden [geüpload](api-management-howto-ca-certificates.md) naar API Management for `context.Request.Certificate.Verify()` en `context.Request.Certificate.VerifyNoRevocation()` to work.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Een vinger afdruk controleren op certificaten die zijn geüpload naar API Management

In het volgende voor beeld ziet u hoe u de vinger afdruk van een client certificaat controleert op certificaten die zijn geüpload naar API Management:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Als u het controleren van de lijst met ingetrokken certificaten wilt uitschakelen, gebruikt u `context.Request.Certificate.VerifyNoRevocation()` in plaats van `context.Request.Certificate.Verify()` .
> Als het client certificaat zelfondertekend is, moeten de basis-(of tussenliggende) CA-certificaten worden [geüpload](api-management-howto-ca-certificates.md) naar API Management for `context.Request.Certificate.Verify()` en `context.Request.Certificate.VerifyNoRevocation()` to work.

> [!TIP]
> Probleem met het publiceren van het client certificaat dat in dit [artikel](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) wordt beschreven, kan zich op verschillende manieren manifesteren, zoals het blok keren van aanvragen, aanvragen met de `403 Forbidden` status code na een time-out `context.Request.Certificate` `null` . Dit probleem is doorgaans `POST` van invloed op en `PUT` aanvragen met een inhouds lengte van ongeveer 60KB of hoger.
> Ga als volgt te werk om te voor komen dat dit probleem optreedt bij het inschakelen van de instelling ' onderhandelen over client certificaat ' voor gewenste hostnamen op de Blade ' aangepaste domeinen ', zoals hieronder wordt weer gegeven. Deze functie is niet beschikbaar in de laag verbruik.

![Onderhandelen over client certificaat](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Volgende stappen

-   [Back-end-services beveiligen met verificatie op basis van client certificaten](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Certificaten uploaden](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

---
title: Api's beveiligen met verificatie op basis van client certificaten in API Management-Azure API Management | Microsoft Docs
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
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 263f8495b9dbb0a1c5b3c54301b4b4deab425e31
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072368"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Api's beveiligen met verificatie op basis van client certificaten in API Management

API Management biedt de mogelijkheid om de toegang tot Api's (client naar API Management) te beveiligen met behulp van client certificaten. U kunt het inkomende certificaat valideren en certificaat eigenschappen controleren op basis van de gewenste waarden met behulp van beleids expressies.

Voor informatie over het beveiligen van de toegang tot de back-end-service van een API met behulp van client certificaten (API Management naar back-end), raadpleegt u [back-end-services beveiligen met verificatie op basis van client certificaten](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Als u client certificaten wilt ontvangen en verifiëren in de laag verbruik, moet u eerst de instelling client certificaat aanvragen inschakelen op de Blade aangepaste domeinen, zoals hieronder wordt weer gegeven.

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
> Als u het controleren van de lijst met `context.Request.Certificate.VerifyNoRevocation()` ingetrokken certificaten `context.Request.Certificate.Verify()`wilt uitschakelen, gebruikt u in plaats van.
> Als het client certificaat zelfondertekend is, moeten de basis-(of tussenliggende) CA-certificaten worden [geüpload](api-management-howto-ca-certificates.md) naar API Management for `context.Request.Certificate.Verify()` en `context.Request.Certificate.VerifyNoRevocation()` to work.

## <a name="checking-the-thumbprint"></a>De vinger afdruk controleren

Onder beleids regels kunnen worden geconfigureerd om de vinger afdruk van een client certificaat te controleren:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Als u het controleren van de lijst met `context.Request.Certificate.VerifyNoRevocation()` ingetrokken certificaten `context.Request.Certificate.Verify()`wilt uitschakelen, gebruikt u in plaats van.
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
> Als u het controleren van de lijst met `context.Request.Certificate.VerifyNoRevocation()` ingetrokken certificaten `context.Request.Certificate.Verify()`wilt uitschakelen, gebruikt u in plaats van.
> Als het client certificaat zelfondertekend is, moeten de basis-(of tussenliggende) CA-certificaten worden [geüpload](api-management-howto-ca-certificates.md) naar API Management for `context.Request.Certificate.Verify()` en `context.Request.Certificate.VerifyNoRevocation()` to work.

> [!TIP]
> Probleem met het publiceren van het client certificaat dat in dit [artikel](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) wordt beschreven, kan zich op verschillende manieren manifesteren, `403 Forbidden` zoals het blok keren van aanvragen `context.Request.Certificate` , `null`aanvragen met de status code na een time-out. Dit probleem is doorgaans `POST` van `PUT` invloed op en aanvragen met een inhouds lengte van ongeveer 60KB of hoger.
> Als u wilt voor komen dat dit probleem optreedt, schakelt u de instelling onderhandelend client certificaat in voor gewenste hostnamen op de Blade ' aangepaste domeinen ', zoals hieronder wordt weer gegeven. Deze functie is niet beschikbaar in de laag verbruik.

![Onderhandelen over clientcertificaat](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Volgende stappen

-   [Back-end-services beveiligen met verificatie op basis van client certificaten](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Certificaten uploaden](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

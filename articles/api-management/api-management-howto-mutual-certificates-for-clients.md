---
title: Beveiligde API's met verificatie clientcertificaat in API-beheer
titleSuffix: Azure API Management
description: Meer informatie over het beveiligen van toegang tot API's met clientcertificaten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76713148"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API's beveiligen met behulp van verificatie via clientcertificaten in API Management

API-beheer biedt de mogelijkheid om toegang tot API's (d.w.z. client-naar-API-beheer) te beveiligen met behulp van clientcertificaten. U binnenkomend certificaat valideren en certificaateigenschappen controleren op de gewenste waarden met behulp van beleidsexpressies.

Zie [Back-endservices beveiligen met clientcertificaatverificatie](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) voor informatie over het beveiligen van toegang tot de back-endservice van een API met clientcertificaten (d.w.z. API-beheer naar backend)

> [!IMPORTANT]
> Als u clientcertificaten wilt ontvangen en verifiëren via HTTP/2 in de lagen Ontwikkelaars, Basic, Standard of Premium, moet u de instelling 'Onderhandelen over clientcertificaat' inschakelen op het blad 'Aangepaste domeinen', zoals hieronder wordt weergegeven.

![Onderhandelen over clientcertificaat](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Als u clientcertificaten in de categorie Verbruik wilt ontvangen en verifiëren, moet u de instelling Clientcertificaat aanvragen inschakelen op het blad 'Aangepaste domeinen', zoals hieronder wordt weergegeven.

![Clientcertificaat aanvragen](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Controle van de uitgevende instelling en het onderwerp

Onderstaand beleid kan worden geconfigureerd om de uitgever te controleren en het onderwerp van een clientcertificaat:

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
> Het gebruik `context.Request.Certificate.VerifyNoRevocation()` van de intrekkingslijst voor het controleren van certificaten uitschakelen in plaats van `context.Request.Certificate.Verify()`.
> Als clientcertificaat zelf is ondertekend, moeten root (of intermediaire) CA-certificaat(s) worden [geüpload](api-management-howto-ca-certificates.md) naar API-beheer voor `context.Request.Certificate.Verify()` en `context.Request.Certificate.VerifyNoRevocation()` naar het werk.

## <a name="checking-the-thumbprint"></a>De duimafdruk controleren

Hieronder u de duimafdruk van een clientcertificaat controleren:

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
> Het gebruik `context.Request.Certificate.VerifyNoRevocation()` van de intrekkingslijst voor het controleren van certificaten uitschakelen in plaats van `context.Request.Certificate.Verify()`.
> Als clientcertificaat zelf is ondertekend, moeten root (of intermediaire) CA-certificaat(s) worden [geüpload](api-management-howto-ca-certificates.md) naar API-beheer voor `context.Request.Certificate.Verify()` en `context.Request.Certificate.VerifyNoRevocation()` naar het werk.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Een duimafdruk controleren op certificaten die zijn geüpload naar API-beheer

In het volgende voorbeeld ziet u hoe u de duimafdruk van een clientcertificaat controleren op certificaten die zijn geüpload naar API-beheer:

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
> Het gebruik `context.Request.Certificate.VerifyNoRevocation()` van de intrekkingslijst voor het controleren van certificaten uitschakelen in plaats van `context.Request.Certificate.Verify()`.
> Als clientcertificaat zelf is ondertekend, moeten root (of intermediaire) CA-certificaat(s) worden [geüpload](api-management-howto-ca-certificates.md) naar API-beheer voor `context.Request.Certificate.Verify()` en `context.Request.Certificate.VerifyNoRevocation()` naar het werk.

> [!TIP]
> Client certificaat impasse probleem beschreven in dit [artikel](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) kan zich manifesteren op verschillende `403 Forbidden` manieren, bijvoorbeeld `context.Request.Certificate` aanvragen `null`bevriezen, verzoeken resulteren in statuscode na timing uit, is . Dit probleem heeft `POST` `PUT` meestal invloed op en aanvragen met inhoudslengte van ongeveer 60 KB of groter.
> Als u wilt voorkomen dat dit probleem optreedt, schakelt u de instelling 'Onderhandelen over clientcertificaat' in voor de gewenste hostnamen op het blad 'Aangepaste domeinen', zoals hieronder wordt weergegeven. Deze functie is niet beschikbaar in de laag Verbruik.

![Onderhandelen over clientcertificaat](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Volgende stappen

-   [Back-endservices beveiligen met verificatie van clientcertificaat](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Certificaten uploaden](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

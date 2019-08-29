---
title: Azure API Management-verificatie beleid | Microsoft Docs
description: Meer informatie over de verificatie beleidsregels die beschikbaar zijn voor gebruik in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 69584b434ac0442df48dcdea2a7d9f2aca9c1ccd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073744"
---
# <a name="api-management-authentication-policies"></a>Verificatie beleid voor API Management
In dit onderwerp vindt u een verwijzing naar de volgende API Management-beleids regels. Zie [beleid in API Management](https://go.microsoft.com/fwlink/?LinkID=398186)voor meer informatie over het toevoegen en configureren van beleid.

##  <a name="AuthenticationPolicies"></a>Verificatie beleid

-   [Authenticatie met Basic](api-management-authentication-policies.md#Basic) : verifiëren met een back-end-service met basis verificatie.

-   [Verificatie met client certificaat](api-management-authentication-policies.md#ClientCertificate) : Verifieer met een back-end-service met behulp van client certificaten.

-   [Verificatie met beheerde identiteit](api-management-authentication-policies.md#ManagedIdentity) verificatie met de [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) voor de API Management service.

##  <a name="Basic"></a>Verifiëren met Basic
 Gebruik het `authentication-basic` beleid om met een back-end-service te verifiëren met behulp van basis verificatie. Met dit beleid wordt de HTTP-autorisatie-header in feite ingesteld op de waarde die overeenkomt met de referenties die zijn opgenomen in het beleid.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Voorbeeld

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementen

|Name|Description|Vereist|
|----------|-----------------|--------------|
|verificatie-basis|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

|Name|Description|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|username|Hiermee geeft u de gebruikers naam van de basis referentie.|Ja|N/A|
|password|Hiermee geeft u het wacht woord van de basis referentie op.|Ja|N/A|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleids secties:** binnenkomend

-   **Beleids bereik:** alle bereiken

##  <a name="ClientCertificate"></a>Verifiëren met client certificaat
 Gebruik het `authentication-certificate` beleid om te verifiëren met een back-end-service met behulp van het client certificaat. Het certificaat moet eerst worden [geïnstalleerd in API Management](https://go.microsoft.com/fwlink/?LinkID=511599) en wordt geïdentificeerd door de vinger afdruk.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Voorbeelden

In dit voor beeld wordt het client certificaat aangeduid met de vinger afdruk.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
In dit voor beeld wordt het client certificaat geïdentificeerd op basis van de resource naam.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementen  
  
|Name|Description|Vereist|  
|----------|-----------------|--------------|  
|verificatie-certificaat|Hoofd element.|Ja|  
  
### <a name="attributes"></a>Kenmerken  
  
|Name|Description|Vereist|Standaard|  
|----------|-----------------|--------------|-------------|  
|thumbprint|De vinger afdruk voor het client certificaat.|Ofwel `thumbprint` of`certificate-id` moeten aanwezig zijn.|N/A|  
|certificaat-id|De naam van de certificaat resource.|Ofwel `thumbprint` of`certificate-id` moeten aanwezig zijn.|N/A|  
  
### <a name="usage"></a>Gebruik  
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Beleids secties:** binnenkomend  
  
-   **Beleids bereik:** alle bereiken  

##  <a name="ManagedIdentity"></a>Verifiëren met beheerde identiteit  
 Gebruik het `authentication-managed-identity` beleid om te verifiëren met een back-end-service met behulp van de beheerde identiteit van de API Management service. Dit beleid gebruikt in feite de beheerde identiteit voor het verkrijgen van een toegangs token van Azure Active Directory voor toegang tot de opgegeven resource. Nadat het token is verkregen, wordt met het beleid de waarde van het token in de `Authorization` header ingesteld met `Bearer` behulp van het schema.
  
### <a name="policy-statement"></a>Beleids verklaring  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Voorbeeld  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Beheerde identiteit gebruiken om te verifiëren met een back-end-service
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Beheerde identiteit gebruiken in beleid voor verzenden en aanvragen
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementen  
  
|Name|Description|Vereist|  
|----------|-----------------|--------------|  
|verificatie-beheerd identiteit |Hoofd element.|Ja|  
  
### <a name="attributes"></a>Kenmerken  
  
|Name|Description|Vereist|Standaard|  
|----------|-----------------|--------------|-------------|  
|resource|Tekenreeksexpressie. De App-ID-URI van de doel-Web-API (beveiligde bron) in Azure Active Directory.|Ja|N/A|  
|uitvoer-token-variabele-naam|Tekenreeksexpressie. De naam van de context variabele die de token waarde ontvangt als object type `string`. |Nee|N/A|  
|negeren-fout|True. Als deze is `true`ingesteld op, blijft de beleids pijplijn worden uitgevoerd, zelfs als er geen toegangs token is verkregen.|Nee|false|  
  
### <a name="usage"></a>Gebruik  
 Dit beleid kan worden gebruikt in de volgende beleids [secties](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [bereiken](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Beleids secties:** binnenkomend  
  
-   **Beleids bereik:** alle bereiken

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over het gebruik van beleid:

+ [Beleid in API Management](api-management-howto-policies.md)
+ [Api's transformeren](transform-api.md)
+ [Beleids verwijzing](api-management-policy-reference.md) voor een volledige lijst met beleids instructies en hun instellingen
+ [Voor beelden van beleid](policy-samples.md)

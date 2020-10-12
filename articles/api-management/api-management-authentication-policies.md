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
ms.date: 06/12/2020
ms.author: apimpm
ms.openlocfilehash: 4d077f6b3c84b0279a7a1c99243240192c2b45d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86243712"
---
# <a name="api-management-authentication-policies"></a>API Management-verificatiebeleid
In dit onderwerp vindt u een verwijzing naar de volgende API Management-beleids regels. Zie [beleid in API Management](https://go.microsoft.com/fwlink/?LinkID=398186)voor meer informatie over het toevoegen en configureren van beleid.

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> Verificatie beleid

-   [Authenticatie met Basic](api-management-authentication-policies.md#Basic) : verifiëren met een back-end-service met basis verificatie.

-   [Verificatie met client certificaat](api-management-authentication-policies.md#ClientCertificate) : Verifieer met een back-end-service met behulp van client certificaten.

-   [Verificatie met beheerde identiteit](api-management-authentication-policies.md#ManagedIdentity) verificatie met de [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) voor de API Management service.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> Verifiëren met Basic
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

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|verificatie-basis|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|gebruikersnaam|Hiermee geeft u de gebruikers naam van de basis referentie.|Ja|N.v.t.|
|wachtwoord|Hiermee geeft u het wacht woord van de basis referentie op.|Ja|N.v.t.|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

-   **Beleids secties:** binnenkomend

-   **Beleids bereik:** alle bereiken

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> Verifiëren met client certificaat
 Gebruik het `authentication-certificate` beleid om te verifiëren met een back-end-service met behulp van het client certificaat. Het certificaat moet eerst worden [geïnstalleerd in API Management](https://go.microsoft.com/fwlink/?LinkID=511599) en wordt geïdentificeerd door de vinger afdruk.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Voorbeelden

In dit voor beeld wordt het client certificaat aangeduid met de vinger afdruk:

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```

In dit voor beeld wordt het client certificaat aangeduid met de naam van de resource:

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

In dit voor beeld wordt het client certificaat ingesteld in het beleid in plaats van opgehaald uit het ingebouwde certificaat archief:

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>Elementen  
  
|Naam|Beschrijving|Vereist|  
|----------|-----------------|--------------|  
|verificatie-certificaat|Hoofd element.|Ja|  
  
### <a name="attributes"></a>Kenmerken  
  
|Naam|Beschrijving|Vereist|Standaard|  
|----------|-----------------|--------------|-------------|  
|vingerafdruk|De vinger afdruk voor het client certificaat.|Ofwel `thumbprint` of `certificate-id` moeten aanwezig zijn.|N.v.t.|
|certificaat-id|De naam van de certificaat resource.|Ofwel `thumbprint` of `certificate-id` moeten aanwezig zijn.|N.v.t.|
|body|Client certificaat als een byte matrix.|Nee|N.v.t.|
|wachtwoord|Het wacht woord voor het client certificaat.|Wordt gebruikt als het certificaat dat is opgegeven in `body` , is beveiligd met een wacht woord.|N.v.t.|
  
### <a name="usage"></a>Gebruik  
 Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).  
  
-   **Beleids secties:** binnenkomend  
  
-   **Beleids bereik:** alle bereiken  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> Verifiëren met beheerde identiteit  
 Gebruik het `authentication-managed-identity` beleid om te verifiëren met een back-end-service met behulp van de beheerde identiteit. Dit beleid gebruikt in feite de beheerde identiteit voor het verkrijgen van een toegangs token van Azure Active Directory voor toegang tot de opgegeven resource. Nadat het token is verkregen, wordt met het beleid de waarde van het token in de `Authorization` header ingesteld met behulp van het `Bearer` schema.

Zowel door het systeem toegewezen identiteit als een van de door de gebruiker toegewezen identiteit kan worden gebruikt om een token aan te vragen. Als `client-id` er geen door het systeem toegewezen identiteit wordt gebruikt, wordt ervan uitgegaan. Als de `client-id` variabele is opgegeven, wordt er een token aangevraagd voor de door de gebruiker toegewezen identiteit van Azure Active Directory
  
### <a name="policy-statement"></a>Beleids verklaring  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Voorbeeld  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Beheerde identiteit gebruiken om te verifiëren met een back-end-service
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Bus-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>Beheerde identiteit hand matig instellen

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
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
  
|Naam|Beschrijving|Vereist|  
|----------|-----------------|--------------|  
|verificatie-beheerd identiteit |Hoofd element.|Ja|  
  
### <a name="attributes"></a>Kenmerken  
  
|Naam|Beschrijving|Vereist|Standaard|  
|----------|-----------------|--------------|-------------|  
|resource|Tekenreeks. De App-ID van de doel-Web-API (beveiligde bron) in Azure Active Directory.|Ja|N.v.t.|
|client-id|Tekenreeks. De App-ID van de door de gebruiker toegewezen identiteit in Azure Active Directory.|Nee|door het systeem toegewezen identiteit|
|uitvoer-token-variabele-naam|Tekenreeks. De naam van de context variabele die de token waarde ontvangt als object type `string` . |Nee|N.v.t.|  
|negeren-fout|True. Als `true` deze is ingesteld op, blijft de beleids pijplijn worden uitgevoerd, zelfs als er geen toegangs token is verkregen.|Nee|onjuist|  
  
### <a name="usage"></a>Gebruik  
 Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).  
  
-   **Beleids secties:** binnenkomend  
  
-   **Beleids bereik:** alle bereiken

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over het gebruik van beleid:

+ [Beleid in API Management](api-management-howto-policies.md)
+ [Api's transformeren](transform-api.md)
+ [Beleids verwijzing](./api-management-policies.md) voor een volledige lijst met beleids instructies en hun instellingen
+ [Voor beelden van beleid](policy-samples.md)

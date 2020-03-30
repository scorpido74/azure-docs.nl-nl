---
title: Verificatiebeleid voor Azure API-beheer | Microsoft Documenten
description: Meer informatie over het verificatiebeleid dat beschikbaar is voor gebruik in Azure API Management.
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
ms.openlocfilehash: 828f738ff8923dc8194e2449f5fb0be74ef45ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473554"
---
# <a name="api-management-authentication-policies"></a>API Management-verificatiebeleid
In dit onderwerp wordt een verwijzing naar het volgende API-beheerbeleid bevat. Zie [Beleid in API-beheer](https://go.microsoft.com/fwlink/?LinkID=398186)voor informatie over het toevoegen en configureren van beleidsregels.

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a>Verificatiebeleid

-   [Authenticeer met Basic](api-management-authentication-policies.md#Basic) - Verifieer met een backendservice met basisverificatie.

-   [Authenticeer met clientcertificaat](api-management-authentication-policies.md#ClientCertificate) - Verifieer met een backendservice met clientcertificaten.

-   [Authenticeer met beheerde identiteit](api-management-authentication-policies.md#ManagedIdentity) - Verifieer met de [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) voor de API Management-service.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a>Verifiëren met Basic
 Gebruik `authentication-basic` het beleid om te verifiëren met een backendservice met basisverificatie. Met dit beleid wordt de kopvan de HTTP-autorisatie effectief ingesteld op de waarde die overeenkomt met de referenties die in het beleid worden opgegeven.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Voorbeeld

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementen

|Name|Beschrijving|Vereist|
|----------|-----------------|--------------|
|verificatie-basic|Hoofdelement.|Ja|

### <a name="attributes"></a>Kenmerken

|Name|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|gebruikersnaam|Hiermee geeft u de gebruikersnaam van de basisreferentie op.|Ja|N.v.t.|
|wachtwoord|Hiermee geeft u het wachtwoord op van de basisreferentie.|Ja|N.v.t.|

### <a name="usage"></a>Gebruik
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Beleidsonderdelen:** binnenkomend

-   **Beleidsscopes:** alle scopes

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a>Verifiëren met clientcertificaat
 Gebruik `authentication-certificate` het beleid om te verifiëren met een back-endservice met behulp van clientcertificaat. Het certificaat moet eerst [worden geïnstalleerd in API Management](https://go.microsoft.com/fwlink/?LinkID=511599) en wordt geïdentificeerd door de duimafdruk.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Voorbeelden

In dit voorbeeld wordt het clientcertificaat geïdentificeerd aan de duimafdruk.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
In dit voorbeeld wordt clientcertificaat aangeduid met de naam van de resource.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementen  
  
|Name|Beschrijving|Vereist|  
|----------|-----------------|--------------|  
|verificatie-certificaat|Hoofdelement.|Ja|  
  
### <a name="attributes"></a>Kenmerken  
  
|Name|Beschrijving|Vereist|Standaard|  
|----------|-----------------|--------------|-------------|  
|Vingerafdruk|De duimafdruk voor het clientcertificaat.|Of `thumbprint` `certificate-id` moet aanwezig zijn.|N.v.t.|  
|certificaat-id|De naam van de certificaatbron.|Of `thumbprint` `certificate-id` moet aanwezig zijn.|N.v.t.|  
  
### <a name="usage"></a>Gebruik  
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Beleidsonderdelen:** binnenkomend  
  
-   **Beleidsscopes:** alle scopes  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a>Verifiëren met beheerde identiteit  
 Gebruik `authentication-managed-identity` het beleid om te verifiëren met een backendservice met de beheerde identiteit van de API-beheerservice. Dit beleid gebruikt in wezen de beheerde identiteit om een toegangstoken van Azure Active Directory te verkrijgen voor toegang tot de opgegeven bron. Nadat het token met succes is verkregen, stelt `Authorization` het beleid `Bearer` de waarde van het token in de koptekst in met behulp van het schema.
  
### <a name="policy-statement"></a>Beleidsverklaring  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Voorbeeld  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Beheerde identiteit gebruiken om te verifiëren met een backendservice
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
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Beheerde identiteit gebruiken in verzendaanvraagbeleid
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementen  
  
|Name|Beschrijving|Vereist|  
|----------|-----------------|--------------|  
|verificatie-beheerde identiteit |Hoofdelement.|Ja|  
  
### <a name="attributes"></a>Kenmerken  
  
|Name|Beschrijving|Vereist|Standaard|  
|----------|-----------------|--------------|-------------|  
|resource|Tekenreeks. De App-id van de doelweb-API (beveiligde bron) in Azure Active Directory.|Ja|N.v.t.|  
|naam uitvoer-token-variabele|Tekenreeks. Naam van de contextvariabele die tokenwaarde `string`ontvangt als objecttype . |Nee|N.v.t.|  
|negeren-fout|Booleaanse. Als ingesteld `true`op , wordt de beleidspijplijn voortgezet, zelfs als een toegangstoken niet wordt verkregen.|Nee|false|  
  
### <a name="usage"></a>Gebruik  
 Dit beleid kan worden gebruikt in de volgende [beleidsonderdelen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) en [-scopes](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Beleidsonderdelen:** binnenkomend  
  
-   **Beleidsscopes:** alle scopes

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over beleid:

+ [Beleid in API-beheer](api-management-howto-policies.md)
+ [API's transformeren](transform-api.md)
+ [Beleidsreferentie](api-management-policy-reference.md) voor een volledige lijst met beleidsoverzichten en hun instellingen
+ [Beleidsvoorbeelden](policy-samples.md)

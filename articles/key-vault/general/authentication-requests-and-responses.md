---
title: Verificatie, vragen en antwoorden
description: Verifiëren bij AD voor het gebruik van Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 33e3bc13e67e268b82bf517033b4b1c7c51c361f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430888"
---
# <a name="authentication-requests-and-responses"></a>Verificatie, vragen en antwoorden

Azure Key Vault ondersteunt OPGEMAAKTE AANVRAGEN en reacties op een JSON-indeling. Aanvragen voor de Azure Key Vault worden doorgestuurd naar een geldige AZURE Key Vault-URL met HTTPS met enkele URL-parameters en JSON-gecodeerde aanvraag- en antwoordinstanties.

In dit onderwerp worden details voor de Azure Key Vault-service besproken. Zie [Azure REST API Reference](https://docs.microsoft.com/rest/api/azure)voor algemene informatie over het gebruik van Azure REST-interfaces, waaronder verificatie/autorisatie en het aanschaffen van een toegangstoken.

## <a name="request-url"></a>Aanvraag-URL  
 Sleutelbeheerbewerkingen gebruiken HTTP DELETE, GET, PATCH, PUT en HTTP POST en cryptografische bewerkingen tegen bestaande belangrijke objecten gebruiken HTTP POST. Clients die specifieke HTTP-werkwoorden niet kunnen ondersteunen, kunnen HTTP POST ook gebruiken met de koptekst X-HTTP-REQUEST om het beoogde werkwoord op te geven. verzoeken waarvoor normaal gesproken geen lichaam nodig is, moeten een lege instantie bevatten bij het gebruik van HTTP POST, bijvoorbeeld bij het gebruik van POST in plaats van DELETE.  

 Als u wilt werken met objecten in de Azure Key Vault, worden de volgende URL's als volgt gegeven:  

- Een sleutel maken die TESTKEY wordt genoemd in een sleutelkluisgebruik -`PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Een sleutel met de naam IMPORT IMPORTEREN in een key vault-gebruik -`POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Om een geheim genaamd MYSECRET in een Key Vault gebruik -`GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Een samenvatting ondertekenen met een sleutel genaamd TESTKEY in een sleutelkluisgebruik -`POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  De autoriteit voor een verzoek om een Key Vault is altijd als volgt,`https://{keyvault-name}.vault.azure.net/`  

  Sleutels worden altijd opgeslagen onder de / sleutels pad, Geheimen worden altijd opgeslagen onder de / geheimen pad.  

## <a name="api-version"></a>API-versie  
 De Azure Key Vault Service ondersteunt protocolversiewerk om compatibiliteit te bieden met down-level clients, hoewel niet alle mogelijkheden beschikbaar zijn voor deze clients. Clients moeten `api-version` de parameter querytekenreeks gebruiken om de versie van het protocol op te geven dat ze ondersteunen omdat er geen standaardinstelling is.  

 De versies van het Azure Key Vault-protocol volgen een datumnummeringsschema met een {YYYY}. {MM}. {DD}-indeling.  

## <a name="request-body"></a>Aanvraagtekst  
 Volgens de HTTP-specificatie mogen GET-bewerkingen GEEN aanvraaginstantie hebben en moeten POST- en PUT-bewerkingen een aanvraaginstantie hebben. De hoofdtekst in delete-bewerkingen is optioneel in HTTP.  

 Tenzij anders vermeld in de beschrijving van de werking, moet het inhoudstype van de aanvraag-instantie worden aangeschreven/iJson en moet het een geserialiseerd JSON-object bevatten dat voldoet aan het inhoudstype.  

 Tenzij anders vermeld in de beschrijving van de bewerking, moet de koptekst Aanvraag accepteren het mediatype toepassing/json bevatten.  

## <a name="response-body"></a>Reactie-instantie  
 Tenzij anders vermeld in de beschrijving van de werking, de reactie body inhoud type van zowel succesvolle en mislukte bewerkingen zal worden toepassing / json en bevat gedetailleerde foutinformatie.  

## <a name="using-http-post"></a>HTTP POST gebruiken  
 Sommige clients kunnen bepaalde HTTP-werkwoorden, zoals PATCH of DELETE, mogelijk niet gebruiken. Azure Key Vault ondersteunt HTTP POST als alternatief voor deze clients, op voorwaarde dat de client ook de koptekst 'X-HTTP-METHODE' bevat om het oorspronkelijke HTTP-werkwoord specifiek te maken. Ondersteuning voor HTTP POST wordt opgemerkt voor elk van de API die in dit document is gedefinieerd.  

## <a name="error-responses"></a>Foutreacties  
 Voor foutafhandeling worden HTTP-statuscodes gebruikt. Typische resultaten zijn:  

- 2xx – Succes: Gebruikt voor normaal gebruik. De responsinstantie zal het verwachte resultaat  

- 3xx – Omleiding: De 304 "Not Modified" kan worden geretourneerd om te voldoen aan een voorwaardelijke GET. Andere 3xx-codes kunnen in de toekomst worden gebruikt om DNS- en padwijzigingen aan te geven.  

- 4xx – Clientfout: gebruikt voor foute aanvragen, ontbrekende sleutels, syntaxisfouten, ongeldige parameters, verificatiefouten, enz. De antwoordinstantie bevat gedetailleerde foutuitleg.  

- 5xx – Serverfout: wordt gebruikt voor interne serverfouten. De antwoordtekst bevat samengevatte foutinformatie.  

  Het systeem is ontworpen om te werken achter een proxy of firewall. Daarom kan een client andere foutcodes ontvangen.  

  Azure Key Vault retourneert ook foutgegevens in de reactieinstantie wanneer zich een probleem voordoet. De responsbody is JSON geformatteerd en neemt de vorm aan:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Verificatie  
 Alle aanvragen voor Azure Key Vault moeten worden geverifieerd. Azure Key Vault ondersteunt Azure Active Directory-toegangstokens die kunnen worden verkregen met OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Zie [Uw clienttoepassing registreren bij Azure AD](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad)voor meer informatie over het registreren van uw toepassing en het verifiëren om Azure Key Vault te gebruiken.
 
 Toegangstokens moeten naar de service worden verzonden met de kop-inhoud http-autorisatie:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Wanneer een toegangstoken niet wordt geleverd of wanneer een token niet door de service wordt geaccepteerd, wordt een HTTP 401-fout teruggestuurd naar de client en wordt bijvoorbeeld de HEADER WWW-Authenticate opgenomen:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 De parameters op de HEADER WWW-Authenticate zijn:  

-   autorisatie: het adres van de OAuth2-autorisatieservice die kan worden gebruikt om een toegangstoken voor de aanvraag te verkrijgen.  

-   resource: de naam van`https://vault.azure.net`de resource ( ) die moet worden gebruikt in de autorisatieaanvraag.  


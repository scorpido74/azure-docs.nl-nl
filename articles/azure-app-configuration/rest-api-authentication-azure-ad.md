---
title: Azure Active Directory REST API-verificatie
description: Gebruik Azure Active Directory om te verifiëren bij Azure-app configuratie met behulp van de REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424017"
---
# <a name="azure-active-directory-authentication"></a>Verificatie via Azure Active Directory

HTTP-aanvragen kunnen worden geverifieerd met behulp van een **Bearer** -verificatie schema met een token dat is verkregen van Azure Active Directory (Azure AD). Deze aanvragen moeten via TLS worden verzonden.

## <a name="prerequisites"></a>Vereisten

De principal die wordt gebruikt om een Azure AD-token aan te vragen, moet worden toegewezen aan een van de toepasselijke [app-configuratie rollen](./rest-api-authorization-azure-ad.md)

Geef elke aanvraag met alle HTTP-headers die vereist zijn voor authenticatie. De mini maal vereiste waarde is:

|  Aanvraagkoptekst | Beschrijving  |
| --------------- | ------------ |
| **Autorisatie** | Verificatie-informatie vereist door **Bearer** -schema. Indeling en Details worden hieronder uitgelegd. |

**Voorbeeld:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>Aanschaf van Azure Active Directory-tokens

Alvorens een Azure AD-token op te halen, moet worden bepaald welke gebruiker ze wil verifiëren, op welke doel groep ze het token aanvragen en wat Azure AD-eind punt (instantie) ze moeten gebruiken.

### <a name="audience"></a>Doelgroep

Het Azure AD-token moet worden aangevraagd met een juiste doel groep. Voor Azure-app configuratie moet een van de volgende doel groepen worden opgegeven bij het aanvragen van een token. De doel groep kan ook worden aangeduid als de ' resource ' waarvoor het token wordt aangevraagd.

- {configurationStoreName}. azconfig. io
- *. azconfig.io

> [!IMPORTANT]
> Wanneer de aangevraagde doel groep {configurationStoreName}. azconfig. io is, moet deze exact overeenkomen met de aanvraag header van de host (hoofdletter gevoelig) die wordt gebruikt om de aanvraag te verzenden.

### <a name="azure-ad-authority"></a>Azure AD-instantie

De Azure AD-instantie is het eind punt dat wordt gebruikt voor het verkrijgen van een Azure AD-token. Deze heeft de vorm van `https://login.microsoftonline.com/{tenantId}` . Het `{tenantId}` segment verwijst naar de Azure Active Directory Tenant-id waarmee de gebruiker/toepassing die de verificatie uitvoert, behoort.

### <a name="authentication-libraries"></a>Verificatiebibliotheken

Azure biedt een set bibliotheken met de naam Azure Active Directory Authentication libraries (ADAL) om het proces van het ophalen van een Azure AD-token te vereenvoudigen. Deze bibliotheken zijn gebouwd voor meerdere talen. Documentatie vindt u [hier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).

## <a name="errors"></a>**Fouten**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Reden:** De header van de autorisatie aanvraag met een Bearer-schema is niet gegeven.
**Oplossing:** Geldige ```Authorization``` HTTP-aanvraag header opgeven

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Reden:** Het Azure AD-token is ongeldig.
**Oplossing:** Verkrijg een Azure AD-token van de Azure AD-instantie en zorg ervoor dat de juiste doel groep wordt gebruikt.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Reden:** Het Azure AD-token is ongeldig.
**Oplossing:** Verkrijg een Azure AD-token van de Azure AD-certificerings instantie en zorg ervoor dat de Azure AD-Tenant is gekoppeld aan het abonnement, waartoe het configuratie archief behoort. Deze fout kan optreden als de principal deel uitmaakt van meer dan één Azure AD-Tenant.

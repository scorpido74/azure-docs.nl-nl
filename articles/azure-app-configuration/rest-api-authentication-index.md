---
title: Azure-app configuratie REST API-verificatie
description: Referentie pagina's voor verificatie met behulp van de Azure-app configuratie REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 21a43a005b78c8916d06e97ca9d2ba21d5a585a3
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424332"
---
# <a name="authentication"></a>Verificatie

Alle HTTP-aanvragen moeten worden geverifieerd. De volgende verificatie schema's worden ondersteund.

## <a name="hmac"></a>HMAC

[HMAC-verificatie](./rest-api-authentication-hmac.md) maakt gebruik van een wille keurig gegenereerd geheim voor het ondertekenen van nettoladingen van aanvragen. Details over hoe aanvragen die gebruikmaken van deze verificatie methode worden geautoriseerd, vindt u in de sectie [HMAC-autorisatie](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory-verificatie (Azure AD)](/azure/active-directory/authentication/overview-authentication) maakt gebruik van een Bearer-token dat is verkregen van Azure Active Directory om aanvragen te verifiëren. Meer informatie over hoe aanvragen die gebruikmaken van deze verificatie methode, kunt u vinden in de sectie over [Azure AD-autorisatie](./rest-api-authorization-azure-ad.md) .

---
title: Azure-app configuratie REST API-Azure Active Directory autorisatie
description: Gebruik Azure Active Directory voor verificatie op basis van Azure-app configuratie met behulp van de REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bebab7c06062726f7b5c7868f984cadda3b4c98e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424013"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory autorisatie-REST API verwijzing

Als Azure Active Directory-verificatie (Azure AD) wordt gebruikt, wordt de autorisatie verwerkt door de Azure Role based Access Control (RBAC). Voor Azure RBAC moeten gebruikers worden toegewezen aan rollen om toegang tot resources te verlenen. Elke rol bevat een reeks acties die gebruikers aan de rol hebben toegewezen, kunnen worden uitgevoerd.

## <a name="roles"></a>Rollen

De volgende rollen zijn ingebouwde rollen die standaard beschikbaar zijn in azure-abonnementen:

- **Azure-app eigenaar van de configuratie gegevens** : deze rol biedt volledige toegang tot alle bewerkingen.
- **Azure-app-configuratie gegevens lezer** : met deze rol kunnen Lees bewerkingen worden uitgevoerd.

## <a name="actions"></a>Acties

Rollen bevatten een lijst met acties die gebruikers aan deze rol kunnen uitvoeren. De configuratie van Azure-app ondersteunt de volgende acties:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Met deze actie wordt lees toegang tot de app-configuratie sleutel-value-resources, zoals/KV en/labels., toegestaan
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Met deze actie wordt schrijf toegang tot de resources van de app-configuratie sleutel waarde toegestaan.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Met deze actie kunnen de app-configuratie sleutel-value-resources worden verwijderd. Opmerking: als u een resource verwijdert, wordt de verwijderde sleutel waarde geretourneerd.

## <a name="errors"></a>Fouten

```http
HTTP/1.1 403 Forbidden
```

**Reden:** De principal die de aanvraag maakt, beschikt niet over de vereiste machtigingen om de aangevraagde bewerking uit te voeren.
**Oplossing:** Wijs de vereiste rol toe om de aangevraagde bewerking uit te voeren op de principal die de aanvraag heeft ingediend.

## <a name="managing-role-assignments"></a>Roltoewijzingen beheren

Het beheren van roltoewijzingen wordt uitgevoerd met behulp van [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) -procedures die standaard zijn voor alle Azure-Services. U kunt dit doen via Azure CLI, Power shell, de Azure Portal en meer. Officiële documentatie over het maken van roltoewijzingen vindt u [hier](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

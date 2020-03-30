---
title: Een zelf gehoste gateway inrichten in Azure API Management | Microsoft Documenten
description: Meer informatie over het inrichten van een zelfgehoste gateway in Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075294"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Een zelf gehoste gateway inrichten in Azure API-beheer

Het inrichten van een gatewaybron in uw Azure API Management-exemplaar is een vereiste voor het implementeren van een zelf gehoste gateway. In dit artikel worden de stappen doorlopen om een gatewaybron in API-beheer in te richten.

> [!NOTE]
> Zelf gehoste gateway-functie is in preview. Tijdens de preview is de zelf gehoste gateway alleen beschikbaar in de lagen Developer en Premium zonder extra kosten. De ontwikkelaarslaag is beperkt tot één zelf gehoste gateway-implementatie.

## <a name="prerequisites"></a>Vereisten

De volgende quickstart voltooien: [een azure API-beheerexemplaar maken](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Een zelf-hostende gateway inrichten

1. Selecteer de **gateways** van onder **Instellingen**.
2. Klik op **+ Toevoegen**.
3. Voer de **naam** en **het gebied** van de gateway in.
> [!TIP]
> **Regio** geeft de beoogde locatie op van de gatewayknooppunten die aan deze gatewaybron worden gekoppeld. Het is semantisch gelijk aan een vergelijkbare eigenschap die is gekoppeld aan een Azure-bron, maar kan een willekeurige tekenreekswaarde worden toegewezen.

4. Voer destijds een **beschrijving** van de gatewaybron in.
5. Selecteer eventueel **+** onder **API's** om een of meer API's aan deze gatewaybron te koppelen.
> [!TIP]
> U een API koppelen en verwijderen van een gateway op het tabblad **Instellingen van** de API.

> [!IMPORTANT]
> Standaard wordt geen van de bestaande API's gekoppeld aan de nieuwe gatewaybron. Daarom zullen pogingen om ze aan te `404 Resource Not Found` roepen via de nieuwe gateway resulteren in reacties.

6. Klik op**toevoegen**.

Nu is de gatewaybron ingericht in uw API-beheerexemplaar. U doorgaan met het implementeren van de gateway.

## <a name="next-steps"></a>Volgende stappen

* Zie Azure [API Management zelf gehostgatewayoverzicht](self-hosted-gateway-overview.md) voor meer informatie over de zelfgehoste gateway
* Meer informatie over het [implementeren van een zelfgehoste gateway naar Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Meer informatie over het [implementeren van een zelfgehoste gateway naar Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)

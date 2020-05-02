---
title: Api's exporteren van Azure API Management naar het energie platform | Microsoft Docs
description: Meer informatie over het exporteren van Api's van API Management naar het Power platform.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 9af20972a47e2d0ad20de62f1bb9d10e4d43563c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82725950"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Api's exporteren van Azure API Management naar het Power platform 

Burger ontwikkelaars die het micro soft- [energie platform](https://powerplatform.microsoft.com) gebruiken, moeten vaak de zakelijke mogelijkheden bereiken die door professionele ontwikkel aars zijn ontwikkeld en in azure zijn geïmplementeerd. Met [Azure API Management](https://aka.ms/apimrocks) kunnen professionele ontwikkel aars hun back-end-service publiceren als api's en deze api's eenvoudig exporteren naar het Power platform (Power apps en Power autoautomatiseerd) als aangepaste connectors voor gebruik door burger kers. 

In dit artikel worden de stappen beschreven voor het exporteren van Api's van API Management naar het Power platform. 

## <a name="prerequisites"></a>Vereisten

+ Voer de volgende Snelstartgids uit: [een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
+ Zorg ervoor dat er een API in uw API Management-exemplaar is die u naar het Power platform wilt exporteren
+ Zorg ervoor dat u beschikt over een Power-app of een geautomatiseerde [omgeving](https://docs.microsoft.com/powerapps/powerapps-overview#power-apps-for-admins) voor energie beheer 

## <a name="export-an-api"></a>Een API exporteren

1. Navigeer naar uw API Management-service in de Azure Portal en selecteer **api's** in het menu.
2. Klik op de drie puntjes naast de API die u wilt exporteren. 
3. Selecteer **Exporteren**.
4. Selecteer **Power apps en energie automatisering**.
5. Kies een omgeving waarnaar u de API wilt exporteren. 
6. Geef een weergave naam op, die wordt gebruikt als de naam van de aangepaste connector.  
7. Optioneel, als de API wordt beveiligd door een OAuth 2,0-server, moet u ook extra gegevens opgeven, zoals `Client ID`, `Client secret` `Authorization URL` `Token URL`,, en `Refresh URL`.  
8. Selecteer **Exporteren**. 

Wanneer het exporteren is voltooid, gaat u naar uw Power app of de geautomatiseerde omgeving voor energie beheer. U ziet de API als een aangepaste connector.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het Power platform](https://powerplatform.microsoft.com/)
* [Algemene taken in API Management leren door de zelf studies te volgen](https://docs.microsoft.com/azure/api-management/import-and-publish)
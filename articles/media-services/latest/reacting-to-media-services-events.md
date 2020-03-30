---
title: Reageren op azure mediaservices-gebeurtenissen | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Azure Event Grid gebruiken om u te abonneren op Gebeurtenissen in Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/08/2019
ms.author: juliako
ms.openlocfilehash: e24bacb0ea7ab406442022915872fc77e9cc1a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887881"
---
# <a name="handling-event-grid-events"></a>Event Grid-gebeurtenissen verwerken

Met gebeurtenissen in Media Services kunnen toepassingen reageren op verschillende gebeurtenissen (bijvoorbeeld de gebeurtenis taakstatuswijziging) met behulp van moderne serverloze architecturen. Het doet dit zonder de noodzaak van ingewikkelde code of dure en inefficiënte polling diensten. In plaats daarvan worden gebeurtenissen via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) gepusht naar gebeurtenishandlers zoals [Azure-functies,](https://azure.microsoft.com/services/functions/) [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)of zelfs naar uw eigen Webhook, en betaalt u alleen voor wat u gebruikt. Zie Prijzen voor [gebeurtenisrasters](https://azure.microsoft.com/pricing/details/event-grid/)voor informatie over prijzen .

Beschikbaarheid voor Media Services-evenementen is gekoppeld aan [de beschikbaarheid](../../event-grid/overview.md) van het eventgrid en zal beschikbaar komen in andere regio's, zoals Event Grid doet.  

## <a name="media-services-events-and-schemas"></a>Gebeurtenissen en schema's van Media Services

Gebeurtenisraster gebruikt [gebeurtenisabonnementen](../../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten door te sturen naar abonnees. Gebeurtenissen in Media Services bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in uw gegevens. U een Gebeurtenis Media Services identificeren omdat de eigenschap eventType begint met 'Microsoft.Media'.

Zie [Programmaschema's voor mediaservices-gebeurtenissen](media-services-event-schemas.md)voor meer informatie .

## <a name="practices-for-consuming-events"></a>Praktijken voor het consumeren van gebeurtenissen

Toepassingen die mediaservices-gebeurtenissen verwerken, moeten een aantal aanbevolen procedures volgen:

* Aangezien meerdere abonnementen kunnen worden geconfigureerd om gebeurtenissen naar dezelfde gebeurtenishandler te leiden, is het belangrijk om niet aan te nemen dat gebeurtenissen van een bepaalde bron zijn, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van het opslagaccount dat u verwacht.
* Controleer ook of het eventType een gebeurtenis is die u bereid bent te verwerken en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de typen zijn die u verwacht.
* Velden negeren die u niet begrijpt.  Deze praktijk zal u helpen om u bestand te houden tegen nieuwe functies die in de toekomst kunnen worden toegevoegd.
* Gebruik het voorvoegsel en achtervoegsel van onderwerp om gebeurtenissen te beperken tot een bepaalde gebeurtenis.

> [!NOTE]
> Gebeurtenissen zijn onderworpen aan de SLA (Event Grid [Service Level Agreement).](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) Als u gebeurtenismeldingen wilt ontvangen met API's, raadpleegt u voorbeelden over het consumeren van gebeurtenissen, met [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) of [Java SDK.](https://github.com/Azure-Samples/media-services-v3-java)

## <a name="next-steps"></a>Volgende stappen

* [Gebeurtenissen bewaken - portal](monitor-events-portal-how-to.md)
* [Gebeurtenissen bewaken - CLI](job-state-events-cli-how-to.md)

---
title: bestand opnemen
description: bestand opnemen
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 39bff26baea622e6c0ed524ca68c3c8bae4e770d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76020967"
---
1. Als u een Azure SignalR Service-bron wilt maken, meldt u zich eerst aan bij de [Azure-portal.](https://portal.azure.com) Selecteer linksboven op de pagina **+ Een resource maken**. Voer **in het** tekstvak Zoeken in de Marketplace-functie **de SignalR-service**in .

2. Selecteer **SignalR-service** in de resultaten en selecteer **Maken**.

3. Voeg op de nieuwe pagina **Instellingen van SignalR** de volgende instellingen toe voor uw nieuwe SignalR-bron:

    | Name | Aanbevolen waarde | Beschrijving |
    | ---- | ----------------- | ----------- |
    | Resourcenaam | *testsignalr* | Voer een unieke resourcenaam in voor de SignalR-resource. De naam moet een tekenreeks van 1 tot 63 tekens zijn en`-`alleen cijfers, letters en het teken teken van het koppelteken bevatten. De naam kan niet beginnen of eindigen met het teken koppelteken en opeenvolgende koppeltekens zijn niet geldig.|
    | Abonnement | Kies uw abonnement |  Selecteer het Azure-abonnement dat u wilt gebruiken om SignalR te testen. Als uw account slechts één abonnement heeft, wordt het automatisch geselecteerd en wordt de vervolgkeuzelijst **Abonnement** niet weergegeven.|
    | Resourcegroep | Een resourcegroep met de naam *SignalRTestResources maken*| Selecteer of maak een resourcegroep voor uw SignalR-resource. Deze groep is handig voor het organiseren van meerdere resources die u tegelijkertijd wilt verwijderen door de brongroep te verwijderen. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../articles/azure-resource-manager/management/overview.md) voor meer informatie. |
    | Locatie | *VS - oost* | Gebruik **Locatie** om de geografische locatie op te geven waar SignalR-resource wordt gehost. Voor de beste prestaties wordt u aangeraden om de resource te maken in dezelfde regio als de andere onderdelen van uw toepassing. |
    | Prijscategorie | *Gratis* | Momenteel zijn **gratis** en **standaardopties** beschikbaar. |
    | Vastmaken aan dashboard | ✔ | Selecteer dit vak om de resource vast te maken aan uw dashboard, zodat u deze gemakkelijker vinden. |

4. Selecteer **Maken**. Het kan enkele minuten duren voordat de implementatie is voltooid.

5. Nadat de implementatie is voltooid, selecteert u **Toetsen** onder **INSTELLINGEN**. Kopieer de verbindingstekenreeks voor de primaire sleutel. U gebruikt deze tekenreeks later om uw app te configureren om de Azure SignalR Service-bron te gebruiken.

    Deze verbindingsreeks heeft de volgende vorm:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;

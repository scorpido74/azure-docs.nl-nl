---
title: Hybride verbinding door geven als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
description: Hierin wordt beschreven hoe u Azure Relay hybride verbindingen als gebeurtenis-handlers kunt gebruiken voor Azure Event Grid-gebeurtenissen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0631724e688a71d7e9685f5f0ad738d81e2a8034
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598428"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Hybride verbinding door geven als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een aantal verdere acties nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het afhandelen van gebeurtenissen en **Azure relay** is een hiervan. 

Gebruik Azure **Relay hybride verbindingen** om gebeurtenissen te verzenden naar toepassingen die zich binnen een bedrijfs netwerk bevinden en geen openbaar toegankelijk eind punt hebben.

## <a name="tutorials"></a>Zelfstudies
Raadpleeg de volgende zelf studie voor een voor beeld van het gebruik van een Azure Relay hybride verbinding als een gebeurtenis-handler. 

|Titel  |Beschrijving  |
|---------|---------|
| [Zelf studie: gebeurtenissen verzenden naar hybride verbinding](custom-event-to-hybrid-connection.md) | Hiermee wordt een aangepaste gebeurtenis verzonden naar een bestaande hybride verbinding voor verwerking door een listener-toepassing. |

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over [gebeurtenis-handlers](event-handlers.md) voor een lijst met ondersteunde gebeurtenis-handlers. 
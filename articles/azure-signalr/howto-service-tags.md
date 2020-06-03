---
title: Service tags gebruiken
titleSuffix: Azure SignalR Service
description: Service tags gebruiken om uitgaand verkeer naar uw Azure signalerings service toe te staan
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 8810309fef5dbbb35465a2af15d42fa8a59d5401
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300174"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Service tags gebruiken voor de Azure signalerings service

U kunt [service Tags](../virtual-network/security-overview.md#service-tags) voor Azure signalerings service gebruiken bij het configureren van de [netwerk beveiligings groep](../virtual-network/security-overview.md#network-security-groups). U kunt hiermee de uitgaande netwerk beveiligings regel voor eind punten van de Azure signalerings service definiëren zonder dat er IP-adressen hoeven te worden voorlopig hardcoderen we.

Deze service tags worden beheerd door de Azure signalerings service. U kunt geen eigen servicetag maken of een bestaande wijzigen. Micro soft beheert deze adres voorvoegsels die overeenkomen met het servicetag en werkt de servicetag automatisch bij met gewijzigde adressen.

## <a name="use-service-tag-on-portal"></a>Service label op Portal gebruiken

U kunt uitgaand verkeer naar de Azure signalerings service toestaan door een nieuwe regel voor uitgaand netwerk beveiliging toe te voegen:

1. Ga naar de netwerk beveiligings groep.

1. Klik op het menu instellingen met de naam **uitgaande beveiligings regels**.

1. Klik bovenaan op de knop **+ toevoegen** .

1. Kies **service-tag** onder **bestemming**.

1. Kies **AzureSignalR** onder **bestemmings servicetag**.

1. Vul **443** in als **doel poort bereik**.

    ![Een uitgaande beveiligings regel maken](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Pas andere velden aan op basis van uw behoeften.

1. Klik op **Toevoegen**.


## <a name="next-steps"></a>Volgende stappen

- [Netwerk beveiligings groepen: Service Tags](../virtual-network/security-overview.md#security-rules)

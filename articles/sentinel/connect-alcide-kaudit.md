---
title: Alcide kAudit-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over hoe u Alcide kAudit-gegevens verbindt met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: 121c9258353505b6147aa059d327bc711e9fede4
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85368433"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Uw Alcide kAudit verbinden met Azure Sentinel

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) helpt u afwijkende Kubernetes-gedrag te identificeren en te focussen op Kubernetes schendingen en incidenten, terwijl de detectie tijd wordt verminderd. In dit artikel wordt uitgelegd hoe u uw Alcide kAudit-oplossing verbindt met Azure Sentinel. Met de Alcide kAudit Data Connector kunt u eenvoudig uw kAudit-logboek gegevens in azure-Sentinel brengen, zodat u deze in werkmappen kunt bekijken, gebruiken om aangepaste waarschuwingen te maken en deze op te nemen om het onderzoek te verbeteren. Integratie tussen Alcide kAudit en Azure Sentinel maakt gebruik van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- U moet Lees machtigingen hebben voor gedeelde sleutels voor de werk ruimte.

## <a name="configure-and-connect-alcide-kaudit"></a>Alcide kAudit configureren en verbinden

Met Alcide kAudit kunnen Logboeken rechtstreeks naar Azure Sentinel worden geëxporteerd.

1. Klik in de Azure-Sentinel Portal op **Data connectors** in het navigatie menu.

1. Selecteer **Alcide kAudit** in de galerie en klik vervolgens op de knop **connector pagina openen** .

1. Volg de stapsgewijze instructies in de [installatie handleiding van de Alcide kAudit](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf).

1. Wanneer u wordt gevraagd om de werk ruimte-ID en de primaire sleutel, kunt u deze kopiëren van de Alcide kAudit Data Connector-pagina.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="Werk ruimte-ID en primaire sleutel":::

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken** onder de volgende gegevens typen in **CustomLogs**:

- detectie van **alcide_kaudit_detections_1_CL** -Alcide kaudit 
- **alcide_kaudit_activity_1_CL** -Alcide kaudit-activiteiten logboeken
- aantallen **alcide_kaudit_selections_count_1_CL** -Alcide kaudit-activiteiten
- Alcide kAudit-activiteit Details **alcide_kaudit_selections_details_1_CL**

Als u het relevante schema in Logboeken voor de Alcide kAudit wilt gebruiken, zoekt u naar de hierboven vermelde gegevens typen.

Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Alcide kAudit verbindt met Azure Sentinel. Klik op het tabblad **volgende stappen** op de pagina Data Connector om optimaal gebruik te maken van de mogelijkheden die zijn ingebouwd in deze gegevens connector. Hier vindt u een aantal kant-en-klare voorbeeld query's, zodat u meteen aan de slag kunt met het vinden van nuttige informatie.

Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.

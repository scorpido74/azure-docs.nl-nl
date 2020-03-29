---
title: Azure-activiteitsgegevens verbinden met Azure Sentinel | Microsoft Documenten
description: Meer informatie over het verbinden van Azure Activity-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 82dfcaf3394703aae531c828a1b96ad290bab798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124990"
---
# <a name="connect-data-from-azure-activity-log"></a>Gegevens verbinden vanuit het Azure-activiteitenlogboek

U logboeken van [Azure Activity-logboeken](../azure-monitor/platform/platform-logs-overview.md) met één klik streamen naar Azure Sentinel. Het activiteitenlogboek is een abonnementslogboek dat gebeurtenissen op abonnementsniveau in Azure registreert en weergeeft, van operationele gegevens van Azure Resource Manager tot updates voor servicestatusgebeurtenissen. Met behulp van het logboek Activiteit u bepalen wat, wie en wanneer voor elke schrijfbewerking (PUT, POST, DELETE) die is uitgevoerd op de bronnen in uw abonnement. U ook de status van de bewerking en andere relevante eigenschappen leren. Het logboek Activiteit bevat geen leesbewerkingen of bewerkingen voor resources die het klassieke/"RDFE"-model gebruiken. 

## <a name="prerequisites"></a>Vereisten

- Uw gebruiker moet machtigingen voor bijdragen hebben aan de werkruimte Log Analytics.
- Uw gebruiker moet readermachtigingen hebben voor elk abonnement waarvan u de logboeken wilt streamen naar Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>De Azure-activiteitsconnector instellen

1. Selecteer **gegevensconnectoren**in het navigatiemenu van Azure Sentinel . Klik in de lijst met connectors op **Azure Activity**en klik vervolgens op de knop **Connector openen** rechtsonder.

2. Klik op het tabblad **Instructies** op de koppeling **Azure-activiteitslogboeken >** configureren.

3. Selecteer in het **logboekvenster azure activity** de abonnementen waarvan u de logboeken wilt streamen naar Azure Sentinel. 

4. Klik in het abonnementsvenster dat rechts wordt geopend op **Verbinden**.

5. Als u het relevante schema wilt gebruiken `AzureActivity` in Logboekanalyse voor Azure-activiteitswaarschuwingen, typt u het queryvenster.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u azure-activiteitslogboek verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Ga aan de slag met het detecteren van bedreigingen met Azure Sentinel, met behulp van [ingebouwde](tutorial-detect-threats-built-in.md) of [aangepaste](tutorial-detect-threats-custom.md) regels.

---
title: Azure ATP-gegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Azure ATP-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588583"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Gegevens van Azure Advanced Threat Protection (ATP) verbinden

> [!IMPORTANT]
> De Azure Advanced Threat Protection-gegevensconnector in Azure Sentinel bevindt zich momenteel in een openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

U logboeken van [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) met één klik streamen naar Azure Sentinel.

## <a name="prerequisites"></a>Vereisten

- Gebruiker met machtigingen voor globale beheerders of beveiligingsbeheerders
- U moet een voorbeeldklant van Azure ATP zijn en integratie tussen Azure ATP en Microsoft Cloud App Security inschakelen. Zie [Azure Advanced Protection Integration](https://docs.microsoft.com/cloud-app-security/aatp-integration)voor meer informatie.

## <a name="connect-to-azure-atp"></a>Verbinding maken met Azure ATP

Controleer of de Azure ATP-previewversie is [ingeschakeld in uw netwerk.](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)
Als Azure ATP wordt geïmplementeerd en uw gegevens wordt ingenomen, kunnen de verdachte waarschuwingen eenvoudig worden gestreamd naar Azure Sentinel. Het kan tot 24 uur duren voordat de waarschuwingen worden gestreamd naar Azure Sentinel.


1. Als u Azure ATP wilt verbinden met Azure Sentinel, moet u eerst integratie tussen Azure ATP en Microsoft Cloud App Security inschakelen. Zie [Azure Advanced Threat Protection-integratie](https://docs.microsoft.com/cloud-app-security/aatp-integration)voor informatie over hoe u dit doen.

1. Selecteer in Azure Sentinel **gegevensconnectors** en klik vervolgens op de tegel **Azure Advanced Threat Protection (Preview).**

1. U selecteren of u wilt dat de waarschuwingen van Azure ATP automatisch incidenten genereren in Azure Sentinel. Selecteer **onder Incidenten maken** **inschakelen** om de standaardanalytische regel in te schakelen die incidenten automatisch maakt op uit waarschuwingen die zijn gegenereerd in de verbonden beveiligingsservice. U deze regel vervolgens bewerken onder **Analytics** en vervolgens **Actieve regels**.

1. Klik op **Verbinden**.

1. Als u het relevante schema wilt gebruiken in Log Analytics voor de Azure ATP-waarschuwingen, zoekt u **naar SecurityAlert**.

> [!NOTE]
> Als de waarschuwingen groter zijn dan 30 KB, stopt Azure Sentinel met het weergeven van het veld Entiteiten in de waarschuwingen.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure Advanced Threat Protection verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)


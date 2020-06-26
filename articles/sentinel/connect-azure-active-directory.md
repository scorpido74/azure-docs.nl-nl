---
title: Azure Active Directory gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Azure Active Directory gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5ec8a37a0c782a5426b87f785af2d047ca35aa22
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374811"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Verbinding maken met gegevens van Azure Active Directory (Azure AD)



Met Azure Sentinel kunt u gegevens verzamelen van [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) en deze streamen naar Azure Sentinel. U kunt ervoor kiezen om [aanmeldings logboeken](../active-directory/reports-monitoring/concept-sign-ins.md) en [controle logboeken](../active-directory/reports-monitoring/concept-audit-logs.md) te streamen.

## <a name="prerequisites"></a>Vereisten

- Als u aanmeldings gegevens vanuit Azure AD wilt exporteren, moet u een Azure AD P1-of P2-licentie hebben.

- Gebruiker met de machtigingen globale beheerder of beveiligings beheerder voor de Tenant waarvan u de logboeken wilt streamen.

- U moet gemachtigd zijn om toegang te krijgen tot Azure AD Diagnostische logboeken om de verbindings status te kunnen zien. 


## <a name="connect-to-azure-active-directory"></a>Verbinding maken met Azure Active Directory

1. Selecteer in azure Sentinel **Data connectors** in het navigatie menu.

1. Selecteer in de galerie data connectors de optie **Azure Active Directory** en klik vervolgens op de knop **connector openen** .

1. Schakel de selectie vakjes in naast de logboeken die u wilt streamen naar Azure Sentinel en klik op **verbinden**.

1. U kunt selecteren of u wilt dat de waarschuwingen van Azure AD automatisch incidenten genereren in azure Sentinel. Selecteer **inschakelen** onder **incidenten maken** om de standaard analyse regel in te scha kelen waarmee incidenten automatisch worden gemaakt op basis van waarschuwingen die zijn gegenereerd in de verbonden beveiligings service. U kunt deze regel vervolgens bewerken onder **analyse** en vervolgens op **actieve regels**.

1. Als u het relevante schema in Log Analytics wilt gebruiken voor het uitvoeren van query's op Azure AD-waarschuwingen, typt u `SigninLogs` of `AuditLogs` in het query venster.




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure Active Directory kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

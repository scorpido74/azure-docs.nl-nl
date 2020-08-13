---
title: Azure Active Directory gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verzamelen van gegevens van Azure Active Directory en het streamen van Azure AD-aanmeld logboeken en audit Logboeken in azure Sentinel.
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
ms.openlocfilehash: a19ced1cb9496bddd1f9aa7ea9e3eb58ba7eea3d
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185686"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Verbinding maken met gegevens van Azure Active Directory (Azure AD)



Met Azure Sentinel kunt u gegevens verzamelen van [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) en deze streamen naar Azure Sentinel. U kunt ervoor kiezen om [aanmeldings logboeken](../active-directory/reports-monitoring/concept-sign-ins.md) en [controle logboeken](../active-directory/reports-monitoring/concept-audit-logs.md) te streamen.

## <a name="prerequisites"></a>Vereisten

- Elke Azure AD-licentie (Free/O365/P1/P2) kan worden geïntegreerd met Azure Monitor-en opname logboek registraties in azure Sentinel. Er gelden extra kosten per gigabyte voor Azure Monitor of Azure Sentinel.

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

---
title: Azure AD-gegevens verbinden met Azure Sentinel | Microsoft Docs
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
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588617"
---
# <a name="connect-data-from-azure-active-directory"></a>Verbinding maken met gegevens van Azure Active Directory



Met Azure Sentinel kunt u gegevens verzamelen van [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) en deze streamen naar Azure Sentinel. U kunt ervoor kiezen om [aanmeldings logboeken](../active-directory/reports-monitoring/concept-sign-ins.md) en [controle logboeken](../active-directory/reports-monitoring/concept-audit-logs.md) te streamen.

## <a name="prerequisites"></a>Vereisten

- Als u aanmeldings gegevens van Active Directory wilt exporteren, moet u een Azure AD P1-of P2-licentie hebben.

- Gebruiker met de machtigingen globale beheerder of beveiligings beheerder voor de Tenant waarvan u de logboeken wilt streamen.

- U moet gemachtigd zijn om toegang te krijgen tot Azure AD Diagnostische logboeken om de verbindings status te kunnen zien. 


## <a name="connect-to-azure-ad"></a>Verbinding maken met Azure AD

1. Selecteer in azure Sentinel **Data connectors** en klik vervolgens op de tegel **Azure Active Directory** .

1. Klik naast de logboeken die u wilt streamen naar Azure Sentinel op **verbinden**.

1. U kunt selecteren of u wilt dat de waarschuwingen van Azure AD automatisch incidenten genereren in azure Sentinel. Selecteer **inschakelen** onder **incidenten maken** om de standaard analyse regel in te scha kelen waarmee incidenten automatisch worden gemaakt op basis van waarschuwingen die zijn gegenereerd in de verbonden beveiligings service. U kunt deze regel vervolgens bewerken onder **analyse** en vervolgens op **actieve regels**.

1. Als u het relevante schema in Log Analytics voor de Azure AD-waarschuwingen wilt gebruiken, zoekt u naar **SigninLogs** en **audit logs bevat**.




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure AD kunt verbinden met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

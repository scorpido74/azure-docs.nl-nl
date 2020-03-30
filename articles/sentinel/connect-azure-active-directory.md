---
title: Azure-GEGEVENS verbinden met Azure Sentinel | Microsoft Documenten
description: Meer informatie over het verbinden van Azure Active Directory-gegevens met Azure Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588617"
---
# <a name="connect-data-from-azure-active-directory"></a>Gegevens verbinden vanuit Azure Active Directory



Met Azure Sentinel u gegevens uit [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) verzamelen en streamen naar Azure Sentinel. U ervoor kiezen om [aanmeldingslogboeken](../active-directory/reports-monitoring/concept-sign-ins.md) en [controlelogboeken](../active-directory/reports-monitoring/concept-audit-logs.md) te streamen.

## <a name="prerequisites"></a>Vereisten

- Als u aanmeldingsgegevens wilt exporteren vanuit Active Directory, moet u over een Azure AD P1- of P2-licentie beschikken.

- Gebruiker met globale beheerders- of beveiligingsbeheerdersmachtigingen op de tenant waaruit u de logboeken wilt streamen.

- Als u de verbindingsstatus wilt zien, moet u toestemming hebben om toegang te krijgen tot diagnostische azure-logboeken van Azure. 


## <a name="connect-to-azure-ad"></a>Verbinding maken met Azure AD

1. Selecteer in Azure Sentinel **gegevensconnectors** en klik vervolgens op de tegel **Azure Active Directory.**

1. Klik naast de logboeken die u naar Azure Sentinel wilt streamen op **Verbinden**.

1. U selecteren of u wilt dat de waarschuwingen van Azure AD automatisch incidenten genereren in Azure Sentinel. Selecteer **onder Incidenten maken** **inschakelen** om de standaardanalytische regel in te schakelen die incidenten automatisch maakt op uit waarschuwingen die zijn gegenereerd in de verbonden beveiligingsservice. U deze regel vervolgens bewerken onder **Analytics** en vervolgens **Actieve regels**.

1. Als u het relevante schema wilt gebruiken in Logboekanalyse voor de Azure AD-waarschuwingen, zoekt u naar **SigninLogs** en **AuditLogs**.




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure AD verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)

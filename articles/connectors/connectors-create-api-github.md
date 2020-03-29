---
title: Uw GitHub-repo openen, bewaken en beheren
description: GitHub-gebeurtenissen bewaken en uw GitHub-repo beheren door geautomatiseerde werkstromen te maken met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378446"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Uw GitHub-repo bewaken en beheren met Azure Logic Apps

GitHub is een webgebaseerde Git repository hosting service die alle gedistribueerde revisie controle en broncode beheer (SCM) functionaliteit in Git plus andere functies biedt.

Als u aan de slag wilt met de GitHub-connector, [maakt u eerst een logische app.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-a-connection-to-github"></a>Een verbinding met GitHub maken

Als u de GitHub-connector in een logische app wilt gebruiken, moet u eerst een *verbinding* maken en vervolgens details voor deze eigenschappen opgeven: 

| Eigenschap | Vereist | Beschrijving | 
| -------- | -------- | ----------- | 
| Token | Ja | Geef uw GitHub-referenties op. |

Nadat u de verbinding hebt gemaakt, u de acties uitvoeren en luisteren naar de triggers die in dit artikel worden beschreven.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina van](/connectors/github/)de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
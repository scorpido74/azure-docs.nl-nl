---
title: Block Chain-App-versie beheer-Azure Block Chain workbench
description: Toepassings versies gebruiken in azure Block Chain Workbench preview.
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 45219790cf0cd064e0fcd456e262b2f93aa03ac9
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74323914"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Azure Block Chain Workbench preview-versie van de toepassing

U kunt meerdere versies van een Azure Block Chain Workbench preview-app maken en gebruiken. Als meerdere versies van dezelfde applicatie zijn geüpload, is er een versiegeschiedenis beschikbaar en kunnen gebruikers kiezen welke versie ze willen gebruiken.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Een Blockchain Workbench-implementatie. Zie [Azure Blockchain Workbench implementeren](deploy.md) voor meer informatie.
* Een geïmplementeerde blockchain-toepassing in Blockchain Workbench. Zie [Een blockchain-toepassing maken in Azure Blockchain Workbench](create-app.md) voor meer informatie.

## <a name="add-an-app-version"></a>Een app-versie toevoegen

Voeg de nieuwe configuratie en slimme contractbestanden toe aan Blockchain Workbench om een nieuwe versie toe te voegen.

1. Ga in een webbrowser naar het webadres van Blockchain Workbench, Zie bijvoorbeeld `https://{workbench URL}.azurewebsites.net/` voor meer informatie over het vinden van uw Block Chain Workbench-webadres: [Block Chain Workbench web URL](deploy.md#blockchain-workbench-web-url)
2. Meld u aan als een [Blockchain Workbench-beheerder](manage-users.md#manage-blockchain-workbench-administrators).
3. Selecteer de Block Chain-toepassing die u wilt bijwerken met een andere versie.
4. Selecteer **versie toevoegen**. Het deel venster **versie toevoegen** wordt weer gegeven.
5. Kies de nieuwe configuratie van het versie-contract en contract codebestanden om te uploaden. Het configuratiebestand wordt automatisch gevalideerd. Herstel eventuele validatie fouten voordat u de toepassing implementeert.
6. Selecteer **versie toevoegen** om de nieuwe versie van de Block Chain-toepassing toe te voegen.

    ![Een nieuwe versie toevoegen](media/version-app/add-version.png)

De implementatie van de Block Chain-toepassing kan enkele minuten duren. Wanneer de implementatie is voltooid, vernieuwt u de toepassings pagina. Als u de toepassing kiest en de knop **versie geschiedenis** selecteert, wordt de versie geschiedenis van de toepassing weer gegeven.

> [!IMPORTANT]
> Eerdere versies van de toepassing zijn uitgeschakeld. U kunt vorige versies afzonderlijk opnieuw inschakelen.
>
> Mogelijk moet u leden opnieuw toevoegen aan toepassingsrollen als er wijzigingen zijn aangebracht in de toepassingsrollen in de nieuwe versie.

## <a name="using-app-versions"></a>Gebruik van app-versies

Standaard wordt de nieuwste ingeschakelde versie van de toepassing gebruikt in Blockchain Workbench. Als u een vorige versie van een toepassing wilt gebruiken, moet u eerst de versie kiezen via de toepassingspagina.

1. Selecteer in de sectie toepassingen van Blockchain Workbench het selectievakje de toepassing die het contract bevat dat u wilt gebruiken. Als vorige versies zijn ingeschakeld, is de knop versie geschiedenis beschikbaar.
2. Selecteer de knop **versie geschiedenis** .
3. Kies in het deel venster versie geschiedenis de versie van de toepassing door de koppeling in de kolom *datum gewijzigd* te selecteren.

    ![Een vorige versie kiezen](media/version-app/use-version.png)

    U kunt nieuwe contracten maken of acties uitvoeren op contracten van eerdere versies. De versie van de applicatie wordt weergegeven na de naam van de applicatie en er wordt een waarschuwing weergegeven over de oudere versie.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met Azure Block Chain Workbench oplossen](troubleshooting.md)

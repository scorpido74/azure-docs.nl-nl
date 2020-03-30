---
title: Blockchain-app-versiewerk - Azure Blockchain Workbench
description: Toepassingsversies gebruiken in Azure Blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 45219790cf0cd064e0fcd456e262b2f93aa03ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74323914"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Versieing van Azure Blockchain Workbench Preview-toepassingen

U meerdere versies van een Azure Blockchain Workbench Preview-app maken en gebruiken. Als er meerdere versies van dezelfde toepassing worden geüpload, is er een versiegeschiedenis beschikbaar en kunnen gebruikers kiezen welke versie ze willen gebruiken.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Een Blockchain Workbench-implementatie. Zie [Azure Blockchain Workbench-implementatie](deploy.md) voor meer informatie over implementatie voor meer informatie
* Een geïmplementeerde blockchain-toepassing in Blockchain Workbench. Zie [Een blockchain-toepassing maken in Azure Blockchain Workbench](create-app.md) voor meer informatie.

## <a name="add-an-app-version"></a>Een app-versie toevoegen

Upload de nieuwe configuratie en slimme contractbestanden naar Blockchain Workbench om een nieuwe versie toe te voegen.

1. Ga in een webbrowser naar het webadres van Blockchain Workbench, Zie bijvoorbeeld `https://{workbench URL}.azurewebsites.net/` de URL van [Blockchain Workbench Web](deploy.md#blockchain-workbench-web-url) voor informatie over het vinden van uw Blockchain Workbench-webadres
2. Meld u aan als een [Blockchain Workbench-beheerder](manage-users.md#manage-blockchain-workbench-administrators).
3. Selecteer de blockchain-toepassing die u wilt bijwerken met een andere versie.
4. Selecteer **Versie toevoegen**. Het **deelvenster Versie toevoegen** wordt weergegeven.
5. Kies de nieuwe versiecontractconfiguratie en contractcodebestanden die u wilt uploaden. Het configuratiebestand wordt automatisch gevalideerd. Herstel validatiefouten voordat u de toepassing implementeert.
6. Selecteer **Versie toevoegen** om de nieuwe blockchain-toepassingsversie toe te voegen.

    ![Een nieuwe versie toevoegen](media/version-app/add-version.png)

Implementatie van de blockchain-toepassing kan enkele minuten duren. Wanneer de implementatie is voltooid, vernieuwt u de toepassingspagina. Als u de toepassing kiest en de knop **Versiegeschiedenis** selecteert, wordt de versiegeschiedenis van de toepassing weergegeven.

> [!IMPORTANT]
> Eerdere versies van de toepassing zijn uitgeschakeld. U eerdere versies afzonderlijk opnieuw inschakelen.
>
> Mogelijk moet u leden opnieuw toevoegen aan toepassingsrollen als er wijzigingen zijn aangebracht in de toepassingsrollen in de nieuwe versie.

## <a name="using-app-versions"></a>App-versies gebruiken

Standaard wordt de nieuwste ingeschakelde versie van de toepassing gebruikt in Blockchain Workbench. Als u een vorige versie van een toepassing wilt gebruiken, moet u eerst de versie kiezen op de toepassingspagina.

1. Schakel in de sectie Blockchain Workbench-toepassing het selectievakje voor toepassingen in dat het contract bevat dat u wilt gebruiken. Als eerdere versies zijn ingeschakeld, is de knop versiegeschiedenis beschikbaar.
2. Selecteer de knop **Versiegeschiedenis.**
3. Kies in het deelvenster Versiegeschiedenis de versie van de toepassing door de koppeling in de *kolom Datum gewijzigd te* selecteren.

    ![Een vorige versie kiezen](media/version-app/use-version.png)

    U nieuwe contracten maken of acties uitvoeren voor eerdere versiecontracten. De versie van de toepassing wordt weergegeven na de naam van de toepassing en er wordt een waarschuwing weergegeven over de oudere versie.

## <a name="next-steps"></a>Volgende stappen

* [Azure Blockchain Workbench-probleemoplossing](troubleshooting.md)

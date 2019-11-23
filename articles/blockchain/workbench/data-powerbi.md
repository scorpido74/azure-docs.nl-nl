---
title: Gegevens van Azure Blockchain Workbench gebruiken in Microsoft Power BI
description: Leer hoe u gegevens uit een SQL-database van Azure Blockchain Workbench laadt en weergeeft in Microsoft Power BI.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 6e1f160c3563a280548c74ebe84f30bf08945c3f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324798"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Gegevens van Azure Blockchain Workbench weergeven met Microsoft Power BI

Microsoft Power BI biedt de mogelijkheid om met Power BI Desktop eenvoudig krachtige rapporten te genereren van SQL DB-databases en deze vervolgens te publiceren naar [https://www.powerbi.com](https://www.powerbi.com).

Dit artikel bevat stapsgewijze instructies om vanuit Power BI Desktop verbinding te maken met een SQL-database van Azure Blockchain Workbench, een rapport te maken en dit rapport te implementeren op powerbi.com.

## <a name="prerequisites"></a>Vereisten

* Download [Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Connecting Power BI to data in Azure Blockchain Workbench

1.  Open Power BI Desktop.
2.  Selecteer **Gegevens ophalen**.

    ![Gegevens ophalen](./media/data-powerbi/get-data.png)
3.  Selecteer **SQL Server** in de lijst met gegevensbronnen.

4.  Geef de naam van de server en de database op in het dialoogvenster. Geef aan of u de gegevens wilt importeren of een **DirectQuery** wilt uitvoeren. Selecteer **OK**.

    ![SQL-server selecteren](./media/data-powerbi/select-sql.png)

5.  Geef de databasereferenties op voor toegang tot Azure Blockchain Workbench. Selecteer **Database** en voer uw referenties in.

    Als u de referenties gebruikt die zijn gemaakt tijdens de implementatie van Azure Blockchain Workbench, is de gebruikersnaam **dbadmin** en het wachtwoord het wachtwoord dat u hebt opgegeven tijdens de implementatie.

    ![Instellingen van SQL-database](./media/data-powerbi/db-settings.png)

6.  Als er verbinding is met de database, ziet u in het dialoogvenster **Navigator** de tabellen en weergaven die beschikbaar zijn in de database. De weergaven zijn ontworpen voor rapportagedoeleinden en herkent u aan het voorvoegsel **vw**.

    ![Navigator](./media/data-powerbi/navigator.png)

7.  Selecteer de weergaven die u wilt opnemen in het rapport. For demonstration purposes, we include **vwContractAction**, which provides details on the actions that have taken place on a contract.

    ![Weergaven selecteren](./media/data-powerbi/select-views.png)

U kunt nu rapporten maken en publiceren zoals u dat gewend bent met Power BI.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Databaseweergaven in Azure Blockchain Workbench](database-views.md)
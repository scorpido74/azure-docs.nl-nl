---
title: Verbinding maken met Azure Data Explorer met ODBC
description: In dit artikel leert u hoe u een ODBC-verbinding (Open Database Connectivity) instelt op Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034019"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Verbinding maken met Azure Data Explorer met ODBC

Open Database Connectivity[(ODBC)](/sql/odbc/reference/odbc-overview)is een algemeen aanvaarde application programming interface (API) voor database toegang. Gebruik ODBC om verbinding te maken met Azure Data Explorer vanuit toepassingen die geen speciale connector hebben.

Achter de schermen, toepassingen oproep functies in de ODBC-interface, die worden geïmplementeerd in database-specifieke modules genaamd *drivers*. Azure Data Explorer ondersteunt een subset van het SQL[Server-communicatieprotocol (MS-TDS),](/azure/kusto/api/tds/)zodat het ODBC-stuurprogramma voor SQL Server kan worden gebruikt.

Met behulp van de volgende video u leren een ODBC-verbinding te maken. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

U ook [de ODBC-gegevensbron configureren](#configure-the-odbc-data-source) zoals hieronder beschreven. 

In het artikel leert u hoe u het ODBC-stuurprogramma van SQL Server gebruikt, zodat u verbinding maken met Azure Data Explorer vanuit elke toepassing die ODBC ondersteunt. 

## <a name="prerequisites"></a>Vereisten

Je hebt het volgende nodig:

* [Microsoft ODBC Driver voor SQL Server versie 17.2.0.1 of hoger](/sql/connect/odbc/download-odbc-driver-for-sql-server) voor uw besturingssysteem.

## <a name="configure-the-odbc-data-source"></a>De ODBC-gegevensbron configureren

Volg deze stappen om een ODBC-gegevensbron te configureren met het ODBC-stuurprogramma voor SQL Server.

1. Zoek in Windows naar *ODBC-gegevensbronnen*en open de odbc-bureaubladapp voor gegevensbronnen.

1. Selecteer **Toevoegen**.

    ![Gegevensbron toevoegen](media/connect-odbc/add-data-source.png)

1. Selecteer **ODBC Driver 17 voor SQL Server** en **voltooi**.

    ![Stuurprogramma selecteren](media/connect-odbc/select-driver.png)

1. Voer een naam en beschrijving in voor de verbinding en het cluster waarmee u verbinding wilt maken en selecteer **Volgende**. De cluster-URL moet zich in het formulier * \<ClusterNaam bevinden\>.\< Regio\>.kusto.windows.net*.

    ![Server selecteren](media/connect-odbc/select-server.png)

1. Selecteer **Active Directory geïntegreerd** en **volgende**.

    ![Active Directory geïntegreerd](media/connect-odbc/active-directory-integrated.png)

1. Selecteer de database met de voorbeeldgegevens en **volgende**.

    ![Standaarddatabase wijzigen](media/connect-odbc/change-default-database.png)

1. Laat in het volgende scherm alle opties als standaardinstellingen staan en selecteer **Voltooien**.

1. Selecteer **Gegevensbron testen**.

    ![Gegevensbron testen](media/connect-odbc/test-data-source.png)

1. Controleer of de test is geslaagd en selecteer **OK**. Als de test niet is geslaagd, controleert u de waarden die u in eerdere stappen hebt opgegeven en controleert u of u over voldoende machtigingen beschikt om verbinding te maken met het cluster.

    ![Test geslaagd](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met Azure Data Explorer vanuit Tableau](tableau.md)
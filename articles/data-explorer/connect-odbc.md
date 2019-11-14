---
title: Verbinding maken met Azure Data Explorer met ODBC
description: In dit artikel leert u hoe u een Open Database Connectivity (ODBC)-verbinding kunt instellen met Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034019"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Verbinding maken met Azure Data Explorer met ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) is een algemeen geaccepteerde Application Programming Interface (API) voor toegang tot de data base. Gebruik ODBC om verbinding te maken met Azure Data Explorer van toepassingen die geen specifieke connector hebben.

Achter de schermen worden toepassingen aangeroepen in de ODBC-interface, die zijn geïmplementeerd in data base-specifieke modules die *Stuur Programma's*worden genoemd. Azure Data Explorer ondersteunt een subset van het SQL Server Communication Protocol ([MS-TDS](/azure/kusto/api/tds/)), zodat het ODBC-stuur programma voor SQL Server kan worden gebruikt.

Met behulp van de volgende video kunt u leren hoe u een ODBC-verbinding maakt. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

U kunt ook [de ODBC-gegevens bron configureren](#configure-the-odbc-data-source) zoals hieronder wordt beschreven. 

In het artikel leest u hoe u het ODBC-stuur programma van SQL Server gebruikt, zodat u verbinding kunt maken met Azure Data Explorer vanuit elke toepassing die ODBC ondersteunt. 

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig:

* [Micro soft ODBC-stuur programma voor SQL Server versie 17.2.0.1 of hoger](/sql/connect/odbc/download-odbc-driver-for-sql-server) voor uw besturings systeem.

## <a name="configure-the-odbc-data-source"></a>De ODBC-gegevens bron configureren

Volg deze stappen voor het configureren van een ODBC-gegevens bron met behulp van het ODBC-stuur programma voor SQL Server.

1. Zoek in Windows naar *ODBC-gegevens bronnen*en open de ODBC-gegevens bronnen bureau blad-app.

1. Selecteer **Toevoegen**.

    ![Gegevensbron toevoegen](media/connect-odbc/add-data-source.png)

1. Selecteer **ODBC-stuur programma 17 voor SQL Server** klik vervolgens op **volt ooien**.

    ![Stuur programma selecteren](media/connect-odbc/select-driver.png)

1. Voer een naam en beschrijving in voor de verbinding en het cluster waarmee u verbinding wilt maken en selecteer vervolgens **volgende**. De cluster-URL moet de notatie *\<clustername\>hebben.\<regio\>. kusto.Windows.net*.

    ![Server selecteren](media/connect-odbc/select-server.png)

1. Selecteer **Active Directory geïntegreerd** , vervolgens **volgende**.

    ![Active Directory geïntegreerd](media/connect-odbc/active-directory-integrated.png)

1. Selecteer de data base met de voorbeeld gegevens en vervolgens op **volgende**.

    ![Standaard database wijzigen](media/connect-odbc/change-default-database.png)

1. Geef in het volgende scherm alle opties op als standaard waarden en selecteer vervolgens **volt ooien**.

1. Selecteer **gegevens bron testen**.

    ![Gegevens bron testen](media/connect-odbc/test-data-source.png)

1. Controleer of de test is geslaagd en selecteer **OK**. Als de test mislukt, controleert u de waarden die u in de vorige stappen hebt opgegeven en zorgt u ervoor dat u voldoende machtigingen hebt om verbinding te maken met het cluster.

    ![Test geslaagd](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met Azure Data Explorer vanuit tableau](tableau.md)
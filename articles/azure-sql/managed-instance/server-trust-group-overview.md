---
title: Server vertrouwens groep
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over de vertrouwens groep van de server en hoe u de vertrouwens relatie tussen Azure SQL Managed instances beheert.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: 911d7ffa2b1d313147ca73d0ceb285ea2e84b1f7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979491"
---
# <a name="use-server-trust-groups-to-setup-and-manage-trust-between-sql-managed-instances"></a>Server vertrouwensrelaties gebruiken voor het instellen en beheren van de vertrouwens relatie tussen SQL Managed instances
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Server vertrouwens groep is een concept dat wordt gebruikt voor het beheren van de vertrouwens relatie tussen Azure SQL Managed instances. Door een groep te maken en instanties toe te voegen aan het op het certificaat gebaseerde vertrouwens relatie wordt tot stand gebracht tussen alle leden en dit kan worden gebruikt voor verschillende scenario's voor meerdere exemplaren. Het verwijderen van servers uit de groep of het verwijderen van de groep resulteert in het verwijderen van de vertrouwens relatie tussen de servers. Gebruikers die een server vertrouwens groep willen maken of verwijderen, moeten schrijf machtigingen hebben voor een beheerd exemplaar.
[Server vertrouwens groep](https://aka.ms/mi-server-trust-group-arm) is een Azure Resource Manager-object dat overeenkomt met Azure Portal entiteit met de naam **SQL-vertrouwens groep**.

> [!NOTE]
> De vertrouwens groep van de server wordt geïntroduceerd in een open bare preview van gedistribueerde trans acties tussen Azure SQL Managed instances en heeft momenteel enkele beperkingen die verderop in dit artikel worden beschreven.

## <a name="server-trust-group-setup"></a>Setup van server vertrouwens groep

In de volgende sectie wordt het instellen van de server vertrouwens groep beschreven.

1. Ga naar de [Azure Portal](https://portal.azure.com/).

2. Ga naar Azure SQL Managed instance dat u wilt toevoegen aan een nieuwe server vertrouwensrelatie groep.

3. Selecteer op de pagina **beveiligings** instellingen het tabblad **SQL-vertrouwens groepen** .

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="Server vertrouwens groepen":::

4. Selecteer op de pagina configuratie van server vertrouwens groep het pictogram **nieuwe groep** .

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="Server vertrouwens groepen":::

5. Stel op de Blade **SQL-vertrouwens groep** maken de **groeps naam**in. Het moet uniek zijn in alle regio's waar de groeps leden zich bevinden. Met het **vertrouwens bereik** wordt het type scenario met meerdere exemplaren gedefinieerd dat is ingeschakeld met de vertrouwens groep van de server en in het voor beeld wordt het enige vertrouwde bereik **gedistribueerde trans acties**, zodat het vooraf wordt geselecteerd en niet kan worden gewijzigd. Alle **groeps leden** moeten deel uitmaken van hetzelfde **abonnement** , maar kunnen zich onder verschillende resource groepen bevindt. Selecteer de **resource groep** en **SQL Server/instantie** om het door Azure SQL beheerde exemplaar te kiezen dat lid moet zijn van de groep.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="Server vertrouwens groepen":::

6. Nadat alle vereiste velden zijn ingevuld, klikt u op **Opslaan**.

## <a name="server-trust-group-maintenance-and-deletion"></a>Onderhoud en verwijdering van vertrouwens groep voor Server

Het is niet mogelijk om de vertrouwens groep van de server te bewerken. Als u een beheerd exemplaar uit een groep wilt verwijderen, moet u de groep verwijderen en een nieuwe maken.

In de volgende sectie wordt het verwijderings proces voor de server vertrouwens groep beschreven. 
1. Ga naar Azure Portal.
2. Navigeer naar een beheerd exemplaar dat bij de vertrouwens groep hoort.
3. Selecteer op de pagina **beveiligings** instellingen het tabblad **SQL-vertrouwens groepen** .
4. Selecteer de vertrouwens groep die u wilt verwijderen.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="Server vertrouwens groepen":::
5. Klik op **groep verwijderen**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="Server vertrouwens groepen":::
6. Typ de naam van de server vertrouwens groep om de verwijdering te bevestigen en klik op **verwijderen**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="Server vertrouwens groepen":::

> [!NOTE]
> Als u de vertrouwens groep van de server verwijdert, wordt de vertrouwens relatie tussen de twee beheerde exemplaren mogelijk niet onmiddellijk verwijderd. Het verwijderen van vertrouwens relaties kan worden afgedwongen door het aanroepen van een [failover](https://docs.microsoft.com/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) van beheerde exemplaren. Controleer de [bekende problemen](https://docs.microsoft.com/azure/azure-sql/database/doc-changes-updates-release-notes?tabs=managed-instance#known-issues) voor de meest recente updates.

## <a name="limitations"></a>Beperkingen

Tijdens de open bare preview gelden de volgende beperkingen voor Server vertrouwens groepen.
 * De naam van de vertrouwens groep van de server moet uniek zijn in alle regio's waar de leden zich bevinden.
 * De groep kan alleen Azure SQL Managed instances bevatten en ze moeten zich onder hetzelfde Azure-abonnement bevinden.
 * De groep kan precies twee beheerde exemplaren hebben. Als u gedistribueerde trans acties wilt uitvoeren voor meer dan twee beheerde exemplaren die kunnen worden uitgevoerd door een server vertrouwensrelatie groep te maken voor elk paar beheerde exemplaren.
 * Gedistribueerde trans acties zijn het enige toepasselijke bereik voor de server vertrouwens groepen.
 * De vertrouwens groep van de server kan alleen worden beheerd vanuit Azure Portal. De ondersteuning voor Power shell en CLI wordt later weer geboden.
 * De vertrouwens groep van de server kan niet worden bewerkt op het Azure Portal. Deze kan alleen worden gemaakt of verwijderd.
 * Aanvullende beperkingen van gedistribueerde trans acties kunnen betrekking hebben op uw scenario. De meest voor de hand zijnde SQL-instanties die deel uitmaken van de server vertrouwens groep moeten bereikbaar zijn via particuliere eind punten of in een andere connectiviteit moeten werken op het niveau van het virtuele netwerk. Zorg ervoor dat u op de hoogte bent van de beperkingen van de huidige [gedistribueerde trans acties voor Azure SQL Managed instance](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview#limitations).

## <a name="next-steps"></a>Volgende stappen

* Zie [gedistribueerde trans acties](../database/elastic-transactions-overview.md)voor meer informatie over gedistribueerde trans acties in Azure SQL Managed instance.
* Zie [release opmerkingen voor beheerde instanties](../database/doc-changes-updates-release-notes.md)voor release-updates en bekende problemen.
* Voor functie aanvragen voegt u deze toe aan het [forum Managed instance](https://feedback.azure.com/forums/915676-sql-managed-instance).
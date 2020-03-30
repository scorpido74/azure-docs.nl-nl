---
title: Db of tabel niet maken of verwijderen in Azure Data Explorer
description: In dit artikel worden stappen voor het oplossen van problemen beschreven voor het maken en verwijderen van databases en tabellen in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 67e4c33498b05884fe667a7977ddb40e647ab4c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562391"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Problemen oplossen: een database of tabel niet maken of verwijderen in Azure Data Explorer

In Azure Data Explorer werkt u regelmatig met databases en tabellen. In dit artikel worden stappen voor het oplossen van problemen voor problemen die kunnen opduiken.

## <a name="creating-a-database"></a>Een database maken

1. Zorg ervoor dat u over de benodigde machtigingen beschikt. Als u een database wilt maken, moet u lid zijn van de rol *Inzender* of *Eigenaar* voor het Azure-abonnement. Werk indien nodig samen met uw abonnementsbeheerder, zodat ze u kunnen toevoegen aan de juiste rol.

1. Zorg ervoor dat er geen fouten zijn voor naamvalidatie voor de databasenaam. De naam moet alfanumeriek zijn, met een maximale lengte van 260 tekens.

1. Controleer of de waarden voor het bewaren en opslaan van cache binnen de toegestane bereiken vallen. Retentie moet tussen 1 en 36500 dagen (100 jaar) liggen. Caching moet tussen 1 en de maximale waarde voor retentie.

## <a name="deleting-or-renaming-a-database"></a>Een database verwijderen of de naam van een database wijzigen

Zorg ervoor dat u over de benodigde machtigingen beschikt. Als u een database wilt verwijderen of de naam wijzigen, moet u lid zijn van de rol *Inzender* of *Eigenaar* voor het Azure-abonnement. Werk indien nodig samen met uw abonnementsbeheerder, zodat ze u kunnen toevoegen aan de juiste rol.

## <a name="creating-a-table"></a>Een tabel maken

1. Zorg ervoor dat u over de benodigde machtigingen beschikt. Als u een tabel wilt maken, moet u lid zijn van de rol van de *databasebeheerder* of *databasegebruiker* in de database of de rol *Inzender* of *Eigenaar* voor het Azure-abonnement. Werk indien nodig samen met uw abonnement of clusterbeheerder, zodat ze u kunnen toevoegen aan de juiste rol.

    Zie [Databasemachtigingen beheren](manage-database-permissions.md) voor meer informatie over machtigingen.

1. Zorg ervoor dat een tabel met dezelfde naam nog niet bestaat. Als deze bestaat, u: Een tabel maken met een andere naam; de naam van de bestaande tabel wijzigen (vereist *een tabelbeheerrol);* of de bestaande tabel laten vallen (vereist *een databasebeheerrol).* Gebruik de volgende opdrachten.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Een tabel verwijderen of de naam van een tabel wijzigen

Zorg ervoor dat u over de benodigde machtigingen beschikt. Als u een tabel wilt verwijderen of de naam wilt wijzigen, moet u lid zijn van de *rol van databasebeheerder* of *tabelbeheerder* in de database. Werk indien nodig samen met uw abonnement of clusterbeheerder, zodat ze u kunnen toevoegen aan de juiste rol.

Zie [Databasemachtigingen beheren](manage-database-permissions.md) voor meer informatie over machtigingen.

## <a name="general-guidance"></a>Algemene richtlijnen

1. Controleer het [dashboard met de status van de Azure-service](https://azure.microsoft.com/status/). Zoek naar de status van Azure Data Explorer in het gebied waar u probeert te werken met een database of tabel.

    Als de status niet **goed** is (groen vinkje), probeer het dan opnieuw nadat de status is verbeterd.

1. Als u nog steeds hulp nodig hebt bij het oplossen van uw probleem, opent u een ondersteuningsverzoek in de [Azure-portal.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="next-steps"></a>Volgende stappen

[Clusterstatus controleren](check-cluster-health.md)
---
title: Het maken of verwijderen van een Data Base of tabel in azure Data Explorer is mislukt
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen met het maken en verwijderen van data bases en tabellen in azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 67e4c33498b05884fe667a7977ddb40e647ab4c8
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562391"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Problemen oplossen: fout bij het maken of verwijderen van een Data Base of tabel in azure Data Explorer

In azure Data Explorer werkt u regel matig met data bases en tabellen. In dit artikel worden de stappen beschreven voor het oplossen van problemen die zich kunnen voordoen.

## <a name="creating-a-database"></a>Een database maken

1. Zorg ervoor dat u over de benodigde machtigingen beschikt. Als u een Data Base wilt maken, moet u lid zijn van de rol *Inzender* of *eigenaar* voor het Azure-abonnement. Als dat nodig is, kunt u samen werken met uw abonnements beheerder zodat ze u kunnen toevoegen aan de juiste rol.

1. Zorg ervoor dat er geen naam validatie fouten zijn voor de database naam. De naam moet alfanumeriek zijn, met een maximale lengte van 260 tekens.

1. Zorg ervoor dat de waarden voor het bewaren en opslaan in de data base binnen toegestane bereiken vallen. De Bewaar periode moet tussen 1 en 36500 dagen (100 jaar) liggen. Cache gebruik moet tussen 1 en de maximum waarde zijn ingesteld voor retentie.

## <a name="deleting-or-renaming-a-database"></a>Een Data Base verwijderen of een andere naam geven

Zorg ervoor dat u over de benodigde machtigingen beschikt. Als u een Data Base wilt verwijderen of de naam ervan wilt wijzigen, moet u lid zijn van de rol *Inzender* of *eigenaar* voor het Azure-abonnement. Als dat nodig is, kunt u samen werken met uw abonnements beheerder zodat ze u kunnen toevoegen aan de juiste rol.

## <a name="creating-a-table"></a>Een tabel maken

1. Zorg ervoor dat u over de benodigde machtigingen beschikt. Als u een tabel wilt maken, moet u lid zijn van de rol *database beheerder* of *database gebruiker* in de data base of de rol *Inzender* of *eigenaar* voor het Azure-abonnement. Als dat nodig is, kunt u samen werken met uw abonnement of cluster beheerder zodat ze u kunnen toevoegen aan de juiste rol.

    Zie [Databasemachtigingen beheren](manage-database-permissions.md) voor meer informatie over machtigingen.

1. Zorg ervoor dat er nog geen tabel met dezelfde naam bestaat. Als deze bestaat, kunt u het volgende doen: een tabel met een andere naam maken. de naam van de bestaande tabel wijzigen (vereist *tabel* beheerdersrol); of verwijder de bestaande tabel (vereist *database* beheerdersrol). Gebruik de volgende opdrachten.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Een tabel verwijderen of de naam ervan wijzigen

Zorg ervoor dat u over de benodigde machtigingen beschikt. Als u een tabel wilt verwijderen of de naam ervan wilt wijzigen, moet u lid zijn van de rol *database beheerder* of *tabel beheerder* in de-data base. Als dat nodig is, kunt u samen werken met uw abonnement of cluster beheerder zodat ze u kunnen toevoegen aan de juiste rol.

Zie [Databasemachtigingen beheren](manage-database-permissions.md) voor meer informatie over machtigingen.

## <a name="general-guidance"></a>Algemene richtlijnen

1. Controleer het [dashboard met de status van de Azure-service](https://azure.microsoft.com/status/). Zoek naar de status van de Azure-Data Explorer in de regio waarin u wilt werken met een Data Base of tabel.

    Als de status niet **goed** is (groen vinkje), probeert u het opnieuw nadat de status is verbeterd.

1. Als u nog hulp nodig hebt bij het oplossen van uw probleem, opent u een ondersteunings aanvraag in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Volgende stappen

[De cluster status controleren](check-cluster-health.md)
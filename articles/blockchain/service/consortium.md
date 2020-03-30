---
title: Azure Blockchain Service consortium
description: Overzicht van hoe Azure Blockchain-service blockchain-netwerken van consortiums implementeert.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247616"
---
# <a name="azure-blockchain-service-consortium"></a>Azure Blockchain Service consortium

Met Azure Blockchain Service u blockchain-netwerken voor privé-consortiums maken waarbij elk blockchain-netwerk kan worden beperkt tot specifieke deelnemers in het netwerk. Alleen deelnemers aan het blockchain-netwerk van het particuliere consortium kunnen de blockchain bekijken en ermee communiceren. Consortiumnetwerken in Azure Blockchain Service kunnen twee typen rollen voor deelnemers van leden bevatten:

* **Beheerder** - Bevoorrechte deelnemers die consortiummanagementacties kunnen ondernemen en kunnen deelnemen aan blockchain-transacties.

* **Gebruiker** - Deelnemers die geen consortiummanagementactie kunnen ondernemen, maar kunnen deelnemen aan blockchain-transacties.

Consortiumnetwerken kunnen een mix zijn van deelnemersrollen en kunnen een willekeurig aantal van elk roltype hebben. Er moet ten minste één beheerder zijn.

In het volgende diagram ziet u een consortiumnetwerk met meerdere deelnemers:

![Diagram voor het particuliere consortiumnetwerk](./media/consortium/network-diagram.png)

Met consortiumbeheer in Azure Blockchain Service u deelnemers in het consortiumnetwerk beheren. Het beheer van het consortium is gebaseerd op het consensusmodel van het netwerk. In de huidige preview-release biedt Azure Blockchain Service een gecentraliseerd consensusmodel voor consortiumbeheer. Elke bevoorrechte deelnemer met een beheerrol kan consortiumbeheeracties uitvoeren, zoals het toevoegen of verwijderen van deelnemers uit een netwerk.

## <a name="roles"></a>Rollen

Deelnemers aan een consortium kunnen individuen of organisaties zijn en kunnen een gebruikersrol of een beheerdersrol toegewezen krijgen. In de volgende tabel worden de verschillen op hoog niveau tussen de twee rollen weergegeven:

| Actie | Gebruikersrol | Beheerdersrol
|--------|:----:|:------------:|
| Nieuw lid maken | Ja | Ja |
| Nieuwe leden uitnodigen | Nee | Ja |
| Rol van deelnemer van leden instellen of wijzigen | Nee | Ja |
| De naam van de weergavevan leden wijzigen | Alleen voor eigen lid | Alleen voor eigen lid |
| Leden verwijderen | Alleen voor eigen lid | Ja |
| Deelnemen aan blockchain-transacties | Ja | Ja |

### <a name="user-role"></a>Gebruikersrol

Gebruikers zijn consortiumdeelnemers zonder beheerdersmogelijkheden. Zij kunnen niet deelnemen aan het beheer van leden die banden hebben met het consortium. Gebruikers kunnen de weergavenaam van hun lid wijzigen en zichzelf uit een consortium verwijderen.

### <a name="administrator"></a>Beheerder

Een beheerder kan leden binnen het consortium beheren. Een beheerder kan leden uitnodigen, leden verwijderen of ledenrollen binnen het consortium bijwerken.
Er moet altijd ten minste één beheerder binnen een consortium zijn. De laatste beheerder moet een andere deelnemer opgeven als beheerdersrol voordat u een consortium verlaat.

## <a name="managing-members"></a>Leden beheren

Alleen beheerders kunnen andere deelnemers uitnodigen voor het consortium. Beheerders nodigen deelnemers uit met hun Azure-abonnements-ID.

Zodra ze zijn uitgenodigd, kunnen deelnemers lid worden van het blockchain-consortium door een nieuw lid in Azure Blockchain Service te implementeren. Als u het uitgenodigde consortium wilt weergeven en lid wilt worden, moet u dezelfde Azure-abonnements-id opgeven die wordt gebruikt in de uitnodiging van de netwerkbeheerder.

Beheerders kunnen elke deelnemer uit het consortium verwijderen, inclusief andere beheerders. Leden kunnen zich alleen uit een consortium verwijderen.

## <a name="consortium-management-smart-contract"></a>Slim contract consortiummanagement

Consortiumbeheer in Azure Blockchain Service gebeurt via slimme contracten voor consortiumbeheer. De slimme contracten worden automatisch geïmplementeerd op uw knooppunten wanneer u een nieuw blockchain-lid implementeert.

Het adres van het slimme contract voor rootconsortiumbeheer kan worden weergegeven in de Azure-portal. Het **RootContract-adres** bevindt zich in de overzichtssectie van het blockchain-lid.

![RootContract-adres](./media/consortium/rootcontract-address.png)

U communiceren met het slim contract voor consortiumbeheer met behulp van de [PowerShell-module](manage-consortium-powershell.md)voor consortiumbeheer, Azure-portal of rechtstreeks via het slimme contract met behulp van het Azure Blockchain-servicegegenereerde Ethereum-account.

## <a name="ethereum-account"></a>Ethereum-account

Wanneer een lid wordt gemaakt, wordt een Ethereum-accountsleutel gemaakt. Azure Blockchain Service gebruikt de sleutel om transacties te maken met betrekking tot consortiumbeheer. De Ethereum-accountsleutel wordt automatisch beheerd door Azure Blockchain Service.

Het ledenaccount kan worden weergegeven in de Azure-portal. Het ledenaccount bevindt zich in de overzichtssectie van het blockchain-lid.

![Ledenaccount](./media/consortium/member-account.png)

U uw Ethereum-account opnieuw instellen door op uw ledenaccount te klikken en een nieuw wachtwoord in te voeren. Zowel het Ethereum-accountadres als het wachtwoord worden opnieuw ingesteld.  

## <a name="next-steps"></a>Volgende stappen

Consortiumbeheeracties zijn toegankelijk via PowerShell. Zie [Consortiumleden beheren in Azure Blockchain-service voor](manage-consortium-powershell.md)meer informatie met PowerShell.

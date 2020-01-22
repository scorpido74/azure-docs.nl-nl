---
title: Azure Portal Dash boards delen met behulp van RBAC | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u een dash board kunt delen in de Azure Portal door gebruik te maken van op rollen gebaseerde Access Control.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: mblythe
ms.openlocfilehash: ce94a35ebcbf5d8cf3d176f05ac75ea5da5d8d99
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310744"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Azure-dashboards delen met behulp van op rollen gebaseerd toegangsbeheer

Nadat u een dash board hebt geconfigureerd, kunt u het publiceren en delen met andere gebruikers in uw organisatie. U kunt anderen uw dash board weer geven met behulp van [op rollen gebaseerde Access Control](../role-based-access-control/role-assignments-portal.md)van Azure. U wijst een gebruiker of een groep gebruikers aan een rol toe en die rol definieert of deze gebruikers het gepubliceerde dash board kunnen weer geven of wijzigen. 

Alle gepubliceerde Dash boards worden geïmplementeerd als Azure-resources, wat betekent dat ze als beheer bare items in uw abonnement bestaan en deel uitmaken van een resource groep.  Vanuit een perspectief voor toegangs beheer zijn Dash boards niet anders dan andere resources, zoals een virtuele machine of een opslag account.

> [!TIP]
> Afzonderlijke tegels op het dash board afdwingen hun eigen vereisten voor toegangs beheer op basis van de resources die ze weer geven.  Daarom kunt u een dash board ontwerpen dat breed wordt gedeeld en de gegevens op afzonderlijke tegels toch beveiligen.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Meer informatie over toegangs beheer voor dash boards
Met Access Control op basis van rollen (RBAC) kunt u gebruikers toewijzen aan rollen op drie verschillende niveaus van bereik:

* abonnement
* resourcegroep
* resource

De machtigingen die u toewijst, worden overgenomen van een abonnement op de resource. Het gepubliceerde dash board is een resource. Daarom is het mogelijk dat er al gebruikers zijn toegewezen aan rollen voor het abonnement die ook werken voor het gepubliceerde dash board. 

Hier volgt een voorbeeld.  Stel dat u een Azure-abonnement hebt en dat er aan verschillende leden van uw team de rollen van **eigenaar**, **bijdrager**of **lezer** voor het abonnement zijn toegewezen. Gebruikers die eigen aars of mede werkers zijn, kunnen Dash boards in het abonnement weer geven, bekijken, maken, wijzigen of verwijderen.  Gebruikers die lezers kunnen een lijst maken en ze weer geven, maar ze kunnen ze niet wijzigen of verwijderen.  Gebruikers met toegang tot de lezer kunnen lokale bewerkingen op een gepubliceerd dash board maken (zoals bij het oplossen van problemen met een probleem), maar kunnen deze wijzigingen niet weer publiceren op de server.  Ze hebben de mogelijkheid om voor zichzelf een persoonlijke kopie van het dash board te maken

U kunt echter ook machtigingen toewijzen aan de resource groep die verschillende Dash boards of aan een afzonderlijk dash board bevat. U kunt bijvoorbeeld besluiten dat een groep gebruikers beperkte machtigingen moet hebben voor het abonnement, maar wel meer toegang tot een bepaald dash board. U wijst deze gebruikers toe aan een rol voor dat dash board. 

## <a name="publish-dashboard"></a>Dashboard publiceren
Stel dat u klaar bent met het configureren van een dash board dat u wilt delen met een groep gebruikers in uw abonnement. In de onderstaande stappen ziet u een aangepaste groep met de naam opslag beheerders, maar u kunt uw groep een naam gegeven wat u wilt. Zie [groepen beheren in azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie over het maken van een Active Directory groep en het toevoegen van gebruikers aan die groep.

1. Selecteer **delen**in het dash board.
   
     ![share selecteren](./media/azure-portal-dashboard-share-access/select-share.png)
2. Voordat u toegang toewijst, moet u het dash board publiceren. Het dash board wordt standaard gepubliceerd naar een resource groep met de naam **Dash boards**. Selecteer **Publiceren**.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Het dash board is nu gepubliceerd. Als de machtigingen die zijn overgenomen van het abonnement, geschikt zijn, hoeft u verder niets te doen. Andere gebruikers in uw organisatie kunnen het dash board openen en aanpassen op basis van de rol op abonnements niveau. Voor deze zelf studie gaan we echter een groep gebruikers toewijzen aan een rol voor dat dash board.

## <a name="assign-access-to-a-dashboard"></a>Toegang tot een dash board toewijzen
1. Nadat u het dash board hebt gepubliceerd, selecteert u **gebruikers beheren**.
   
     ![gebruikers beheren](./media/azure-portal-dashboard-share-access/manage-users.png)
2. U ziet een lijst met bestaande gebruikers waaraan al een rol is toegewezen voor dit dash board. De lijst met bestaande gebruikers wijkt af van de onderstaande afbeelding. Waarschijnlijk worden de toewijzingen overgenomen van het abonnement. Selecteer **toevoegen**om een nieuwe gebruiker of groep toe te voegen.
   
     ![gebruiker toevoegen](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Selecteer de rol die de machtigingen vertegenwoordigt die u wilt verlenen. Selecteer voor dit voor beeld **Inzender**.
   
     ![rol selecteren](./media/azure-portal-dashboard-share-access/select-role.png)
4. Selecteer de gebruiker of groep die u aan de rol wilt toewijzen. Als de gebruiker of groep die u zoekt, niet in de lijst wordt weer geven, gebruikt u het zoekvak. De lijst met beschik bare groepen is afhankelijk van de groepen die u hebt gemaakt in uw Active Directory.
   
     ![gebruiker selecteren](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Wanneer u klaar bent met het toevoegen van gebruikers of groepen, selecteert u **OK**. 
6. De nieuwe toewijzing wordt toegevoegd aan de lijst met gebruikers. U ziet dat de **toegang** wordt weer gegeven als **toegewezen** in plaats van worden **overgenomen**.
   
     ![toegewezen rollen](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Volgende stappen
* Zie [RBAC: ingebouwde rollen](../role-based-access-control/built-in-roles.md)voor een lijst met rollen.
* Zie [Azure-resources beheren via de portal](resource-group-portal.md)voor meer informatie over het beheren van resources.


---
title: Toewijzingen voor Azure-resources met de Azure-portal weergeven
description: Meer informatie over het weergeven van de gebruikers, groepen, serviceprincipals en beheerde identiteiten die toegang hebben gekregen tot specifieke Azure-bronacties op bepaalde scopes met behulp van de Azure-portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4db76e5c6191457346ca1f95678cf73843334d3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137430"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Toewijzingen voor Azure-resources voor Azure-resources weergeven met behulp van de Azure-portal

[Toewijzingen weigeren](deny-assignments.md) blokkeren gebruikers om specifieke Azure-bronacties uit te voeren, zelfs als een roltoewijzing hen toegang verleent. In dit artikel wordt beschreven hoe u weigeringstoewijzingen weergeven met behulp van de Azure-portal.

> [!NOTE]
> Je niet direct je eigen weigeringsopdrachten maken. Zie Toewijzingen weigeren voor informatie over het maken van [weigeringsopdrachten.](deny-assignments.md)

## <a name="prerequisites"></a>Vereisten

Als u informatie wilt krijgen over een weigeringsopdracht, moet u het:

- `Microsoft.Authorization/denyAssignments/read`machtigingen, die is opgenomen in de meeste [ingebouwde rollen voor Azure-resources.](built-in-roles.md)

## <a name="list-deny-assignments"></a>Lijst met geweigerde toewijzingen weergeven

Volg deze stappen om weigeringstoewijzingen aan te bieden in het bereik van de abonnements- of beheergroep.

1. Klik in de Azure-portal op **Alle services** en vervolgens **beheergroepen** of **abonnementen**.

1. Klik op de beheergroep of het abonnement dat u wilt aanbieden.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **Toewijzingen weigeren** (of klik op de knop **Weergave** op de tegel Toewijzingen weergeven).

    Als er een in dit bereik toewijzingen zijn geweigerd of zijn overgenomen, worden deze weergegeven.

    ![Toegangsbeheer - tabblad Toewijzingen weigeren](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Als u extra kolommen wilt weergeven, klikt u op **Kolommen bewerken**.

    ![Toewijzingen weigeren - Kolommen](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Naam** | Naam van de weigeringsopdracht. |
    | **Hoofdtype** | Gebruiker, groep, systeemgedefinieerde groep of serviceprincipal. |
    | **Geweigerd**  | Naam van de beveiligingsprincipal die is opgenomen in de toewijzing weigeren. |
    | **Id** | Unieke id voor de weigeringstoewijzing. |
    | **Uitgesloten opdrachtgevers** | Of er beveiligingsprincipals zijn die zijn uitgesloten van de weigeringstoewijzing. |
    | **Is niet van toepassing op kinderen** | Of de toewijzing weigeren is overgenomen aan subscopen. |
    | **Systeem beveiligd** | Of de weigeringstoewijzing wordt beheerd door Azure. Momenteel, altijd ja. |
    | **Scope** | Beheergroep, abonnement, resourcegroep of resource. |

1. Voeg een vinkje toe aan een van de ingeschakelde items en klik op **OK** om de geselecteerde kolommen weer te geven.

## <a name="list-details-about-a-deny-assignment"></a>Details over een weigeringstoewijzing weergeven

Volg deze stappen om aanvullende details over een weigeringstoewijzing op te sommen.

1. Open het deelvenster **Toewijzingen weigeren** zoals beschreven in de vorige sectie.

1. Klik op de naam van de toewijzing weigeren om het blad **Gebruikers** te openen.

    ![Toewijzing weigeren - Gebruikers](./media/deny-assignments-portal/deny-assignment-users.png)

    Het **gebruikersblad** bevat de volgende twee secties.

    |  |  |
    | --- | --- |
    | **Weigeringstoewijzing is van toepassing op**  | Beveiligingsprincipals waarop de weigeringsopdracht van toepassing is. |
    | **Toewijzing weigeren is uitgesloten** | Beveiligingsprincipals die zijn uitgesloten van de weigeringstoewijzing. |

    **System-Defined Principal** vertegenwoordigt alle gebruikers, groepen, serviceprincipals en beheerde identiteiten in een Azure AD-map.

1. Als u een lijst wilt zien met de machtigingen die worden geweigerd, klikt u op **Geweigerde machtigingen**.

    ![Toewijzing weigeren - Geweigerde machtigingen](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Actietype | Beschrijving |
    | --- | --- |
    | **Acties**  | Geweigerde beheeroperaties. |
    | **NotActions** | Beheeractiviteiten die zijn uitgesloten van geweigerde beheeractiviteiten. |
    | **Gegevensacties**  | Geweigerde gegevensbewerkingen. |
    | **NietGegevensacties** | Gegevensbewerkingen zijn uitgesloten van de bewerking van geweigerde gegevens. |

    Voor het voorbeeld in de vorige schermafbeelding zijn de volgende de effectieve machtigingen:

    - Alle opslagbewerkingen op het gegevensvlak worden geweigerd, behalve voor rekenbewerkingen.

1. Als u de eigenschappen voor een weigeringstoewijzing wilt bekijken, klikt u op **Eigenschappen**.

    ![Toewijzing weigeren - Eigenschappen](./media/deny-assignments-portal/deny-assignment-properties.png)

    Op het blad **Eigenschappen** ziet u de naam, id, beschrijving en bereik van de toewijzing weigeren. De **schakelaar Is niet van toepassing op kinderen** en geeft aan of de weigeringstoewijzing is overgenomen naar subscopes. De **systeembeveiligde** switch geeft aan of deze weigeringstoewijzing wordt beheerd door Azure. Momenteel is dit **ja** in alle gevallen.

## <a name="next-steps"></a>Volgende stappen

* [Inzicht in weigeringstoewijzingen voor Azure-resources](deny-assignments.md)
* [Toewijzingen voor Azure-resources voor Azure-resources weergeven met Azure PowerShell](deny-assignments-powershell.md)

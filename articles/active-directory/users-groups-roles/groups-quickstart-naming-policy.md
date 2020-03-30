---
title: Snel start beleid voor groepsnaamgeving - Azure Active Directory | Microsoft Documenten
description: Legt uit hoe u nieuwe gebruikers toevoegt of bestaande gebruikers verwijdert in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e419ac5c3d292b7e630d1ebb3d3b9f59ef7b8a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74026935"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Snelstart: naambeleid voor groepen in Azure Active Directory

In deze snelstart stelt u naambeleid in uw Azure Active Directory (Azure AD)-tenant in voor Office 365-groepen die door gebruikers zijn gemaakt. Hierdoor kunt u de groepen van de tenant sorteren en doorzoeken. U kunt het naambeleid onder meer gebruiken voor:

* Het communiceren van de functie van een groep, het lidmaatschap, de geografische regio of de maker van een groep.
* Het classificeren van groepen in het adresboek.
* Zorgen dat het gebruik van bepaalde woorden voor groepsnamen en -aliassen wordt geblokkeerd.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal"></a>Het groepsnaamgevingsbeleid configureren voor een tenant met Azure-portal

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een gebruikersbeheerdersaccount.
1. Selecteer **Groepen**en selecteer **vervolgens Naamgevingsbeleid** om de pagina Naamgevingsbeleid te openen.

    ![de pagina Naamgevingsbeleid openen in het beheercentrum](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Het naamgevingsbeleid voor voorvoegsel weergeven of bewerken

1. Selecteer op de pagina **Naamgevingsbeleid** de optie **Groepsnaamgevingsbeleid**.
1. U het huidige voorvoegsel- of achtervoegselnaamgevingsbeleid afzonderlijk weergeven of bewerken door de kenmerken of tekenreeksen te selecteren die u wilt afdwingen als onderdeel van het naamgevingsbeleid.
1. Als u een voorvoegsel of achtervoegsel uit de lijst wilt verwijderen, selecteert u het voorvoegsel of achtervoegsel en selecteert u **Verwijderen**. Meerdere items kunnen tegelijkertijd worden verwijderd.
1. Selecteer **Opslaan** voor uw wijzigingen in het beleid dat in werking treedt.

### <a name="view-or-edit-the-custom-blocked-words"></a>De aangepaste geblokkeerde woorden weergeven of bewerken

1. Selecteer op de pagina **Naamgevingsbeleid** de optie **Geblokkeerde woorden**.

    ![lijst met geblokkeerde woorden bewerken en uploaden voor naamgevingsbeleid](./media/groups-naming-policy/blockedwords.png)

1. De huidige lijst met aangepaste geblokkeerde woorden weergeven of bewerken door **Downloaden te**selecteren.
1. Upload de nieuwe lijst met aangepaste geblokkeerde woorden door het bestandspictogram te selecteren.
1. Selecteer **Opslaan** voor uw wijzigingen in het beleid dat in werking treedt.

Dat is alles. U hebt het naambeleid ingesteld en uw aangepaste, geblokkeerde woorden toegevoegd.

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="remove-the-naming-policy-using-azure-portal"></a>Het naamgevingsbeleid verwijderen met Azure-portal

1. Selecteer op de pagina **Naamgevingsbeleid** de optie **Beleid verwijderen**.
1. Nadat u de verwijdering hebt bevestigd, wordt het naamgevingsbeleid verwijderd, inclusief alle naamgevingsbeleid voor voorvoegsel en aangepaste geblokkeerde woorden.

## <a name="next-steps"></a>Volgende stappen

In deze snelle start hebt u geleerd hoe u het naamgevingsbeleid voor uw Azure AD-organisatie instellen via de Azure-portal.

Ga naar het volgende artikel voor meer informatie, waaronder de PowerShell-cmdlets voor naamgevingsbeleid, technische beperkingen, het toevoegen van een lijst met aangepaste geblokkeerde woorden en de ervaringen van de eindgebruiker in Office 365-apps.
> [!div class="nextstepaction"]
> [Naamgevingsbeleid PowerShell](groups-naming-policy.md)
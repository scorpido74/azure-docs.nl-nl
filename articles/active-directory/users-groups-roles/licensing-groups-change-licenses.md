---
title: Licentieplannen voor gebruikers en groepen wijzigen - Azure AD | Microsoft Documenten
description: Gebruikers binnen een groep migreren naar verschillende serviceplannen met groepslicenties in Azure Active Directory
services: active-directory
keywords: Azure AD-licenties
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2f04e1728f94c89bddcc31c287cc017a79020f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025906"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Licentietoewijzingen voor een gebruiker of groep wijzigen in Azure Active Directory

In dit artikel wordt beschreven hoe u gebruikers en groepen verplaatst tussen servicelicentieplannen in Azure Active Directory (Azure AD). Het doel van Azure AD is ervoor te zorgen dat er geen verlies van service of gegevens tijdens de licentiewijziging. Gebruikers moeten naadloos schakelen tussen services. In de toewijzingsstappen voor licentieplannen in dit artikel wordt beschreven dat een gebruiker of groep in Office 365 E1 in Office 365 E3 wordt gewijzigd, maar de stappen zijn van toepassing op alle licentieplannen. Wanneer u licentietoewijzingen voor een gebruiker of groep bijwerkt, worden de verwijderingen van licentietoewijzingen en nieuwe toewijzingen gelijktijdig uitgevoerd, zodat gebruikers geen toegang tot hun services verliezen tijdens licentiewijzigingen of licentieconflicten tussen plannen zien.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u de licentietoewijzingen bijwerkt, is het belangrijk om te controleren of bepaalde veronderstellingen gelden voor alle gebruikers of groepen die moeten worden bijgewerkt. Als de veronderstellingen niet waar zijn voor alle gebruikers in een groep, kan de migratie voor sommige gebruikers mislukken. Als gevolg hiervan kunnen sommige gebruikers de toegang tot services of gegevens verliezen. Zorg ervoor dat:

- Gebruikers hebben het huidige licentieplan (in dit geval Office 365 E1) dat is toegewezen aan een groep en wordt overgenomen door de gebruiker en niet rechtstreeks is toegewezen.

- U hebt voldoende beschikbare licenties voor het licentieplan dat u toevertrouwt. Als u niet genoeg licenties hebt, krijgen sommige gebruikers mogelijk geen nieuw licentieplan toegewezen. U het aantal beschikbare licenties controleren.

- Gebruikers hebben geen andere toegewezen servicelicenties die in strijd kunnen zijn met de gewenste licentie of verwijdering van de huidige licentie kunnen voorkomen. Bijvoorbeeld een licentie van een service zoals Workplace Analytics of Project Online die afhankelijk is van andere services.

- Als u groepen on-premises beheert en deze synchroniseert met Azure AD via Azure AD Connect, voegt u gebruikers toe of verwijdert u deze met behulp van uw on-premises systeem. Het kan enige tijd duren voordat de wijzigingen worden gesynchroniseerd met Azure AD door groepslicenties.

- Als u azure AD-dynamische groepslidmaatschappen gebruikt, voegt of verwijdert u gebruikers door hun kenmerken te wijzigen, maar blijft het updateproces voor licentietoewijzingen hetzelfde.

## <a name="change-user-license-assignments"></a>Gebruikerslicentietoewijzingen wijzigen

Als u op de pagina **Licentietoewijzingen** bijwerken ziet dat sommige selectievakjes niet beschikbaar zijn, worden services weergegeven die niet kunnen worden gewijzigd omdat ze zijn overgenomen van een groepslicentie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een licentiebeheerdersaccount in uw Azure AD-organisatie.
1. Selecteer **Azure Active Directory** > **Users**en open de **profielpagina** voor een gebruiker.
1. Selecteer **Licenties**.
1. Selecteer **Toewijzingen** om licentietoewijzing voor de gebruiker of groep te bewerken. Op de pagina **Toewijzingen** u conflicten met licentietoewijzingoplossen.
1. Schakel het selectievakje voor Office 366 E3 in en controleer of ten minste alle E1-services die aan de gebruiker zijn toegewezen, zijn geselecteerd.
1. Schakel het selectievakje voor Office 365 E1 uit.

    ![pagina licentietoewijzingen voor een gebruiker met Office 365 E1 gewist en Office 365 E3 geselecteerd](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Selecteer **Opslaan**.

Azure AD past de nieuwe licenties toe en verwijdert de oude licenties tegelijkertijd om servicecontinuïteit te bieden.

## <a name="change-group-license-assignments"></a>Groepslicentietoewijzingen wijzigen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een licentiebeheerdersaccount in uw Azure AD-organisatie.
1. Selecteer Azure Active**Directory-groepen** **Azure Active Directory** > en open de pagina **Overzicht** voor een groep.
1. Selecteer **Licenties**.
1. Selecteer de opdracht **Toewijzingen** om licentietoewijzing voor de gebruiker of groep te bewerken.
1. Schakel het selectievakje voor Office 366 E3 in. Als u de continuïteit van de service wilt behouden, moet u ervoor zorgen dat u alle E1-services selecteert die al aan de gebruiker zijn toegewezen.
1. Schakel het selectievakje voor Office 365 E1 uit.

    ![De opdracht Toewijzingen selecteren op een pagina met gebruikers- of groepslicenties](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Selecteer **Opslaan**.

Om servicecontinuïteit te bieden, past Azure AD de nieuwe licenties toe en verwijdert het de oude licenties tegelijkertijd voor alle gebruikers in de groep.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere scenario's voor licentiebeheer via groepen in de volgende artikelen:

- [Licenties toewijzen aan een groep in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
- [Licentieproblemen voor een groep vaststellen en oplossen in Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
- [Individuele gelicentieerde gebruikers migreren naar groepslicenties in Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
- [Aanvullende scenario's voor Azure Active Directory-groeplicenties](../users-groups-roles/licensing-group-advanced.md)
- [PowerShell-voorbeelden voor groepslicenties in Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)

---
title: Groepseigenaren toevoegen of verwijderen - Azure Active Directory | Microsoft Documenten
description: Instructies voor het toevoegen of verwijderen van groepseigenaren met Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83a48ad13fe4ae217319b2aa85adf976aa6a7ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149844"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Groepseigenaren toevoegen of verwijderen in Azure Active Directory
Azure Active Directory-groepen (Azure AD) zijn eigendom van en worden beheerd door groepseigenaren. Groepseigenaren kunnen gebruikers of serviceprincipals zijn en kunnen de groep inclusief lidmaatschap beheren. Alleen bestaande groepseigenaren of groepsbeheerders kunnen groepseigenaren toewijzen. Groepseigenaren hoeven geen lid te zijn van de groep.

Wanneer een groep geen eigenaar heeft, kunnen groepsbeheerders de groep nog steeds beheren. Het wordt aanbevolen voor elke groep om ten minste één eigenaar te hebben. Zodra eigenaren zijn geassueerd aan een groep, kan de laatste eigenaar van de groep niet worden verwijderd. Zorg ervoor dat u een andere eigenaar selecteert voordat u de laatste eigenaar uit de groep verwijdert.

## <a name="add-an-owner-to-a-group"></a>Een eigenaar toevoegen aan een groep
Hieronder vindt u instructies voor het toevoegen van een gebruiker als eigenaar aan een groep met behulp van de Azure AD-portal. Als u een serviceprincipal als eigenaar van een groep wilt toevoegen, volgt u de instructies om dit te doen met [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Een groepseigenaar toevoegen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**, selecteer **Groepen**en selecteer vervolgens de groep waarvoor u een eigenaar wilt toevoegen (in dit voorbeeld *MDM-beleid - West).*

3. Selecteer **Eigenaren**op de pagina **MDM-beleid - Westoverzicht** .

    ![MDM-beleid - Overzichtspagina West met optie Eigenaren gemarkeerd](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Selecteer op de pagina **MDM-beleid - West - Eigenaren** de optie Eigenaren **toevoegen**en zoek en selecteer vervolgens de gebruiker die de nieuwe groepseigenaar is en kies **Vervolgens Selecteren**.

    ![MDM-beleid - West - Pagina Eigenaren met optie Eigenaren toevoegen gemarkeerd](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Nadat u de nieuwe eigenaar hebt geselecteerd, u de pagina **Eigenaren** vernieuwen en de naam zien die is toegevoegd aan de lijst met eigenaren.

## <a name="remove-an-owner-from-a-group"></a>Een eigenaar uit een groep verwijderen
Een eigenaar uit een groep verwijderen met Azure AD.

### <a name="to-remove-an-owner"></a>Een eigenaar verwijderen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**, selecteer **Groepen**en selecteer vervolgens de groep waarvoor u een eigenaar wilt verwijderen (in dit voorbeeld *MDM-beleid - West).*

3. Selecteer **Eigenaren**op de pagina **MDM-beleid - Westoverzicht** .

    ![MDM-beleid - Overzichtspagina West met optie Eigenaren gemarkeerd](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Selecteer op de pagina **MDM-beleid - West - Eigenaren** de gebruiker die u als groepseigenaar wilt verwijderen, kies **Verwijderen** op de informatiepagina van de gebruiker en selecteer **Ja** om uw beslissing te bevestigen.

    ![Pagina informatie van de gebruiker met de optie Verwijderen gemarkeerd](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Nadat u de eigenaar hebt verwijderd, u terugkeren naar de pagina **Eigenaren** en zien dat de naam is verwijderd uit de lijst met eigenaren.

## <a name="next-steps"></a>Volgende stappen
- [Managing access to resources with Azure Active Directory groups](active-directory-manage-groups.md) (Toegang tot resources beheren met Azure Active Directory-groepen)

- [Azure Active Directory cmdlets voor het configureren van groepsinstellingen](../users-groups-roles/groups-settings-cmdlets.md)

- [Groepen gebruiken om toegang toe te wijzen aan een geïntegreerde SaaS-app](../users-groups-roles/groups-saasapps.md)

- [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](../hybrid/whatis-hybrid-identity.md)

- [Azure Active Directory cmdlets voor het configureren van groepsinstellingen](../users-groups-roles/groups-settings-v2-cmdlets.md)

---
title: Gebruikers beheren in Azure FarmBeats
description: In dit artikel wordt beschreven hoe u gebruikers beheert in Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 6ccc29422c6abf1120d13c05d10125efd0871ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502064"
---
# <a name="manage-users"></a>Gebruikers beheren

Azure FarmBeats bevat gebruikersbeheer voor mensen die deel uitmaken van uw Azure Active Directory-exemplaar (Azure AD). U gebruikers toevoegen aan uw Azure FarmBeats-exemplaar om toegang te krijgen tot de API's, de gegenereerde kaarten te bekijken en toegang te krijgen tot sensortelemetrie vanuit de farm.

## <a name="prerequisites"></a>Vereisten

- Azure FarmBeats-installatie is vereist. Zie [Azure FarmBeats installeren](install-azure-farmbeats.md)voor meer informatie.
- De e-id's van de gebruikers die u wilt toevoegen of verwijderen uit uw Azure FarmBeats-exemplaar.

## <a name="manage-azure-farmbeats-users"></a>Azure FarmBeats-gebruikers beheren

Azure FarmBeats gebruikt Azure AD voor verificatie, toegangscontrole en rollen. U gebruikers in de Azure AD-tenant toevoegen als gebruikers in Azure FarmBeats.

> [!NOTE]
> Als een gebruiker geen Azure AD-tenantgebruiker is, volgt u de instructies in de sectie **Azure AD-gebruikers toevoegen** om de installatie te voltooien.

Azure FarmBeats ondersteunt twee typen gebruikersrollen:

 - **Beheerder**: Volledige toegang tot Azure FarmBeats Datahub API's. Gebruikers in deze rol kunnen alle Azure FarmBeats Datahub-objecten opvragen en alle bewerkingen uitvoeren vanuit de FarmBeats Accelerator.
 - **Alleen-lezen**: Alleen-lezen toegang tot FarmBeats Datahub API's. Gebruikers kunnen de Datahub API's, de Accelerator Dashboards en de kaarten bekijken. Gebruikers met alleen-lezen toegang kunnen geen bewerkingen uitvoeren, zoals het genereren van kaarten, het koppelen van apparaten of het maken van boerderijen.

## <a name="add-users-to-azure-farmbeats"></a>Gebruikers toevoegen aan Azure FarmBeats

Gebruikers toevoegen aan Azure FarmBeats:

1. Meld u aan bij Accelerator en selecteer het pictogram **Instellingen.**
2. Selecteer **Toegangsbeheer**.

    ![Het deelvenster Instellingen voor landbouwbedrijven](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Voer de e-id in van de gebruiker waartoe u toegang wilt verlenen.
4. Selecteer de gewenste rol, **Beheerder** of **Alleen-lezen**.
5. Selecteer **Rol toevoegen**.

De toegevoegde gebruiker heeft nu toegang tot Azure FarmBeats (zowel Datahub als Accelerator).

## <a name="delete-users-from-azure-farmbeats"></a>Gebruikers verwijderen van Azure FarmBeats

Ga als u gebruikers uit het Azure FarmBeats-systeem verwijderen:

1. Meld u aan bij Accelerator en selecteer het pictogram **Instellingen.**
2. Selecteer **Toegangsbeheer**.
3. Selecteer **Verwijderen**.

   De gebruiker wordt uit het systeem verwijderd. Je ontvangt het volgende bevestigingsbericht:

   ![Bevestigingsbericht Azure FarmBeats](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Azure AD-gebruikers toevoegen

> [!NOTE]
> Azure FarmBeats-gebruikers moeten in de Azure AD-tenant bestaan voordat u ze toewijst aan toepassingen en rollen. Als een gebruiker niet bestaat in de Azure AD-tenant, volgt u de instructies in deze sectie. Sla de instructies over als er al een gebruiker bestaat in de Azure AD-tenant.

Volg de stappen om gebruikers toe te voegen aan Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer rechtsboven uw account en schakel over naar de Azure AD-tenant die is gekoppeld aan FarmBeats.
3. Selecteer **Azure Active Directory** > **Users**.

    Er wordt een lijst met Azure AD-gebruikers weergegeven.

4. Als u een gebruiker aan de map wilt toevoegen, selecteert u **Nieuwe gebruiker**. Als u een externe gebruiker wilt toevoegen, selecteert u **Nieuwe gastgebruiker**.

    ![Het deelvenster 'Alle gebruikers'](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Selecteer de naam van de nieuwe gebruiker en vul vervolgens de vereiste velden voor die gebruiker in.
6. Selecteer **Maken**.

Zie [Gebruikers toevoegen of verwijderen in Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/)voor informatie over het beheren van Azure AD-gebruikers.

## <a name="next-steps"></a>Volgende stappen

U hebt gebruikers toegevoegd aan uw Azure FarmBeats-exemplaar. Nu, leren hoe [je boerderijen te creëren en te beheren](manage-farms-in-azure-farmbeats.md#create-farms).

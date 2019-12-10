---
title: Gebruikers beheren
description: In dit artikel wordt beschreven hoe u gebruikers beheert.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: f58db00e598fd08972adb60cbd8aae0d3bf3391d
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74942299"
---
# <a name="manage-users"></a>Gebruikers beheren

Azure FarmBeats bevat gebruikers beheer voor personen die deel uitmaken van uw Azure Active Directory (Azure AD). U kunt gebruikers toevoegen aan uw Azure FarmBeats-exemplaar om toegang te krijgen tot de Api's, de gegenereerde kaarten en de telemetrie van de sensor van de farm weer te geven.

## <a name="prerequisites"></a>Vereisten

- Een Azure FarmBeats-implementatie is vereist. Zie [FarmBeats implementeren](prepare-for-deployment.md) voor meer informatie over het instellen van Azure FarmBeats.
- De e-mail-ID van de gebruikers die u wilt toevoegen aan of verwijderen uit uw Azure FarmBeats-exemplaar.

## <a name="manage-azure-farmbeats-users"></a>Azure FarmBeats-gebruikers beheren

Azure FarmBeats gebruikt Azure AD voor verificatie, Toegangs beheer en rollen. Gebruikers in de Azure AD-Tenant kunnen worden toegevoegd als gebruikers in azure FarmBeats.

> [!NOTE]
> Als de gebruiker die u wilt toevoegen niet aanwezig is in de Azure AD-Tenant, volgt u de instructies in het gedeelte **Azure AD-gebruikers toevoegen** om de installatie te volt ooien voordat u doorgaat met het instellen van de gebruikers als Azure FarmBeats.

**Rollen**

Er zijn momenteel twee soorten gebruikers rollen die in azure FarmBeats worden ondersteund:

 - **Beheerder** : alle toegang tot Azure FarmBeats data hub-api's. Gebruikers met deze rol kunnen alle Azure FarmBeats data hub-objecten opvragen, alle bewerkingen van de FarmBeats-Accelerator uitvoeren.
 - **Alleen-** lezen toegang tot FarmBeats data hub-api's. Gebruikers kunnen de data hub Api's, de Dash boards en de kaarten weer geven. Een gebruiker met de rol alleen-lezen kan geen bewerkingen uitvoeren zoals het genereren van Maps, het koppelen van apparaten of het maken van farms.


## <a name="add-user-to-azure-farmbeats"></a>Gebruiker toevoegen aan Azure FarmBeats

Volg de stappen om een gebruiker toe te voegen aan Azure FarmBeats:

1.  Meld u aan bij de Accelerator en selecteer vervolgens het pictogram **instellingen** .
2.  Selecteer **Access Control**.

    ![Maten van project-Farm](./media/create-farms/settings-users-1.png)

3.  Voer de e-mail-ID in van de gebruiker die u toegang wilt geven.
4.  Selecteer de gewenste rol: beheerder of alleen-lezen.
5.  Selecteer **rol toevoegen**.

De toegevoegde gebruiker (s) heeft nu toegang tot Azure FarmBeats (Data hub en Accelerator).

## <a name="delete-user-from-azure-farmbeats"></a>Gebruiker verwijderen uit Azure FarmBeats

Volg de stappen om een gebruiker uit het Azure FarmBeats-systeem te verwijderen:

1.  Meld u aan bij de Accelerator en selecteer vervolgens het pictogram **instellingen** .
2.  Selecteer **Access Control**.
3.  Selecteer **verwijderen** om een gebruiker te verwijderen.

De gebruiker wordt verwijderd uit het systeem. U ontvangt het volgende bericht om te controleren of de bewerking is geslaagd.

![Maten van project-Farm](./media/create-farms/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Azure AD-gebruikers toevoegen

> [!NOTE]
> Volg de onderstaande stappen om gebruikers toegang te geven tot Azure FarmBeats als de gebruiker niet bestaat in de Azure AD-Tenant. U kunt de onderstaande stappen overs Laan als de gebruiker zich in de Azure AD-Tenant bevindt.
>

FarmBeats-gebruikers moeten zich in de Azure AD-Tenant bevinden voordat u ze aan toepassingen en rollen kunt toewijzen. Gebruik de volgende stappen om gebruikers toe te voegen aan Azure AD:

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2.  Selecteer uw account in de rechter bovenhoek en schakel over naar de Azure AD-Tenant die is gekoppeld aan FarmBeats.
3.  Selecteer **Azure Active Directory > gebruikers**.
    U ziet een lijst met gebruikers in uw Directory.
4.  Selecteer **nieuwe gebruiker**om gebruikers toe te voegen aan de Directory. Voor externe gebruikers selecteert u **nieuwe gast gebruiker**.

    ![Maten van project-Farm](./media/create-farms/manage-users-3.png)

5.  Vul de vereiste velden in voor de nieuwe gebruiker. Selecteer **Maken**.

Raadpleeg de documentatie van [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/) voor meer informatie over het beheren van gebruikers binnen Azure AD.

## <a name="next-steps"></a>Volgende stappen

U hebt gebruikers toegevoegd aan het Azure FarmBeats-exemplaar. Nu leert u hoe u [Farms maakt](manage-farms.md#create-farms).

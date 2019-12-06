---
title: Gebruikers beheren
description: Hierin wordt beschreven hoe u gebruikers beheert
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 37dacf0adfc9e3dcbab963cef50e2ee5209df314
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852352"
---
# <a name="manage-users"></a>Gebruikers beheren

Azure FarmBeats bevat gebruikers beheer voor personen die deel uitmaken van uw Azure Active Directory (Azure AD). U kunt gebruikers toevoegen aan uw Azure FarmBeats-exemplaar om toegang te krijgen tot de Api's, de gegenereerde kaarten en de telemetrie van de sensor in de farm te bekijken

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

Als u een gebruiker wilt toevoegen aan Azure FarmBeats, 
1.  Meld u aan bij de Accelerator en klik op het pictogram instellingen
2.  Klik op Access Control

    ![Maten van project-Farm](./media/create-farms/settings-users-1.png)

3.  Voer de e-mail-ID in van de gebruiker aan wie u toegang wilt verlenen
4.  Selecteer de gewenste rol: beheerder of alleen-lezen
5.  Klik op rol toevoegen

De toegevoegde gebruiker (s) heeft nu toegang tot Azure FarmBeats (Data hub en Accelerator).

## <a name="delete-user-from-azure-farmbeats"></a>Gebruiker verwijderen uit Azure FarmBeats

Als u een gebruiker uit het Azure FarmBeats-systeem wilt verwijderen, kunt u
1.  Meld u aan bij de Accelerator en klik op het pictogram instellingen
2.  Klik op Access Control
3.  Klik op het pictogram verwijderen bij de e-mail-ID van de gebruiker die u wilt verwijderen

De gebruiker wordt verwijderd uit het systeem. Het volgende bericht wordt weer gegeven om de geslaagde bewerking te bevestigen


![Maten van project-Farm](./media/create-farms/manage-users-2.png)


## <a name="add-azure-ad-users"></a>Azure AD-gebruikers toevoegen

> [!NOTE]
> U moet de volgende stappen uitvoeren, alleen als de gebruiker die u probeert toegang te geven tot Azure FarmBeats niet aanwezig is in de Azure AD-Tenant. Als de gebruiker al aanwezig is, moeten de onderstaande stappen niet worden uitgevoerd

FarmBeats-gebruikers moeten zich in de Azure AD-Tenant bevinden voordat u ze aan toepassingen en rollen kunt toewijzen. Gebruik de volgende stappen om gebruikers toe te voegen aan Azure AD:
1.  Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2.  Selecteer uw account in de rechter bovenhoek en schakel over naar de Azure AD-Tenant die is gekoppeld aan FarmBeats
3.  Selecteer **Azure Active Directory > gebruikers**. U ziet een lijst met gebruikers in uw Directory.
4.  Selecteer **nieuwe gebruiker**om gebruikers toe te voegen aan de Directory. Voor externe gebruikers selecteert u **nieuwe gast gebruiker**.

    ![Maten van project-Farm](./media/create-farms/manage-users-3.png)

5.  Vul de vereiste velden in voor de nieuwe gebruiker. Selecteer **Maken**.

Raadpleeg de documentatie van [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/) voor meer informatie over het beheren van gebruikers binnen Azure AD.

## <a name="next-steps"></a>Volgende stappen

U hebt Azure FarmBeats geïmplementeerd. Nu leert u hoe u [Farms maakt](manage-farms.md#create-farms).


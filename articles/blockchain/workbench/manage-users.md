---
title: Gebruikers beheren in Azure Blockchain Workbench
description: Gebruikers beheren in Azure Blockchain Workbench.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 573ec477a3e75beb91f90da0545fb7d4c0f9bf39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252179"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Gebruikers beheren in Azure Blockchain Workbench

Azure Blockchain Workbench bevat gebruikersbeheer voor mensen en organisaties die deel uitmaken van uw consortium.

## <a name="prerequisites"></a>Vereisten

Een Blockchain Workbench-implementatie is vereist. Zie [Azure Blockchain Workbench-implementatie](deploy.md) voor meer informatie over implementatie.

## <a name="add-azure-ad-users"></a>Azure AD-gebruikers toevoegen

De Azure Blockchain Workbench gebruikt Azure Active Directory (Azure AD) voor verificatie, toegangscontrole en rollen. Gebruikers in de Azure AD-tenant van Blockchain Workbench kunnen Blockchain Workbench verifiëren en gebruiken. Voeg gebruikers toe aan de functie Administrator-toepassings om te communiceren en acties uit te voeren.

Blockchain Workbench-gebruikers moeten in de Azure AD-tenant bestaan voordat u ze toewijzen aan toepassingen en rollen. Als u gebruikers wilt toevoegen aan Azure AD, gebruikt u de volgende stappen:

1.  Meld u aan bij [Azure Portal](https://portal.azure.com).
2.  Selecteer uw account in de rechterbovenhoek en schakel over naar de Azure AD-tenant die is gekoppeld aan Blockchain Workbench.
3.  Selecteer **Azure Active Directory > Gebruikers**. U ziet een lijst met gebruikers in uw directory.
4.  Als u gebruikers aan de map wilt toevoegen, selecteert u **Nieuwe gebruiker**. Selecteer **Nieuwe gastgebruiker**voor externe gebruikers .

    ![Nieuwe gebruiker](./media/manage-users/add-ad-user.png)

5.  Vul de vereiste velden voor de nieuwe gebruiker in. Selecteer **Maken**.

Ga naar [Azure AD-documentatie](../../active-directory/fundamentals/add-users-azure-active-directory.md) voor meer informatie over het beheren van gebruikers binnen Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Blockchain Workbench-beheerders beheren

Zodra gebruikers aan de directory zijn toegevoegd, is de volgende stap om te kiezen welke gebruikers Blockchain Workbench-beheerders zijn. Gebruikers in de **groep Administrator** zijn gekoppeld aan de **toepassingsrol Administrator** in Blockchain Workbench. Beheerders kunnen gebruikers toevoegen of verwijderen, gebruikers toewijzen aan specifieke scenario's en nieuwe toepassingen maken.

Als u gebruikers wilt toevoegen aan de **beheerdersgroep** in de Azure AD-map:

1.  Meld u aan bij [Azure Portal](https://portal.azure.com).
2.  Controleer of u zich in de Azure AD-tenant bevindt die is gekoppeld aan Blockchain Workbench door uw account in de rechterbovenhoek te selecteren.
3.  Selecteer **Azure Active Directory > Enterprise-toepassingen**.
4.  De Azure AD-clienttoepassing voor Blockchain Workbench selecteren
    
    ![Alle registraties van bedrijfstoepassingen](./media/manage-users/select-blockchain-client-app.png)

5.  Selecteer **Gebruikers en groepen > Gebruiker toevoegen**.
6.  Selecteer in **Toewijzing toevoegen** de optie **Gebruikers**. Kies of zoek naar de gebruiker die u als beheerder wilt toevoegen. Klik **op Selecteren** wanneer u klaar bent met kiezen.

    ![Toewijzing toevoegen](./media/manage-users/add-user-assignment.png)

9.  Controleren **of rol** is ingesteld op **beheerder**
10. Selecteer **Toewijzen**. De toegevoegde gebruikers worden weergegeven in de lijst met de toegewezen beheerdersrol.

    ![Gebruikers van blockchain-clientapps](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Blockchain Workbench-leden beheren

Gebruik de Blockchain Workbench-toepassing om gebruikers en organisaties te beheren die deel uitmaken van uw consortium. U gebruikers toevoegen of verwijderen aan toepassingen en rollen.

1. [Open de Blockchain Workbench](deploy.md#blockchain-workbench-web-url) in uw browser en meld u aan als beheerder.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Leden worden toegevoegd aan elke toepassing. Leden kunnen een of meer toepassingsrollen hebben om contracten te starten of acties uit te voeren.

2. Als u leden voor een toepassing wilt beheren, selecteert u een toepassingstegel in het deelvenster **Toepassingen.**

    Het aantal leden dat aan de geselecteerde toepassing is gekoppeld, wordt weergegeven in de ledentegel.

    ![Toepassing selecteren](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Lid toevoegen aan toepassing

1. Selecteer de ledentegel om een lijst met de huidige leden weer te geven.
2. Selecteer **Leden toevoegen**.

    ![Leden toevoegen](./media/manage-users/application-add-members.png)

3. Zoek naar de naam van de gebruiker.  Alleen Azure AD-gebruikers die voorkomen in de Blockchain Workbench-tenant staan vermeld. Als de gebruiker niet wordt gevonden, moet u [Azure AD-gebruikers toevoegen.](#add-azure-ad-users)

    ![Leden toevoegen](./media/manage-users/find-user.png)

4. Selecteer een **rol** in de vervolgkeuzelijst.

    ![Rolleden selecteren](./media/manage-users/application-select-role.png)

5. Selecteer **Toevoegen** om het lid met de gekoppelde rol aan de toepassing toe te voegen.

#### <a name="remove-member-from-application"></a>Lid uit toepassing verwijderen

1. Selecteer de ledentegel om een lijst met de huidige leden weer te geven.
2. Kies **Verwijderen** uit de vervolgkeuzelijst voor de gebruiker die u wilt verwijderen.

    ![Lid verwijderen](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Rol wijzigen of toevoegen

1. Selecteer de ledentegel om een lijst met de huidige leden weer te geven.
2. Voor de gebruiker die u wilt wijzigen, klikt u op de vervolgkeuzelijst en selecteert u de nieuwe rol.

    ![Rol wijzigen](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u gebruikers beheren voor Azure Blockchain Workbench. Ga door naar het volgende how-to-artikel om te leren hoe je een blockchain-toepassing maakt.

> [!div class="nextstepaction"]
> [Een blockchain-toepassing maken in Azure Blockchain Workbench](create-app.md)

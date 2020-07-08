---
title: Azure Stack Edge gebruikers beheren | Microsoft Docs
description: Hierin wordt beschreven hoe u de Azure Portal gebruikt om gebruikers te beheren op uw Azure Stack rand.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 1012ce417fb4952878dada29a9080c70954ed21f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84339634"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-azure-stack-edge"></a>De Azure Portal gebruiken om gebruikers te beheren op uw Azure Azure Stack Edge

In dit artikel wordt beschreven hoe u gebruikers op uw Azure Stack Edge beheert. U kunt de Azure Stack Edge beheren via de Azure Portal of via de lokale web-UI. Gebruik Azure Portal om gebruikers toe te voegen, te wijzigen of te verwijderen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een gebruiker toevoegen
> * Gebruiker wijzigen
> * Een gebruiker verwijderen

## <a name="about-users"></a>Over gebruikers

Gebruikers kunnen het recht alleen-lezen of volledige bevoegdheid hebben. Zoals de naam al aangeeft, kunnen gebruikers met het recht alleen-lezen de sharegegevens alleen weergeven. Gebruikers met volledige bevoegdheid kunnen sharegegevens lezen, schrijven, wijzigen of verwijderen.

 - **Gebruiker met volledige bevoegdheden** - een lokale gebruiker met volledige toegang.
 - **Alleen-lezengebruiker** - een lokale gebruiker met alleen-lezentoegang. Deze gebruikers zijn gekoppeld aan shares waarmee alleen-lezenbewerkingen mogelijk zijn.

De gebruikersmachtigingen worden eerst gedefinieerd wanneer de gebruiker wordt gemaakt tijdens het maken van de share. Het wijzigen van machtigingen op share niveau wordt momenteel niet ondersteund.

## <a name="add-a-user"></a>Een gebruiker toevoegen

Voer in Azure Portal de volgende stappen uit om een gebruiker toe te voegen.

1. Ga in het Azure Portal naar de resource Azure Stack Edge en ga vervolgens naar **overzicht > gebruikers**. Selecteer **+ gebruiker toevoegen** op de opdracht balk.

    ![Gebruiker toevoegen selecteren](media/azure-stack-edge-manage-users/add-user-1.png)

2. Geef de gebruikersnaam en het wachtwoord op voor de gebruiker die u wilt toevoegen. Bevestig het wacht woord en selecteer **toevoegen**.

    ![Gebruikers naam en wacht woord opgeven](media/azure-stack-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Deze gebruikers zijn gereserveerd door het systeem en moeten niet worden gebruikt: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Er wordt een melding weer gegeven wanneer het maken van de gebruiker wordt gestart en voltooid. Nadat de gebruiker is gemaakt, selecteert u op de opdracht balk **vernieuwen** om de bijgewerkte lijst met gebruikers weer te geven.


## <a name="modify-user"></a>Gebruiker wijzigen

U kunt het wachtwoord dat is gekoppeld aan een gebruiker, wijzigen wanneer de gebruiker is gemaakt. Selecteer in de lijst met gebruikers. Voer het nieuwe wacht woord in en bevestig dit. Sla de wijzigingen op.
 
![Gebruiker wijzigen](media/azure-stack-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Een gebruiker verwijderen

Voer in Azure Portal de volgende stappen uit om een gebruiker te verwijderen.


1. Ga in het Azure Portal naar de resource Azure Stack Edge en ga vervolgens naar **overzicht > gebruikers**.

    ![Selecteer de gebruiker die u wilt verwijderen](media/azure-stack-edge-manage-users/delete-user-1.png)

2. Selecteer een gebruiker in de lijst met gebruikers en selecteer vervolgens **verwijderen**.  

   ![Verwijderen selecteren](media/azure-stack-edge-manage-users/delete-user-2.png)

3. Bevestig de verwijdering als u daarom wordt gevraagd. 

   ![De verwijdering bevestigen](media/azure-stack-edge-manage-users/delete-user-3.png)

De lijst met gebruikers wordt bijgewerkt en de verwijderde gebruiker wordt niet meer weergegeven.

![Bijgewerkte lijst met gebruikers](media/azure-stack-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Bandbreedte beheren](azure-stack-edge-manage-bandwidth-schedules.md).

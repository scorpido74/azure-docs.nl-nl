---
title: Azure Data Box Edge beheergebruikers | Microsoft Documenten
description: Beschrijft hoe u de Azure-portal gebruiken om gebruikers te beheren op uw Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 443ed983a0eec5dfd8f7a917fbc1440cd66c3db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946128"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>De Azure-portal gebruiken om gebruikers te beheren op uw Azure Data Box Edge

In dit artikel wordt beschreven hoe u gebruikers beheert op uw Azure Data Box Edge. U de Azure Data Box Edge beheren via de Azure-portal of via de lokale webgebruikersinterface. Gebruik Azure Portal om gebruikers toe te voegen, te wijzigen of te verwijderen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een gebruiker toevoegen
> * Gebruiker wijzigen
> * Een gebruiker verwijderen

## <a name="about-users"></a>Over gebruikers

Gebruikers kunnen het recht alleen-lezen of volledige bevoegdheid hebben. Zoals de naam al aangeeft, kunnen gebruikers met het recht alleen-lezen de sharegegevens alleen weergeven. Gebruikers met volledige bevoegdheid kunnen sharegegevens lezen, schrijven, wijzigen of verwijderen.

 - **Gebruiker met volledige bevoegdheden** - een lokale gebruiker met volledige toegang.
 - **Alleen-lezengebruiker** - een lokale gebruiker met alleen-lezentoegang. Deze gebruikers zijn gekoppeld aan shares waarmee alleen-lezenbewerkingen mogelijk zijn.

De gebruikersmachtigingen worden eerst gedefinieerd wanneer de gebruiker wordt gemaakt tijdens het maken van de share. Wijziging van machtigingen op share-level wordt momenteel niet ondersteund.

## <a name="add-a-user"></a>Een gebruiker toevoegen

Voer in Azure Portal de volgende stappen uit om een gebruiker toe te voegen.

1. Ga in de Azure-portal naar uw Data Box Edge-bron en ga vervolgens naar **Overzicht > Gebruikers**. Selecteer **+ Gebruiker toevoegen** op de opdrachtbalk.

    ![Gebruiker toevoegen selecteren](media/data-box-edge-manage-users/add-user-1.png)

2. Geef de gebruikersnaam en het wachtwoord op voor de gebruiker die u wilt toevoegen. Bevestig het wachtwoord en selecteer **Toevoegen**.

    ![Gebruikersnaam en wachtwoord opgeven](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Deze gebruikers zijn gereserveerd door het systeem en moeten niet worden gebruikt: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Er wordt een melding weergegeven wanneer de aanmaak van de gebruiker wordt gestart en voltooid. Nadat de gebruiker is gemaakt, selecteert u op de opdrachtbalk **Vernieuwen** om de bijgewerkte lijst met gebruikers weer te geven.


## <a name="modify-user"></a>Gebruiker wijzigen

U kunt het wachtwoord dat is gekoppeld aan een gebruiker, wijzigen wanneer de gebruiker is gemaakt. Kies in de lijst met gebruikers. Voer het nieuwe wachtwoord in en bevestig deze. Sla de wijzigingen op.
 
![Gebruiker wijzigen](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Een gebruiker verwijderen

Voer in Azure Portal de volgende stappen uit om een gebruiker te verwijderen.


1. Ga in de Azure-portal naar uw Data Box Edge-bron en ga vervolgens naar **Overzicht > Gebruikers**.

    ![Gebruiker selecteren om te verwijderen](media/data-box-edge-manage-users/delete-user-1.png)

2. Selecteer een gebruiker in de lijst met gebruikers en selecteer **Verwijderen**.  

   ![Verwijderen selecteren](media/data-box-edge-manage-users/delete-user-2.png)

3. Bevestig de verwijdering als u daarom wordt gevraagd. 

   ![De verwijdering bevestigen](media/data-box-edge-manage-users/delete-user-3.png)

De lijst met gebruikers wordt bijgewerkt en de verwijderde gebruiker wordt niet meer weergegeven.

![Bijgewerkte lijst met gebruikers](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Bandbreedte beheren](data-box-edge-manage-bandwidth-schedules.md).

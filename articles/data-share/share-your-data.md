---
title: 'Zelfstudie: Share buiten uw organisatie-preview van Azure data-share'
description: 'Zelf studie: gegevens delen met klanten en partners met behulp van Azure data share preview'
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: f7df46a6a6f149ef0228fda8c967469a25dc3d50
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327422"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Zelfstudie: Uw gegevens delen met behulp van Azure data share preview

In deze zelf studie leert u hoe u een nieuwe Azure-gegevens share instelt en hoe u uw gegevens met klanten en partners buiten uw Azure-organisatie gaat delen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een gegevens share.
> * Voeg gegevens sets toe aan uw gegevens share.
> * Een synchronisatie schema inschakelen voor uw gegevens share. 
> * Voeg ontvangers toe aan uw gegevens share. 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Een Azure Storage-account: Als u er nog geen hebt, kunt u een [Azure Storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) maken
* Machtiging voor het toevoegen van roltoewijzing aan het opslag account dat aanwezig is in de machtiging *micro soft. Authorization/roltoewijzingen/schrijven* . Deze machtiging bevindt zich in de rol van eigenaar. 
* Het e-mail adres van uw ontvangers voor Azure (met behulp van hun e-mail alias) werkt niet.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Een gegevens share-account maken

Een Azure-gegevens share bron maken in een Azure-resource groep.

1. Selecteer de knop **Een resource maken** (+) in de linkerbovenhoek van de portal.

1. Zoeken naar *gegevens share*.

1. Selecteer gegevens share (preview) en selecteer **maken**.

1. Vul de basis gegevens van uw Azure-gegevens share bron in met de volgende gegevens. 

     **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | Name | *datashareacount* | Geef een naam op voor uw gegevens share-account. |
    | Subscription | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw gegevens share-account.|
    | Resource group | *test-resource-group* | Gebruik een bestaande resourcegroep of maak een nieuwe. |
    | Location | *VS-Oost 2* | Selecteer een regio voor uw gegevens share-account.
    | | |

1. Selecteer **maken** om uw gegevens share-account in te richten. Het inrichten van een nieuw gegevens share account duurt doorgaans ongeveer 2 minuten of minder. 

1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.

## <a name="create-a-data-share"></a>Een gegevens share maken

1. Ga naar de pagina overzicht van gegevens delen.

    ![Uw gegevens]delen met(./media/share-receive-data.png "uw gegevens") 

1. Selecteer **beginnen met het delen van uw gegevens**.

1. Selecteer **Maken**.   

1. Vul de details in voor uw gegevens share. Geef een naam, beschrijving van de share-inhoud en gebruiks voorwaarden op (optioneel). 

    ![EnterShareDetails](./media/enter-share-details.png "invoeren") 

1. Selecteer **door gaan**

1. Selecteer gegevens **sets toevoegen**om gegevens sets toe te voegen aan uw gegevens share. 

    ![](./media/datasets.png "Gegevens sets") gegevensgegevens sets

1. Selecteer het type gegevensset dat u wilt toevoegen. 

    ![](./media/add-datasets.png "Gegevens sets AddDatasets toevoegen")    

1. Navigeer naar het object dat u wilt delen en selecteer gegevens sets toevoegen. 

    ![SelectDatasets](./media/select-datasets.png "selecteren")    

1. Voer op het tabblad ontvangers in de e-mail adressen van uw gegevens consument in door + ontvanger toevoegen te selecteren. 

    ![AddRecipients](./media/add-recipient.png "toevoegen") 

1. Selecteer **door gaan**

1. Als u wilt dat uw gegevens gebruiker incrementele updates van uw gegevens kan ophalen, moet u het schema voor de moment opname inschakelen. 

    ![EnableSnapshots]-(./media/enable-snapshots.png "moment opnamen inschakelen") 

1. Selecteer een begin tijd en een herhalings interval. 

1. Selecteer **door gaan**

1. Controleer op het tabblad controleren en maken de inhoud, instellingen, ontvangers en synchronisatie-instellingen van uw pakket. Selecteer **Maken**

Uw Azure-gegevens share is nu gemaakt en de ontvanger van uw gegevens share is nu klaar om uw uitnodiging te accepteren. 

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie leert u hoe u een Azure-gegevens share maakt en ontvangers uitnodigt. Ga door naar de zelf studie voor het [accepteren en ontvangen van gegevens](subscribe-to-data-share.md) voor meer informatie over hoe een gegevens gebruiker een gegevens share kan accepteren en ontvangen. 

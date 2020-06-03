---
title: Systeem onderwerpen maken, weer geven en beheren in Azure Event Grid
description: In dit artikel wordt beschreven hoe u een bestaand systeem onderwerp weergeeft, Azure Event Grid systeem onderwerpen maakt met behulp van de Azure Portal.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: ac22afb351973397960e66c2a8fe86031e0b213a
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316654"
---
# <a name="create-view-and-manage-system-topics-in-azure-event-grid"></a>Systeem onderwerpen maken, weer geven en beheren in Azure Event Grid
In dit artikel leest u hoe u de volgende taken kunt uitvoeren:

- Een systeem onderwerp maken
- Alle bestaande systeem onderwerpen weer geven 
- Een systeem onderwerp verwijderen
- Een gebeurtenis abonnement maken voor een systeem onderwerp


## <a name="create-a-system-topic"></a>Een systeem onderwerp maken
U kunt op twee manieren een systeem onderwerp maken voor een Azure-resource:

- Gebruik de pagina Resource, bijvoorbeeld pagina met het opslag account of de pagina Event Hubs naam ruimte. 
- Op de pagina met **Event grid systeem onderwerpen** . 

Bekijk [deze Snelstartgids](blob-event-quickstart-portal.md) voor een voor beeld van het maken van een systeem onderwerp met behulp van een resource pagina (tabblad**gebeurtenissen** van een resource pagina in de Azure Portal). De volgende stappen zijn voor het maken van een systeem onderwerp met behulp van de pagina **Event grid systeem onderwerpen** . 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Typ **Event grid systeem onderwerpen**in het zoekvak bovenaan en druk op **Enter**. 

    ![Systeem onderwerpen zoeken](./media/create-view-manage-system-topics/search-system-topics.png)
3. Selecteer op de pagina **Event grid systeem onderwerpen** **+ toevoegen** op de werk balk.

    ![Systeem onderwerp toevoegen-werkbalk knop](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. Voer de volgende stappen uit op de pagina **onderwerp Event grid systeem maken** :
    1. Selecteer het **type onderwerp**. In het volgende voor beeld is de optie **opslag accounts** geselecteerd. 
    2. Selecteer het **Azure-abonnement** met uw opslag account bron. 
    3. Selecteer de **resource groep** die het opslag account heeft. 
    4. Selecteer het **opslag account**. 
    5. Voer een **naam** in voor het systeem onderwerp dat moet worden gemaakt. 
    
        > [!NOTE]
        > U kunt de naam van het systeem onderwerp gebruiken om metrische gegevens en Diagnostische logboeken te zoeken.
    6. Selecteer **Controleren + maken**.

        ![Systeem onderwerp maken](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. Controleer de instellingen en selecteer **maken**. 
        
        ![Een systeem onderwerp controleren en maken](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. Nadat de implementatie is voltooid, selecteert u **naar resource gaan** om de pagina met **Event grid systeem onderwerpen** weer te geven voor het systeem onderwerp dat u hebt gemaakt. 

        ![Pagina systeem onderwerp](./media/create-view-manage-system-topics/system-topic-page.png)

## <a name="view-all-system-topics"></a>Alle systeem onderwerpen weer geven
Volg deze stappen om alle bestaande Event Grid systeem onderwerpen weer te geven. 

> [!NOTE]
> Wanneer u eerder een abonnement hebt gemaakt voor een gebeurtenis die door Azure-bronnen wordt gegenereerd, maakt de Event Grid-Service automatisch een systeem onderwerp met een wille keurig gegenereerde naam. U kunt nu een naam opgeven voor het onderwerp System tijdens het maken van het onderwerp. U kunt deze systeem onderwerp Resource gebruiken om metrische gegevens en Diagnostische logboeken te detecteren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Typ **Event grid systeem onderwerpen**in het zoekvak bovenaan en druk op **Enter**. 

    ![Systeem onderwerpen zoeken](./media/create-view-manage-system-topics/search-system-topics.png)
3. Op de pagina **Event grid systeem onderwerpen** ziet u alle systeem onderwerpen. 

    ![Lijst met systeem onderwerpen](./media/create-view-manage-system-topics/list-system-topics.png)
4. Selecteer een **systeem onderwerp** in de lijst om de details ervan weer te geven. 

    ![Details van het systeem onderwerp](./media/create-view-manage-system-topics/system-topic-details.png)

    Op deze pagina vindt u informatie over het systeem onderwerp, zoals de volgende informatie: 
    - Bron. De naam van de resource waarop het systeem onderwerp is gemaakt.
    - Bron type. Het type van de resource. Bijvoorbeeld: `Microsoft.Storage.StorageAccounts` , `Microsoft.EventHub.Namespaces` , enzovoort `Microsoft.Resources.ResourceGroups` .
    - Abonnementen die zijn gemaakt voor het onderwerp System.

    Op deze pagina kunnen bewerkingen zoals de volgende worden uitgevoerd:
    - Een gebeurtenis abonnement maken Selecteer **+ gebeurtenis abonnement** op de werk balk. 
    - Een gebeurtenis abonnement verwijderen. Selecteer **verwijderen** op de werk balk. 
    - Labels voor het onderwerp systeem toevoegen. Selecteer **labels** in het linkermenu en geef label namen en waarden op. 


## <a name="delete-a-system-topic"></a>Een systeem onderwerp verwijderen
1. Volg de instructies in de sectie [systeem onderwerpen weer geven](#view-all-system-topics) om alle systeem onderwerpen weer te geven en selecteer het onderwerp van het systeem dat u wilt verwijderen uit de lijst. 
2. Selecteer op de pagina **Event grid systeem onderwerp** **verwijderen** op de werk balk. 

    ![Systeem onderwerp-knop verwijderen](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. Selecteer op de pagina Bevestiging **OK** om de verwijdering te bevestigen. Het onderwerp System en ook de gebeurtenis abonnementen voor het onderwerp System worden verwijderd.  

## <a name="create-an-event-subscription"></a>Een gebeurtenisabonnement maken
1. Volg de instructies in de sectie [systeem onderwerpen weer geven](#view-all-system-topics) om alle systeem onderwerpen weer te geven en selecteer het onderwerp van het systeem dat u wilt verwijderen uit de lijst. 
2. Selecteer op de pagina **Event grid systeem onderwerp** **+ gebeurtenis abonnement** op de werk balk. 

    ![Systeem onderwerp: knop gebeurtenis abonnement toevoegen](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. Controleer of het **onderwerp type**, **bron resource**en **onderwerpnaam** automatisch worden ingevuld. Voer een naam in, selecteer een **eindpunt type**en geef het **eind punt**op. Selecteer vervolgens **maken** om het gebeurtenis abonnement te maken. 

    ![Systeem onderwerp: gebeurtenis abonnement maken](./media/create-view-manage-system-topics/create-event-subscription.png)


## <a name="next-steps"></a>Volgende stappen
Zie de sectie [systeem onderwerpen in azure Event grid](system-topics.md) voor meer informatie over systeem onderwerpen en onderwerp typen die door Azure Event grid worden ondersteund. 

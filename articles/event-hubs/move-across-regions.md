---
title: Naamruimte azure-gebeurtenishubs naar een andere regio verplaatsen | Microsoft Documenten
description: In dit artikel ziet u hoe u een naamruimte van Azure Event Hubs verplaatst van het huidige gebied naar een andere regio.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 2dfc9c517605bbb48bee0b306fb275464cfebe39
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606806"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Een naamruimte voor Azure Event Hubs verplaatsen naar een ander gebied
Er zijn verschillende scenario's waarin u de naamruimte van uw bestaande gebeurtenishubs van de ene regio naar de andere wilt verplaatsen. U bijvoorbeeld een naamruimte maken met dezelfde configuratie voor het testen. U ook een secundaire naamruimte in een andere regio maken als onderdeel van [noodherstelplanning.](event-hubs-geo-dr.md#setup-and-failover-flow)

> [!NOTE]
> In dit artikel ziet u hoe u een Azure Resource Manager-sjabloon exporteert voor een bestaande naamruimte voor gebeurtenishubs en vervolgens de sjabloon gebruikt om een naamruimte met dezelfde configuratie-instellingen in een andere regio te maken. Dit proces verplaatst echter geen gebeurtenissen die nog niet zijn verwerkt. U moet de gebeurtenissen uit de oorspronkelijke naamruimte verwerken voordat u deze verwijderd.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de services en functies die uw account gebruikt, worden ondersteund in de doelregio.
- Voor preview-functies moet u ervoor zorgen dat uw abonnement op de witte lijst staat voor de doelregio.
- Als u **de opnamefunctie** hebt ingeschakeld voor gebeurtenishubs in de naamruimte, verplaatst u [Azure Storage- of Azure Data Lake Store Gen 2-](../storage/common/storage-account-move.md) of [Azure Data Lake Store Gen 1-accounts](../data-lake-store/data-lake-store-migration-cross-region.md) voordat u de naamruimte van gebeurtenishubs verplaatst. U de resourcegroep met naamruimten opslag- en gebeurtenishubs ook naar het andere gebied verplaatsen door stappen te volgen die vergelijkbaar zijn met die welke in dit artikel worden beschreven. 
- Als de naamruimte van gebeurtenishubs zich in een **cluster van gebeurtenishubs**bevindt, [maakt u een speciaal cluster](event-hubs-dedicated-cluster-create-portal.md) in het **doelgebied** voordat u stappen in dit artikel doorloopt. 

## <a name="prepare"></a>Voorbereiden
Exporteer een resourcemanagersjabloon om aan de slag te gaan. Deze sjabloon bevat instellingen die de naamruimte van uw gebeurtenishubs beschrijven.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer **Alle bronnen** en selecteer vervolgens de naamruimte van uw gebeurtenishubs.

3. Selecteer > **>-instellingen-exportsjabloon**. **Settings**

4. Kies **Downloaden** op de pagina **Sjabloon exporteren.**

    ![Sjabloon ResourceManager downloaden](./media/move-across-regions/download-template.png)

5. Zoek het .zip-bestand dat u van de portal hebt gedownload en rits dat bestand uit naar een map naar keuze.

   Dit zip-bestand bevat de .json-bestanden die de sjabloon en scripts bevatten om de sjabloon te implementeren.


## <a name="move"></a>Verplaatsen

Implementeer de sjabloon om een naamruimte voor gebeurtenishubs in het doelgebied te maken. 


1. Selecteer in Azure Portal **Een resource maken**.

2. In **Marketplace doorzoeken** typt u **sjabloonimplementatie**. Druk vervolgens op **ENTER**.

3. Selecteer **Sjabloonimplementatie**.

4. Selecteer **Maken**.

5. Selecteer **Bouw uw eigen sjabloon in de editor**.

6. Selecteer **Bestand laden**en volg de instructies om het **template.json-bestand** te laden dat u in de laatste sectie hebt gedownload.

7. Selecteer **Opslaan** om de sjabloon op te slaan. 

8. Voer op de pagina **Aangepaste implementatie** de volgende stappen uit: 

    1. Selecteer een **Azure-abonnement**. 

    2. Selecteer een bestaande **resourcegroep** of maak er een. Als de bronnaamruimte zich in een cluster van Gebeurtenishubs bevond, selecteert u de brongroep die cluster bevat in het doelgebied. 

    3. Selecteer de **doellocatie** of -regio. Als u een bestaande resourcegroep hebt geselecteerd, is deze instelling alleen-lezen. 

    4. Ga in de sectie **INSTELLINGEN** de volgende stappen uit:
    
        1. voer de nieuwe **naamruimtenaam**in . 

            ![Sjabloon Resourcemanager implementeren](./media/move-across-regions/deploy-template.png)

        2. Als uw bronnaamruimte zich in een **cluster van gebeurtenishubs**bevond, voert u namen van **het cluster resourcegroep** en **gebeurtenishubs** in als onderdeel van **externe id**. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Als gebeurtenishub in uw naamruimte een opslagaccount gebruikt voor het vastleggen van `StorageAccounts_<original storage account name>_external` gebeurtenissen, geeft u de naam van de brongroep en het opslagaccount voor het veld op. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Schakel het **selectievakje Ik ga akkoord met de bovenstaande voorwaarden.** 
    
    6. Selecteer nu **Aankoop selecteren** om het implementatieproces te starten. 

## <a name="discard-or-clean-up"></a>Weggooien of opruimen
Als u na de implementatie opnieuw wilt beginnen, u de **naamruimte van doelgebeurtenishubs**verwijderen en de stappen herhalen die zijn beschreven in de secties [Voorbereiden](#prepare) en [verplaatsen](#move) van dit artikel.

Als u de wijzigingen wilt vastleggen en de naamruimte van een gebeurtenishubs wilt voltooien, verwijdert u de naamruimte van de **brongebeurtenishubs**. Zorg ervoor dat u alle gebeurtenissen in de naamruimte hebt verwerkt voordat u de naamruimte verwijderd. 

Ga als lid van de naamruimte van een gebeurtenishubs (bron of doel) met de Azure-portal:

1. Typ **Gebeurtenishubs**en selecteer Gebeurtenishubs in het zoekvenster boven aan de Azure-portal en selecteer **Gebeurtenishubs** in zoekresultaten. U ziet de naamruimten van gebeurtenishubs in een lijst.

2. Selecteer de doelnaamruimte die u wilt verwijderen en selecteer **Verwijderen op** de werkbalk. 

    ![Naamruimte verwijderen - knop](./media/move-across-regions/delete-namespace-button.png)

3. Controleer op de pagina **Resources verwijderen*** de geselecteerde bronnen en bevestig de verwijdering door **ja**te typen en selecteer **Vervolgens Verwijderen**. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een naamruimte van Azure Event Hubs van de ene regio naar de andere verplaatst en de bronbronnen opgeschoond.  Zie voor meer informatie over het verplaatsen van resources tussen regio's en disaster recovery in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

---
title: Virtuele Azure-machines verplaatsen naar beschikbaarheids zones in een andere regio met Azure resource verhuizer
description: Meer informatie over het verplaatsen van Azure-Vm's naar beschikbaarheids zones met Azure resource-overgave.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 315ea9b683ccd583f5c29c7527013f0d924336f4
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061870"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Virtuele Azure-machines verplaatsen naar een beschikbaarheids zone in een andere regio

In dit artikel leert u hoe u virtuele Azure-machines (en gerelateerde netwerk-en opslag bronnen) verplaatst naar een beschikbaarheids zone in een andere Azure-regio met behulp van [Azure resource](overview.md)-overstap.

[Azure-beschikbaarheids zones](../availability-zones/az-overview.md#availability-zones) helpen uw Azure-implementatie te beschermen tegen Data Center-fouten. Elke beschikbaarheidszone bestaat uit een of meer datacenters die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. Om tolerantie te garanderen, zijn er mini maal drie afzonderlijke zones in alle [ingeschakelde regio's](../availability-zones/az-region.md). Met resource verplaatsen kunt u het volgende doen:

- Een virtuele machine met één exemplaar naar een beschikbaarheids zone of beschikbaarheidsset in de doel regio.
- Een virtuele machine in een beschikbaarheidsset met een beschikbaarheids zone/beschikbaarheidsset in de doel regio.
- Een virtuele machine in een beschikbaarheids zone van een bron regio naar een beschikbaarheids zone in de doel regio.


> [!IMPORTANT]
> Azure resource-overdrijfing is momenteel beschikbaar als open bare preview.

Als u Vm's wilt verplaatsen naar een andere beschikbaarheids zone in dezelfde regio, [raadpleegt u dit artikel](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="prerequisites"></a>Vereisten

- Toegang voor de *eigenaar* van het abonnement waarin resources zich bevinden die u wilt verplaatsen.
    - De eerste keer dat u een resource toevoegt voor een specifieke bron-en doel toewijzing in een Azure-abonnement, maakt resource-overschakeling een door het [systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (voorheen bekend als managed service identify (MSI)) die wordt vertrouwd door het abonnement.
    - Als u de identiteit wilt maken en de vereiste rol (Inzender of beheerder voor gebruikers toegang in het bron abonnement) wilt toewijzen, moet het account dat u gebruikt om resources toe te voegen *eigenaars* machtigingen voor het abonnement hebben. Meer [informatie](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) over Azure-rollen.
- Het abonnement heeft voldoende quota nodig om de bron resources in de doel regio te maken. Als dat niet het geval is, moet u aanvullende limieten aanvragen. [Meer informatie](/azure/azure-resource-manager/management/azure-subscription-service-limits).
- Controleer de prijzen en kosten die zijn gekoppeld aan de doel regio waarnaar u Vm's verplaatst. Gebruik de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) om u te helpen.
    


## <a name="check-vm-requirements"></a>VM-vereisten controleren

1. Controleer of de virtuele machines die u wilt verplaatsen, worden ondersteund.

    - [Controleer](support-matrix-move-region-azure-vm.md#windows-vm-support) de ondersteunde Windows-vm's.
    - [Controleer](support-matrix-move-region-azure-vm.md#linux-vm-support) ondersteunde vm's en kernel-versies van Linux.
    - Controleer ondersteunde instellingen voor [Compute](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [opslag](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)en [netwerk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
2. Controleer of de Vm's die u wilt verplaatsen, zijn ingeschakeld.
3. Zorg ervoor dat Vm's de meest recente vertrouwde basis certificaten en een bijgewerkte certificaatintrekkingslijst (CRL) hebben. 
    - Installeer de meest recente Windows-updates op virtuele Azure-machines waarop Windows wordt uitgevoerd.
    - Op Vm's waarop Linux wordt uitgevoerd, volgt u de richt lijnen voor Linux-distributeurs om te controleren of de computer de meest recente certificaten en CERTIFICAATINTREKKINGSLIJST heeft. 
4. Uitgaande verbindingen van Vm's toestaan:
    - Als u een firewall proxy op basis van een URL gebruikt voor het beheren van uitgaande connectiviteit, kunt u toegang tot deze [url's](support-matrix-move-region-azure-vm.md#url-access) toestaan
    - Als u regels voor de netwerk beveiligings groep (NSG) gebruikt om de uitgaande connectiviteit te beheren, maakt u deze [service-code regels](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Te verplaatsen resources selecteren

Selecteer de resources die u wilt verplaatsen.

- U kunt elk ondersteund resource type voor alle resource groepen selecteren in de bron regio die u selecteert.
- U kunt resources verplaatsen naar een doel regio in het bron regio abonnement. Als u het abonnement wilt wijzigen, kunt u dat doen nadat de resources zijn verplaatst.

1. Zoek in het Azure Portal naar resource verplaatsen. Selecteer vervolgens onder **Services** **Azure resource**-overschakeling.

    ![Zoeken naar resource verplaatsen](./media/move-region-availability-zone/search.png)

2. Selecteer in **overzicht**aan de **slag**.

    ![Knop om aan de slag te gaan](./media/move-region-availability-zone/get-started.png)

3. Selecteer in bron voor **resources verplaatsen**  >  **+ bestemming**het bron abonnement en de regio.
4. Selecteer in **doel**de regio waarnaar u de vm's wilt verplaatsen. Klik op **Volgende**.

     ![Pagina om het bron-en doel abonnement/de regio in te vullen](./media/move-region-availability-zone/source-target.png)

6. Klik in **resources om te verplaatsen**op **resources selecteren**.
7. Selecteer in **resources selecteren**de virtuele machine. U kunt alleen resources toevoegen die voor verplaatsen worden ondersteund. Klik vervolgens op **gereed**. Klik in **resources om te verplaatsen**op **volgende**.

    ![Pagina voor het selecteren van Vm's die moeten worden verplaatst](./media/move-region-availability-zone/select-vm.png)
8. Controleer de bron-en doel instellingen bij **controleren en toevoegen**.

    ![Pagina om instellingen te controleren en door te gaan met verplaatsen](./media/move-region-availability-zone/review.png)

9. Klik op **door gaan**om te beginnen met het toevoegen van de resources.
10. Nadat het toevoegen is voltooid, klikt u op **resources toevoegen voor verplaatsen** in het meldings pictogram.

    ![Bericht in meldingen](./media/move-region-availability-zone/notification.png)

Nadat u op de melding hebt geklikt, worden de resources weer gegeven op de pagina **over verschillende regio's**

> [!NOTE]
> Nadat u op de melding hebt geklikt, worden de resources weer gegeven op de pagina **over verschillende regio's** , in de status in *behandeling voorbereiden* .
> - Als u een resource uit een verzameling verplaatsen wilt verwijderen, is dit afhankelijk van waar u zich in het Verplaats proces bevindt. [Meer informatie](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Afhankelijkheden oplossen

1. Als resources een bericht voor het *valideren van afhankelijkheden* in de kolom **problemen** weer geven, klikt u op de knop **afhankelijkheden valideren** . Het validatie proces wordt uitgevoerd.
2. Als er afhankelijkheden worden gevonden, klikt u op **afhankelijkheden toevoegen**. 
3. Selecteer bij **afhankelijkheden toevoegen**de afhankelijke resources > **afhankelijkheden toevoegen**. De voortgang in de meldingen bewaken.

    ![Knop voor het toevoegen van afhankelijkheden](./media/move-region-availability-zone/add-dependencies.png)

3. Voeg indien nodig extra afhankelijkheden toe en valideer afhankelijkheden opnieuw. 

    ![Pagina om extra afhankelijkheden toe te voegen](./media/move-region-availability-zone/add-additional-dependencies.png)

4. Controleer op de pagina **over verschillende regio's** of de resources nu in behandeling zijn en zonder problemen worden voor *bereid* .

    ![Pagina met resources bij het voorbereiden van de status in behandeling](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>De bron resource groep verplaatsen 

Voordat u Vm's kunt voorbereiden en verplaatsen, moet de bron resource groep aanwezig zijn in de doel regio. 

### <a name="prepare-to-move-the-source-resource-group"></a>Het verplaatsen van de bron resource groep voorbereiden

Bereid u als volgt voor:

1. In **meerdere regio's**selecteert u de bron resource groep die > **voorbereiden**.
2. Klik in **resources voorbereiden**op **voorbereiden**.

    ![Knop voor het voorbereiden van de bron resource groep](./media/move-region-availability-zone/prepare-resource-group.png)

    Tijdens het voorbereidings proces genereert resource-overwerker Azure Resource Manager (ARM)-Sjablonen met behulp van de instellingen van de resource groep. Resources in de resource groep worden niet beïnvloed.

> [!NOTE]
>  Nadat u de resource groep hebt voor bereid, is deze in de status *initiëren verplaatsen in behandeling* . 

![Status waarin de status initiate in behandeling wordt weer gegeven](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>De bron resource groep verplaatsen

Start de verplaatsing als volgt:

1. In **meerdere regio's**selecteert u de resource groep > **initieert verplaatsen**
2. **resources verplaatsen**, klikt u op **verplaatsen starten**. De resource groep wordt verplaatst naar een status *van initiëren verplaatsen in uitvoering* .
3. Nadat de verplaatsing is gestart, wordt de doel resource groep gemaakt op basis van de gegenereerde ARM-sjabloon. De bron resource groep wordt verplaatst naar de status *door voeren verplaatsen in behandeling* .

![Status van door Voer verplaatsen](./media/move-region-availability-zone/commit-move-pending.png)

Ga als volgt te werk om het proces te verplaatsen en te volt ooien:

1. In **meerdere regio's**selecteert u de resource groep > **door voeren verplaatsen**
2. **resources verplaatsen**, klikt u op **door voeren**.

> [!NOTE]
> Nadat de verplaatsing is doorgevoerd, bevindt de bron resource groep zich in de status *Verwijder bron in behandeling* .


## <a name="add-a-target-availability-zone"></a>Een doel beschikbaarheids zone toevoegen

Voordat we de rest van de resources verplaatsen, stellen we een doel beschikbaarheids zone in voor de virtuele machine.

1. Klik op de pagina **over verschillende regio's** op de koppeling in de **doel configuratie** kolom van de virtuele machine die u wilt verplaatsen.

    ![VM-eigenschappen](./media/move-region-availability-zone/select-vm-settings.png)

3. Geef in **configuratie-instellingen**de instelling voor de doel-VM op. U kunt als volgt een virtuele machine in de doel regio configureren:
    -  Maak een nieuwe equivalente resource in de doel regio. Met uitzonde ring van de instellingen die u opgeeft, wordt de doel resource gemaakt met dezelfde instellingen als de bron.
    - Selecteer een bestaande virtuele machine in de doel regio. 

4. Selecteer in **zones**de zone waarin u de virtuele machine wilt plaatsen wanneer deze wordt verplaatst.

    Wijzigingen worden alleen aangebracht voor de resource die u wilt bewerken. U moet afhankelijke resources afzonderlijk bijwerken.

5. Geef in **SKU**de [Azure-laag](..//virtual-machines/sizes.md) op die u wilt toewijzen aan de doel-VM.
6. Selecteer in **beschikbaarheidsset**een beschikbaarheidsset als u wilt dat de doel-VM wordt uitgevoerd in een beschikbaarheidsset in de beschikbaarheids zone.
7. Selecteer **Save changes**.

    ![instellingen VM](./media/move-region-availability-zone/vm-settings.png)


## <a name="prepare-resources-to-move"></a>Resources voorbereiden om te verplaatsen

Nu de bron resource groep is verplaatst, kunt u voorbereiden om de andere resources te verplaatsen.

1. In **meerdere regio's**selecteert u de resources die u wilt voorbereiden. 

    ![Pagina voor het selecteren van voor bereiding voor andere resources](./media/move-region-availability-zone/prepare-other.png)

2. Selecteer **voorbereiden**. 

> [!NOTE]
> - Tijdens het voorbereidings proces wordt de Azure Site Recovery Mobility-agent geïnstalleerd op virtuele machines, voor replicatie.
> - VM-gegevens worden periodiek gerepliceerd naar de doel regio. Dit heeft geen invloed op de bron-VM.
> - Met resource verplaatsen worden ARM-sjablonen voor de andere bron bronnen gegenereerd.
> - Na het voorbereiden van de resources bevinden ze zich in een status van *initiëren verplaatsen in behandeling* .

![Pagina met resources in de status initiate verplaatsen in behandeling](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>De verplaatsing initiëren

Met de resources die zijn voor bereid, kunt u de verplaatsing nu initiëren. 

1. In **meerdere regio's**selecteert u resources met de status *initiëren verplaatsen in behandeling*. Klik vervolgens op **Start verplaatsen**
2. Klik in **resources verplaatsen**op **verplaatsen starten**.

    ![Pagina voor het initiëren van verplaatsen van resources](./media/move-region-availability-zone/initiate-move.png)

3. Voortgang van verplaatsen volgen op de balk meldingen.


> [!NOTE]
> - Voor Vm's worden replica-Vm's gemaakt in de doel regio. De bron-VM wordt afgesloten en er treedt enige downtime op (meestal minuten).
> - Resource-verplaatsen maakt andere resources opnieuw met behulp van de ARM-sjablonen die zijn voor bereid. Meestal is er geen downtime.
> - Na het voorbereiden van resources, hebben ze een status *door voeren die in behandeling* is.


![Pagina voor het weer geven van resources in de status door Voer verplaatsen in behandeling](./media/move-region-availability-zone/resources-commit-move-pending.png)

## <a name="discard-or-commit"></a>Negeren of door voeren?

Na de eerste verplaatsing kunt u beslissen of u de verplaatsing wilt door voeren of wilt negeren. 

- **Negeren**: u kunt een verplaatsing negeren als u wilt testen en u niet echt de bron resource wilt verplaatsen. Als u de verplaatsing verwijdert, wordt de resource teruggezet naar een status van *initiëren verplaatsen in behandeling*.
- **Door voeren**: door voeren voltooit de verplaatsing naar de doel regio. Na het door voeren heeft een bron resource de status *verwijderen in behandeling*en kunt u bepalen of u deze wilt verwijderen.

## <a name="discard-the-move"></a>Verplaatsen verwijderen 

U kunt de verplaatsing als volgt negeren:

1. In **meerdere regio's**selecteert u resources met status *doorvoer verplaatsen in behandeling*en klikt u op **verwijderen verplaatsen**.
2. Klik in **verwijderen verplaatsen**op **negeren**.
3. Voortgang van verplaatsen volgen op de balk meldingen.
 

> [!NOTE]
> Voor virtuele machines, na het verwijderen van resources, hebben ze de status *initiëren in behandeling* .

## <a name="commit-the-move"></a>De verplaatsing door voeren

Als u het Verplaats proces wilt volt ooien, moet u de verplaatsing door voeren. 

1. In **meerdere regio's**selecteert u resources met status *doorvoer verplaatsen in behandeling*en klikt u op **door voeren verplaatsen**.
2. Klik in **resources vastleggen**op **door voeren**.

    ![Pagina voor het door voeren van resources voor het volt ooien van verplaatsen](./media/move-region-availability-zone/commit-resources.png)

3. Volg de voortgang van de toewijzing in de balk meldingen.

> [!NOTE]
> - Na het door voeren van de verplaatsing, stoppen Vm's repliceren. De bron-VM heeft geen invloed op de door voer.
> - Door voeren heeft geen invloed op bron netwerk bronnen.
> - Na het door voeren van de verplaatsing bevinden resources zich in de status *verwijderings bron in behandeling* .

![Pagina met bronnen in * Verwijder bron in behandeling * status](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Instellingen configureren na het verplaatsen

De Mobility-service wordt niet automatisch verwijderd van Vm's. Verwijder de service hand matig of sluit deze als u van plan bent om de server opnieuw te verplaatsen.


## <a name="delete-source-resources-after-commit"></a>Bron bronnen verwijderen na door voeren

Na de verplaatsing kunt u eventueel resources verwijderen in de bron regio.

1. In **meerdere regio's**klikt u op de naam van elke bron resource die u wilt verwijderen.
2. Selecteer op de pagina eigenschappen voor elke resource **verwijderen**.

## <a name="delete-additional-resources-created-for-move"></a>Extra resources verwijderen die zijn gemaakt voor verplaatsen

Na de verplaatsing kunt u hand matig de verzameling verplaatsen en Site Recovery gemaakte resources verwijderen.

- De verzameling verplaatsen wordt standaard verborgen. Als u dit wilt zien, moet u verborgen resources inschakelen.
- De cache opslag heeft een vergren deling die moet worden verwijderd voordat deze kan worden verwijderd.

Verwijder als volgt: 

1. Zoek de resources in de resource groep ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Controleer of alle virtuele machines en andere bron bronnen in de bron regio zijn verplaatst of verwijderd. Dit zorgt ervoor dat er geen resources in behandeling zijn.
2. De resources verwijderen:

    - De naam van de verzameling die u wilt verplaatsen ```movecollection-<sourceregion>-<target-region>``` .
    - De naam van het cache-opslag account is ```resmovecache<guid>```
    - De kluis naam is ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](about-move-process.md) het verplaatsings proces.

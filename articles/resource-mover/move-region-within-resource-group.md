---
title: Resources verplaatsen naar een andere regio met Azure resource-overdrijfing
description: Meer informatie over het verplaatsen van resources binnen een resource groep naar een andere regio met Azure resource-overzetten.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 716928761d23c2cf04ebcc72e253ad7884408065
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061836"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Resources verplaatsen tussen regio's (van resource groep)

In dit artikel vindt u informatie over het verplaatsen van resources in een specifieke resource groep naar een andere Azure-regio. In de resource groep selecteert u de resources die u wilt verplaatsen. Vervolgens verplaatst u deze met [Azure resource](overview.md)-overschakeling.

> [!IMPORTANT]
> Azure resource-overdrijfing is momenteel beschikbaar als open bare preview.


## <a name="prerequisites"></a>Vereisten

- U hebt *eigenaars* toegang nodig voor het abonnement waarin resources zich bevinden die u wilt verplaatsen.
    - De eerste keer dat u een resource toevoegt voor een specifieke bron-en doel toewijzing in een Azure-abonnement, maakt resource-overschakeling een door het [systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (voorheen bekend als managed service identify (MSI)) die wordt vertrouwd door het abonnement.
    - Als u de identiteit wilt maken en de vereiste rol (Inzender of beheerder voor gebruikers toegang in het bron abonnement) wilt toewijzen, moet het account dat u gebruikt om resources toe te voegen *eigenaars* machtigingen voor het abonnement hebben. Meer [informatie](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) over Azure-rollen.
- Het abonnement heeft voldoende quota nodig om de bron resources in de doel regio te maken. Als dat niet het geval is, moet u aanvullende limieten aanvragen. [Meer informatie](/azure/azure-resource-manager/management/azure-subscription-service-limits).
- Controleer de prijzen en kosten die zijn gekoppeld aan de doel regio waarnaar u Vm's verplaatst. Gebruik de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) om u te helpen.
- Controleer of de resources die u wilt verplaatsen, worden ondersteund door resource-overschakeling:
    - Azure-Vm's en gekoppelde schijven
    - NIC’s
    - Beschikbaarheidssets
    - Virtuele netwerken van Azure
    - Openbare IP-adressen
    - Netwerkbeveiligingsgroepen (NSG's)
    - Interne en open bare load balancers
    - Azure SQL-data bases en elastische Pools


## <a name="check-vm-requirements"></a>VM-vereisten controleren

1. Controleer of de virtuele machines die u wilt verplaatsen, worden ondersteund.

    - [Controleer](support-matrix-move-region-azure-vm.md#windows-vm-support) de ondersteunde Windows-vm's.
    - [Controleer](support-matrix-move-region-azure-vm.md#linux-vm-support) ondersteunde vm's en kernel-versies van Linux.
    - Controleer ondersteunde instellingen voor [Compute](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [opslag](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)en [netwerk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
2. Zorg ervoor dat Vm's de meest recente vertrouwde basis certificaten en een bijgewerkte certificaatintrekkingslijst (CRL) hebben. 
    - Installeer de meest recente Windows-updates op virtuele Azure-machines waarop Windows wordt uitgevoerd.
    - Op Vm's waarop Linux wordt uitgevoerd, volgt u de richt lijnen voor Linux-distributeurs om te controleren of de computer de meest recente certificaten en CERTIFICAATINTREKKINGSLIJST heeft. 
3. Uitgaande verbindingen van Vm's toestaan:
    - Als u een firewall proxy op basis van een URL gebruikt voor het beheren van uitgaande connectiviteit, kunt u toegang tot deze [url's](support-matrix-move-region-azure-vm.md#url-access) toestaan
    - Als u regels voor de netwerk beveiligings groep (NSG) gebruikt om de uitgaande connectiviteit te beheren, maakt u deze [service-code regels](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Te verplaatsen resources selecteren

Selecteer de resources die u wilt verplaatsen. U kunt resources verplaatsen naar een doel regio in het bron regio abonnement. Als u het abonnement wilt wijzigen, kunt u dat doen nadat de resources zijn verplaatst.

1. Open de relevante resource groep in het Azure Portal.
2. Op de pagina resource groep selecteert u de resources die u wilt verplaatsen.
3. Selecteer **verplaatsing verplaatsen**  >  **naar een andere regio**.

    ![Selectie voor het verplaatsen van resources naar een andere regio](./media/move-region-within-resource-group/select-move-region.png)
    
4. Selecteer in **bron + bestemming**de doel regio waarnaar u de resources wilt verplaatsen. Selecteer vervolgens **Volgende**.


    ![Bron-en doel pagina voor het selecteren van de doel regio](./media/move-region-within-resource-group/source-target.png)


7. Selecteer **volgende**in **resources die moeten worden verplaatst**.  
8. Selecteer in **resources selecteren**de resource die u wilt verplaatsen. U kunt alleen resources toevoegen die voor verplaatsen worden ondersteund. Selecteer vervolgens **Done**.
9. Selecteer **volgende**bij **resources verplaatsen**. 
10. Controleer de bron-en doel gegevens in **beoordeling + toevoegen**.
11. Controleer of u begrijpt dat meta gegevens over de resources die worden verplaatst, worden opgeslagen in een resource groep die voor dit doel is gemaakt en dat u het verplaatsen van resources toestaat een door het systeem beheerde identiteit te maken voor toegang tot de abonnements bronnen.
1. Selecteer **door gaan** om te beginnen met het toevoegen van de resources.

    ![Overzichts pagina om de details te controleren en door te gaan met verplaatsen](./media/move-region-within-resource-group/summary.png)    

11. De bewerking resource toevoegen wordt gestart. Wanneer de bewerking is voltooid, tonen de meldingen dat de resources zijn toegevoegd en is de implementatie geslaagd.
13. Selecteer in de meldingen **resources toevoegen voor verplaatsen**.

    ![Bericht weer gegeven in meldingen](./media/move-region-within-resource-group/notification.png)    


14. Nadat u de melding hebt geselecteerd, worden de geselecteerde resources toegevoegd aan een verzameling verplaatsen in de Azure resource-overschakeling hub.  Resource-overschakeling helpt u bij het controleren van afhankelijkheden en het verplaatsen van resources naar de doel regio.

## <a name="resolve-dependencies"></a>Afhankelijkheden oplossen

Resources die u verplaatst, worden weer gegeven op de pagina **over verschillende regio's** , in *afwachting van voor bereide* status. Begin validatie als volgt:

1. Als resources een bericht voor het *valideren van afhankelijkheden* weer geven in de kolom **kwesties** , selecteert u de knop **afhankelijkheden valideren** . Het validatie proces wordt gestart.

    ![Knop voor het valideren van afhankelijkheden](./media/move-region-within-resource-group/validate-dependencies.png)

2. Als er afhankelijkheden worden gevonden, selecteert u **afhankelijkheden toevoegen**. 
3. Selecteer bij **afhankelijkheden toevoegen**de afhankelijke resources > **afhankelijkheden toevoegen**. De voortgang in de meldingen bewaken.

    ![Knop voor het toevoegen van afhankelijkheden](./media/move-region-within-resource-group/add-dependencies.png)

3. Voeg indien nodig extra afhankelijkheden toe en valideer afhankelijkheden als dat nodig is. Selecteer **vernieuwen** om ervoor te zorgen dat resources een actuele status weer geven.

4. Controleer op de pagina **over verschillende regio's** of de resources nu in behandeling zijn en zonder problemen worden voor *bereid* .

    ![Pagina om de status voor bereiding in behandeling weer te geven voor alle resources](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>De bron resource groep verplaatsen 

Voordat u resources kunt voorbereiden en verplaatsen, moet de bron resource groep aanwezig zijn in de doel regio. 

### <a name="prepare-to-move-the-source-resource-group"></a>Het verplaatsen van de bron resource groep voorbereiden

Bereid u als volgt voor:

1. In **meerdere regio's**selecteert u de bron resource groep die > **voorbereiden**.
2. In **resources voorbereiden**, Selecteer voor **bereiding**.
1. 
    ![Knop voor het voorbereiden van de bron resource groep](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Tijdens het voorbereidings proces genereert resource-overwerker Azure Resource Manager (ARM)-Sjablonen met behulp van de instellingen van de resource groep. Resources in de resource groep worden niet beïnvloed.
    
> [!NOTE]
>  Nadat u de resource groep hebt voor bereid, is deze in de status *initiëren verplaatsen in behandeling* . Vernieuwen om de meest recente status weer te geven.

![Status waarin de status initiate in behandeling wordt weer gegeven](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>De bron resource groep verplaatsen

Start de verplaatsing als volgt:

1. In **meerdere regio's**selecteert u de resource groep > **initieert verplaatsen**
2. **resources verplaatsen**: Selecteer **initiëren verplaatsen**. De resource groep wordt verplaatst naar een status *van initiëren verplaatsen in uitvoering* .
3. Nadat de verplaatsing is gestart, wordt de doel resource groep gemaakt op basis van de gegenereerde ARM-sjabloon. De bron resource groep wordt verplaatst naar de status *door voeren verplaatsen in behandeling* .

![Status van door Voer verplaatsen](./media/move-region-availability-zone/commit-move-pending.png)

Ga als volgt te werk om het proces te verplaatsen en te volt ooien:

1. In **meerdere regio's**selecteert u de resource groep > **door voeren verplaatsen**
2. **resources verplaatsen**, selecteer **door voeren**.

> [!NOTE]
> Nadat de verplaatsing is doorgevoerd, bevindt de bron resource groep zich in de status *Verwijder bron in behandeling* .

## <a name="modify-target-settings"></a>Doel instellingen wijzigen

Als u een bron resource niet wilt verplaatsen, kunt u een van de volgende handelingen uitvoeren:

- Maak een resource in de doel regio met dezelfde naam en instellingen als de resource in de bron regio.
- Maak een nieuwe equivalente resource in de doel regio. Met uitzonde ring van de instellingen die u opgeeft, wordt de doel resource gemaakt met dezelfde instellingen als de bron.
- Gebruik een bestaande resource in de doel regio.

Wijzig een instelling als volgt:

1. Als u een instelling wilt wijzigen, selecteert u de vermelding in de kolom **doel configuratie** voor de resource.
2. Geef op de pagina **doel configuratie** de doel instellingen op die u wilt gebruiken.
    Wijzigingen worden alleen aangebracht voor de resource die u wilt bewerken. U moet afhankelijke resources afzonderlijk bijwerken.   
    
De exacte instellingen die u wijzigt, zijn afhankelijk van het resource type. Meer [informatie](modify-target-settings.md) over het bewerken van doel instellingen.

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

1. In **meerdere regio's**selecteert u resources met de status *initiëren verplaatsen in behandeling*. Selecteer vervolgens **initiëren verplaatsen**.
2. In **resources verplaatsen**selecteert u **verplaatsen starten**.

    ![Selecteren voor de knop verplaatsen initiëren](./media/move-region-within-resource-group/initiate-move.png)

3. Voortgang van verplaatsen volgen op de balk meldingen.


> [!NOTE]
> - Voor Vm's worden replica-Vm's gemaakt in de doel regio. De bron-VM wordt afgesloten en er treedt enige downtime op (meestal minuten).<br/>
> - Resource-verplaatsen maakt andere resources opnieuw met behulp van de ARM-sjablonen die zijn voor bereid. Meestal is er geen downtime.<br/> 
> - Voor load balancers worden NAT-regels niet gekopieerd. Maak ze in de doel regio nadat u de verplaatsing hebt doorgevoerd.
> - Voor open bare IP-adressen wordt het DNS-naam label niet gekopieerd. Maak het label opnieuw nadat u de verplaatsing hebt doorgevoerd.
> - Na het voorbereiden van resources, hebben ze een status *door voeren die in behandeling* is.


## <a name="discard-or-commit"></a>Negeren of door voeren?

Na de eerste verplaatsing kunt u beslissen of u de verplaatsing wilt door voeren of wilt negeren. 

- **Negeren**: u kunt een verplaatsing negeren als u wilt testen en u niet echt de bron resource wilt verplaatsen. Als u de verplaatsing verwijdert, wordt de resource teruggezet naar een status van *initiëren verplaatsen in behandeling*.
- **Door voeren**: door voeren voltooit de verplaatsing naar de doel regio. Na het door voeren heeft een bron resource de status *verwijderen in behandeling*en kunt u bepalen of u deze wilt verwijderen.


## <a name="discard-the-move"></a>Verplaatsen verwijderen 

U kunt de verplaatsing als volgt negeren:

1. In **meerdere regio's**selecteert u resources met status *doorvoer verplaatsen in behandeling*en selecteert u **verplaatsen negeren**.
2. Selecteer **negeren**bij **verplaatsen negeren**.
3. Voortgang van verplaatsen volgen op de balk meldingen.
4. Wanneer de meldingen aangeven dat de verplaatsing is geslaagd, selecteert u **vernieuwen**. 

> [!NOTE]
> Voor virtuele machines, na het verwijderen van resources, hebben ze de status *initiëren in behandeling* .

## <a name="commit-the-move"></a>De verplaatsing door voeren

Als u het Verplaats proces wilt volt ooien, moet u de verplaatsing door voeren. 


1. In **meerdere regio's**selecteert u resources met status *doorvoer verplaatsen in behandeling*en selecteert u **verplaatsen door voeren**.
2. In **resources door voeren**selecteert u **door voeren**.

    ![Pagina voor het door voeren van resources voor het volt ooien van verplaatsen](./media/move-region-within-resource-group/commit-resources.png)

3. Volg de voortgang van de toewijzing in de balk meldingen.

> [!NOTE]
> - Na het door voeren van de verplaatsing, stoppen Vm's repliceren. De bron-VM heeft geen invloed op de door voer.
> - Door voeren heeft geen invloed op bron netwerk bronnen.
> - Na het door voeren van de verplaatsing bevinden resources zich in de status *verwijderings bron in behandeling* .

## <a name="configure-settings-after-the-move"></a>Instellingen configureren na het verplaatsen

1. Omdat de DNS-namen van labels niet worden gekopieerd voor open bare IP-adressen, gaat u nadat de verplaatsing is voltooid naar de doel resources en werkt u het label bij. 
2. Voor interne load balancers, omdat NAT-regels niet worden gekopieerd over, navigeert u naar de resources die zijn gemaakt in de doel regio en werkt u de NAT-regels bij.
3. De Mobility-service wordt niet automatisch verwijderd van Vm's. Verwijder de service hand matig of sluit deze als u van plan bent om de server opnieuw te verplaatsen.
## <a name="delete-source-resources-after-commit"></a>Bron bronnen verwijderen na door voeren

Na de verplaatsing kunt u eventueel resources verwijderen in de bron regio. 

1. In **meerdere regio's**selecteert u de naam van elke bron resource die u wilt verwijderen.
2. Selecteer op de pagina eigenschappen voor elke resource **verwijderen**.

## <a name="delete-additional-resources-created-for-move"></a>Extra resources verwijderen die zijn gemaakt voor verplaatsen

Na de verplaatsing kunt u hand matig de verzameling verplaatsen en Site Recovery gemaakte resources verwijderen.

- De verzameling verplaatsen wordt standaard verborgen. Als u dit wilt zien, moet u verborgen resources inschakelen.
- De cache opslag heeft een vergren deling die moet worden verwijderd voordat deze kan worden verwijderd.

Verwijder als volgt: 

1. Zoek de resources in de resource groep ```RegionMoveRG-<sourceregion>-<target-region>``` in de bron regio.
2. Controleer of alle virtuele machines en andere bron bronnen in de verplaatsings verzameling zijn verplaatst/verwijderd. Dit zorgt ervoor dat er geen resources in behandeling zijn.
2. De resources verwijderen:

    - De naam van de verzameling die u wilt verplaatsen ```movecollection-<sourceregion>-<target-region>``` .
    - De naam van het cache-opslag account is ```resmovecache<guid>```
    - De kluis naam is ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Volgende stappen


[Meer informatie over](about-move-process.md) het verplaatsings proces.

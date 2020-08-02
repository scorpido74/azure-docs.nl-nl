---
title: Moment opnamen beheren met behulp van Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u moment opnamen maakt en beheert met behulp van Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: 7d583172fe4021a2709a4d58b5488e9bc3898919
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497593"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Momentopnamen beheren met behulp van Azure NetApp Files

Azure NetApp Files ondersteunt het maken van moment opnamen op aanvraag en het gebruik van momentopname beleid om het automatisch maken van moment opnamen te plannen.  U kunt een moment opname ook herstellen naar een nieuw volume.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Een moment opname op aanvraag maken voor een volume

U kunt op aanvraag volume momentopnamen maken. 

1.  Ga naar het volume waarvoor u een moment opname wilt maken. Klik op **moment opnamen**.

    ![Navigeren naar moment opnamen](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Klik op **+ moment opname toevoegen** om een moment opname op aanvraag voor een volume te maken.

    ![Moment opname toevoegen](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Geef in het venster nieuwe moment opname een naam op voor de nieuwe moment opname die u maakt.   

    ![Nieuwe moment opname](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klik op **OK**. 

## <a name="manage-snapshot-policies"></a>Momentopname beleid beheren

U kunt plannen voor volume momentopnamen die automatisch moeten worden uitgevoerd met behulp van het momentopname beleid. U kunt ook een beleid voor moment opnamen naar wens aanpassen of een momentopname beleid verwijderen dat u niet meer nodig hebt.  

### <a name="register-the-feature"></a>De functie registreren

1. De functie voor het **momentopname beleid** is momenteel beschikbaar als preview-versie. Als dit de eerste keer is dat u deze functie gebruikt, moet u de functie registreren voordat u deze gebruikt: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Controleer de status van de functie registratie: 

    > [!NOTE]
    > Het **RegistrationState** kan `Registering` enkele minuten duren voordat de status wordt gewijzigd in `Registered` . Wacht totdat de status is **geregistreerd** voordat u doorgaat.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

### <a name="create-a-snapshot-policy"></a>Een momentopname beleid maken 

Met een momentopname beleid kunt u de frequentie voor het maken van moment opnamen opgeven in elk uur, dagelijks, wekelijks of maandelijks. U moet ook het maximum aantal moment opnamen opgeven dat voor het volume moet worden bewaard.  

1.  Klik in de NetApp-account weergave op **beleid voor moment opnamen**.

    ![Navigatie van momentopname beleid](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  Stel in het venster snap shot-beleid de beleids status in op **ingeschakeld**. 

3.  Klik op het tabblad **elk uur**, **dagelijks**, **wekelijks**of **maandelijks** om elk uur, dagelijks, wekelijks of maandelijks momentopname beleid te maken. Geef het **aantal moment opnamen op dat moet worden bewaard**.  

    Zie [resource limieten voor Azure NetApp files](azure-netapp-files-resource-limits.md) over het maximum aantal moment opnamen dat is toegestaan voor een volume. 

    In het volgende voor beeld wordt de configuratie van het beleid voor moment opnamen van uren weer gegeven. 

    ![Momentopname beleid per uur](../media/azure-netapp-files/snapshot-policy-hourly.png)

    In het volgende voor beeld wordt de configuratie van het dagelijkse momentopname beleid weer gegeven.

    ![Momentopname beleid dagelijks](../media/azure-netapp-files/snapshot-policy-daily.png)

    In het volgende voor beeld wordt de configuratie van een wekelijks momentopname beleid weer gegeven.

    ![Wekelijks momentopname beleid](../media/azure-netapp-files/snapshot-policy-weekly.png)

    In het volgende voor beeld wordt de configuratie van het maandelijkse momentopname beleid weer gegeven.

    ![Maandelijks momentopname beleid](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Klik op **Opslaan**.  

Als u extra momentopname beleid wilt maken, herhaalt u stap 3.
Het beleid dat u hebt gemaakt, wordt weer gegeven op de pagina snap shot Policy.

Als u wilt dat een volume het momentopname beleid gebruikt, moet u [het beleid Toep assen op het volume](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Een momentopname beleid Toep assen op een volume

Als u wilt dat een volume een momentopname beleid gebruikt dat u hebt gemaakt, moet u het beleid Toep assen op het volume. 

1.  Ga naar de pagina **volumes** , klik met de rechter muisknop op het volume waarop u een momentopname beleid wilt Toep assen en selecteer **bewerken**.

    ![Het menu voor volumes met de rechter muisknop](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  Selecteer in het venster bewerken onder **momentopname beleid**het beleid dat u wilt gebruiken voor het volume.  Klik op **OK** om het beleid toe te passen.  

    ![Momentopname beleid bewerken](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Een momentopname beleid wijzigen 

U kunt een bestaand momentopname beleid wijzigen om de beleids status te wijzigen, de frequentie van moment opnamen (per uur, dagelijks, wekelijks of maandelijks) of het aantal moment opnamen dat moet worden bewaard.  
 
1.  Klik in de NetApp-account weergave op **beleid voor moment opnamen**.

2.  Klik met de rechter muisknop op het momentopname beleid dat u wilt wijzigen en selecteer vervolgens **bewerken**.

    ![Snelmenu voor momentopname beleid](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Breng de wijzigingen aan in het venster met het momentopname beleid dat wordt weer gegeven en klik vervolgens op **Opslaan**. 

### <a name="delete-a-snapshot-policy"></a>Een momentopname beleid verwijderen 

U kunt een momentopname beleid verwijderen dat u niet meer wilt blijven gebruiken.   

1.  Klik in de NetApp-account weergave op **beleid voor moment opnamen**.

2.  Klik met de rechter muisknop op het momentopname beleid dat u wilt wijzigen en selecteer vervolgens **verwijderen**.

    ![Snelmenu voor momentopname beleid](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Klik op **Ja** om te bevestigen dat u het momentopname beleid wilt verwijderen.   

    ![Verwijdering van momentopname beleid bevestigen](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Een moment opname herstellen naar een nieuw volume

Op dit moment kunt u een moment opname alleen herstellen naar een nieuw volume. 
1. Selecteer **moment opnamen** op de Blade volume om de lijst met moment opnamen weer te geven. 
2. Klik met de rechter muisknop op de moment opname die u wilt herstellen en selecteer **herstellen naar nieuw volume** in de menu optie.  

    ![Moment opname terugzetten naar nieuw volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. Geef in het venster een volume maken informatie op voor het nieuwe volume:  
    * **Naam**   
        Geef de naam op voor het volume dat u wilt maken.  
        
        De naam moet uniek zijn binnen de resourcegroep. De naam moet minstens drie tekens bevatten.  U kunt hiervoor alle alfanumerieke tekens gebruiken.

    * **Quota**  
        Geef de hoeveelheid logische opslag op die u wilt toewijzen aan het volume.  

    ![Herstellen naar nieuw volume](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Klik op **beoordeling + maken**.  Klik op **Maken**.   
    Het nieuwe volume gebruikt hetzelfde protocol dat door de moment opname wordt gebruikt.   
    Het nieuwe volume waarnaar de moment opname wordt teruggezet, wordt weer gegeven op de Blade volumes.

## <a name="next-steps"></a>Volgende stappen

* [Informatie over de opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)

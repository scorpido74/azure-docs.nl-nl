---
title: Momentopnamen beheren met Azure NetApp-bestanden | Microsoft Documenten
description: Beschrijft hoe u momentopnamen maakt voor een volume of herstelt van een momentopname naar een nieuw volume met Azure NetApp-bestanden.
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
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: 48055a774808aea86452e8410b7e717f5019d172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267908"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Momentopnamen beheren met Azure NetApp-bestanden

U Azure NetApp-bestanden gebruiken om handmatig een on-demand momentopname voor een volume te maken of te herstellen van een momentopname naar een nieuw volume. De Azure NetApp Files-service maakt niet automatisch volumemomentopnamen.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Een momentopname op aanvraag maken voor een volume

U alleen op aanvraag momentopnamen maken. Momentopnamebeleid wordt momenteel niet ondersteund.

1.  Klik in het blad Volume op **Momentopnamen**.

    ![Navigeren naar momentopnamen](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Klik **op + Momentopname toevoegen** om een momentopname op aanvraag voor een volume te maken.

    ![Momentopname toevoegen](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Geef in het venster Nieuwe momentopname een naam op voor de nieuwe momentopname die u maakt.   

    ![Nieuwe momentopname](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klik op **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Een momentopname herstellen naar een nieuw volume

Momenteel u een momentopname alleen herstellen naar een nieuw volume. 
1. Ga naar het blad **Momentopnamen beheren** vanuit het volumeblad om de momentopnamelijst weer te geven. 
2. Selecteer een momentopname die u wilt herstellen.  
3. Klik met de rechtermuisknop op de momentopnamenaam en selecteer **Herstellen naar nieuw volume** in de menuoptie.  

    ![Momentopname herstellen naar nieuw volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Geef in het venster Nieuw volume informatie voor het nieuwe volume:  
    * **Naam**   
        Geef de naam op voor het volume dat u wilt maken.  
        
        De naam moet uniek zijn binnen de resourcegroep. De naam moet minstens drie tekens bevatten.  U kunt hiervoor alle alfanumerieke tekens gebruiken.

    * **Bestandspad**     
        Geef het bestandspad op dat wordt gebruikt om het exportpad voor het nieuwe volume te maken. Het exportpad wordt gebruikt om het volume te koppelen en benaderen.   
        
        Een koppeldoel is het eindpunt van het IP-adres van de NFS-service. Deze waarde wordt automatisch gegenereerd.   
        
        Het bestandspad mag alleen letters, cijfers en afbreekstreepjes ('-') bevatten. Het bestandspad moet tussen de 16 en 40 tekens lang zijn. 

    * **Quotum**  
        Geef de hoeveelheid logische opslag op die u wilt toewijzen aan het volume.  

        Het veld **Beschikbare quotum** toont hoeveel ongebruikte ruimte er is in de gekozen capaciteitspool, die u kunt gebruiken om een nieuw volume te maken. De grootte van het nieuwe volume mag niet groter zijn dan het beschikbare quotum.

    *   **Virtueel netwerk**  
        Geef het virtuele Azure-netwerk (Vnet) op dat u wilt gebruiken om het volume te benaderen.  
        Het opgegeven VNet moet een subnet bevatten dat is gedelegeerd aan Azure NetApp Files. U hebt alleen toegang tot Azure NetApp-bestanden vanaf hetzelfde Vnet of vanuit een Vnet dat zich in dezelfde regio bevindt als het volume via Vnet-peering. U heeft toegang tot het volume vanuit uw on-premises netwerk via Express Route. 

    * **Subnet**  
        Geef het subnet op dat u wilt gebruiken voor het volume.  
        Het subnet dat u opgeeft, moet worden gedelegeerd aan de Azure NetApp Files-service. U een nieuw subnet maken door **Nieuw maken** te selecteren onder het veld Subnet.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Klik op **OK**.   
    Het nieuwe volume waarop de momentopname wordt hersteld, wordt weergegeven in het blad Volumes.

## <a name="next-steps"></a>Volgende stappen

[Informatie over de opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)

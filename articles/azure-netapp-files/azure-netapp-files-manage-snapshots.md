---
title: Moment opnamen beheren met behulp van Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u met behulp van Azure NetApp Files moment opnamen voor een volume maakt of herstelt vanuit een moment opname naar een nieuw volume.
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
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: ed13c61646bd2a6672b613964507d291a69a6821
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483598"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Momentopnamen beheren met behulp van Azure NetApp Files

U kunt Azure NetApp Files hand matig een moment opname op aanvraag maken voor een volume of herstellen van een moment opname naar een nieuw volume. Met de Azure NetApp Files service worden niet automatisch moment opnamen van volumes gemaakt.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Een moment opname op aanvraag maken voor een volume

U kunt moment opnamen alleen op aanvraag maken. Het momentopname beleid wordt momenteel niet ondersteund.

1.  Klik op de Blade volume op **moment opnamen**.

    ![Navigeren naar moment opnamen](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Klik op **+ moment opname toevoegen** om een moment opname op aanvraag voor een volume te maken.

    ![Moment opname toevoegen](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Geef in het venster nieuwe moment opname een naam op voor de nieuwe moment opname die u maakt.   

    ![Nieuwe moment opname](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klik op **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Een moment opname herstellen naar een nieuw volume

Op dit moment kunt u een moment opname alleen herstellen naar een nieuw volume. 
1. Ga naar de Blade **moment opnamen beheren** van de Blade volume om de lijst met moment opnamen weer te geven. 
2. Selecteer een moment opname die u wilt herstellen.  
3. Klik met de rechter muisknop op de naam van de moment opname en selecteer **herstellen naar nieuw volume** in de menu optie.  

    ![Moment opname terugzetten naar nieuw volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Geef in het venster Nieuw volume informatie op voor het nieuwe volume:  
    * **Naam**   
        Geef de naam op voor het volume dat u wilt maken.  
        
        De naam moet uniek zijn binnen de resourcegroep. De naam moet minstens drie tekens bevatten.  U kunt hiervoor alle alfanumerieke tekens gebruiken.

    * **Bestandspad**     
        Geef het bestandspad op dat wordt gebruikt om het exportpad voor het nieuwe volume te maken. Het exportpad wordt gebruikt om het volume te koppelen en benaderen.   
        
        Een koppeldoel is het eindpunt van het IP-adres van de NFS-service. Deze waarde wordt automatisch gegenereerd.   
        
        Het bestandspad mag alleen letters, cijfers en afbreekstreepjes ('-') bevatten. Het bestandspad moet tussen de 16 en 40 tekens lang zijn. 

    * **Quota**  
        Geef de hoeveelheid logische opslag op die u wilt toewijzen aan het volume.  

        Het veld **Beschikbare quotum** toont hoeveel ongebruikte ruimte er is in de gekozen capaciteitspool, die u kunt gebruiken om een nieuw volume te maken. De grootte van het nieuwe volume mag niet groter zijn dan het beschikbare quotum.

    *   **Virtueel netwerk**  
        Geef het virtuele Azure-netwerk (Vnet) op dat u wilt gebruiken om het volume te benaderen.  
        Het opgegeven VNet moet een subnet bevatten dat is gedelegeerd aan Azure NetApp Files. U hebt alleen toegang tot Azure NetApp Files van hetzelfde Vnet of van een Vnet dat zich in dezelfde regio bevindt als het volume via Vnet-peering. U kunt het volume vanaf uw on-premises netwerk openen via een snelle route. 

    * **Subnet**  
        Geef het subnet op dat u wilt gebruiken voor het volume.  
        Het subnet dat u opgeeft, moet aan de Azure NetApp Files-service worden gedelegeerd. U kunt een nieuw subnet maken door **Nieuw maken** te selecteren onder het veld subnet.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Klik op **OK**.   
    Het nieuwe volume waarnaar de moment opname wordt teruggezet, wordt weer gegeven op de Blade volumes.

## <a name="next-steps"></a>Volgende stappen

[Informatie over de opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)

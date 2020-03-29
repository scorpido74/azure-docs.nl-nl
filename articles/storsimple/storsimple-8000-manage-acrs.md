---
title: Toegangscontrolerecords beheren in StorSimple | Microsoft Documenten
description: Beschrijft hoe u toegangscontrolerecords (ABE's) gebruiken om te bepalen welke hosts verbinding kunnen maken met een volume op het StorSimple-apparaat.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: ade7da25d2307a382c17e7a3cbb26b601c34ef78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64693236"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>De StorSimple Manager-service gebruiken om toegangsbeheerrecords te beheren

## <a name="overview"></a>Overzicht
Met Toegangscontrolerecords (ACL's) u opgeven welke hosts verbinding kunnen maken met een volume op het StorSimple-apparaat. ADR's zijn ingesteld op een specifiek volume en bevatten de iSCSI-gekwalificeerde namen (IQN's) van de hosts. Wanneer een host verbinding probeert te maken met een volume, controleert het apparaat de ACR die aan dat volume is gekoppeld op de IQN-naam en als er een overeenkomst is, wordt de verbinding tot stand gebracht. De toegangscontrolerecords in de sectie **Configuratie** van uw StorSimple Device Manager-serviceblade geven alle toegangscontrolerecords weer met de bijbehorende IQN's van de hosts.

In deze zelfstudie worden de volgende veelvoorkomende ACR-gerelateerde taken uitgelegd:

* Een toegangscontrolerecord toevoegen
* Een toegangscontrolerecord bewerken
* Een toegangscontrolerecord verwijderen

> [!IMPORTANT]
> * Let er bij het toewijzen van een ACR op een volume op dat het volume niet gelijktijdig wordt geopend door meer dan één niet-geclusterde host, omdat dit het volume kan beschadigen.
> * Controleer bij het verwijderen van een ACR uit een volume of de bijbehorende host geen toegang heeft tot het volume, omdat de verwijdering kan leiden tot een lees-schrijfonderbreking.

## <a name="get-the-iqn"></a>Haal de IQN

Voer de volgende stappen uit om het IQN van een Windows-host op te halen waarop Windows Server 2012 wordt uitgevoerd.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Een toegangscontrolerecord toevoegen
U gebruikt de sectie **Configuratie** in het StorSimple Device Manager-serviceblad om AAC's toe te voegen. Meestal associeert u één ACR met één volume.

Voer de volgende stappen uit om een ACR toe te voegen.

#### <a name="to-add-an-acr"></a>Een ACR toevoegen

1. Ga naar uw StorSimple Device Manager-service, dubbelklik op de servicenaam en klik vervolgens in de sectie **Configuratie** op **Toegangsbeheerrecords**.
2. Klik in het **blad Voor het controlerecord van Access** op + **ACR toevoegen**.

    ![Klik op ACR toevoegen](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Ga in het **ACR-blad toevoegen** de volgende stappen uit:

    1. Lever een naam voor uw ACR.
    
    2. Geef de IQN-naam van uw Windows Server-host op onder **iqn (iSCSI Initiator Name).**

    3. Klik **op Toevoegen** om de ACR te maken.

        ![Klik op ACR toevoegen](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  De nieuw toegevoegde ACR wordt weergegeven in de tabeltabellijst van ACL's.

    ![Klik op ACR toevoegen](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Een toegangscontrolerecord bewerken
U gebruikt de sectie **Configuratie** in het storSimple Device Manager-serviceblad om AVR's te bewerken.

> [!NOTE]
> Het wordt aanbevolen dat u alleen de ACL's wijzigt die momenteel niet in gebruik zijn. Als u een ACR wilt bewerken die is gekoppeld aan een volume dat momenteel wordt gebruikt, moet u eerst het volume offline halen.

Voer de volgende stappen uit om een ACR te bewerken.

#### <a name="to-edit-an-access-control-record"></a>Een toegangscontrolerecord bewerken
1.  Ga naar uw StorSimple Device Manager-service, dubbelklik op de servicenaam en klik vervolgens in de sectie **Configuratie** op **Toegangsbeheerrecords**.

    ![Naar toegangsbeheerrecords gaan](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Klik in de tabeltabellijst van de toegangscontrolerecords op de ACR die u wilt wijzigen.

    ![Toegangsbeheerrecords bewerken](./media/storsimple-8000-manage-acrs/editacr1.png)

3. Geef in het **recordblad voor toegangscontrole bewerken** een ander IQN op dat overeenkomt met een andere host.

    ![Toegangsbeheerrecords bewerken](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Klik op **Opslaan**. Klik op **Ja** als u om bevestiging wordt gevraagd. 

    ![Toegangsbeheerrecords bewerken](./media/storsimple-8000-manage-acrs/editacr3.png)

5. U wordt op de hoogte gesteld wanneer de ACR wordt bijgewerkt. De tabelvermelding wordt ook bijgewerkt om de wijziging weer te geven.

   
## <a name="delete-an-access-control-record"></a>Een toegangscontrolerecord verwijderen
U gebruikt de sectie **Configuratie** in het serviceblad StorSimple Device Manager om AVR's te verwijderen.

> [!NOTE]
> U alleen de ACL's verwijderen die momenteel niet in gebruik zijn. Als u een ACR wilt verwijderen die is gekoppeld aan een volume dat momenteel wordt gebruikt, moet u eerst het volume offline halen.

Voer de volgende stappen uit om een toegangscontrolerecord te verwijderen.

#### <a name="to-delete-an-access-control-record"></a>Een toegangscontrolerecord verwijderen
1.  Ga naar uw StorSimple Device Manager-service, dubbelklik op de servicenaam en klik vervolgens in de sectie **Configuratie** op **Toegangsbeheerrecords**.

    ![Naar toegangsbeheerrecords gaan](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Klik in de tabeltabellijst van de toegangscontrolerecords op de ACR die u wilt verwijderen.

    ![Naar toegangsbeheerrecords gaan](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Klik met de rechtermuisknop om het contextmenu aan te roepen en selecteer **Verwijderen**.

    ![Naar toegangsbeheerrecords gaan](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Controleer de informatie en klik op **Verwijderen**wanneer u om bevestiging wordt gevraagd.

    ![Naar toegangsbeheerrecords gaan](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. U wordt op de hoogte gesteld wanneer de verwijdering is voltooid. De tabelvermelding wordt bijgewerkt om de verwijdering weer te geven.

    ![Naar toegangsbeheerrecords gaan](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het beheren van StorSimple-volumes.](storsimple-8000-manage-volumes-u2.md)
* Meer informatie over [het gebruik van de StorSimple Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)


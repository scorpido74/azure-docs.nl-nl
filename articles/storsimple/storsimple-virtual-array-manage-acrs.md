---
title: Toegangsbeheerrecords beheren voor StorSimple Virtual Array | Microsoft Documenten
description: Beschrijft hoe u toegangscontrolerecords (ABE's) beheert om te bepalen welke hosts verbinding kunnen maken met een volume op de StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1dfc1b0e0576402624bfe62de0e206d9bd7cd1b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64724429"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>StorSimple-apparaatbeheer gebruiken om toegangsbeheerrecords voor StorSimple Virtual Array te beheren

## <a name="overview"></a>Overzicht

Met Toegangscontrolerecords (ABE's) u opgeven welke hosts verbinding kunnen maken met een volume op de StorSimple Virtual Array (ook wel bekend als het virtuele apparaat StorSimple). ADR's zijn ingesteld op een specifiek volume en bevatten de iSCSI-gekwalificeerde namen (IQN's) van de hosts. Wanneer een host verbinding probeert te maken met een volume, controleert het apparaat de ACR die aan dat volume is gekoppeld op de IQN-naam en als er een overeenkomst is, wordt de verbinding tot stand gebracht. Het **recordblad voor toegangsbeheer** in de sectie **Configuratie** van uw Apparaatbeheer-service geeft alle toegangscontrolerecords weer met de bijbehorende IQN's van de hosts.

![Toegangsbeheerrecords beheren](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

In deze zelfstudie worden de volgende veelvoorkomende ACR-gerelateerde taken uitgelegd:

* Haal de IQN
* Een toegangscontrolerecord toevoegen
* Een toegangscontrolerecord bewerken
* Een toegangscontrolerecord verwijderen

> [!IMPORTANT]
> 
> * Let er bij het toewijzen van een ACR op een volume op dat het volume niet gelijktijdig wordt geopend door meer dan één niet-geclusterde host, omdat dit het volume kan beschadigen.
> * Controleer bij het verwijderen van een ACR uit een volume of de bijbehorende host geen toegang heeft tot het volume, omdat de verwijdering kan leiden tot een lees-schrijfonderbreking.


## <a name="get-the-iqn"></a>Haal de IQN

Voer de volgende stappen uit om het IQN van een Windows-host op te halen waarop Windows Server 2012 wordt uitgevoerd.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Een ACR toevoegen

U gebruikt **het recordblad access control** in de sectie **Configuratie** van uw StorSimple Device Manager-service om AAC's toe te voegen. Doorgaans associeert u één ACR met één volume.

Ga voor informatie over het koppelen van een ACR met een volume naar [een volume toevoegen.](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume)

> [!IMPORTANT]
> Let er bij het toewijzen van een ACR op een volume op dat het volume niet gelijktijdig wordt geopend door meer dan één niet-geclusterde host, omdat dit het volume kan beschadigen.


Voer de volgende stappen uit om een ACR toe te voegen.

#### <a name="to-add-an-acr"></a>Een ACR toevoegen

1. Selecteer op de bestemmingspagina van de service uw service, dubbelklik op de servicenaam en klik vervolgens in de sectie **Configuratie** op **Toegangsbeheerrecords**.
2. Klik in het **blad Voor recordrecords van Access** op **Toevoegen**.
3. Ga als volgt te werk in het **ACR-blad toevoegen:**
   
    1. Geef een **naam** voor uw ACR op.
    
    2. Geef onder **de naam van de iSCSI-initiator**de IQN-naam van uw Windows-host op. Ga als volgt te werk om de IQN van uw Windows Server-host te krijgen:
   
    3. Start de Microsoft iSCSI-initiator op uw Windows-host. Selecteer en kopieer in het venster Eigenschappen iSCSI-initiator op het tabblad **Configuratie** de tekenreeks in het veld **Naam van initiator**.
    Plak deze tekenreeks in het veld **IQN** in het **ACR-blad toevoegen.**
   
    6. Klik **op Toevoegen** om de ACR toe te voegen.  
   
        ![Toegangsbeheerrecords toevoegen](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. De tabelvermelding wordt bijgewerkt om deze toevoeging weer te geven.

## <a name="edit-an-acr"></a>Een ACR bewerken

U gebruikt het **recordblad voor toegangsbeheer** in de sectie **Configuratie** van uw Apparaatbeheerservice in de Azure-portal om AVR's te bewerken.

> [!NOTE]
> U moet een ACR die momenteel in gebruik is, niet wijzigen. Als u een ACR wilt bewerken die is gekoppeld aan een volume dat momenteel wordt gebruikt, moet u eerst het volume offline halen.


Voer de volgende stappen uit om een ACR te bewerken.

#### <a name="to-edit-an-acr"></a>Een ACR bewerken

1. Selecteer op de bestemmingspagina van de service uw service, dubbelklik op de servicenaam en dubbelklik vervolgens in de sectie **Configuratie,** **toegangsbeheerrecords**.
2. Dubbelklik in het **blad Access-controlerecords** in de tabeltabellijst van de toegangscontrolerecords op de ACR die u wilt wijzigen.
3. Ga als volgt te werk in het **blad voor toegangsbeheer bewerken:**
   
    1. Lever de IQN voor de ACR.
   
    2. Klik boven aan het blad op **Opslaan** om de gewijzigde ACR op te slaan. U ziet het volgende bevestigingsbericht:
   
        ![Toegangsbeheerrecords bewerken](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Een toegangscontrolerecord verwijderen

U gebruikt de **configuratiepagina** in de Azure-portal om ABE's te verwijderen.

> [!NOTE]
> 
> * U mag een ACR die momenteel in gebruik is, niet verwijderen. Als u een ACR wilt verwijderen die is gekoppeld aan een volume dat momenteel wordt gebruikt, moet u eerst het volume offline halen.
> * Controleer bij het verwijderen van een ACR uit een volume of de bijbehorende host geen toegang heeft tot het volume, omdat de verwijdering kan leiden tot een lees-schrijfonderbreking.


Voer de volgende stappen uit om een toegangscontrolerecord te verwijderen.

#### <a name="to-delete-an-access-control-record"></a>Een toegangscontrolerecord verwijderen

1. Selecteer op de bestemmingspagina van de service uw service, dubbelklik op de servicenaam en dubbelklik vervolgens in de sectie **Configuratie,** **toegangsbeheerrecords**.

2. Dubbelklik in het **blad Access-controlerecords** in de tabeltabellijst van de toegangscontrolerecords op de ACR die u wilt verwijderen.

3. Klik in het blad Toegangsbeheer bewerken op **Verwijderen**.
   
    ![ACRS verwijderen](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Klik op **Verwijderen** om door te gaan met de verwijdering wanneer u om bevestiging wordt gevraagd. De tabelvermelding wordt bijgewerkt om de verwijdering weer te geven.
   
   ![Waarschuwingsbericht](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het toevoegen van volumes en het configureren van ADR's](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).


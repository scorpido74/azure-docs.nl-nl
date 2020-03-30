---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013773"
---
Met Storage Explorer 1.10.0 kunnen gebruikers beheerde schijven uploaden, downloaden en kopiëren en snapshots maken. Vanwege deze extra mogelijkheden u Storage Explorer gebruiken om gegevens van on-premises naar Azure te migreren en gegevens te migreren in Azure-regio's.

## <a name="prerequisites"></a>Vereisten

Om dit artikel te voltooien, heb je het volgende nodig:
- Een Azure-abonnement
- Een of meer door Azure beheerde schijven
- De nieuwste versie van [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Verbinding maken met een Azure-abonnement

Als uw Storage Explorer niet is verbonden met Azure, u deze niet gebruiken om resources te beheren. In deze sectie wordt het aansluiten op uw Azure-account uitgevoerd, zodat u resources beheren met Storage Explorer.

1. Start Azure Storage Explorer en klik op het **pictogram plug-in** aan de linkerkant.

    ![Klik op het insteekpictogram](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Selecteer **Een Azure-account toevoegen**en klik op **Volgende**.

    ![Een Azure-account toevoegen](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Voer in het dialoogvenster **Azure Sign-in** uw Azure-referenties in.

    ![Dialoogvenster Azure-aanmelding](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Selecteer uw abonnement in de lijst en klik op **Toepassen**.

    ![Selecteer uw abonnement](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Een beheerde schijf uploaden vanaf een on-prem VHD

1. **Vouw** schijven uit in het linkerdeelvenster en selecteer de brongroep waarnaar u uw schijf wilt uploaden.

    ![Resourcegroep 1 selecteren](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selecteer **Uploaden**.

    ![Uploaden selecteren](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. Geef in **VHD uploaden** uw bron VHD op, de naam van de schijf, het ostype, het gebied waaru de schijf wilt uploaden en het accounttype. In sommige regio's worden beschikbaarheidszones ondersteund, voor die regio's u een zone naar keuze selecteren.
1. Selecteer **Maken** om te beginnen met het uploaden van uw schijf.

    ![Vhd-dialoogvenster uploaden](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. De status van de upload wordt nu weergegeven in **Activiteiten**.

    ![Uploadstatus](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Als de upload is voltooid en u de schijf niet in het rechterdeelvenster ziet, selecteert u **Vernieuwen**.

## <a name="download-a-managed-disk"></a>Een beheerde schijf downloaden

In de volgende stappen wordt uitgelegd hoe u een beheerde schijf downloaden naar een on-prem VHD. De status van een schijf moet worden **ontkoppeld** om te kunnen worden gedownload, u geen **gekoppelde** schijf downloaden.

1. Als deze niet is uitgevouwen, vouwt u **schijven** uit en selecteert u in het linkerdeelvenster de brongroep waarvan u uw schijf wilt downloaden.

    ![Resourcegroep 1 selecteren](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selecteer in het rechterdeelvenster de schijf die u wilt downloaden.
1. Selecteer **Downloaden** en kies vervolgens waar u de schijf wilt opslaan.

    ![Een beheerde schijf downloaden](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Selecteer **Opslaan** en uw schijf begint met downloaden. De status van de download wordt weergegeven in **Activiteiten**.

    ![Downloadstatus](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Een beheerde schijf kopiëren

Met Storage Explorer u een manged-schijf binnen of tussen regio's kopiëren. Een schijf kopiëren:

1. Selecteer in de vervolgkeuzelijst **Schijven** aan de linkerkant de brongroep die de schijf bevat die u wilt kopiëren.

    ![Resourcegroep 1 selecteren](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selecteer in het rechterdeelvenster de schijf die u wilt kopiëren en selecteer **Kopiëren**.

    ![Een beheerde schijf kopiëren](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. Selecteer in het linkerdeelvenster de resourcegroep waarin u de schijf wilt plakken.

    ![Resourcegroep 2 selecteren](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Selecteer **Plakken** in het rechterdeelvenster.

    ![Een beheerde schijf plakken](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. Vul in het dialoogvenster **Schijf plakken** de waarden in. U ook een beschikbaarheidszone opgeven in ondersteunde regio's.

    ![Dialoogvenster Schijf plakken](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Selecteer **Plakken** en uw schijf begint te kopiëren, de status wordt weergegeven in **Activiteiten**.

    ![Plakstatus kopiëren](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Een momentopname maken

1. Selecteer in de vervolgkeuzelijst **Schijven** aan de linkerkant de brongroep die de schijf bevat die u wilt momentopnamemaken.

    ![Resourcegroep 1 selecteren](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selecteer aan de rechterkant de schijf die u wilt momentopname maken en selecteer **Momentopname maken**.

    ![Een momentopname maken](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. Geef in **Momentopname maken**de naam op van de momentopname en de resourcegroep waarin u deze wilt maken. Selecteer vervolgens **Maken**.

    ![Dialoogvenster Momentopname maken](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Zodra de momentopname is gemaakt, u **Openen in Portal** in **Activiteiten** selecteren om de momentopname in de Azure-portal weer te geven.

    ![Momentopname openen in portal](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Volgende stappen

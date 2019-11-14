---
title: 'Azure Backup: Recovery Services kluizen maken'
description: In dit artikel leert u hoe u Recovery Services kluizen maakt waarin de back-ups en herstel punten worden opgeslagen.
ms.reviewer: sogup
author: dcurwin
manager: carmonm
keywords: Recovery Services kluis; Back-ups van Azure-VM'S; Azure VM herstellen;
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: dacurwin
ms.openlocfilehash: 30e2f8812b14f27b57ef22e30aea89f19fa56e58
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074882"
---
# <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Services kluis is een entiteit waarin de back-ups en herstel punten worden opgeslagen die in de loop van de tijd zijn gemaakt. De Recovery Services kluis bevat ook het back-upbeleid dat is gekoppeld aan de beveiligde virtuele machines.

Een Recovery Services-kluis maken:

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

2. Selecteer in het menu links **Alle services**.

    ![Alle services selecteren](./media/backup-create-rs-vault/click-all-services.png)

3. In het dialoogvenster **Alle services** voert u **Recovery Services** in. De lijst met resources filtert op basis van uw invoer. Selecteer **Recovery Services kluizen**in de lijst met resources.

    ![Recovery Services-kluizen invoeren en kiezen](./media/backup-create-rs-vault/all-services.png)

    De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

4. Selecteer **toevoegen**op het **Recovery Services kluizen** -dash board.

    ![Een Recovery Services kluis toevoegen](./media/backup-create-rs-vault/add-button-create-vault.png)

    Het dialoog venster **Recovery Services kluis** wordt geopend. Geef waarden op voor de **naam**, het **abonnement**, de **resource groep**en de **locatie**.

    ![De Recovery Services kluis configureren](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Naam**: Voer een beschrijvende naam in om de kluis aan te duiden. De naam moet uniek zijn voor het Azure-abonnement. Geef een naam op met ten minste twee, maar niet meer dan 50 tekens. De naam moet beginnen met een letter en mag alleen bestaan uit letters, cijfers en afbreek streepjes.
   - **Abonnement**: Kies het abonnement dat u wilt gebruiken. Als u lid bent van slechts één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u moet gebruiken, gebruikt u het standaard abonnement (aanbevolen). Er zijn alleen meerdere opties als uw werk-of school account is gekoppeld aan meer dan één Azure-abonnement.
   - **Resource groep**: gebruik een bestaande resource groep of maak een nieuwe. Als u de lijst met beschik bare resource groepen in uw abonnement wilt weer geven, selecteert u **bestaande gebruiken**en selecteert u vervolgens een resource in de vervolg keuzelijst. Als u een nieuwe resource groep wilt maken, selecteert u **nieuwe maken** en voert u de naam in. Zie [Azure Resource Manager Overview](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)voor meer informatie over resource groepen.
   - **Locatie**: Selecteer de geografische regio voor de kluis. De kluis **moet** zich in dezelfde regio bevinden als de virtuele machines om een kluis te maken om virtuele machines te beveiligen.

      > [!IMPORTANT]
      > Als u niet zeker bent van de locatie van de virtuele machine, sluit u het dialoog venster. Ga naar de lijst met virtuele machines in de portal. Als u virtuele machines in meerdere regio's hebt, moet u in elke regio een Recovery Services kluis maken. Maak de kluis op de eerste locatie voordat u de kluis voor een andere locatie maakt. U hoeft geen opslag accounts op te geven om de back-upgegevens op te slaan. De Recovery Services kluis en de Azure Backup service-handler die automatisch worden uitgevoerd.
      >
      >

5. Wanneer u klaar bent om de Recovery Services kluis te maken, selecteert u **maken**.

    ![De Recovery Services kluis maken](./media/backup-create-rs-vault/click-create-button.png)

    Het kan even duren om de Recovery Services kluis te maken. Bewaak de status meldingen in het gebied **meldingen** in de rechter bovenhoek van de portal. Nadat de kluis is gemaakt, wordt deze weer gegeven in de lijst met Recovery Services kluizen. Als uw kluis niet wordt weer geven, selecteert u **vernieuwen**.

     ![De lijst met back-upkluizen vernieuwen](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Opslag redundantie instellen

Azure Backup beheert automatisch de opslag voor de kluis. U moet opgeven hoe die opslag wordt gerepliceerd.

1. Klik op de blade **Recovery Services-kluizen** op de nieuwe kluis. Klik onder de sectie **instellingen** op **Eigenschappen**.
2. In **Eigenschappen**, onder **back-upconfiguratie**, klikt u op **bijwerken**.

3. Selecteer het type opslag replicatie en klik op **Opslaan**.

     ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Als u Azure als een primair eind punt voor back-upopslag gebruikt, wordt u aangeraden de standaard **geo-redundante** instelling te gebruiken.
   - Als Azure niet uw primaire eindpunt is voor back-upopslag, kiest u **Lokaal redundant**, zodat u de kosten voor Azure-opslag verlaagt.
   - Meer informatie over [geo](../storage/common/storage-redundancy-grs.md) en [lokale](../storage/common/storage-redundancy-lrs.md) redundantie.

> [!NOTE]
> Het wijzigen van het **type opslag replicatie** (lokaal redundante/geo-redundant) voor een Recovery Services-kluis moet worden uitgevoerd voordat u back-ups in de kluis configureert. Zodra u een back-up hebt geconfigureerd, is de optie om te wijzigen uitgeschakeld en kunt u het **type opslag replicatie**niet wijzigen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](backup-azure-recovery-services-vault-overview.md) Recovery Services kluizen.
[Meer informatie over](backup-azure-delete-vault.md) Recovery Services kluizen verwijderen.

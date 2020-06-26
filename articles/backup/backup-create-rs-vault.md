---
title: Recovery Services kluizen maken
description: In dit artikel leert u hoe u Recovery Services kluizen maakt waarin de back-ups en herstel punten worden opgeslagen.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: ac30a57dcbceb4616961c9d100e6ac8639ef2172
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391057"
---
# <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Service-kluis is een entiteit waarin de back-ups en herstelpunten worden opgeslagen die in de loop van de tijd zijn gemaakt. De Recovery Service-kluis bevat ook de beleidsregels voor back-up die aan de beveiligde virtuele machines zijn gekoppeld.

Een Recovery Services-kluis maken:

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

2. Selecteer in het menu links **alle services**.

    ![Alle services selecteren](./media/backup-create-rs-vault/click-all-services.png)

3. In het dialoogvenster **Alle services** voert u **Recovery Services** in. De lijst met resources wordt gefilterd op basis van uw invoer. In de lijst met resources selecteert u **Recovery Service-kluizen**.

    ![Recovery Services-kluizen invoeren en kiezen](./media/backup-create-rs-vault/all-services.png)

    De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

4. Selecteer **toevoegen**op het **Recovery Services kluizen** -dash board.

    ![Een Recovery Services kluis toevoegen](./media/backup-create-rs-vault/add-button-create-vault.png)

    Het dialoogvenster **Recovery Service-kluis** wordt geopend. Geef waarden op voor de **naam**, het **abonnement**, de **resource groep**en de **locatie**.

    ![De Recovery Services kluis configureren](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Naam**: Voer een beschrijvende naam in om de kluis aan te duiden. De naam moet uniek zijn voor het Azure-abonnement. Geef een naam op van minimaal 2 en maximaal 50 tekens. De naam moet beginnen met een letter en mag alleen uit letters, cijfers en afbreekstreepjes bestaan.
   - **Abonnement**: Kies het abonnement dat u wilt gebruiken. Als u lid bent van maar één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u moet gebruiken, gebruikt u het standaardabonnement (voorgesteld). Er zijn alleen meerdere mogelijkheden als uw werk- of schoolaccount is gekoppeld aan meerdere Azure-abonnementen.
   - **Resourcegroep**: Gebruik een bestaande resourcegroep of maak een nieuwe. Als u de lijst met beschikbare resourcegroepen in uw abonnement wilt weergeven, selecteert u **Bestaande gebruiken** en vervolgens selecteert u een resource uit de vervolgkeuzelijst. Als u een nieuwe resourcegroep wilt maken, selecteert u **Nieuwe maken** en voert u de naam in. Zie [Overzicht van Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) voor meer informatie over resourcegroepen.
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

>[!NOTE]
>De opslag replicatie-instellingen voor de kluis zijn niet relevant voor Azure file share backup, omdat de huidige oplossing moment opname is en er geen gegevens worden overgebracht naar de kluis. Moment opnamen worden opgeslagen in hetzelfde opslag account als de back-up van de bestands share.

1. Klik op de blade **Recovery Services-kluizen** op de nieuwe kluis. Klik onder de sectie **instellingen** op **Eigenschappen**.
2. In **Eigenschappen**, onder **back-upconfiguratie**, klikt u op **bijwerken**.

3. Selecteer het type opslag replicatie en klik op **Opslaan**.

     ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Als u Azure als een primair eind punt voor back-upopslag gebruikt, wordt u aangeraden de standaard **geo-redundante** instelling te gebruiken.
   - Als Azure niet uw primaire eindpunt is voor back-upopslag, kiest u **Lokaal redundant**, zodat u de kosten voor Azure-opslag verlaagt.
   - Meer informatie over [geo](../storage/common/storage-redundancy-grs.md) en [lokale](../storage/common/storage-redundancy-lrs.md) redundantie.

> [!NOTE]
> Het wijzigen van het **type opslag replicatie** (lokaal redundante/geo-redundant) voor een Recovery Services-kluis moet worden uitgevoerd voordat u back-ups in de kluis configureert. Zodra u een back-up hebt geconfigureerd, is de optie om te wijzigen uitgeschakeld en kunt u het **type opslag replicatie**niet wijzigen.

## <a name="set-cross-region-restore"></a>Meerdere regio's herstellen instellen

Als een van de opties voor terugzetten met behulp van cross Region Restore (CRR) kunt u virtuele Azure-machines herstellen in een secundaire regio, een [Azure-gekoppelde regio](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Met deze optie kunt u:

- oefeningen uitvoeren als er een controle-of nalevings vereiste is
- herstel de virtuele machine of de schijf als er sprake is van een nood geval in de primaire regio.

Als u deze functie wilt kiezen, selecteert u **herstel van meerdere regio's inschakelen** op de Blade van de **back-upconfiguratie** .

Voor dit proces gelden de prijs implicaties voor het opslag niveau.

>[!NOTE]
>Voordat u begint:
>
>- Bekijk de [ondersteunings matrix](backup-support-matrix.md#cross-region-restore) voor een lijst met ondersteunde beheerde typen en regio's.
>- De functie voor het terugzetten van meerdere regio's (CRR) wordt nu weer gegeven in alle open bare Azure-regio's.
>- CRR is een opt-in-functie op kluis niveau voor elke GRS-kluis (standaard uitgeschakeld).
>- Na het inbrengen kan het tot 48 uur duren voordat de back-upitems beschikbaar zijn in secundaire regio's.
>- Momenteel wordt CRR alleen ondersteund voor het type back-upbeheer. ARM Azure VM (klassieke Azure-VM wordt niet ondersteund).  Wanneer extra beheer typen ondersteuning bieden voor CRR, worden ze **automatisch** Inge schreven.
>- Het terugzetten van meerdere regio's kan op dit moment niet worden teruggedraaid naar GRS of LRS zodra de beveiliging voor de eerste keer wordt gestart. 

### <a name="configure-cross-region-restore"></a>Herstel van meerdere regio's configureren

Een kluis die is gemaakt met GRS-redundantie bevat de optie voor het configureren van de functie voor het terugzetten van meerdere regio's. Elke GRS-kluis heeft een banner dat een koppeling naar de documentatie bevat. Als u CRR voor de kluis wilt configureren, gaat u naar de Blade back-upconfiguratie. Deze bevat de optie om deze functie in te scha kelen.

 ![Banner back-upconfiguratie](./media/backup-azure-arm-restore-vms/banner.png)

1. Ga in de portal naar Recovery Services kluis > instellingen > eigenschappen.
2. Klik op **Restore voor meerdere regio's inschakelen in deze kluis** om de functionaliteit in te scha kelen.

   ![Voordat u op Restore voor meerdere regio's inschakelen in deze kluis klikt](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Nadat u op de optie voor het herstellen van meerdere regio's in deze kluis hebt geklikt](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Meer informatie over het [weer geven van back-upitems in de secundaire regio](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Meer informatie over [het herstellen van de secundaire regio](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Meer informatie over het [bewaken van herstel taken van secundaire regio's](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="modifying-default-settings"></a>Standaard instellingen wijzigen

We raden u ten zeerste aan de standaard instellingen voor het **type opslag replicatie** en de **beveiligings instellingen** te controleren voordat u back-ups in de kluis configureert.

- **Type opslag replicatie** is standaard ingesteld op **geo-redundant**. Zodra u de back-up hebt geconfigureerd, is de optie om te wijzigen uitgeschakeld. Volg deze [stappen](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) om de instellingen te controleren en te wijzigen.

- **Voorlopig verwijderen** is standaard **ingeschakeld** op nieuwe kluizen om back-upgegevens te beschermen tegen onbedoelde of schadelijke verwijderingen. Volg deze [stappen](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#enabling-and-disabling-soft-delete) om de instellingen te controleren en te wijzigen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](backup-azure-recovery-services-vault-overview.md) Recovery Services kluizen.
[Meer informatie over](backup-azure-delete-vault.md) Recovery Services kluizen verwijderen.

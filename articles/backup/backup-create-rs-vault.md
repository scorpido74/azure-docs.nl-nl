---
title: Recovery Services kluizen maken
description: In dit artikel leert u hoe u Recovery Services kluizen maakt waarin de back-ups en herstel punten worden opgeslagen.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: eb7dcb4038260b1edac57a2163a1c0eae29ee452
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363723"
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
>- De functie voor het terugzetten van meerdere regio's (CRR) is momenteel alleen beschikbaar in de volgende regio's: West-Centraal VS, VS-West 2, Australië-oost en Australië-zuidoost. 
>- CRR is een opt-in-functie op kluis niveau voor elke GRS-kluis (standaard uitgeschakeld).
>- Gebruik de volgende opdracht om uw abonnement voor deze functie voor te bereiden:<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Als u deze functie tijdens een open bare beperkte preview onbeschikbaar maakt, bevat het e-mail bericht over de goed keuring de prijs informatie.
>- Na het inbrengen kan het tot 48 uur duren voordat de back-upitems beschikbaar zijn in secundaire regio's.
>- Momenteel wordt CRR alleen ondersteund voor het type back-upbeheer. ARM Azure VM (klassieke Azure-VM wordt niet ondersteund).  Wanneer extra beheer typen ondersteuning bieden voor CRR, worden ze **automatisch** Inge schreven.

### <a name="configure-cross-region-restore"></a>Herstel van meerdere regio's configureren

Een kluis die is gemaakt met GRS-redundantie bevat de optie voor het configureren van de functie voor het terugzetten van meerdere regio's. Elke GRS-kluis heeft een banner dat een koppeling naar de documentatie bevat. Als u CRR voor de kluis wilt configureren, gaat u naar de Blade back-upconfiguratie. Deze bevat de optie om deze functie in te scha kelen.

 ![Banner back-upconfiguratie](./media/backup-azure-arm-restore-vms/banner.png)

1. Ga in de portal naar Recovery Services kluis > Instellingen > Eigenschappen.
2. Klik op **Restore voor meerdere regio's inschakelen in deze kluis** om de functionaliteit in te scha kelen.

   ![Voordat u op Restore voor meerdere regio's inschakelen in deze kluis klikt](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Nadat u op de optie voor het herstellen van meerdere regio's in deze kluis hebt geklikt](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Meer informatie over het [weer geven van back-upitems in de secundaire regio](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Meer informatie over [het herstellen van de secundaire regio](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Meer informatie over het [bewaken van herstel taken van secundaire regio's](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="modifying-default-settings"></a>Standaard instellingen wijzigen

We raden u ten zeerste aan de standaard instellingen voor het **type opslag replicatie** en de **beveiligings instellingen** te controleren voordat u back-ups in de kluis configureert.

- **Type opslag replicatie** is standaard ingesteld op **geo-redundant**. Zodra u de back-up hebt geconfigureerd, is de optie om te wijzigen uitgeschakeld. Volg deze [stappen](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) om de instellingen te controleren en te wijzigen.

- **Voorlopig verwijderen** is standaard **ingeschakeld** op nieuwe kluizen om back-upgegevens te beschermen tegen onbedoelde of schadelijke verwijderingen. Volg deze [stappen](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) om de instellingen te controleren en te wijzigen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](backup-azure-recovery-services-vault-overview.md) Recovery Services kluizen.
[Meer informatie over](backup-azure-delete-vault.md) Recovery Services kluizen verwijderen.

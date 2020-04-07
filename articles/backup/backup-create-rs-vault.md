---
title: Vaults van Recovery Services maken
description: In dit artikel leert u hoe u vaults van Recovery Services maakt die de back-ups en herstelpunten opslaan.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 439f102e8f13bff63ab388be8f10df07ab2dc7d2
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672854"
---
# <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een vault van Recovery Services is een entiteit die de back-ups en herstelpunten opslaat die in de loop van de tijd zijn gemaakt. De kluis Recovery Services bevat ook het back-upbeleid dat is gekoppeld aan de beveiligde virtuele machines.

Een Recovery Services-kluis maken:

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

2. Selecteer in het menu links **Alle services**.

    ![Alle services selecteren](./media/backup-create-rs-vault/click-all-services.png)

3. In het dialoogvenster **Alle services** voert u **Recovery Services** in. De lijst met bronnen filtert op basis van uw invoer. Selecteer in de lijst met bronnen de **optie Kluizen van Recovery Services**.

    ![Recovery Services-kluizen invoeren en kiezen](./media/backup-create-rs-vault/all-services.png)

    De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

4. Selecteer In het dashboard van de **vaults van Recovery Services** de optie **Toevoegen**.

    ![Een kluis van Recovery Services toevoegen](./media/backup-create-rs-vault/add-button-create-vault.png)

    Het **dialoogvenster Herstelservices wordt** geopend. Geef waarden op voor de **groep Naam**, **Abonnement**, **Resource**en **Locatie**.

    ![De kluis Recovery Services configureren](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Naam:** Voer een vriendelijke naam in om de kluis te identificeren. De naam moet uniek zijn voor het Azure-abonnement. Geef een naam op met ten minste twee, maar niet meer dan 50 tekens. De naam moet beginnen met een letter en alleen bestaan uit letters, cijfers en koppeltekens.
   - **Abonnement**: Kies het te gebruiken abonnement. Als u lid bent van slechts één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u wilt gebruiken, gebruikt u het standaardabonnement (voorgesteld). Er zijn alleen meerdere opties als uw werk- of schoolaccount is gekoppeld aan meer dan één Azure-abonnement.
   - **Resourcegroep:** gebruik een bestaande resourcegroep of maak een nieuwe groep. Als u de lijst met beschikbare brongroepen in uw abonnement wilt weergeven, selecteert u **Bestaande gebruiken**en selecteert u vervolgens een resource in de vervolgkeuzelijst. Als u een nieuwe resourcegroep wilt maken, selecteert u **Nieuw maken** en voert u de naam in. Zie overzicht van Azure [Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)voor volledige informatie over resourcegroepen.
   - **Locatie:** Selecteer het geografische gebied voor de kluis. Om een kluis te maken om virtuele machines te beschermen, **moet** de kluis zich in dezelfde regio bevinden als de virtuele machines.

      > [!IMPORTANT]
      > Als u niet zeker bent van de locatie van uw virtuele machine, sluit u het dialoogvenster. Ga naar de lijst met virtuele machines in het portaal. Als u virtuele machines in verschillende regio's hebt, maakt u in elke regio een kluis van Recovery Services. Maak de kluis op de eerste locatie, voordat u de kluis maakt voor een andere locatie. Het is niet nodig om opslagaccounts op te geven om de back-upgegevens op te slaan. De kluis Recovery Services en de Azure Backup-service verwerken dat automatisch.
      >
      >

5. Wanneer u klaar bent om de kluis Recovery Services te maken, selecteert u **Maken**.

    ![De kluis Recovery Services maken](./media/backup-create-rs-vault/click-create-button.png)

    Het kan even duren voordat de kluis Van Recovery Services is gemaakt. Controleer de statusmeldingen in het gebied **Meldingen** in de rechterbovenhoek van de portal. Nadat uw kluis is gemaakt, is deze zichtbaar in de lijst met kluizen van Recovery Services. Als u uw kluis niet ziet, selecteert u **Vernieuwen**.

     ![De lijst met back-upkluizen vernieuwen](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Opslagredundantie instellen

Azure Backup verwerkt automatisch de opslag voor de kluis. U moet opgeven hoe die opslag wordt gerepliceerd.

1. Klik op de blade **Recovery Services-kluizen** op de nieuwe kluis. Klik **onder** de sectie Instellingen op **Eigenschappen**.
2. Klik in **Eigenschappen**onder **Back-upconfiguratie**op **Bijwerken**.

3. Selecteer het type opslagreplicatie en klik op **Opslaan**.

     ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - We raden u aan azure te blijven gebruiken als eindpunt voor primaire back-upopslag, en de **standaardinstelling Geo-redundant** blijft gebruiken.
   - Als Azure niet uw primaire eindpunt is voor back-upopslag, kiest u **Lokaal redundant**, zodat u de kosten voor Azure-opslag verlaagt.
   - Meer informatie over [geo-](../storage/common/storage-redundancy-grs.md) en [lokale](../storage/common/storage-redundancy-lrs.md) redundantie.

> [!NOTE]
> Het **type opslagreplicatie** wijzigen (lokaal redundant/ Geo-redundant) voor een kluis van herstelservices moet worden uitgevoerd voordat back-ups in de kluis worden geconfigureerd. Zodra u een back-up hebt geconfigureerd, wordt de optie om te wijzigen uitgeschakeld en u het **type opslagreplicatie**niet wijzigen.

## <a name="set-cross-region-restore"></a>Regio-herstel instellen

Als een van de herstelopties u met CrR (Cross Region Restore) Azure VM's herstellen in een secundaire regio, een [gekoppeld eis voor Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) Met deze optie u:

- oefeningen uitvoeren wanneer er een audit- of nalevingsvereiste is
- de VM of de schijf herstellen als er een ramp is in het primaire gebied.

Als u deze functie wilt kiezen, selecteert u **Cross Region Restore inschakelen** in het blade **Back-upconfiguratie.**

Voor dit proces zijn er prijsimplicaties zoals het is op het opslagniveau.

>[!NOTE]
>Voordat u begint:
>
>- Bekijk de [ondersteuningsmatrix](backup-support-matrix.md#cross-region-restore) voor een lijst met ondersteunde beheerde typen en regio's.
>- De crr-functie (Cross Region Restore) wordt nu weergegeven in alle openbare Azure-regio's.
>- CRR is een opt-in-functie voor een vault level voor elke GRS-kluis (standaard uitgeschakeld).
>- Gebruik de volgende opdracht om aan boord te gaan van uw abonnement voor deze functie:<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Als u tijdens een openbare preview aan deze functie bent vastgemaakt, bevat de e-mail met goedkeuring van de beoordeling details van het prijsbeleid.
>- Na het aanmelden kan het tot 48 uur duren voordat de back-upitems beschikbaar zijn in secundaire regio's.
>- Momenteel wordt CRR alleen ondersteund voor Backup Management Type - ARM Azure VM (klassieke Azure VM wordt niet ondersteund).  Wanneer extra beheertypen CRR ondersteunen, worden ze **automatisch** ingeschreven.

### <a name="configure-cross-region-restore"></a>Herstel van verschillende regio's configureren

Een kluis die is gemaakt met GRS-redundantie bevat de optie om de functie Cross Region Restore te configureren. Elke GRS-kluis heeft een banner, die wordt gekoppeld aan de documentatie. Als u CRR voor de kluis wilt configureren, gaat u naar het hoofd van de back-upconfiguratie, dat de optie bevat om deze functie in te schakelen.

 ![Banner Back-upconfiguratie](./media/backup-azure-arm-restore-vms/banner.png)

1. Ga vanuit de portal naar Vault > Instellingen > Instellingen > Eigenschappen.
2. Klik **op Cross Region Restore inschakelen in deze kluis** om de functionaliteit in te schakelen.

   ![Voordat u in deze kluis op Cross Region restore inschakelen klikt](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Nadat u op Cross Region restore inschakelen hebt geklikt in deze kluis](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Meer informatie over het [weergeven van back-upitems in het secundaire gebied](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Meer informatie over herstellen [in het secundaire gebied](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Meer informatie over het [controleren van hersteltaken in de secundaire regio.](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs)

## <a name="modifying-default-settings"></a>Standaardinstellingen wijzigen

We raden u ten zeerste aan de standaardinstellingen voor **het type opslagreplicatie** en **beveiligingsinstellingen** te controleren voordat u back-ups in de kluis configureert.

- **Het type opslagreplicatie** is standaard ingesteld op **Geo-redundant**. Zodra u de back-up hebt geconfigureerd, wordt de optie om te wijzigen uitgeschakeld. Volg deze [stappen](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) om de instellingen te controleren en te wijzigen.

- **Soft delete** is standaard **ingeschakeld** op nieuw gemaakte kluizen om back-upgegevens te beschermen tegen onbedoelde of schadelijke verwijderingen. Volg deze [stappen](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) om de instellingen te controleren en te wijzigen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](backup-azure-recovery-services-vault-overview.md) Vaults van Recovery Services.
[Meer informatie over](backup-azure-delete-vault.md) Vaults voor Herstelservices verwijderen.

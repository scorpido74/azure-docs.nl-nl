---
title: bestand opnemen
description: bestand opnemen
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 6167774171affda7e5469d5852a79657a6da700d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262751"
---
## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Services kluis is een opslag entiteit waarin de herstel punten worden opgeslagen die in de loop van de tijd zijn gemaakt. Het bevat ook het back-upbeleid dat is gekoppeld aan beveiligde items.

Voer de volgende stappen uit om een Recovery Services kluis te maken.

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

1. Selecteer in het menu links **Alle services**.

    ![Alle services selecteren](./media/backup-create-rs-vault/click-all-services.png)

1. In het dialoogvenster **Alle services** voert u *Recovery Services* in. De lijst met resources filtert op basis van uw invoer. Selecteer **Recovery Services kluizen**in de lijst met resources.

    ![Recovery Services-kluizen invoeren en kiezen](./media/backup-create-rs-vault/all-services.png)

    De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

1. Selecteer **toevoegen**op het **Recovery Services kluizen** -dash board.

    ![Een Recovery Services kluis toevoegen](./media/backup-create-rs-vault/add-button-create-vault.png)

    Het dialoog venster **Recovery Services kluis** wordt geopend. Geef waarden op voor de **naam**, het **abonnement**, de **resource groep**en de **locatie**.

    ![De Recovery Services kluis configureren](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Naam**: Voer een beschrijvende naam in om de kluis aan te duiden. De naam moet uniek zijn voor het Azure-abonnement. Geef een naam op van ten minste 2, maar niet meer dan 50 tekens. De naam moet beginnen met een letter en mag alleen bestaan uit letters, cijfers en afbreek streepjes.
   - **Abonnement**: Kies het abonnement dat u wilt gebruiken. Als u lid bent van slechts één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u moet gebruiken, gebruikt u het standaard abonnement (aanbevolen). Er zijn alleen meerdere opties als uw werk-of school account is gekoppeld aan meer dan één Azure-abonnement.
   - **Resource groep**: gebruik een bestaande resource groep of maak een nieuwe. Als u de lijst met beschik bare resource groepen in uw abonnement wilt weer geven, selecteert u **bestaande gebruiken**en selecteert u een resource in de vervolg keuzelijst. Als u een nieuwe resource groep wilt maken, selecteert u **nieuwe maken** en voert u de naam in. Zie [Azure Resource Manager Overview](../articles/azure-resource-manager/management/overview.md)voor meer informatie over resource groepen.
   - **Locatie**: Selecteer de geografische regio voor de kluis. De kluis *moet* zich in dezelfde regio bevinden als de virtuele machines om een kluis te maken om virtuele machines te beveiligen.

      > [!IMPORTANT]
      > Als u niet zeker bent van de locatie van de virtuele machine, sluit u het dialoog venster. Ga naar de lijst met virtuele machines in de portal. Als u virtuele machines in meerdere regio's hebt, moet u in elke regio een Recovery Services kluis maken. Maak de kluis op de eerste locatie voordat u de kluis voor een andere locatie maakt. U hoeft geen opslag accounts op te geven om de back-upgegevens op te slaan. De Recovery Services kluis en Azure Backup die automatisch worden verwerkt.
      >
      >

1. Wanneer u klaar bent om de Recovery Services kluis te maken, selecteert u **maken**.

    ![De Recovery Services kluis maken](./media/backup-create-rs-vault/click-create-button.png)

    Het kan even duren om de Recovery Services kluis te maken. Bewaak de status meldingen in het gebied **meldingen** in de rechter bovenhoek van de portal. Nadat de kluis is gemaakt, wordt deze weer gegeven in de lijst met Recovery Services kluizen. Als uw kluis niet wordt weer geven, selecteert u **vernieuwen**.

     ![De lijst met back-upkluizen vernieuwen](./media/backup-create-rs-vault/refresh-button.png)

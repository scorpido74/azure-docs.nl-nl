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
ms.openlocfilehash: c0f717e26534151585a51badf4bb1fe1ba58eb00
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970519"
---
## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Services kluis is een beheer entiteit waarmee herstel punten worden opgeslagen die in de loop van de tijd zijn gemaakt en die een interface biedt voor het uitvoeren van back-upbewerkingen. Dit omvat het maken van back-ups op aanvraag, het uitvoeren van herstel bewerkingen en het maken van back-upbeleid.

Voer de volgende stappen uit om een Recovery Services kluis te maken.

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

1. Selecteer in het menu links **alle services**.

    ![Alle services selecteren](./media/backup-create-rs-vault/click-all-services.png)

1. In het dialoogvenster **Alle services** voert u *Recovery Services* in. De lijst met resources wordt gefilterd op basis van uw invoer. In de lijst met resources selecteert u **Recovery Service-kluizen**.

    ![Recovery Services-kluizen invoeren en kiezen](./media/backup-create-rs-vault/all-services.png)

    De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

1. Selecteer **toevoegen**op het **Recovery Services kluizen** -dash board.

    ![Een Recovery Services kluis toevoegen](./media/backup-create-rs-vault/add-button-create-vault.png)

    Het dialoogvenster **Recovery Service-kluis** wordt geopend. Geef waarden op voor de **naam**, het **abonnement**, de **resource groep**en de **locatie**.

    ![De Recovery Services kluis configureren](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Naam**: Voer een beschrijvende naam in om de kluis aan te duiden. De naam moet uniek zijn voor het Azure-abonnement. Geef een naam op van ten minste 2, maar niet meer dan 50 tekens. De naam moet beginnen met een letter en mag alleen uit letters, cijfers en afbreekstreepjes bestaan.
   - **Abonnement**: Kies het abonnement dat u wilt gebruiken. Als u lid bent van maar één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u moet gebruiken, gebruikt u het standaardabonnement (voorgesteld). Er zijn alleen meerdere mogelijkheden als uw werk- of schoolaccount is gekoppeld aan meerdere Azure-abonnementen.
   - **Resourcegroep**: Gebruik een bestaande resourcegroep of maak een nieuwe. Als u de lijst met beschik bare resource groepen in uw abonnement wilt weer geven, selecteert u **bestaande gebruiken**en selecteert u een resource in de vervolg keuzelijst. Als u een nieuwe resourcegroep wilt maken, selecteert u **Nieuwe maken** en voert u de naam in. Zie [Azure Resource Manager Overview](../articles/azure-resource-manager/management/overview.md)voor meer informatie over resource groepen.
   - **Locatie**: Selecteer de geografische regio voor de kluis. De kluis *moet* zich in dezelfde regio bevinden als de gegevens bron om een kluis te maken om gegevens bronnen te beveiligen.

      > [!IMPORTANT]
      > Als u niet zeker bent van de locatie van de gegevens bron, sluit u het dialoog venster. Ga naar de lijst met resources in de portal. Als u gegevens bronnen in meerdere regio's hebt, maakt u een Recovery Services kluis voor elke regio. Maak de kluis op de eerste locatie voordat u de kluis maakt voor een andere locatie. U hoeft geen opslag accounts op te geven om de back-upgegevens op te slaan. De Recovery Services kluis en Azure Backup die automatisch worden verwerkt.
      >
      >

1. Wanneer u klaar bent om de Recovery Services kluis te maken, selecteert u **maken**.

    ![De Recovery Services kluis maken](./media/backup-create-rs-vault/click-create-button.png)

    Het kan even duren om de Recovery Services kluis te maken. Bewaak de status meldingen in het gebied **meldingen** in de rechter bovenhoek van de portal. Nadat de kluis is gemaakt, wordt deze weer gegeven in de lijst met Recovery Services kluizen. Als uw kluis niet wordt weer geven, selecteert u **vernieuwen**.

     ![De lijst met back-upkluizen vernieuwen](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> We raden u ten zeerste aan de standaard instellingen voor het **type opslag replicatie** en de **beveiligings instellingen** te controleren voordat u back-ups in de kluis configureert. Zie de sectie [opslag redundantie instellen](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) voor meer informatie.

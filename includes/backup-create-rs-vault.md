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

Een vault van Recovery Services is een opslagentiteit die de herstelpunten opslaat die in de loop van de tijd zijn gemaakt. Het bevat ook het back-upbeleid dat is gekoppeld aan beveiligde items.

Voer deze stappen uit om een kluis van Recovery Services te maken.

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

1. Selecteer in het menu links **Alle services**.

    ![Alle services selecteren](./media/backup-create-rs-vault/click-all-services.png)

1. In het dialoogvenster **Alle services** voert u *Recovery Services* in. De lijst met bronnen filtert op basis van uw invoer. Selecteer in de lijst met bronnen de **optie Kluizen van Recovery Services**.

    ![Recovery Services-kluizen invoeren en kiezen](./media/backup-create-rs-vault/all-services.png)

    De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

1. Selecteer In het dashboard van de **vaults van Recovery Services** de optie **Toevoegen**.

    ![Een kluis van Recovery Services toevoegen](./media/backup-create-rs-vault/add-button-create-vault.png)

    Het **dialoogvenster Herstelservices wordt** geopend. Geef waarden op voor de **groep Naam**, **Abonnement**, **Resource**en **Locatie**.

    ![De kluis Recovery Services configureren](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Naam:** Voer een vriendelijke naam in om de kluis te identificeren. De naam moet uniek zijn voor het Azure-abonnement. Geef een naam op met ten minste 2 maar niet meer dan 50 tekens. De naam moet beginnen met een letter en alleen bestaan uit letters, cijfers en koppeltekens.
   - **Abonnement**: Kies het te gebruiken abonnement. Als u lid bent van slechts één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u wilt gebruiken, gebruikt u het standaardabonnement (voorgesteld). Er zijn alleen meerdere opties als uw werk- of schoolaccount is gekoppeld aan meer dan één Azure-abonnement.
   - **Resourcegroep:** gebruik een bestaande resourcegroep of maak een nieuwe groep. Als u de lijst met beschikbare brongroepen in uw abonnement wilt weergeven, selecteert u **Bestaande gebruiken**en selecteert u vervolgens een resource in de vervolgkeuzelijst. Als u een nieuwe resourcegroep wilt maken, selecteert u **Nieuw maken** en voert u de naam in. Zie overzicht azure resource [manager](../articles/azure-resource-manager/management/overview.md)voor meer informatie over resourcegroepen.
   - **Locatie:** Selecteer het geografische gebied voor de kluis. Om een kluis te maken om virtuele machines te beschermen, *moet* de kluis zich in dezelfde regio bevinden als de virtuele machines.

      > [!IMPORTANT]
      > Als u niet zeker bent van de locatie van uw virtuele machine, sluit u het dialoogvenster. Ga naar de lijst met virtuele machines in het portaal. Als u virtuele machines in verschillende regio's hebt, maakt u in elke regio een kluis van Recovery Services. Maak de kluis op de eerste locatie, voordat u de kluis maakt voor een andere locatie. Het is niet nodig om opslagaccounts op te geven om de back-upgegevens op te slaan. De kluis Recovery Services en Azure Backup verwerken dat automatisch.
      >
      >

1. Wanneer u klaar bent om de kluis Recovery Services te maken, selecteert u **Maken**.

    ![De kluis Recovery Services maken](./media/backup-create-rs-vault/click-create-button.png)

    Het kan even duren voordat de kluis Van Recovery Services is gemaakt. Controleer de statusmeldingen in het gebied **Meldingen** in de rechterbovenhoek van de portal. Nadat uw kluis is gemaakt, is deze zichtbaar in de lijst met kluizen van Recovery Services. Als u uw kluis niet ziet, selecteert u **Vernieuwen**.

     ![De lijst met back-upkluizen vernieuwen](./media/backup-create-rs-vault/refresh-button.png)

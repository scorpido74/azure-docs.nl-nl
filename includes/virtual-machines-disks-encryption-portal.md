---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b8073240bdda38757a5e4feee66c9f54746966c4
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632052"
---
### <a name="portal"></a>Portal

Als u de sleutels voor uw schijven voor de klant instelt, moet u resources in een bepaalde volgorde maken als u dit voor de eerste keer doet. Eerst moet u een Azure Key Vault maken en instellen.

#### <a name="setting-up-your-azure-key-vault"></a>Uw Azure Key Vault instellen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Zoeken naar en selecteer **Sleutelkluizen**.

    [![sse-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Uw Azure-sleutelkluis, schijfversleutelingsset, VM, schijven en momentopnamen moeten zich allemaal in dezelfde regio bevinden en moeten een abonnement hebben om de implementatie te laten slagen.

1. Selecteer **+Toevoegen** om een nieuwe sleutelkluis te maken.
1. Maak een nieuwe resourcegroep.
1. Voer een naam van een sleutelkluis in, selecteer een regio en selecteer een prijscategorie.
1. Selecteer **Controleren + Maken,** verifieer uw keuzes en selecteer **Vervolgens Maken**.

    ![Schermafbeelding van de ervaring voor het maken van Azure Key Vault. De specifieke waarden weergeven die u maakt](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Zodra de implementatie van uw sleutelkluis is voltooid, selecteert u deze.
1. Selecteer **Toetsen** onder **Instellingen**.
1. Selecteer **Genereren/importeren**.

    ![Schermafbeelding van het deelvenster Bronnen met de bronbron van De sleutel. Toont de knop genereren/importeren in de instellingen.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Laat beide **sleuteltype** ingesteld op **RSA** en **RSA-sleutelgrootte** ingesteld op **2048**.
1. Vul de overige selecties in zoals u wilt en selecteer **Vervolgens Maken**.

    ![Schermafbeelding van het sleutelblad maken dat wordt weergegeven nadat de knop genereren/importeren is geselecteerd](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Uw schijfversleutelingsset instellen

1. Zoek naar **schijfversleutelingssets** en selecteer deze.
1. Selecteer op het blad **Schijfversleutelingssets** de optie **+Toevoegen**.

    ![Schermafbeelding van het hoofdscherm van de schijfversleutelingportal. De knop Toevoegen markeren](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Selecteer uw brongroep, geef uw versleutelingsset een naam en selecteer dezelfde regio als uw sleutelkluis.
1. Selecteer **Sleutelkluis en sleutel**.
1. Selecteer de sleutelkluis en de sleutel die u eerder hebt gemaakt, evenals de versie.
1. Druk op **Selecteren**.
1. Selecteer **Controleren + Maken** en vervolgens **Maken**.

    ![Schermafbeelding van het paginablad voor het maken van schijfversleuteling. Het abonnement, de brongroep, de naam van de schijfversleutelingset, het gebied en de sleutelkluis + sleutelkiezer weergeven.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Open de schijfversleutelingsset zodra deze is gemaakt en selecteer de waarschuwing die wordt opduikt.

    ![Schermafbeelding van waarschuwingspopup: 'Als u een schijf, afbeelding of momentopname wilt koppelen aan een schijfversleutelingsset, moet u machtigingen verlenen aan de sleutelkluis'. Selecteer deze waarschuwing om door te gaan](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Twee meldingen moeten verschijnen en slagen. Als u dit doet, u de schijfversleutelingsset gebruiken met uw sleutelkluis.

![Schermafbeelding van succesvolle machtigingen en roltoewijzing voor uw sleutelkluis.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Een virtuele machine implementeren

Nu u uw sleutelkluis en de schijfversleutelinghebt gemaakt en ingesteld, u een vm implementeren met behulp van de versleuteling.
Het VM-implementatieproces is vergelijkbaar met het standaardimplementatieproces, de enige verschillen zijn dat u de VM in dezelfde regio moet implementeren als uw andere resources en u kiest ervoor om een door de klant beheerde sleutel te gebruiken.

1. Zoek naar **virtuele machines** en selecteer **+ Toevoegen** om een vm te maken.
1. Selecteer op het tabblad **Basic** dezelfde regio als de schijfversleutelingset en Azure Key Vault.
1. Vul de andere waarden op het tabblad **Basis** in zoals u wilt.

    ![Schermafbeelding van de ervaring voor het maken van vm's, waarbij de regiowaarde is gemarkeerd.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Selecteer op het tabblad **Schijven** **Versleuteling in rust met een door de klant beheerde sleutel**.
1. Selecteer de schijfversleutelingdie is ingesteld in de vervolgkeuzelijst **Schijfversleuteling.**
1. Maak de resterende selecties zoals u wilt.

    ![Schermafbeelding van de ervaring voor het maken van vm's, het schijvenblad. Met de schijfversleutelingset wordt de vervolgkeuzelijst gemarkeerd.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Inschakelen op een bestaande schijf

> [!CAUTION]
> Als u schijfversleuteling inschakelt op schijven die aan een vm zijn gekoppeld, moet u de vm stoppen.
    
1. Navigeer naar een vm die zich in dezelfde regio bevindt als een van uw schijfversleutelingssets.
1. Open de VM en selecteer **Stoppen**.

    ![Schermafbeelding van de hoofdoverlay voor uw voorbeeld-vm. Met de knop Stoppen gemarkeerd](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Nadat de VM is gestopt, selecteert u **Schijven** en selecteert u de schijf die u wilt versleutelen.

    ![Schermafbeelding van uw voorbeeld-vm, waarbij het blade Schijven is geopend. De osschijf wordt gemarkeerd als een voorbeeldschijf die u selecteren.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Selecteer **Versleuteling** en selecteer **Versleuteling in rust met een door de klant beheerde sleutel** en selecteer vervolgens de schijfversleuteling die is ingesteld in de vervolgkeuzelijst.
1. Selecteer **Opslaan**.

    ![Schermafbeelding van uw voorbeeld-besturingssysteemschijf. Het versleutelingsblad is geopend, versleuteling in rust met een door de klant beheerde sleutel is geselecteerd, evenals uw voorbeeld Azure Key Vault. Na het maken van deze selecties wordt de knop Opslaan geselecteerd.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Herhaal dit proces voor alle andere schijven die zijn gekoppeld aan de VM die u wilt versleutelen.
1. Wanneer uw schijven zijn overgeschakeld op door de klant beheerde sleutels, als er geen andere gekoppelde schijven zijn die u wilt versleutelen, u uw VM starten.

---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3ba5d74aa245fbcd9d43f2b4398387d7f59e202c
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299492"
---
### <a name="portal"></a>Portal

Door door de klant beheerde sleutels voor uw schijven in te stellen, moet u resources in een bepaalde volg orde maken, als u deze voor de eerste keer uitvoert. Eerst moet u een Azure Key Vault maken en instellen.

#### <a name="setting-up-your-azure-key-vault"></a>Uw Azure Key Vault instellen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en zoek naar Key Vault
1. Zoek en selecteer **sleutel kluizen**.

    [![SSE-Key-Vault-Portal-Search. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > De Azure-sleutel kluis, de schijf versleutelings, de virtuele machine, de schijven en moment opnamen moeten allemaal in dezelfde regio en hetzelfde abonnement staan om te kunnen worden geïmplementeerd.

1. Selecteer **+ toevoegen** om een nieuw Key Vault te maken.
1. Een nieuwe resourcegroep maken
1. Voer een naam voor de sleutel kluis in, selecteer een regio en selecteer een prijs categorie.
1. Selecteer **controleren + maken**, Controleer uw keuzes en selecteer vervolgens **maken**.

    ![Scherm opname van de Azure Key Vault maken. De specifieke waarden weer geven die u maakt](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Als uw sleutel kluis is geïmplementeerd, selecteert u deze.
1. Selecteer **sleutels** onder **instellingen**.
1. Selecteer **genereren/importeren**

    ![Scherm opname van het deel venster Key Vault resource-instellingen. Hiermee wordt de knop genereren/importeren in instellingen weer gegeven.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Zorg dat het **sleutel type** is ingesteld op **RSA** en **RSA-sleutel grootte** ingesteld op **2080**.
1. Vul de resterende selecties in zoals u wilt en selecteer vervolgens **maken**.

    ![Scherm afbeelding van de Blade een sleutel maken die wordt weer gegeven nadat de knop genereren/importeren is geselecteerd](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Uw schijf versleutelings instellen

Als u schijf versleutelings sets wilt maken en configureren, moet u de volgende koppeling gebruiken: https://aka.ms/diskencryptionsets. Als u zich in de Microsoft Azure Government regio's bevindt, moet u in plaats daarvan deze koppeling gebruiken: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff). Het maken van de schijf versleuteling is nog niet beschikbaar in de globale Azure Portal.

1. Open de juiste koppeling voor de schijf versleutelings sets voor uw regio:

    Open bare regio's: [https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure Government regio's: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Selecteer **+ toevoegen**.

    ![Scherm afbeelding van het hoofd scherm van de portal voor schijf versleuteling. De knop toevoegen markeren](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Selecteer uw resource groep, geef uw versleutelings set een naam en selecteer dezelfde regio als uw sleutel kluis.
1. Selecteer **sleutel kluis en sleutel**.
1. Selecteer de sleutel kluis en de sleutel die u eerder hebt gemaakt, evenals de versie.
1. Druk op **selecteren**.
1. Selecteer **controleren + maken** en vervolgens **maken**.

    ![Scherm opname van de Blade voor het maken van schijf versleuteling. Het abonnement, de resource groep, de naam van de schijf versleutelings, de regio en de sleutel kluis + sleutel kiezer worden weer gegeven.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Open de schijf versleutelings nadat deze is gemaakt en selecteer de waarschuwing die wordt weer gegeven.

    ![Scherm afbeelding van pop-up van waarschuwing: ' als u een schijf, installatie kopie of moment opname aan een schijf versleutelings wilt koppelen, moet u machtigingen verlenen aan de sleutel kluis. Selecteer deze waarschuwing om door te gaan](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Er worden twee meldingen over de pop-up en geslaagd. Als u dit doet, kunt u de schijf versleuteling instellen met uw sleutel kluis.

![Scherm opname van geslaagde machtiging en roltoewijzing voor uw sleutel kluis.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Een virtuele machine implementeren

Nu u de sleutel kluis en de schijf versleutelings hebt gemaakt en ingesteld, kunt u een virtuele machine implementeren met behulp van de versleuteling.
Het implementatie proces van de VM is vergelijkbaar met het standaard implementatieproces, maar de enige verschillen zijn dat u de virtuele machine in dezelfde regio als uw andere resources moet implementeren en u ervoor kiest om een door de klant beheerde sleutel te gebruiken.

1. Open de juiste koppeling voor de schijf versleutelings sets voor uw regio:

    Open bare regio's: [https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure Government regio's: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)

1. Zoek naar **virtual machines** en selecteer **+ toevoegen** om een virtuele machine te maken.
1. Op het tabblad **basis** selecteert u dezelfde regio als uw schijf versleutelings en Azure Key Vault.
1. Vul op het tabblad **basis** de andere waarden in zoals u wilt.

    ![Scherm afbeelding van het maken van de VM, waarbij de regio waarde is gemarkeerd.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Op het tabblad **schijven** selecteert u **versleuteling in rust met een door de klant beheerde sleutel**.
1. Selecteer de schijf versleuteling die u hebt ingesteld in de vervolg keuzelijst **schijf versleuteling instellen** .
1. Breng de resterende selecties naar wens aan.

    ![Scherm afbeelding van de werk ervaring voor het maken van de virtuele machine, de Blade schijven. Met de vervolg keuzelijst schijf versleuteling instellen gemarkeerd.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Inschakelen op een bestaande schijf

Als u schijf versleuteling op uw bestaande schijven wilt beheren en configureren, moet u de volgende koppeling gebruiken: https://aka.ms/diskencryptionsets. Het inschakelen van door de klant beheerde sleutels op bestaande schijven is nog niet beschikbaar in de globale Azure Portal.

> [!CAUTION]
> Als u schijf versleuteling wilt inschakelen op schijven die zijn gekoppeld aan een VM, moet u de virtuele machine stoppen.

1. Open de juiste koppeling voor de schijf versleutelings sets voor uw regio:

    Open bare regio's: [https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure Government regio's: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Ga naar een virtuele machine die zich in dezelfde regio bevindt als een van de schijf versleutelings sets.
1. Open de virtuele machine en selecteer **stoppen**.

    ![Scherm afbeelding van de belangrijkste overlay voor uw voorbeeld-VM. Met de knop stoppen gemarkeerd](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Nadat de VM is gestopt, selecteert u **schijven** en selecteert u vervolgens de schijf die u wilt versleutelen.

    ![Scherm opname van uw voor beeld-VM, met de Blade schijven geopend. De besturingssysteem schijf is gemarkeerd als een voor beeld van een schijf die u kunt selecteren.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Selecteer **versleuteling** en selecteer **op rest versleuteling met een door de klant beheerde sleutel** en selecteer vervolgens de schijf versleuteling die u hebt ingesteld in de vervolg keuzelijst.
1. Selecteer **Opslaan**.

    ![Scherm opname van de besturingssysteem schijf van uw voor beeld. De versleutelings Blade is geopend. versleuteling in rust met een door de klant beheerde sleutel is geselecteerd, evenals uw voor beeld Azure Key Vault. Nadat u deze selecties hebt gemaakt, is de knop Opslaan geselecteerd.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Herhaal dit proces voor alle andere schijven die zijn gekoppeld aan de virtuele machine die u wilt versleutelen.
1. Als uw schijven zijn overgeschakeld naar door de klant beheerde sleutels en er geen andere gekoppelde schijven zijn die u wilt versleutelen, kunt u de virtuele machine starten.
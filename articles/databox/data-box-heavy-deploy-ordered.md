---
title: Zelf studie voor het best Ellen van Azure Data Box Heavy | Microsoft Docs
description: Meer informatie over de implementatie vereisten en het best Ellen van een Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: dce3549bde4c62245e1d2b1f8ac0c88c0b70260c
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241391"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Zelfstudie: Azure Data Box Heavy volgorde


Azure Data Box Heavy is een hybride oplossing waarmee u op een snelle, eenvoudige en betrouw bare manier uw on-premises gegevens kunt importeren in Azure. U brengt uw gegevens over naar een opslag apparaat met een door micro soft geleverde 770 TB (beschik bare capaciteit) en verzendt het apparaat vervolgens terug. Deze gegevens worden vervolgens geüpload in Azure.

In deze zelf studie wordt beschreven hoe u een Azure Data Box Heavy kunt best Ellen. In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Vereisten voor Data Box Heavy
> * Een Data Box Heavy best Ellen
> * De bestelling volgen
> * De bestelling annuleren

## <a name="prerequisites"></a>Vereisten

Zorg dat u aan de volgende configuratievereisten voor de Data Box-service en het apparaat voldoet voordat u het apparaat implementeert.

### <a name="for-installation-site"></a>Voor installatie site

Zorg voordat u begint voor het volgende:

- Het apparaat voldoet aan de standaard Deursen en entryways. Controleer echter of het apparaat geschikt is voor al uw entryways. De afmetingen van het apparaat zijn: breedte: 26 "lengte: 48 hoogte: 28 '.
- Als deze is geïnstalleerd op een andere vloer dan de grond vloer, hebt u toegang tot het apparaat nodig via een lift of een platform. Het apparaat weegt ongeveer ~ 500 lbs.
- Zorg ervoor dat u in het Data Center een platte site hebt met een beschik bare netwerk verbinding die een apparaat met deze footprint kan ondersteunen.


### <a name="for-service"></a>Voor de service

Zorg voordat u begint voor het volgende:
- U hebt een Microsoft Azure Storage-account met toegangsreferenties.
- Zorg ervoor dat het abonnement dat u voor de Data Box-service gebruikt, een van de volgende typen is:
    - Microsoft Enterprise Agreement (EA). Meer informatie over [EA-abonnementen](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Meer informatie over het [Azure CSP-programma](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship. Meer informatie over het [Azure Sponsorship-programma](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Zorg ervoor dat u de eigenaar of Inzender toegang tot het abonnement hebt om een Data Box Heavy order te maken.

### <a name="for-device"></a>Voor het apparaat

Zorg voordat u begint voor het volgende:
- Het apparaat is uitgepakt.
- Er is een hostcomputer verbonden met het datacenternetwerk. Data Box Heavy worden de gegevens van deze computer gekopieerd. Op de hostcomputer moet een ondersteund besturings systeem worden uitgevoerd, zoals beschreven in [Azure data Box Heavy systeem vereisten](data-box-system-requirements.md).
- U moet een laptop met RJ-45-kabel hebben om verbinding te maken met de lokale gebruikers interface en het apparaat te configureren. Gebruik de laptop om elk knoop punt van het apparaat één keer te configureren.
- Uw datacenter moet een netwerk met hoge snelheid hebben. Het wordt aangeraden dat u beschikt over minstens één 10 GbE-verbinding.
- U hebt 1 40 Gbps of 10 Gbps kabel per apparaat-knoop punt nodig. Kies de kabels die compatibel zijn met de [MELLANOX MCX314A-BCCT-](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) netwerk interface:

    - Voor de 40 Gbps-kabel moet het apparaat uiteinde van de kabel QSFP + zijn.
    - Voor de 10 Gbps-kabel hebt u een SFP +-kabel nodig die aan een kant van een 10 G-switch wordt aangesloten, met een QSFP + tot SFP +-adapter (of de QSA-adapter) voor het einde dat op het apparaat wordt aangesloten.
    - De stroom kabels zijn opgenomen in het apparaat.

## <a name="order-data-box-heavy"></a>Data Box Heavy volgorde

Voer de volgende stappen uit in de Azure-portal om een apparaat te bestellen.

1. Gebruik uw Microsoft Azure-referenties om u aan te melden op deze URL: [https://portal.azure.com](https://portal.azure.com).
2. Selecteer **+ een resource maken** en zoek naar *Azure data Box*. Selecteer **Azure data Box**.
    
   [![Zoek naar Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Selecteer **Maken**.

4. Controleer of de Data Box-Service beschikbaar is in uw regio. Voer de volgende informatie in of Selecteer deze en selecteer **Toep assen**.

    |Instelling  |Value  |
    |---------|---------|
    |Subscription     | Selecteer een EA-, CSP- of Azure Sponsorship-abonnement voor de Data Box-service. <br> Het abonnement is gekoppeld aan uw factureringsrekening.       |
    |Type overdracht     | Selecteer **Importeren in Azure**.        |
    |Bronland     | Selecteer het land of de regio waar uw gegevens zich momenteel bevinden.         |
    |Doel-Azure-regio     | Selecteer de Azure-regio waarnaar u uw gegevens wilt overdragen.        |

    [![Beschik baarheid van Data Box gezin selecteren](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Selecteer **Data Box Heavy**. De maximale bruikbare capaciteit voor één bestelling is 770 TB.

    [![Data Box Heavy selecteren](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. Voer in **Bestellen** de **Orderdetails** in. Voer de volgende informatie in of Selecteer deze en selecteer **volgende**.
    
    |Instelling  |Waarde  |
    |---------|---------|
    |Name     | Geef een beschrijvende naam op om de bestelling te volgen. <br> De naam kan tussen 3 en 24 tekens bevatten (letters, cijfers en afbreekstreepjes). <br> De naam moet beginnen en eindigen met een letter of cijfer.      |
    |Resource group     | Gebruik een bestaande of maak een nieuwe. <br> Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd.         |
    |Doel-Azure-regio     | Selecteer een regio voor uw opslagaccount. <br> Ga naar [Beschikbaarheid in de regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox) voor meer informatie.        |
    |Opslaglocatie     | Kies uit een opslag account of beheerde schijven of beide. <br> Selecteer een of meer opslagaccounts in de gefilterde lijst van een bestaand opslagaccount, gebaseerd op de opgegeven Azure-regio. <br>Data Box Heavy kunnen worden gekoppeld aan Maxi maal 10 opslag accounts. <br> U kunt ook een nieuw **Algemeen v1**-, **Algemeen v2**-of **Blob-opslag account**maken. <br> Azure Data Lake Storage gen 2-accounts worden niet ondersteund. Bekijk de [opslag accounts die worden ondersteund door uw apparaat](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Opslag accounts met virtuele netwerken worden ondersteund. Als u wilt toestaan dat Data Box-Service met beveiligde opslag accounts werkt, schakelt u de vertrouwde services in in de firewall instellingen van het opslag account. Zie [Azure data Box-Service toevoegen als een vertrouwde service](../storage/common/storage-network-security.md#exceptions)voor meer informatie.|

    Als u Storage-account als opslag doel gebruikt, ziet u de volgende scherm afbeelding:

    ![Data Box Heavy volgorde voor het opslag account](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Als u naast het opslag account ook de opslag bestemming Data Box Heavy gebruikt om beheerde schijven te maken op basis van de on-premises Vhd's, moet u de volgende informatie opgeven:

    |Instelling  |Value  |
    |---------|---------|
    |Resourcegroepen     | Maak nieuwe resource groepen als u beheerde schijven wilt maken op basis van on-premises Vhd's. U kunt een bestaande resource groep alleen gebruiken als de resource groep eerder is gemaakt tijdens het maken van een Data Box Heavy bestelling voor beheerde schijven door Data Box-Service. <br> Geef meerdere resource groepen op, gescheiden door punt komma's. Maxi maal 10 resource groepen worden ondersteund.|

    ![Data Box Heavy volgorde voor beheerde schijf](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Het opslag account dat is opgegeven voor beheerde schijven, wordt gebruikt als een staging-opslag account. De Data Box-Service uploadt de Vhd's als pagina-blobs naar het staging Storage-account voordat u deze omzet in Managed disks en verplaatst naar de resource groepen. Zie voor meer informatie [het uploaden van gegevens naar Azure controleren](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. Bij **Verzendadres** geeft u uw voor- en achternaam, de naam en het postadres van het bedrijf en een geldig telefoonnummer op. Selecteer **adres valideren**. 

    De service controleert of de service beschikbaar is voor de regio van het verzendadres. Als de service beschikbaar is voor het opgegeven verzendadres, ontvangt u daarover een melding. Selecteer **Volgende**.

8. In de **Meldingsdetails** geeft u een e-mailadres op. De service stuurt e-mailmeldingen naar het opgegeven e-mailadres over updates van de bestelstatus.

    We raden u aan een e-mailadres van een groep te gebruiken, zodat u meldingen blijft ontvangen als een beheerder de groep verlaat.

9. Bekijk de gegevens met betrekking tot de bestelling, het contact, de meldingen en de privacyvoorwaarden in **Samenvatting**. Vink het selectievakje aan waarmee u akkoord gaat met de privacyvoorwaarden.

10. Selecteer **volg orde**. Het duurt een paar minuten voordat de bestelling is gemaakt.


## <a name="track-the-order"></a>De bestelling volgen

Nadat u uw bestelling hebt geplaatst, kunt u de status van de bestelling volgen via de Azure-portal. Ga naar de Data Box Heavy volgorde en ga vervolgens naar **overzicht** om de status weer te geven. In de portal ziet u dat de bestelling de status **Besteld** heeft.

Als het apparaat niet beschikbaar is, ontvangt u een melding. Als het apparaat wel beschikbaar is, identificeert Microsoft het apparaat dat moet worden verzonden en bereidt Microsoft de verzending voor. Tijdens de voorbereiding van het apparaat vinden de volgende acties plaats:

- Voor elk opslagaccount dat aan het apparaat is gekoppeld, worden SMB-shares gemaakt.
- Voor elke share worden toegangsreferenties zoals een gebruikersnaam en wachtwoord gegenereerd.
- Er wordt ook een apparaatwachtwoord gegenereerd om het apparaat mee te ontgrendelen.
- De Data Box Heavy is vergrendeld om onbevoegde toegang tot het apparaat op enig moment te voor komen.

Wanneer de apparaatvoorbereiding is voltooid, wordt de bestelling in de portal weergegeven met de status **Verwerkt**.

![Data Box Heavy order verwerkt](media/data-box-overview/data-box-order-status-processed.png)

Microsoft bereidt de verzending vervolgens voor en verzendt het apparaat met een regionale vervoerder. U ontvangt uw volgnummer zodra het apparaat is verzonden. In de portal wordt bestelling weergegeven met de status **Verzonden**.

![Data Box Heavy order verzonden](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>De bestelling annuleren

Als u deze bestelling wilt annuleren, gaat u in de Azure-portal naar **Overzicht** en klikt u in de opdrachtbalk op **Annuleren**.

Nadat u een bestelling hebt geplaatst, kunt u deze op elk moment annuleren voordat de status op Verwerkt is ingesteld.
 
Als u een geannuleerde bestelling wilt verwijderen, gaat u naar **Overzicht** en klikt u in de opdrachtbalk op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd over Azure Data Box Heavy-onderwerpen, zoals:

> [!div class="checklist"]
> * Vereisten
> * Data Box Heavy volgorde
> * De bestelling volgen
> * De bestelling annuleren

Ga naar de volgende zelf studie voor meer informatie over het instellen van uw Data Box Heavy.

> [!div class="nextstepaction"]
> [Uw Azure Data Box Heavy instellen](./data-box-heavy-deploy-set-up.md)

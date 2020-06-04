---
title: Zelfstudie voor bestellen van Azure Data Box Heavy | Microsoft Docs
description: Leer wat de implementatievereisten zijn en hoe u een Azure Data Box Heavy kunt bestellen
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 91d78975106299c3b3a6df6d8dee05f337dd339a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742979"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Zelfstudie: Azure Data Box Heavy bestellen


Azure Data Box Heavy is een hybride oplossing waarmee u uw on-premises gegevens snel, gemakkelijk en betrouwbaar in Azure kunt importeren. U zet uw gegevens op een opslagapparaat van 770 TB (bruikbare capaciteit) dat u in bruikleen krijgt van Microsoft en u stuurt het apparaat vervolgens terug. Deze gegevens worden vervolgens geüpload in Azure.

In deze zelfstudie wordt beschreven hoe u een Azure Data Box Heavy bestelt. In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Vereisten voor Data Box Heavy
> * Een Data Box Heavy bestellen
> * De bestelling volgen
> * De bestelling annuleren

## <a name="prerequisites"></a>Vereisten

Zorg dat u aan de volgende configuratievereisten voor de Data Box-service en het apparaat voldoet voordat u het apparaat implementeert.

### <a name="for-installation-site"></a>Voor installatielocatie

Zorg voordat u begint voor het volgende:

- Het apparaat past door alle gangbare deuren en doorgangen. Om er zeker van te zijn dat het apparaat zonder problemen intern kan worden getransporteerd, zijn dit de afmetingen van het apparaat: breedte: 66 cm lengte: 1,22 m hoogte: 71 cm.
- Als u het apparaat wilt installeren op een andere verdieping dan de begane grond, is een lift of een helling vereist. Het apparaat weegt ongeveer 225 kg.
- U moet toegang hebben tot een vlakke opstellingsplaats in het datacenter met een netwerkverbinding die een apparaat met deze footprint kan ondersteunen.

### <a name="for-service"></a>Voor de service

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Voor het apparaat

Zorg voordat u begint voor het volgende:
- Het apparaat is uitgepakt.
- Er is een hostcomputer verbonden met het datacenternetwerk. Data Box Heavy kopieert de gegevens vanaf deze computer. Uw hostcomputer moet een ondersteund besturingssysteem hebben, zoals beschreven in [Systeemvereisten voor Azure Data Box Heavy](data-box-system-requirements.md).
- U hebt een laptop met een RJ-45-kabel nodig om verbinding te maken met de lokale gebruikersinterface en het apparaat te configureren. Gebruik de laptop om elk knooppunt van het apparaat eenmalig te configureren.
- Uw datacenter moet een netwerk met hoge snelheid hebben. Het wordt aangeraden dat u beschikt over minstens één 10 GbE-verbinding.
- U hebt één kabel van 40 Gbps of 10 Gbps per apparaatknooppunt nodig. Kies kabels die compatibel zijn met de netwerkinterface [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html):

    - Voor de kabel van 40 Gbps moet het apparaatuiteinde van de kabel QSFP+ zijn.
    - Voor de kabel van 10 Gbps hebt u een SFP+-kabel nodig die op één kant van een 10-G-switch wordt aangesloten, met een QSFP+-naar-SFP+-adapter (of de QSA-adapter) voor het uiteinde dat op het apparaat wordt aangesloten.
    - De voedingskabels worden meegeleverd met het apparaat.

## <a name="order-data-box-heavy"></a>Data Box Heavy bestellen

Voer de volgende stappen uit in de Azure-portal om een apparaat te bestellen.

1. Gebruik uw Microsoft Azure-referenties om u aan te melden op deze URL: [https://portal.azure.com](https://portal.azure.com).
2. Selecteer **+ Een resource maken** en zoek naar *Azure Data Box*. Selecteer **Azure Data Box**.
    
   [![Zoek naar Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Selecteer **Maken**.

4. Controleer of de Data Box-service beschikbaar is in uw regio. Voer de volgende gegevens in of selecteer deze en selecteer **Toepassen**.

    |Instelling  |Waarde  |
    |---------|---------|
    |Abonnement     | Selecteer een EA-, CSP- of Azure Sponsorship-abonnement voor de Data Box-service. <br> Het abonnement is gekoppeld aan uw factureringsrekening.       |
    |Type overdracht     | Selecteer **Importeren in Azure**.        |
    |Bronland/-regio     | Selecteer het land/de regio waar uw gegevens zich momenteel bevinden.         |
    |Doel-Azure-regio     | Selecteer de Azure-regio waarnaar u uw gegevens wilt overdragen.        |

    [![Beschikbaarheid van Data Box-familie selecteren](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Selecteer **Data Box Heavy**. De maximale bruikbare capaciteit voor één bestelling is 770 TB.

    [![Selecteer Data Box Heavy](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. Voer in **Bestellen** de **Orderdetails** in. Voer de volgende gegevens in of selecteer deze en selecteer **Volgende**.
    
    |Instelling  |Waarde  |
    |---------|---------|
    |Naam     | Geef een beschrijvende naam op om de bestelling te volgen. <br> De naam kan tussen 3 en 24 tekens bevatten (letters, cijfers en afbreekstreepjes). <br> De naam moet beginnen en eindigen met een letter of cijfer.      |
    |Resourcegroep     | Gebruik een bestaande of maak een nieuwe. <br> Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd.         |
    |Doel-Azure-regio     | Selecteer een regio voor uw opslagaccount. <br> Ga naar [Beschikbaarheid in de regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox) voor meer informatie.        |
    |Opslaglocatie     | Kies een opslagaccount, beheerde schijven of beide. <br> Selecteer een of meer opslagaccounts in de gefilterde lijst van een bestaand opslagaccount, gebaseerd op de opgegeven Azure-regio. <br>Data Box Heavy kan worden gekoppeld aan maximaal tien opslagaccounts. <br> U kunt ook een nieuw account van het type **Algemeen gebruik v1**, **Algemeen gebruik v2** of **Blob-opslag** maken. <br>Bekijk de [opslagaccounts die worden ondersteund door uw apparaat](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Opslagaccounts met virtuele netwerken worden ondersteund. Als u wilt dat de Data Box-service kan werken met beveiligde opslagaccounts, schakelt u in de firewallinstellingen van het opslagaccount de vertrouwde services in. Zie [Azure Data Box-service toevoegen als een vertrouwde service](../storage/common/storage-network-security.md#exceptions) voor meer informatie.|

    Als u een opslagaccount selecteert als de opslaglocatie, ziet u het volgende scherm:

    ![Data Box Heavy-bestelling voor opslagaccount](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Als u Data Box Heavy niet alleen als opslaglocatie wilt gebruiken maar ook om beheerde schijven te maken van de on-premises virtuele harde schijven, moet u de volgende informatie opgeven:

    |Instelling  |Waarde  |
    |---------|---------|
    |Resourcegroepen     | Maak nieuwe resourcegroepen als u beheerde schijven wilt maken van on-premises virtuele harde schijven. U kunt alleen een bestaande resourcegroep gebruiken als de resourcegroep eerder is gemaakt en beschikbaar was op het moment van het plaatsen van een Data Box Heavy-bestelling voor een beheerde schijf door de Data Box-service. <br> U kunt meerdere resourcegroepen opgeven door de namen te scheiden met een puntkomma. Er worden maximaal tien resourcegroepen ondersteund.|

    ![Data Box Heavy-bestelling voor beheerde schijf](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Het opslagaccount dat is opgegeven voor beheerde schijven wordt gebruikt als een opslagaccount waarin de gegevens worden klaargezet. De Data Box-service uploadt de virtuele harde schijven als pagina-blobs naar dit opslagaccount waarna de schijven worden omgezet in beheerde schijven en naar de resourcegroepen worden verplaatst. Zie [Uploaden van gegevens naar Azure controleren](data-box-deploy-picked-up.md#verify-data-upload-to-azure) voor meer informatie.

7. Bij **Verzendadres** geeft u uw voor- en achternaam, de naam en het postadres van het bedrijf en een geldig telefoonnummer op. Selecteer **Adres valideren**. 

    De service controleert of de service beschikbaar is voor de regio van het verzendadres. Als de service beschikbaar is voor het opgegeven verzendadres, ontvangt u daarover een melding. Selecteer **Next**.

8. In de **Meldingsdetails** geeft u een e-mailadres op. De service stuurt e-mailmeldingen naar het opgegeven e-mailadres over updates van de bestelstatus.

    We raden u aan een e-mailadres van een groep te gebruiken, zodat u meldingen blijft ontvangen als een beheerder de groep verlaat.

9. Bekijk de gegevens met betrekking tot de bestelling, het contact, de meldingen en de privacyvoorwaarden in **Samenvatting**. Vink het selectievakje aan waarmee u akkoord gaat met de privacyvoorwaarden.

10. Selecteer **Bestellen**. Het duurt een paar minuten voordat de bestelling is gemaakt.


## <a name="track-the-order"></a>De bestelling volgen

Nadat u uw bestelling hebt geplaatst, kunt u de status van de bestelling volgen via de Azure-portal. Ga naar uw Data Box Heavy-bestelling en vervolgens naar **Overzicht** om de status te bekijken. In de portal ziet u dat de bestelling de status **Besteld** heeft.

Als het apparaat niet beschikbaar is, ontvangt u een melding. Als het apparaat wel beschikbaar is, identificeert Microsoft het apparaat dat moet worden verzonden en bereidt Microsoft de verzending voor. Tijdens de voorbereiding van het apparaat vinden de volgende acties plaats:

- Voor elk opslagaccount dat aan het apparaat is gekoppeld, worden SMB-shares gemaakt.
- Voor elke share worden toegangsreferenties zoals een gebruikersnaam en wachtwoord gegenereerd.
- Er wordt ook een apparaatwachtwoord gegenereerd om het apparaat mee te ontgrendelen.
- De Data Box Heavy wordt vergrendeld om onbevoegde toegang tot het apparaat te voorkomen.

Wanneer de apparaatvoorbereiding is voltooid, wordt de bestelling in de portal weergegeven met de status **Verwerkt**.

![Data Box Heavy-bestelling verwerkt](media/data-box-overview/data-box-order-status-processed.png)

Microsoft bereidt de verzending vervolgens voor en verzendt het apparaat met een regionale vervoerder. U ontvangt uw volgnummer zodra het apparaat is verzonden. In de portal wordt bestelling weergegeven met de status **Verzonden**.

![Data Box Heavy-bestelling verzonden](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>De bestelling annuleren

Als u deze bestelling wilt annuleren, gaat u in de Azure-portal naar **Overzicht** en klikt u in de opdrachtbalk op **Annuleren**.

Nadat u een bestelling hebt geplaatst, kunt u deze op elk moment annuleren voordat de status op Verwerkt is ingesteld.
 
Als u een geannuleerde bestelling wilt verwijderen, gaat u naar **Overzicht** en klikt u in de opdrachtbalk op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn verschillende onderwerpen besproken over de Azure Data Box Heavy, zoals:

> [!div class="checklist"]
> * Vereisten
> * Data Box Heavy bestellen
> * De bestelling volgen
> * De bestelling annuleren

Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box Heavy instelt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box Heavy instellen](./data-box-heavy-deploy-set-up.md)

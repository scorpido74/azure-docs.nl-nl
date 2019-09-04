---
title: Quick start voor Microsoft Azure Data Box Heavy | Microsoft Docs
description: Meer informatie over hoe u uw Azure Data Box Heavy snel kunt implementeren in Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: b2ebb4c5a0df360cede084a90ebe5da041a08aa0
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241381"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Quickstart: Azure Data Box Heavy implementeren met behulp van de Azure Portal

In deze Quick Start wordt beschreven hoe u de Azure Data Box Heavy implementeert met behulp van de Azure Portal. De stappen bevatten informatie over het bekabelen, configureren en kopiëren van gegevens naar Data Box Heavy zodat deze naar Azure worden geüpload. De quickstart wordt uitgevoerd in de Azure-portal en op de lokale webgebruikersinterface van het apparaat.

Ga voor gedetailleerde stapsgewijze instructies voor implementatie en tracering naar [Zelfstudie: Azure Data Box Heavy volgorde](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Vereisten

Voltooi de volgende configuratie vereisten voor de installatie site, Data Box-Service en apparaat voordat u het apparaat implementeert.

### <a name="for-installation-site"></a>Voor installatie site

Zorg voordat u begint voor het volgende:

- Het apparaat kan worden aangepast door al uw entryways. De afmetingen van het apparaat zijn: breedte: 26 "lengte: 48 hoogte: 28 '.
- U hebt toegang tot het apparaat via een lift of een helling als u van plan bent om te installeren op een andere vloer dan de grond vloer.
- U hebt twee personen die het apparaat kunnen afhandelen. Het apparaat weegt ongeveer ~ 500 lbs. en wordt geleverd op wielen.
- U hebt een platte site in het Data Center met nabij een beschik bare netwerk verbinding die een apparaat met deze footprint kan ondersteunen.

### <a name="for-service"></a>Voor de service

Zorg voordat u begint voor het volgende:

- U hebt een Microsoft Azure Storage-account met toegangsreferenties.
- Het abonnement dat u gebruikt voor Data Box Service is [micro soft Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)of [Microsoft Azure sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
- U hebt eigenaar of Inzender toegang tot het abonnement om een Data Box Heavy order te maken.

### <a name="for-device"></a>Voor het apparaat

Zorg voordat u begint voor het volgende:

- U hebt de [veiligheids richtlijnen voor uw data Box Heavy](data-box-safety.md)gecontroleerd.
- U hebt een hostcomputer verbonden met het datacenter netwerk. Data Box Heavy worden de gegevens van deze computer gekopieerd. Op de hostcomputer moet een [ondersteund besturings systeem](data-box-heavy-system-requirements.md)worden uitgevoerd.
- U hebt een laptop met RJ-45-kabel om verbinding te maken met de lokale gebruikers interface en het apparaat te configureren. Gebruik de laptop om elk knoop punt van het apparaat één keer te configureren.
- Uw Data Center heeft een netwerk met hoge snelheid en u hebt ten minste 1 10 GbE-verbinding.
- U hebt een 1 40-Gbps-kabel of een 10 Gbps-kabel per apparaatapparaat nodig. Kies de kabels die compatibel zijn met de Mellanox MCX314A-BCCT-netwerk interface:
    - Voor de 40-Gbps-kabel moet het apparaat uiteinde van de kabel QSFP + zijn.
    - Voor de kabel van 10 Gbps hebt u een SFP +-kabel nodig die aan een van de twee uiteinden wordt aangesloten op een 10-G-switch, met een QSFP + tot SFP +-adapter (of de QSA-adapter) voor het einde dat op het apparaat wordt aangesloten.
- De stroom kabels zijn opgenomen in een lade aan de achterkant van het apparaat.


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Bestellen

Deze stap neemt ongeveer 5 minuten in beslag.

1. Maak een nieuwe Azure Data Box-resource in de Azure-portal.
2. Selecteer een bestaand abonnement dat is ingeschakeld voor deze service, en kies **Importeren** als overdrachtstype. Geef het **Bronland** op waar de gegevens zijn opgeslagen, en geef de **Azure-doelregio** voor de gegevensoverdracht op.
3. Selecteer **Data Box Heavy**. De Maxi maal bruikbare capaciteit is 770 TB en u kunt meerdere orders maken voor grotere gegevens grootten.
4. Voer de order- en verzendgegevens in. Als de service beschikbaar is in uw regio, geeft u e-mailadressen voor meldingen op, controleert u de samenvatting en maakt u vervolgens de order.

Zodra de order is gemaakt, wordt het apparaat voorbereid voor verzending.

::: zone-end

::: zone target = "chromeless"

# <a name="cable-and-connect-to-your-device"></a>Kabel en verbinding maken met uw apparaat

Nadat u de vereisten hebt gecontroleerd, kunt u uw apparaat aansluiten en er verbinding mee maken.

::: zone-end

## <a name="cable-for-power"></a>Kabel voor energie

Deze stap duurt ongeveer vijf minuten.

Wanneer u de Data Box Heavy ontvangt, voert u de volgende stappen uit om het apparaat op de stroom te aansluiten en het apparaat in te scha kelen.

1. Ga niet verder als u vermoedt dat er met het apparaat is geknoeid of dat het is beschadigd. Neem contact op met Microsoft Ondersteuning om een vervangend apparaat naar u te laten verzenden.
2. Verplaats het apparaat naar de installatie site en vergrendel de achterwielpen.
3. Sluit alle vier de stroom kabels aan op de achterkant van het apparaat.
4. Gebruik de aan/uit-knoppen in het voorste vlak om de knoop punten van het apparaat in te scha kelen.

## <a name="cable-first-node-for-network"></a>Kabel eerste knoop punt voor netwerk

Het duurt ongeveer 10-15 minuten om deze stap te volt ooien.

1. Gebruik de RJ-45-netwerkkabel van het type CAT-6 om de hostcomputer te koppelen aan de MGMT-poort (Managementpoort) op het apparaat.
2. Gebruik de kabel van Twinax QSFP + koper om ten minste 1 40 Gbps te verbinden met een netwerk interface met een voor keur van meer dan 1 Gbps, gegevens 1 of gegevens 2 of DATA. Als u een 10-Gbps-switch gebruikt, gebruikt u een Twinax SFP + koper kabel met een QSFP +-naar-SFP +-adapter (de QSA-adapter) om de 40-Gbps-netwerk interface voor gegevens te verbinden.
3. Sluit de kabels aan zoals hieronder wordt weergegeven.  

    ![Data Box Heavy gekabeld](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Eerste knoop punt configureren

Deze stap neemt ongeveer 5 tot 7 minuten in beslag.

1. Als u het wachtwoord van het apparaat wilt ophalen, gaat u naar **Algemeen > Apparaatdetails** in de [Azure-portal](https://portal.azure.com). Voor beide knoop punten van het apparaat wordt hetzelfde wacht woord gebruikt.
2. Wijs een statisch IP-adres van 192.168.100.5 en subnet 255.255.255.0 toe aan de Ethernet-adapter op de computer die u gebruikt om verbinding te maken met Data Box Heavy. U hebt op `https://192.168.100.10` toegang tot de webgebruikersinterface van het apparaat. Nadat u het apparaat hebt ingeschakeld, duurt het maximaal 5 minuten om de verbinding tot stand te brengen.
3. Meld u aan met het wachtwoord uit de Azure-portal. U ziet nu een foutmelding over een probleem met het beveiligingscertificaat van de website. Volg de browserinstructies om naar de webpagina te gaan.
4. De netwerk instellingen voor de interfaces (met uitzonde ring van het beheer) zijn standaard geconfigureerd als DHCP. Als dat nodig is, kunt u deze interfaces configureren als statisch en een IP-adres opgeven.

## <a name="cable-and-configure-the-second-node"></a>Kabel en configureer het tweede knoop punt

Het duurt ongeveer 15-20 minuten om deze stap te volt ooien.

Volg de stappen die voor het eerste knoop punt worden gebruikt voor het bekabelen en configureren van het tweede knoop punt op het apparaat.  


::: zone target = "docs"

## <a name="copy-data"></a>Gegevens kopiëren

De tijd voor het volt ooien van deze bewerking is afhankelijk van de grootte van uw gegevens en de snelheid van het netwerk waarover de gegevens worden gekopieerd.
 
1. Gegevens kopiëren naar de knoop punten van de apparaten met behulp van de gegevens interfaces van 40-Gbps parallel.

    - Als u een Windows-host gebruikt, gebruikt u een met SMB compatibel hulp programma voor het kopiëren van bestanden zoals [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - Voor een NFS-host gebruikt u de opdracht `cp` of `rsync` om de gegevens te kopiëren.
2. Maak verbinding met de shares op het apparaat met behulp van het pad:`\\<IP address of your device>\ShareName`. Als u de toegangs referenties voor de share wilt ophalen, gaat u naar de pagina **verbinding maken & kopiëren** in de lokale webgebruikersinterface van de data Box Heavy.
3. Zorg ervoor dat de namen van shares en mappen en de gegevens voldoen aan de richt lijnen die worden beschreven in de [Azure Storage-en data Box Heavy-service limieten](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Voorbereiding voor verzending

De duur van deze bewerking hangt af van de hoeveelheid gegevens.

1. Nadat het kopiëren van de gegevens zonder fouten is voltooid, gaat u naar **voorbereiding voor verzending** pagina in de lokale webgebruikersinterface en start u de voor bereiding voor de verzen ding.
2. Nadat de **voorbereiding voor verzending** op beide knoop punten is voltooid, schakelt u het apparaat uit via de lokale webgebruikersinterface.

## <a name="ship-to-azure"></a>Verzenden naar Azure

Het volt ooien van deze bewerking duurt ongeveer 15-20 minuten.

1. Verwijder de kabels en stuur deze terug naar de lade op de achterkant van het apparaat.
2. Een ophaling plannen met uw regionale luchtvaart maatschappij.
3. Neem contact op met [Data Box bewerkingen](mailto:DataBoxOps@microsoft.com) om te informeren over de ophaling en om het verzend label voor de retour zending op te halen.
4. Het label voor de retour verzending moet zichtbaar zijn in het paneel front-leeg van het apparaat.

## <a name="verify-data"></a>Gegevens controleren

De duur van deze bewerking hangt af van de hoeveelheid gegevens.

1. Wanneer het Data Box Heavy apparaat is verbonden met het Azure Data Center-netwerk, worden de gegevens automatisch geüpload naar Azure.
2. Data Box-Service meldt u dat het kopiëren van de gegevens is voltooid via de Azure Portal.

    1. Controleer foutenlogboeken op eventuele fouten en onderneem toepasselijke acties.
    2. Controleer of uw gegevens zich in de opslagaccount(s) bevinden voordat u deze uit de bron verwijdert.

## <a name="clean-up-resources"></a>Resources opschonen

Deze stap neemt 2 tot 3 minuten in beslag.

- U kunt de Data Box Heavy volgorde annuleren in de Azure Portal voordat de order wordt verwerkt. Zodra de order is verwerkt, kan deze niet meer worden geannuleerd. De order doorloopt verwerkingsfasen totdat deze is voltooid. Als u de order wilt annuleren, gaat u naar **Overzicht** en klikt u in de opdrachtbalk op **Annuleren**.

- U kunt de order verwijderen zodra de status als **Voltooid** of **Geannuleerd** wordt weergegeven in de Azure-portal. Als u de order wilt verwijderen, gaat u naar **Overzicht** en klikt u in de opdrachtbalk op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een Data Box Heavy geïmplementeerd om uw gegevens te importeren in Azure. Ga verder met de volgende zelf studie voor meer informatie over Azure Data Box Heavy beheer:

> [!div class="nextstepaction"]
> [De Azure Portal gebruiken om Data Box Heavy te beheren](data-box-portal-admin.md)

::: zone-end

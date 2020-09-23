---
title: 'Quickstart: Gegevens exporteren voor Microsoft Azure Data Box'
description: Leer hoe u uw Azure Data Box-gegevens snel kunt exporteren via Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 07/17/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 01fdaa95dd396e5545b65bada2a9d6410169230b
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052457"
---
# <a name="quickstart-get-started-with-azure-data-box-to-export-data-from-azure"></a>Quickstart: Aan de slag met Azure Data Box om gegevens te exporteren vanuit Azure

In deze quickstart wordt beschreven hoe u gegevens uit Azure exporteert naar uw locatie met behulp van Azure Portal. De stappen bevatten informatie over het bekabelen, configureren en kopiëren van gegevens vanuit Azure. De quickstart wordt uitgevoerd in de Azure-portal en op de lokale webgebruikersinterface van het apparaat.

Ga voor gedetailleerde stapsgewijze instructies voor implementatie en tracering naar [Zelfstudie: Een exportopdracht voor Azure Data Box maken](data-box-deploy-export-ordered.md)

## <a name="prerequisites"></a>Vereisten

Voordat u begint:

* Zorg ervoor dat het abonnement dat u voor de Data Box-service gebruikt, een van de volgende typen is:
  * Microsoft Enterprise Agreement (EA). Meer informatie over [EA-abonnementen](https://azure.microsoft.com/pricing/enterprise-agreement/).
  * Cloud Solution Provider (CSP). Meer informatie over het [Azure CSP-programma](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
  * Microsoft Azure Sponsorship. Meer informatie over het [Azure Sponsorship-programma](https://azure.microsoft.com/offers/ms-azr-0036p/).

* U hebt eigenaars- of inzenderstoegang tot het abonnement nodig om een Data Box-order te kunnen maken.
* Controleer de [veiligheidsrichtlijnen voor uw Data Box](data-box-safety.md).
* U hebt een Azure Data Box-apparaat om gegevens naar uw locatie te verplaatsen. Op uw hostcomputer moet
  * Een [ondersteund besturingssysteem](data-box-system-requirements.md) worden uitgevoerd.
  * Een verbinding bestaan met een netwerk met hoge snelheid. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10-GbE-verbinding beschikbaar is, kan een 1-GbE-gegevensverbinding worden gebruikt. Dit heeft echter wel invloed op de kopieersnelheid.
* U moet de beschikking hebben over een plat oppervlak waarop u de Data Box kunt plaatsen. Als u het apparaat op een plank van een standaardrek wilt plaatsen, moet het datacenterrek beschikken over een 7U-sleuf. U kunt het apparaat plat of rechtop in het rek plaatsen.
* U hebt de volgende kabels aangeschaft om de Data Box aan te sluiten op de hostcomputer.
  * Twee koperen 10-GbE-kabels van het type SFP+ Twinax (gebruiken met de netwerkinterfaces DATA 1 en DATA 2)
  * Eén RJ-45-netwerkkabel van het type CAT 6 (gebruiken met de management (MGMT)-netwerkinterface)
  * Eén RJ-45-netwerkkabel van het type CAT 6A OF CAT 6 (gebruiken met DATA 3-netwerkinterface die is geconfigureerd als respectievelijk 10 Gbps of 1 Gbps)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Bestellen

Deze stap duurt ongeveer tien minuten.

1. Maak een nieuwe Azure Data Box-resource in de Azure-portal.
2. Selecteer een bestaand abonnement dat is ingeschakeld voor deze service en kies **Exporteren naar Azure** als overdrachtstype. Geef bij **Bronregio van Azure** op waar de gegevens zijn opgeslagen en geef bij **Doelland** het land voor de gegevensoverdracht op.
3. Selecteer **Data Box**. De maximale bruikbare capaciteit is 80 TB, en voor grotere hoeveelheden gegevens kunt u meerdere orders maken.
4. Selecteer **Opslagaccount en exporttype toevoegen** en vervolgens **Exportoptie selecteren**.
5. Voer de order- en verzendgegevens in. Als de service beschikbaar is in uw regio, geeft u e-mailadressen voor meldingen op, controleert u de samenvatting en maakt u vervolgens de order.

Zodra de order is gemaakt, wordt het apparaat voorbereid voor verzending.

## <a name="cable"></a>Kabels aansluiten

Deze stap duurt ongeveer tien minuten.

Als u de Data Box hebt ontvangen, voert u de volgende stappen uit om de kabels aan te sluiten, en het apparaat te verbinden en in te schakelen. Deze stap duurt ongeveer tien minuten.

1. Ga niet verder als u vermoedt dat er met het apparaat is geknoeid of dat het beschadigd is. Neem contact op met Microsoft Ondersteuning om een vervangend apparaat naar u te laten verzenden.
2. Controleer of u de volgende kabels hebt, voordat u het apparaat gaat aansluiten:

   * Geaard netsnoer (meegeleverd) met een vermogen van 10 A of hoger met een IEC60320 C-13-connector aan één eind om het apparaat te koppelen.
   * Eén RJ-45-netwerkkabel van het type CAT 6 (gebruiken met de management (MGMT)-netwerkinterface)
   * Twee koperen 10 GbE-kabels van het type SFP+ Twinax (gebruiken met 10 Gbps DATA 1-, DATA 2-netwerkinterfaces)
   * Eén RJ-45-netwerkkabel van het type CAT 6A OF CAT 6 (gebruiken met DATA 3-netwerkinterface die is geconfigureerd als respectievelijk 10 Gbps of 1 Gbps)

3. Haal het apparaat uit de doos en plaats het op een plat oppervlak.

4. Sluit de kabels aan zoals hieronder wordt weergegeven.  

    ![Bekabelde achterzijde van Data Box-apparaat](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Sluit de voedingskabel aan op het apparaat.
    2. Gebruik de RJ-45-netwerkkabel van het type CAT-6 om de hostcomputer te koppelen aan de MGMT-poort op het apparaat.
    3. Gebruik de koperen kabel van het type SFP+ Twinax om verbinding te maken met minstens één 10 Gbps-netwerkinterface (liever dan 1 Gbps), DATA 1 of DATA 2 voor gegevens.
    4. Schakel het apparaat in. De Aan/uit-knop bevindt zich op het voorpaneel van het apparaat.

## <a name="connect"></a>Verbinding maken

Deze stap neemt ongeveer 5 tot 7 minuten in beslag.

1. Als u het wachtwoord van het apparaat wilt ophalen, gaat u in [Azure Portal](https://portal.azure.com) naar **Algemeen** > **Apparaatdetails**.
2. Wijs een vast IP-adres 192.168.100.5 en subnet 255.255.255.0 toe aan de Ethernet-adapter op de computer die u gebruikt om verbinding te maken met de Data Box. U hebt op `https://192.168.100.10` toegang tot de webgebruikersinterface van het apparaat. Nadat u het apparaat hebt ingeschakeld, duurt het maximaal 5 minuten om de verbinding tot stand te brengen.
3. Meld u aan met het wachtwoord uit de Azure-portal. U ziet nu een foutmelding over een probleem met het beveiligingscertificaat van de website. Volg de browserinstructies om naar de webpagina te gaan.
4. De netwerkinstellingen voor de 10 Gbps-gegevensinterface (of 1 Gbps) zijn standaard geconfigureerd als DHCP. Indien nodig kunt u deze interface configureren als statisch en een IP-adres opgeven.

## <a name="copy-data"></a>Gegevens kopiëren

De duur van deze bewerking hangt af van de hoeveelheid gegevens en de snelheid van het netwerk.

1. Als u een Windows-client gebruikt, gebruikt u een SMB-compatibel hulpprogramma voor het kopiëren van bestanden, zoals Robocopy. Voor een NFS-host gebruikt u de opdracht `cp` of `rsync` om de gegevens te kopiëren. Ga naar [Robocopy](https://technet.microsoft.com/library/ee851678.aspx) voor meer informatie over het gebruik van Robocopy om gegevens te kopiëren.
2. Maak verbinding met de apparaatshares en begin met het kopiëren van gegevens naar de hostcomputer.
<!-- 1. Connect to the device shares using the path:`\\<IP address of your device>\ShareName`. To get the share access credentials, go to the **Connect & copy** page in the local web UI of the Data Box. --> 

## <a name="ship-to-azure"></a>Verzenden naar Azure

Het voltooien van deze bewerking duurt ongeveer 10 tot 15 minuten.

1. Ga naar de pagina **Voorbereiden voor verzending** in de lokale webgebruikersinterface en start de voorbereiding voor verzending.
2. Schakel het apparaat uit in de lokale webgebruikersinterface. Verwijder de kabels uit het apparaat.
3. Rol het netsnoer op en plaats het veilig aan de achterzijde van het apparaat.
4. Het retourlabel moet zichtbaar zijn in de E-ink-weergave. Als het label niet wordt weergegeven in de E-ink-weergave, gaat u in Azure Portal naar **Overzicht** > **Verzendlabel downloaden**. Download het verzendlabel en bevestig dat op het apparaat.
5. Maak een afspraak met een transportbedrijf voor het ophalen van de zending als u het apparaat terugstuurt.
6. Nadat de Data Box door de vervoerder is opgehaald en gescand, verandert de orderstatus in de portal in **Opgehaald**. Er wordt ook een tracerings-id weergegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Deze stap neemt 2 tot 3 minuten in beslag.

* U kunt de Data Box-order in de Azure-portal annuleren voordat de order wordt verwerkt. Zodra de order is verwerkt, kan deze niet meer worden geannuleerd. De order doorloopt verwerkingsfasen totdat deze is voltooid. Als u de order wilt annuleren, gaat u naar **Overzicht** en selecteert u in de opdrachtbalk de optie **Annuleren**.

* U kunt de order verwijderen zodra de status als **Voltooid** of **Geannuleerd** wordt weergegeven in de Azure-portal. Als u de order wilt verwijderen, gaat u naar **Overzicht** en selecteert u in de opdrachtbalk de optie **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een exportopdracht van Azure naar een Azure Data Box geïmplementeerd. Ga verder naar de volgende zelfstudie voor meer informatie over Azure Data Box-beheer:

> [!div class="nextstepaction"]
> [De Azure-portal gebruiken om Data Box te beheren](data-box-portal-admin.md)

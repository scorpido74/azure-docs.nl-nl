---
title: Zelfstudie voor het instellen van Azure Data Box | Microsoft Docs
description: In deze zelfstudie leert u hoe u de Azure Data Box kunt bekabelen, aansluiten en inschakelen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 53309023a838f4b256327a37866408f556c81d29
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924153"
---
::: zone target="docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Zelfstudie: De Azure Data Box bekabelen en aansluiten

::: zone-end

::: zone target="chromeless"

## <a name="cable-and-connect-to-your-device"></a>Apparaat aansluiten en verbinding maken

::: zone-end

::: zone target="docs"

In deze zelfstudie wordt beschreven hoe u de Azure Data Box bekabelt, aansluit en inschakelt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De Data Box bekabelen
> * De Data Box aansluiten

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de bestelling voor Azure Data Box geplaatst.
    - Bekijk de [Zelfstudie voor een importorder: Azure Data Box bestellen](data-box-deploy-ordered.md) voltooid.
    - Bekijk de [Zelfstudie voor een exportorder: Azure Data Box bestellen](data-box-deploy-export-ordered.md)
1. U hebt uw Data Box ontvangen en de bestelstatus in de portal is bijgewerkt naar **Geleverd**. 
    - Er zit een verzendlabel in het doorzichtige hoesje op het apparaat, onder het stroometiket. Bewaar dit label zorgvuldig aangezien u het nodig hebt voor retourverzending.
    - In sommige regio's in Europa wordt het apparaat in een doos geleverd. Zorg bij het uitpakken van het apparaat dat u de doos bewaart voor retourzending.
1. U hebt de [Data Box-veiligheidsrichtlijnen](data-box-safety.md) bekeken.
1. U hebt één geaard netsnoer ontvangen voor gebruik met uw opslagapparaat van 100 TB.
1. U hebt een hostcomputer die wordt gebruikt om gegevens te kopiëren naar (importorder) of gegevens te kopiëren vanuit (exportorder) uw Data Box. Op uw hostcomputer moet
    - Een [ondersteund besturingssysteem](data-box-system-requirements.md) worden uitgevoerd.
    - Een verbinding bestaan met een netwerk met hoge snelheid. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10-GbE-verbinding beschikbaar is, kan een 1-GbE-gegevensverbinding worden gebruikt. Dit heeft echter wel invloed op de kopieersnelheid. 
1. U moet de beschikking hebben over een plat oppervlak waarop u de Data Box kunt plaatsen. Als u het apparaat op een plank van een standaardrek wilt plaatsen, moet het datacenterrek beschikken over een 7U-sleuf. U kunt het apparaat plat of rechtop in het rek plaatsen.
1. U hebt de volgende kabels aangeschaft om de Data Box aan te sluiten op de hostcomputer.
    - Een of meer koperen 10-GbE-kabels van het type SFP+ Twinax of glasvezelkabels van het type SFP+ (te gebruiken met de netwerkinterfaces DATA 1 en DATA 2). Data Box heeft de Mellanox ConnectX®-3 Pro EN Dual Port 10GBASE-T Adapters met PCI Express 3.0 netwerkinterface. Kabels die compatibel zijn met deze interface moeten dus werken. Voor interne testdoeleinden is bijvoorbeeld een CISCO SFP-H10GB-CU3M 10GBASE-CU TWINAX SFP +3M-kabel gebruikt. Zie de [lijst met ondersteunde kabels en switches van Mellanox](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf) voor meer informatie.
    - Eén RJ-45-netwerkkabel van het type CAT 6 (gebruiken met de MGMT-netwerkinterface)
    - Eén RJ-45-netwerkkabel van het type CAT 6A OF CAT 6 (gebruiken met DATA 3-netwerkinterface die is geconfigureerd als respectievelijk 10 Gbps of 1 Gbps)

## <a name="cable-your-device"></a>Uw apparaat bekabelen

Voer de volgende stappen uit om uw apparaat te bekabelen.

1. Controleer of er met het apparaat is geknoeid en of het is beschadigd. Als er met het apparaat is geknoeid of het ernstig is beschadigd, gaat u niet verder. Neem direct contact op met Microsoft Ondersteuning om te bepalen of het apparaat in een goede staat is of dat er een vervangend apparaat moet worden verzonden.
2. Verplaats het apparaat naar de locatie waar u het wilt inschakelen. Plaats het apparaat op een plat oppervlak. Het apparaat kan ook op een plank van een standaardrek worden geplaatst.
3. Sluit het netsnoer en de netwerkkabels aan. Hieronder ziet u de achterzijde van een aangesloten apparaat in een algemene configuratie. Afhankelijk van uw omgeving kunt u kiezen uit andere [bekabelingsopties](data-box-cable-options.md).
    
    ![Bekabelde achterzijde van Data Box-apparaat](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Sluit de voedingskabel aan op de gelabelde stroomingang. Het andere uiteinde van de voedingskabel moet zijn verbonden met een stroomverdeler.
    2. Gebruik de RJ-45 CAT 6-kabel om de MGMT-poort aan het ene uiteinde en een laptop aan het andere uiteinde aan te sluiten.            
    3. Sluit het ene uiteinde van de RJ-45 CAT 6A-kabel aan op de DATA 3-poort. DATA 3 is geconfigureerd als 10 GbE als u de RJ-45 CAT 6A-kabel gebruikt en als 1 GbE als u de RJ-45 CAT 6-kabel gebruikt.
    4. Afhankelijk van de netwerkinterfaces die u voor de gegevensoverdracht wilt verbinden, moet u maximaal twee koperen 10-GbE-kabels van het type SFP+ Twinax of glasvezelkabels van het type SFP+ gebruiken om respectievelijk verbinding te maken met de DATA 1 en DATA 2-poorten. 
    5. De andere uiteinden van de kabels van de gegevenspoorten worden op de hostcomputer aangesloten via een 10-GbE-switch.

4. Zoek de aan/uit-knop op het bedieningspaneel aan de voorzijde van het apparaat. Schakel het apparaat in.

    ![Aan-/uit-knop Data Box](media/data-box-deploy-set-up/data-box-powered-door-open.png)

::: zone-end

::: zone target="chromeless"

Nadat u het apparaat hebt ontvangen, moet u het apparaat aansluiten en er verbinding mee maken. 

## <a name="cable-your-device"></a>Uw apparaat bekabelen

1. Ga niet verder als u vermoedt dat er met het apparaat is geknoeid of dat het is beschadigd. Neem contact op met Microsoft Ondersteuning om een vervangend apparaat naar u te laten verzenden.
2. Controleer of u de volgende kabels hebt, voordat u het apparaat gaat aansluiten:
    
    - (Meegeleverd) geaard netsnoer met een vermogen van 10 A of hoger met een IEC60320 C-13-connector aan één eind om het apparaat te koppelen.
    - Eén RJ-45-netwerkkabel van het type CAT 6 (gebruiken met de MGMT-netwerkinterface)
    - Twee koperen 10 GbE-kabels van het type SFP+ Twinax (gebruiken met 10 Gbps DATA 1-, DATA 2-netwerkinterfaces)
    - Eén RJ-45-netwerkkabel van het type CAT 6A OF CAT 6 (gebruiken met DATA 3-netwerkinterface die is geconfigureerd als respectievelijk 10 Gbps of 1 Gbps)

3. Haal het apparaat uit de doos en plaats het op een plat oppervlak. 
    
4. Sluit de kabels aan zoals hieronder wordt weergegeven.  

    ![Bekabelde achterzijde van Data Box-apparaat](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Sluit de voedingskabel aan op het apparaat.
    2. Gebruik de RJ-45-netwerkkabel van het type CAT-6 om de hostcomputer te koppelen aan de MGMT-poort (Managementpoort) op het apparaat. 
    3. Gebruik de koperen kabel van het type SFP+ Twinax om verbinding te maken met minstens één 10 Gbps-netwerkinterface (liever dan 1 Gbps), DATA 1 of DATA 2 voor gegevens. 
    4. Schakel het apparaat in. De Aan/uit-knop bevindt zich op het voorpaneel van het apparaat.

::: zone-end

::: zone target="docs"


## <a name="connect-to-your-device"></a>Verbinding maken met uw apparaat

Voer de volgende stappen uit om uw apparaat in te stellen met behulp van de lokale webgebruikersinterface en de gebruikersinterface van de portal.

1. Configureer de Ethernet-adapter op de laptop waarmee u verbinding maakt met het apparaat met het statische IP-adres 192.168.100.5 en het subnet 255.255.255.0. 
2. Maak verbinding met de MGMT-poort van uw apparaat en open de lokale webgebruikersinterface van het apparaat op \://192.168.100.10. Dit kan tot 5 minuten duren nadat u het apparaat hebt ingeschakeld.
3. Klik op **Details** en klik vervolgens op **Ga verder naar de webpagina**.

   ![Verbinding maken met de lokale webgebruikersinterface](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. U ziet een pagina **Aanmelden** voor de lokale webgebruikersinterface. Zorg ervoor dat het serienummer van het apparaat overeenkomt in zowel de gebruikersinterface van de portal als de lokale webgebruikersinterface. Het apparaat is op dit moment vergrendeld.
5. Meld u aan bij de [Azure Portal](https://portal.azure.com).
6. Download de referenties van het apparaat vanuit de portal. Ga naar **Algemeen > Apparaatdetails**. Kopieer het **Apparaatwachtwoord**. Het wachtwoord van het apparaat is aan een specifieke volgorde gekoppeld in de portal. 

    ![Apparaatreferenties ophalen](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. Geef het apparaatwachtwoord op dat u in de vorige stap hebt verkregen via Azure Portal om u aan te melden bij de lokale webgebruikersinterface van het apparaat. Klik op **Aanmelden**.
8. Zorg er op het **Dashboard** voor dat de netwerkinterfaces zijn geconfigureerd. 
   - Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd. 
   - Als DHCP niet is ingeschakeld, gaat u naar **Netwerkinterfaces instellen** en wijst u indien nodig statische IP-adressen toe.

     ![Apparaatdashboard](media/data-box-deploy-set-up/data-box-dashboard-1.png)

Zodra de gegevensnetwerkinterfaces zijn geconfigureerd, kunt u ook het IP-adres van de DATA 1- tot DATA 3-interfaces gebruiken voor toegang tot de lokale webgebruikersinterface (via `https://<IP address of a data network interface>`). 

Zodra de installatie van het apparaat is voltooid, kunt u verbinding maken met de apparaatshares en de gegevens kopiëren. 

::: zone-end

::: zone target="chromeless"

## <a name="connect-your-device"></a>Uw apparaat verbinden

1. Als u het wachtwoord van het apparaat wilt ophalen, gaat u naar **Algemeen > Apparaatdetails** in de [Azure-portal](https://portal.azure.com).
2. Wijs een statisch IP-adres 192.168.100.5 en subnet 255.255.255.0 toe aan de Ethernet-adapter op de computer die u gebruikt om verbinding te maken met Data Box. U hebt op `https://192.168.100.10` toegang tot de webgebruikersinterface van het apparaat. Nadat u het apparaat hebt ingeschakeld, duurt het maximaal 5 minuten om de verbinding tot stand te brengen. 
3. Meld u aan met het wachtwoord uit de Azure-portal. U ziet nu een foutmelding over een probleem met het beveiligingscertificaat van de website. Volg de browserinstructies om naar de webpagina te gaan.
4. De netwerkinstellingen voor de 10 Gbps-gegevensinterface (of 1 Gbps) zijn standaard geconfigureerd als DHCP. Indien nodig kunt u deze interface configureren als statisch en een IP-adres opgeven. 

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
> * De Data Box bekabelen
> * De Data Box aansluiten

Ga naar de volgende zelfstudie om te lezen hoe u gegevens kopieert.

> [!div class="nextstepaction"]
> [Uw gegevens naar Azure Data Box kopiëren voor importorder](./data-box-deploy-copy-data.md)

of

> [!div class="nextstepaction"]
> [Uw gegevens kopiëren van Azure Data Box voor exportorder](./data-box-deploy-export-copy-data.md)

::: zone-end


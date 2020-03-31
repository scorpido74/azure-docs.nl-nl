---
title: Systeemvereisten voor Microsoft Azure Data Box Edge| Microsoft Documenten
description: Meer informatie over de software- en netwerkvereisten voor uw Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260226"
---
# <a name="azure-data-box-edge-system-requirements"></a>Systeemvereisten voor Azure Data Box Edge

In dit artikel worden de belangrijke systeemvereisten beschreven voor uw Microsoft Azure Data Box Edge-oplossing en voor de clients die verbinding maken met Azure Data Box Edge. We raden u aan de informatie zorgvuldig te bekijken voordat u uw Data Box Edge implementeert. U deze informatie zo nodig terugverwijzen tijdens de implementatie en de daaropvolgende bewerking.

De systeemvereisten voor de Data Box Edge zijn:

- **Softwarevereisten voor hosts** - beschrijft de ondersteunde platforms, browsers voor de lokale configuratie-gebruikersinterface, SMB-clients en eventuele aanvullende vereisten voor de clients die toegang hebben tot het apparaat.
- **Netwerkvereisten voor het apparaat** - biedt informatie over eventuele netwerkvereisten voor de werking van het fysieke apparaat.

## <a name="supported-os-for-clients-connected-to-device"></a>Ondersteund besturingssysteem voor clients die zijn verbonden met het apparaat

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Ondersteunde protocollen voor clients die toegang hebben tot het apparaat

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Ondersteunde opslagtypen

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Ondersteunde browsers voor lokale webgebruikersinterface

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Vereisten voor netwerkpoorten

### <a name="port-requirements-for-data-box-edge"></a>Poortvereisten voor Gegevensvakrand

In de volgende tabel worden de poorten weergegeven die in uw firewall moeten worden geopend om smb-, cloud- of beheerverkeer mogelijk te maken. In deze tabel verwijst *in* of *inbound* naar de richting van waaruit binnenkomende client toegang tot uw apparaat vraagt. *Out-* of *outbound* verwijst naar de richting waarin uw Data Box Edge-apparaat gegevens extern verzendt, buiten de implementatie, bijvoorbeeld uitgaand naar het internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Poortvereisten voor IoT Edge

Azure IoT Edge maakt uitgaande communicatie mogelijk van een on-premises Edge-apparaat naar azure-cloud met behulp van ondersteunde IoT Hub-protocollen. Binnenkomende communicatie is alleen vereist voor specifieke scenario's waarbij Azure IoT Hub berichten naar het Azure IoT Edge-apparaat moet pushen (bijvoorbeeld cloud-naar-apparaatberichten).

Gebruik de volgende tabel voor poortconfiguratie voor de servers die de runtime van Azure IoT Edge hosten:

| Poort nr. | In of uit | Poortbereik | Vereist | Richtlijnen |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Uit       | WAN        | Ja      | Uitgaand open voor IoT Edge provisioning. Deze configuratie is vereist bij het gebruik van handmatige scripts of Azure IoT Device Provisioning Service (DPS).|

Ga voor volledige informatie naar [Firewall- en poortconfiguratieregels voor iot edge-implementatie.](https://docs.microsoft.com/azure/iot-edge/troubleshoot)

## <a name="url-patterns-for-firewall-rules"></a>URL-patronen voor firewallregels

Netwerkbeheerders kunnen vaak geavanceerde firewallregels configureren op basis van de URL-patronen om het binnenkomende en het uitgaande verkeer te filteren. Uw Data Box Edge-apparaat en de service zijn afhankelijk van andere Microsoft-toepassingen, zoals Azure Service Bus, Azure Active Directory Access Control, opslagaccounts en Microsoft Update-servers. De URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen worden gebruikt om firewallregels te configureren. Het is belangrijk om te begrijpen dat de URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen veranderen. Deze wijzigingen vereisen dat de netwerkbeheerder firewallregels voor uw Data Box Edge controleert en bijwerkt wanneer dat nodig is.

We raden u aan uw firewallregels voor uitgaand verkeer in te stellen op basis van vaste IP-adressen van Data Box Edge, in de meeste gevallen. U de onderstaande informatie echter gebruiken om geavanceerde firewallregels in te stellen die nodig zijn om beveiligde omgevingen te maken.

> [!NOTE]
> - De IP's van het apparaat (bron) moeten altijd worden ingesteld op alle netwerkinterfaces met cloudvoorziening.
> - De ip-bereiken van de bestemming moeten worden ingesteld op [IP-bereiken voor Azure-datacenters.](https://www.microsoft.com/download/confirmation.aspx?id=41653)

### <a name="url-patterns-for-gateway-feature"></a>URL-patronen voor gatewayfunctie

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>URL-patronen voor compute-functie

| URL-patroon                      | Component of functionaliteit                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*cdn.mscr.io | Microsoft-containerregister (vereist)               |
| https://\*.azurecr.io                     | Persoonlijke containerregisters en containers van derden (optioneel) | 
| https://\*.azure-devices.net              | IoT Hub-toegang (vereist)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>URL-patronen voor gateway voor Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>URL-patronen voor compute voor Azure Government

| URL-patroon                      | Component of functionaliteit                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*cdn.mscr.com | Microsoft-containerregister (vereist)               |
| https://\*.azure-devices.us              | IoT Hub-toegang (vereist)           |
| https://\*.azurecr.us                    | Persoonlijke containerregisters en containers van derden (optioneel) | 

## <a name="internet-bandwidth"></a>Internetbandbreedte

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Overwegingen voor het berekenen van grootte

Gebruik uw ervaring tijdens het ontwikkelen en testen van uw oplossing om ervoor te zorgen dat er voldoende capaciteit is op uw Data Box Edge-apparaat en u de optimale prestaties van uw apparaat krijgt.

Factoren die u moet overwegen zijn:

- **Containerdetails** - Denk na over het volgende.

    - Hoeveel containers zitten er in uw werklast? Je zou kunnen hebben veel lichtgewicht containers ten opzichte van een paar resource-intensieve ones.
    - Wat zijn de resources toegewezen aan deze containers ten opzichte van wat zijn de middelen die ze verbruiken?
    - Hoeveel lagen delen uw containers?
    - Zijn er ongebruikte containers? Een gestopte container neemt nog steeds schijfruimte in beslag.
    - In welke taal zijn uw containers geschreven?
- **Grootte van de verwerkte gegevens** - Hoeveel gegevens worden uw containers verwerkt? Verbruiken deze gegevens schijfruimte of worden de gegevens verwerkt in het geheugen?
- **Verwachte prestaties** - Wat zijn de gewenste prestatiekenmerken van uw oplossing? 

Om de prestaties van uw oplossing te begrijpen en te verfijnen, u het:

- De compute metrics beschikbaar in de Azure-portal. Ga naar uw Data Box Edge-bron en ga vervolgens naar **>-statistieken controleren.** Kijk naar de **Edge compute - Geheugengebruik** en **Edge compute - Percentage CPU** om de beschikbare bronnen te begrijpen en hoe worden de resources verbruikt.
- De bewakingsopdrachten die beschikbaar zijn via de PowerShell-interface van het apparaat, zoals:

    - `dkrdbe stats`om een live stream van container(s) resource gebruik statistieken te krijgen. De opdracht ondersteunt CPU-, geheugengebruik-, geheugenlimiet- en netwerk-IO-statistieken.
    - `dkrdbe system df`om informatie te krijgen over de hoeveelheid gebruikte schijfruimte. 
    - `dkrdbe image prune`om ongebruikte afbeeldingen op te ruimen en ruimte vrij te maken.
    - `dkrdbe ps --size`om de geschatte grootte van een lopende container te bekijken. 

    Ga voor meer informatie over de beschikbare opdrachten naar [Monitor en problemen oplossen van rekenmodules.](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules)

Zorg er tot slot voor dat u uw oplossing op uw gegevensset valideert en de prestaties op Data Box Edge kwantificeert voordat u in productie gaat.


## <a name="next-step"></a>Volgende stap

- [Uw Azure Data Box Edge implementeren](data-box-edge-deploy-prep.md)

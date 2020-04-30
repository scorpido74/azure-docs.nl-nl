---
title: Systeem vereisten voor Microsoft Azure Data Box Edge | Microsoft Docs
description: Meer informatie over de software-en netwerk vereisten voor uw Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79260226"
---
# <a name="azure-data-box-edge-system-requirements"></a>Systeem vereisten voor Azure Data Box Edge

In dit artikel worden de belangrijkste systeem vereisten beschreven voor uw Microsoft Azure Data Box Edge-oplossing en voor de clients die verbinding maken met Azure Data Box Edge. We raden u aan de informatie zorgvuldig te bekijken voordat u uw Data Box Edge implementeert. U kunt deze informatie naar behoefte terugsturen tijdens de implementatie en de volgende bewerking.

De systeem vereisten voor de Data Box Edge zijn onder andere:

- **Software vereisten voor hosts** : beschrijft de ondersteunde platforms, browsers voor de lokale configuratie-UI, SMB-clients en eventuele aanvullende vereisten voor de clients die toegang hebben tot het apparaat.
- **Netwerk vereisten voor het apparaat** : bevat informatie over eventuele netwerk vereisten voor de werking van het fysieke apparaat.

## <a name="supported-os-for-clients-connected-to-device"></a>Ondersteund besturings systeem voor clients die zijn verbonden met het apparaat

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Ondersteunde protocollen voor clients die toegang hebben tot het apparaat

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Ondersteunde opslagtypen

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Ondersteunde browsers voor de lokale web-UI

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Vereisten voor netwerk poort

### <a name="port-requirements-for-data-box-edge"></a>Poort vereisten voor Data Box Edge

De volgende tabel geeft een lijst van de poorten die in uw firewall moeten worden geopend om SMB-, Cloud-of beheer verkeer toe te staan. In deze tabel verwijst *naar* of *binnenkomend* naar de richting waarin de inkomende client toegang tot uw apparaat vraagt. *Out* of *uitgaand* verwijst naar de richting waarin uw data Box edge apparaat gegevens extern verzendt, bijvoorbeeld uitgaand naar Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Poort vereisten voor IoT Edge

Azure IoT Edge staat uitgaande communicatie van een on-premises edge-apparaat naar Azure-Cloud toe met ondersteunde IoT Hub protocollen. Inkomende communicatie is alleen vereist voor specifieke scenario's waarbij Azure IoT Hub berichten naar het Azure IoT Edge apparaat moet pushen (bijvoorbeeld Cloud-naar-apparaat-berichten).

Gebruik de volgende tabel voor poort configuratie voor de servers die als host fungeren voor Azure IoT Edge runtime:

| Poort nummer | In of uit | Poort bereik | Vereist | Richtlijnen |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Uit       | WAN        | Ja      | Uitgaand openen voor IoT Edge inrichting. Deze configuratie is vereist wanneer u hand matige scripts of een Azure IoT Device Provisioning Service (DPS) gebruikt.|

Voor volledige informatie gaat u naar de [firewall-en poort configuratie regels voor IOT Edge-implementatie](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>URL-patronen voor firewall regels

Netwerk beheerders kunnen regel matig geavanceerde firewall regels configureren op basis van de URL-patronen om het binnenkomende en uitgaande verkeer te filteren. Uw Data Box Edge-apparaat en de service zijn afhankelijk van andere micro soft-toepassingen, zoals Azure Service Bus, Azure Active Directory Access Control, opslag accounts en Microsoft Update servers. De URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen worden gebruikt voor het configureren van firewall regels. Het is belang rijk te weten dat de URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen worden gewijzigd. Als u deze wijzigingen aanbrengt, moet de netwerk beheerder de firewall regels voor uw Data Box Edge controleren en bijwerken als dat nodig is.

We raden u aan de firewall regels voor uitgaand verkeer op te nemen, op basis van Data Box Edge vaste IP-adressen, in de meeste gevallen. U kunt echter de onderstaande informatie gebruiken om geavanceerde firewall regels in te stellen die nodig zijn om beveiligde omgevingen te maken.

> [!NOTE]
> - De IP-adressen van het apparaat moeten altijd worden ingesteld op alle netwerk interfaces die zijn ingeschakeld voor de Cloud.
> - De doel-Ip's moeten worden ingesteld op [Azure Data Center IP-bereiken](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>URL-patronen voor de gateway functie

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>URL-patronen voor de functie compute

| URL-patroon                      | Onderdeel of functionaliteit                     |   
|----------------------------------|---------------------------------------------|
| https:\//MCR.Microsoft.com<br></br>https://\*. CDN.mscr.io | Micro soft container Registry (vereist)               |
| https://\*. azurecr.io                     | Persoonlijke en container registers van derden (optioneel) | 
| https://\*. Azure-devices.net              | IoT Hub toegang (vereist)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>URL-patronen voor de gateway voor Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>URL-patronen voor het berekenen van Azure Government

| URL-patroon                      | Onderdeel of functionaliteit                     |  
|----------------------------------|---------------------------------------------|
| https:\//MCR.Microsoft.com<br></br>https://\*. CDN.mscr.com | Micro soft container Registry (vereist)               |
| https://\*. Azure-devices.us              | IoT Hub toegang (vereist)           |
| https://\*. azurecr.us                    | Persoonlijke en container registers van derden (optioneel) | 

## <a name="internet-bandwidth"></a>Internet bandbreedte

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Overwegingen bij reken grootte

Gebruik uw ervaring bij het ontwikkelen en testen van uw oplossing om ervoor te zorgen dat er voldoende capaciteit is op uw Data Box Edge apparaat en dat u de optimale prestaties van uw apparaat krijgt.

Denk hierbij aan de volgende factoren:

- **Container Details** : denk aan het volgende.

    - Hoeveel containers bevinden zich in uw werk belasting? U kunt een groot aantal licht gewicht containers hebben in plaats van een aantal bronnen.
    - Wat zijn de resources die zijn toegewezen aan deze containers en wat zijn de resources die ze gebruiken?
    - Hoeveel lagen delen uw containers?
    - Zijn er ongebruikte containers? Een gestopt container neemt nog steeds schijf ruimte in beslag.
    - In welke taal zijn uw containers geschreven?
- **Grootte van de verwerkte gegevens** -hoeveel gegevens worden er door uw containers verwerkt? Nemen deze gegevens schijf ruimte in beslag of worden de gegevens in het geheugen verwerkt?
- **Verwachte prestaties** : wat zijn de gewenste prestatie kenmerken van uw oplossing? 

Als u de prestaties van uw oplossing wilt begrijpen en verfijnen, kunt u het volgende gebruiken:

- De metrische reken gegevens die beschikbaar zijn in de Azure Portal. Ga naar uw Data Box Edge-resource en ga vervolgens naar **bewaking > metrische gegevens**. Bekijk de **Edge Compute-Memory Usage** en **Edge Compute-percentage CPU** om inzicht te krijgen in de beschik bare bronnen en hoe de bronnen worden gebruikt.
- De beschik bare bewakings opdrachten via de Power shell-interface van het apparaat, zoals:

    - `dkrdbe stats`om een live stream van de resource gebruiks statistieken van de container (s) op te halen. De opdracht ondersteunt CPU, geheugen gebruik, geheugen limiet en metrische gegevens voor netwerk-i/o.
    - `dkrdbe system df`om informatie te krijgen over de hoeveelheid schijf ruimte die wordt gebruikt. 
    - `dkrdbe image prune`ongebruikte installatie kopieën opruimen en ruimte vrijmaken.
    - `dkrdbe ps --size`de geschatte grootte van een actieve container weer geven. 

    Ga voor meer informatie over de beschik bare opdrachten naar [bewaken en problemen oplossen van Compute-modules](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Ten slotte moet u ervoor zorgen dat u uw oplossing valideert op uw gegevensset en de prestaties van Data Box Edge te kwantificeren voordat u in productie implementeert.


## <a name="next-step"></a>Volgende stap

- [Uw Azure Data Box Edge implementeren](data-box-edge-deploy-prep.md)

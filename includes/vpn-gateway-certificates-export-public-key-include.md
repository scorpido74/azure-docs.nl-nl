---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2dba0f657b418267db90c07014dc8996ed12a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059941"
---
Nadat u een zelfondertekend rootcertificaat hebt gemaakt, exporteert u het hoofdcertificaat public key .cer-bestand (niet de privésleutel). U uploadt dit bestand later naar Azure. Met de volgende stappen u het .cer-bestand exporteren voor uw zelfondertekende rootcertificaat:

1. Als u een CER-bestand wilt genereren van het certificaat, opent u **Gebruikerscertificaten beheren**. Zoek het zelfondertekende basiscertificaat, meestal in 'Certificaten - Huidige gebruiker\Persoonlijk\Certificaten', en klik er met de rechtermuisknop op. Klik op **Alle taken** en vervolgens op **Exporteren**. Hiermee opent u de **Wizard Certificaat exporteren**. Als u het certificaat niet vinden onder Huidige gebruiker\Persoonlijk\Certificaten, hebt u mogelijk per ongeluk 'Certificaten - lokale computer' geopend in plaats van 'Certificaten - Huidige gebruiker'). Als u Certificaatbeheer wilt openen in het huidige gebruikersbereik met PowerShell, typt u *certmgr* in het consolevenster.

   ![Exporteren](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. Klik in de wizard op **Volgende**.

   ![Certificaat exporteren](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Selecteer **Nee, de persoonlijke sleutel niet exporteren** en klik vervolgens op **Volgende**.

   ![Exporteer de privésleutel niet](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Selecteer op de pagina **Bestandsindeling voor export** de optie **Met Base64 gecodeerde X.509 (*.CER)** en klik op **Volgende**.

   ![Basis-64 gecodeerd](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Als **u Bestand wilt exporteren,** **bladert u** naar de locatie waarnaar u het certificaat wilt exporteren. Geef bij **Bestandsnaam** de naam van het certificaatbestand op. Klik vervolgens op **Volgende.**

   ![Bladeren](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Klik op **Voltooien** om het certificaat te exporteren.

   ![Voltooien](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Uw certificaat wordt geëxporteerd.

   ![Geslaagd](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Het geëxporteerde certificaat lijkt op:

   ![Geëxporteerd](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Als u het geëxporteerde certificaat opent met Kladblok, ziet u iets vergelijkbaars met dit voorbeeld. De sectie in het blauw bevat de informatie die wordt geüpload naar Azure. Als u uw certificaat opent met Kladblok en het ziet er niet vergelijkbaar met deze, betekent dit meestal dat u het niet hebt geëxporteerd met behulp van de Basis-64 gecodeerde X.509 (. CER) formaat. Als u een andere teksteditor wilt gebruiken, moet u bovendien begrijpen dat sommige editors onbedoelde opmaak op de achtergrond kunnen introduceren. Dit kan problemen veroorzaken bij het uploaden van de tekst van dit certificaat naar Azure.

   ![Openen met kladblok](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)

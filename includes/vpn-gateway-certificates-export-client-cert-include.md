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
ms.openlocfilehash: d16412e4e35714c840516670f520f77daed1676d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80059915"
---
Wanneer u een client certificaat genereert, wordt het automatisch geïnstalleerd op de computer die u hebt gebruikt om het te genereren. Als u het client certificaat op een andere client computer wilt installeren, moet u het door u gegenereerde client certificaat exporteren.

1. Als u een clientcertificaat wilt exporteren, opent u **Gebruikerscertificaten beheren**. De client certificaten die u hebt gegenereerd, zijn standaard opgeslagen in ' certificaten-huidige Gebruiker\persoonlijk\certificaten '. Klik met de rechter muisknop op het client certificaat dat u wilt exporteren, klik op **alle taken**en klik vervolgens op **exporteren** om de **wizard Certificaat exporteren**te openen.

   ![Exporteren](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. Klik in de wizard Certificaat exporteren op **volgende** om door te gaan.

   ![Volgende](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Selecteer **Ja, de persoonlijke sleutel exporteren**en klik vervolgens op **volgende**.

   ![persoonlijke sleutel exporteren](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Laat op de pagina **Bestandsindeling voor export** de standaardinstellingen geselecteerd. Zorg ervoor dat **en mogelijk alle certificaten in het certificeringspad opnemen** is geselecteerd. Met deze instelling exporteert u ook de basis certificaat gegevens die nodig zijn voor geslaagde client authenticatie. Zonder deze reden mislukt de client verificatie, omdat de client niet over het vertrouwde basis certificaat beschikt. Klik vervolgens op **volgende**.

   ![bestands indeling voor export](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Op de pagina **Beveiliging** moet u de persoonlijke sleutel beveiligen. Als u ervoor kiest om een wachtwoord te gebruiken, is het belangrijk dat u het wachtwoord voor dit certificaat ergens noteert of onthoudt. Klik vervolgens op **volgende**.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Op de pagina **Te exporteren bestand****bladert** u naar de locatie waar u het certificaat wilt exporteren. Geef bij **Bestandsnaam** de naam van het certificaatbestand op. Klik vervolgens op **volgende**.

   ![te exporteren bestand](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Klik op **Voltooien** om het certificaat te exporteren.

   ![voltooien](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)
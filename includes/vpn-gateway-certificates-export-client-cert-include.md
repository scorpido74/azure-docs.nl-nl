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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059915"
---
Wanneer u een clientcertificaat genereert, wordt het automatisch geïnstalleerd op de computer die u hebt gebruikt om het te genereren. Als u het clientcertificaat op een andere clientcomputer wilt installeren, moet u het clientcertificaat exporteren dat u hebt gegenereerd.

1. Als u een clientcertificaat wilt exporteren, opent u **Gebruikerscertificaten beheren**. De clientcertificaten die u hebt gegenereerd, bevinden zich standaard in 'Certificaten - Huidige gebruiker\Persoonlijk\Certificaten'. Klik met de rechtermuisknop op het clientcertificaat dat u wilt exporteren, klik op **alle taken**en klik vervolgens op **Exporteren** om de wizard Certificaat exporteren te **openen.**

   ![Exporteren](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. Klik in de wizard Certificaatexporteren op **Volgende** om door te gaan.

   ![Volgende](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Selecteer **Ja, exporteer de privésleutel**en klik op **Volgende**.

   ![privésleutel exporteren](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Laat op de pagina **Bestandsindeling voor export** de standaardinstellingen geselecteerd. Zorg ervoor dat **en mogelijk alle certificaten in het certificeringspad opnemen** is geselecteerd. Met deze instelling exporteert u bovendien de basiscertificaatgegevens die nodig zijn voor succesvolle clientverificatie. Zonder dit certificaat mislukt clientverificatie omdat de client niet over het vertrouwde rootcertificaat beschikt. Klik vervolgens op **Volgende.**

   ![bestandsindeling exporteren](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Op de pagina **Beveiliging** moet u de persoonlijke sleutel beveiligen. Als u ervoor kiest om een wachtwoord te gebruiken, is het belangrijk dat u het wachtwoord voor dit certificaat ergens noteert of onthoudt. Klik vervolgens op **Volgende.**

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Op de pagina **Te exporteren bestand****bladert** u naar de locatie waar u het certificaat wilt exporteren. Geef bij **Bestandsnaam** de naam van het certificaatbestand op. Klik vervolgens op **Volgende.**

   ![bestand om te exporteren](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Klik op **Voltooien** om het certificaat te exporteren.

   ![voltooien](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)
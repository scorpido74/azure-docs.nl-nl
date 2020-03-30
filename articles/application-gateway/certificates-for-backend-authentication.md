---
title: Certificaten die nodig zijn om backendservers toe te staan
titleSuffix: Azure Application Gateway
description: In dit artikel vindt u voorbeelden van hoe een TLS/SSL-certificaat kan worden geconverteerd naar verificatiecertificaat en vertrouwd rootcertificaat dat vereist is om backend-exemplaren toe te staan in Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 20f588639c54b0a8b7cd304f33b5a9d633a73be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133053"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Certificaten maken om de backend toe te staan met Azure Application Gateway

Om TLS te beëindigen, vereist Application Gateway dat de backend-exemplaren worden toegestaan door verificatie/vertrouwde rootcertificaten te uploaden. Voor de v1 SKU zijn verificatiecertificaten vereist, maar voor de vertrouwde basiscertificaten v2 SKU zijn basiscertificaten vereist om de certificaten toe te staan.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> - Verificatiecertificaat exporteren vanaf een backendcertificaat (voor v1 SKU)
> - Vertrouwd rootcertificaat exporteren vanaf een backendcertificaat (voor v2 SKU)

## <a name="prerequisites"></a>Vereisten

Een bestaand backendcertificaat is vereist om de verificatiecertificaten of vertrouwde rootcertificaten te genereren die nodig zijn voor het toestaan van backend-exemplaren met Application Gateway. Het backend-certificaat kan hetzelfde zijn als het TLS/SSL-certificaat of anders voor extra beveiliging. Application Gateway biedt u geen mechanisme om een TLS/SSL-certificaat te maken of aan te schaffen. Voor testdoeleinden u een zelfondertekend certificaat maken, maar u moet het niet gebruiken voor productieworkloads. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Verificatiecertificaat exporteren (voor v1 SKU)

Er is een verificatiecertificaat vereist om backend-exemplaren toe te staan in Application Gateway v1 SKU. Het verificatiecertificaat is de openbare sleutel van backendservercertificaten in Base-64 gecodeerde X.509(. CER) formaat. In dit voorbeeld gebruikt u een TLS/SSL-certificaat voor het backend-certificaat en exporteert u de openbare sleutel om te worden gebruikt als verificatiecertificering. In dit voorbeeld gebruikt u ook het gereedschap Windows Certificaatbeheer om de vereiste certificaten te exporteren. U ervoor kiezen om een ander gereedschap te gebruiken dat handig is.

Exporteer vanuit uw TLS/SSL-certificaat het bestand van de openbare sleutel (niet de privésleutel). Met de volgende stappen u het .cer-bestand exporteren in Base-64 gecodeerde X.509(. CER) formaat voor uw certificaat:

1. Als u een CER-bestand wilt genereren van het certificaat, opent u **Gebruikerscertificaten beheren**. Zoek het certificaat, meestal in 'Certificaten - Huidige gebruiker\Persoonlijk\Certificaten', en klik met de rechtermuisknop. Klik op **Alle taken** en vervolgens op **Exporteren**. Hiermee opent u de **Wizard Certificaat exporteren**. Als u het certificaat niet vinden onder Huidige gebruiker\Persoonlijk\Certificaten, hebt u mogelijk per ongeluk 'Certificaten - lokale computer' geopend in plaats van 'Certificaten - Huidige gebruiker'). Als u Certificaatbeheer wilt openen in het huidige gebruikersbereik met PowerShell, typt u *certmgr* in het consolevenster.

   ![Exporteren](./media/certificates-for-backend-authentication/export.png)

2. Klik in de wizard op **Volgende**.

   ![Certificaat exporteren](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selecteer **Nee, de persoonlijke sleutel niet exporteren** en klik vervolgens op **Volgende**.

   ![Exporteer de privésleutel niet](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Selecteer op de pagina **Bestandsindeling voor export** de optie **Met Base64 gecodeerde X.509 (*.CER)** en klik op **Volgende**.

   ![Basis-64 gecodeerd](./media/certificates-for-backend-authentication/base64.png)

5. Als **u Bestand wilt exporteren,** **bladert u** naar de locatie waarnaar u het certificaat wilt exporteren. Geef bij **Bestandsnaam** de naam van het certificaatbestand op. Klik vervolgens op **Volgende.**

   ![Bladeren](./media/certificates-for-backend-authentication/browse.png)

6. Klik op **Voltooien** om het certificaat te exporteren.

   ![Voltooien](./media/certificates-for-backend-authentication/finish.png)

7. Uw certificaat wordt geëxporteerd.

   ![Geslaagd](./media/certificates-for-backend-authentication/success.png)

   Het geëxporteerde certificaat lijkt op:

   ![Geëxporteerd](./media/certificates-for-backend-authentication/exported.png)

8. Als u het geëxporteerde certificaat opent met Kladblok, ziet u iets vergelijkbaars met dit voorbeeld. De sectie in het blauw bevat de informatie die wordt geüpload naar de toepassingsgateway. Als u uw certificaat opent met Kladblok en het ziet er niet vergelijkbaar met deze, betekent dit meestal dat u het niet hebt geëxporteerd met behulp van de Basis-64 gecodeerde X.509 (. CER) formaat. Als u een andere teksteditor wilt gebruiken, moet u bovendien begrijpen dat sommige editors onbedoelde opmaak op de achtergrond kunnen introduceren. Dit kan problemen veroorzaken bij het uploaden van de tekst van dit certificaat naar Azure.

   ![Openen met kladblok](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Vertrouwd rootcertificaat exporteren (voor v2 SKU)

Vertrouwd rootcertificaat is vereist om backend-exemplaren in de toepassingsgateway v2 SKU op de witte lijst te krijgen. Het rootcertificaat is een Basis-64 gecodeerde X.509(. CER) het hoofdcertificaat opmaken van de backendservercertificaten. In dit voorbeeld gebruiken we een TLS/SSL-certificaat voor het backend-certificaat, exporteren we de openbare sleutel en exporteren we het hoofdcertificaat van de vertrouwde CA van de openbare sleutel in de gecodeerde basisindeling64 om het vertrouwde rootcertificaat te krijgen. Het tussencertificaat(en) moet worden gebundeld met servercertificaat en op de backendserver worden geïnstalleerd.

Met de volgende stappen u het .cer-bestand voor uw certificaat exporteren:

1. Gebruik de stappen 1-9 die in de sectie **Verificatiecertificaat exporteren van een backendcertificaat (voor v1 SKU)** hierboven wordt genoemd om de openbare sleutel uit uw backendcertificaat te exporteren.

2. Zodra de openbare sleutel is geëxporteerd, opent u het bestand.

   ![Autorisatiecertificaat openen](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![over certificaat](./media/certificates-for-backend-authentication/general.png)

3. Ga naar de weergave Certificeringspad om de certificeringsinstantie te bekijken.

   ![cert details](./media/certificates-for-backend-authentication/certdetails.png)

4. Selecteer het basiscertificaat en klik op **Weergavecertificaat**.

   ![certpad](./media/certificates-for-backend-authentication/rootcert.png)

   U ziet de details van het basiscertificaat.

   ![cert-informatie](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Ga naar de weergave **Details** en klik op **Kopiëren naar bestand...**

   ![kopiëren root cert](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Op dit moment hebt u de details van het basiscertificaat uit het backendcertificaat gehaald. U ziet de **wizard Certificaat exporteren**. Gebruik nu stappen 2-9 die in de sectie **Exportverificatiecertificaat van een backendcertificaat (voor v1 SKU)** hierboven worden vermeld om het vertrouwde rootcertificaat te exporteren in de Basis-64 gecodeerde X.509(. CER) formaat.

## <a name="next-steps"></a>Volgende stappen

Nu hebt u het verificatiecertificaat/vertrouwd rootcertificaat in Base-64 gecodeerde X.509(. CER) formaat. U dit toevoegen aan de toepassingsgateway om uw backendservers op de witte lijst te plaatsen voor tls-versleuteling van end-to-end. Zie [TLS van end-to-end configureren met Toepassinggateway met PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).


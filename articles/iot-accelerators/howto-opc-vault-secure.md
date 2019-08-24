---
title: De communicatie van OPC UA-apparaten met OPC-kluis beveiligen-Azure | Microsoft Docs
description: Het registreren van OPC UA-toepassingen en het uitgeven van ondertekende toepassings certificaten voor uw OPC UA-apparaten met OPC-kluis.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae10bb3488c21b6637163e333231e95a18c652bf
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996005"
---
# <a name="how-to-use-the-opc-vault-certificate-management-service"></a>De OPC-kluis certificaat beheer service gebruiken

In dit artikel wordt uitgelegd hoe u toepassingen kunt registreren en ondertekende toepassings certificaten kunt verlenen voor uw OPC UA-apparaten.

## <a name="prerequisites"></a>Vereisten

### <a name="deploy-the-certificate-management-service"></a>De Certificate Management-service implementeren

Eerst moet de service worden geïmplementeerd in de Azure-Cloud.
Ga naar een artikel waarin wordt beschreven hoe u [de OPC kluis Certificate Management-service implementeert](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Het CA-certificaat van de verlener maken

Als u dit nog niet hebt gedaan, maakt u het CA-certificaat van de certificaat verlener.

Een artikel met informatie over [het maken en beheren van het certificaat van de verlener voor OPC-kluis](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Veilige OPC UA-toepassingen

### <a name="step-1-register-your-opc-ua-application"></a>Stap 1: Uw OPC UA-toepassing registreren 

> [!IMPORTANT]
> De rol schrijver is vereist voor het registreren van een toepassing.

1. Open uw certificaat service op `https://myResourceGroup-app.azurewebsites.net` en meld u aan.
2. Navigeer naar de `Register New` pagina.
1. Voor een toepassings registratie moet aan een gebruiker ten minste de rol schrijver zijn toegewezen.
2. Het invoer formulier volgt naam conventies in de OPC UA-wereld. Als voor beeld in de afbeelding onder de instellingen voor het voor beeld van het [OPC UA-referentie server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) in de OPC ua. Netstandaard stack wordt weer gegeven:

   ![Registratie van UA-referentie server](media/howto-opc-vault-secure/reference-server-registration.png "Registratie van UA-referentie server")

5. Druk op `Register` de knop om de toepassing te registreren in de toepassings database van de certificaat service. De werk stroom leidt de gebruiker rechtstreeks naar de volgende stap om een ondertekend certificaat aan te vragen voor de toepassing.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Stap 2: Uw toepassing beveiligen met een door een CA ondertekend toepassings certificaat

Een OPC UA-toepassing kan worden beveiligd door een ondertekend certificaat uit te geven op basis van een aanvraag voor certificaat ondertekening (CSR) of door een nieuw sleutel paar op te vragen, dat ook een nieuwe persoonlijke sleutel in PFX of PEM-indeling bevat. Volg de documentatie van uw OPC UA-apparaat op welke methode voor uw toepassing wordt ondersteund. In het algemeen wordt de CSR-methode aanbevolen, omdat er geen persoonlijke sleutel moet worden overgedragen over een kabel.

- Als uw apparaat een nieuw sleutel paar moet uitgeven, volgt u [Step3a](##step-3a-request-a-new-certificate-with-a-new-keypair).
- Als uw apparaat ondersteuning biedt voor het uitgeven van een aanvraag voor certificaat ondertekening (CSR), volgt u [Step3b](#step-3b-request-a-new-certificate-with-a-csr).

### <a name="step-3a-request-a-new-certificate-with-a-new-keypair"></a>Stap 3a: Een nieuw certificaat aanvragen met een nieuw sleutel paar

1. Navigeer naar de `Applications` pagina.
3. Start de werk stroom voor certificaat aanvragen `New Request` door voor een vermelde toepassing te kiezen.

   ![Nieuw certificaat aanvragen](media/howto-opc-vault-secure/request-new-certificate.png "Nieuw certificaat aanvragen")

3. Druk op Nieuw sleutel paar en certificaat aanvragen om een persoonlijke en een nieuw ondertekend certificaat aan te vragen met de open bare sleutel voor uw toepassing.

   ![Nieuw sleutel paar genereren](media/howto-opc-vault-secure/generate-new-key-pair.png "Nieuw sleutel paar genereren")

4. Vul het formulier in met een onderwerp, de domein namen en kies PEM of PFX met een wacht woord voor de persoonlijke sleutel. Druk op `Generate New Certificate` de knop om de certificaat aanvraag te maken.

   ![Certificaat goed keuren](media/howto-opc-vault-secure/approve-reject.png "Certificaat goed keuren")

5. Voor de goedkeurings stap is een gebruiker met de rol ' fiatteur ' en met de machtiging ondertekenen in Azure Key Vault vereist. In de standaard werk stroom moet de rol goed keurder en aanvrager worden toegewezen aan verschillende gebruikers.
6. De certificaat aanvraag goed keuren of afwijzen om de daad werkelijke aanmaak van het sleutel paar en de ondertekening bewerking te starten of te annuleren. Het nieuwe sleutel paar wordt gemaakt en veilig opgeslagen in Azure Key Vault totdat het is gedownload door de aanvrager van het certificaat. Het resulterende certificaat met de open bare sleutel is ondertekend door de CA. Het kan een paar seconden duren voordat deze bewerkingen zijn voltooid.

   ![Sleutel paar weer geven](media/howto-opc-vault-secure/view-key-pair.png "Sleutel paar weer geven")

7. De resulterende persoonlijke sleutel (PFX of PEM) en het certificaat (DER) kunnen hier worden gedownload in de indeling die is geselecteerd als binair bestand downloaden. Er is ook een met base64 gecodeerde versie beschikbaar, bijvoorbeeld om het certificaat te kopiëren naar een opdracht regel of tekst invoer. 
8. Zodra de persoonlijke sleutel is gedownload en beveiligd opgeslagen, kan deze worden verwijderd uit de service met de `Delete Private Key` knop. Het certificaat met de open bare sleutel blijft beschikbaar voor toekomstig gebruik.
9. Als gevolg van het gebruik van een door een CA ondertekend certificaat, moeten het CA-certificaat en de CRL ook hier worden gedownload.
10. Nu is het afhankelijk van het OPC UA-apparaat hoe het nieuwe sleutel paar moet worden toegepast. Normaal gesp roken worden het CA-certificaat en de CRL `trusted` gekopieerd naar een map, terwijl de open bare en persoonlijke sleutel van het toepassings certificaat `own` wordt toegepast op een map in het certificaat archief. Sommige apparaten ondersteunen mogelijk al ' server push ' voor certificaat updates. Raadpleeg de documentatie van uw OPC UA-apparaat.

### <a name="step-3b-request-a-new-certificate-with-a-csr"></a>Stap 3b: Een nieuw certificaat aanvragen bij een CSR 

1. Navigeer naar de `Applications` pagina.
3. Start de werk stroom voor certificaat aanvragen `New Request` door voor een vermelde toepassing te kiezen.

   ![Nieuw certificaat aanvragen](media/howto-opc-vault-secure/request-new-certificate.png "Nieuw certificaat aanvragen")

3. Druk op nieuw certificaat aanvragen met een handtekening aanvraag om een nieuw ondertekend certificaat aan te vragen voor uw toepassing.

   ![Nieuw certificaat genereren](media/howto-opc-vault-secure/generate-new-certificate.png "Nieuw certificaat genereren")

4. Upload CSR door een lokaal bestand te selecteren of door een met base64 gecodeerde CSR in het formulier te plakken. Druk op `Generate New Certificate` de knop om de certificaat aanvraag te maken.

   ![CSR goed keuren](media/howto-opc-vault-secure/approve-reject-csr.png "CSR goed keuren")

5. Voor de goedkeurings stap is een gebruiker met de rol ' fiatteur ' en met de machtiging ondertekenen in Azure Key Vault vereist. De certificaat aanvraag goed keuren of afwijzen om de werkelijke ondertekening bewerking te starten of te annuleren. Het resulterende certificaat met de open bare sleutel is ondertekend door de CA. Het kan een paar seconden duren voordat deze bewerking is voltooid.

   ![Certificaat weer geven](media/howto-opc-vault-secure/view-cert-csr.png "Certificaat weer geven")

6. Het resulterende certificaat (DER) kan hier worden gedownload als binair bestand. Er is ook een met base64 gecodeerde versie beschikbaar, bijvoorbeeld om het certificaat te kopiëren naar een opdracht regel of tekst invoer. 
10. Zodra het certificaat is gedownload en veilig is opgeslagen, kan het worden verwijderd uit de service met `Delete Certificate` de knop.
11. Als gevolg van het gebruik van een door een CA ondertekend certificaat, moeten het CA-certificaat en de CRL ook hier worden gedownload.
12. Nu is het afhankelijk van het OPC UA-apparaat hoe het nieuwe certificaat moet worden toegepast. Normaal gesp roken worden het CA-certificaat en de CRL `trusted` gekopieerd naar een map, terwijl het toepassings certificaat wordt `own` toegepast op een map in het certificaat archief. Sommige apparaten ondersteunen mogelijk al ' server push ' voor certificaat updates. Raadpleeg de documentatie van uw OPC UA-apparaat.

### <a name="step-4-device-secured"></a>Stap 4: Apparaat beveiligd

Het OPC UA-apparaat is nu klaar om te communiceren met andere OPC UA-apparaten die zijn beveiligd door door certificerings instanties ondertekende certificaten zonder verdere configuratie. Veel plezier!


## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC UA-apparaten kunt beveiligen, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Een Secure Certificate Management-service uitvoeren](howto-opc-vault-secure-ca.md)
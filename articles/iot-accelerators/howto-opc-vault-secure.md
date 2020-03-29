---
title: De communicatie van OPC UA-apparaten beveiligen met OPC Vault - Azure | Microsoft Documenten
description: Opc UA-toepassingen registreren en ondertekende toepassingscertificaten uitgeven voor uw OPC UA-apparaten met OPC Vault.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: af5e511cbf273bc4e4fa0a08d089a955426fe75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75454198"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>De opc Vault-certificaatbeheerservice gebruiken

In dit artikel wordt uitgelegd hoe u toepassingen registreert en hoe u ondertekende toepassingscertificaten uitgeeft voor uw OPC UA-apparaten.

## <a name="prerequisites"></a>Vereisten

### <a name="deploy-the-certificate-management-service"></a>De certificaatbeheerservice implementeren

Implementeer de service eerst in de Azure-cloud. Zie De [OPC Vault-certificaatbeheerservice implementeren](howto-opc-vault-deploy.md)voor meer informatie.

### <a name="create-the-issuer-ca-certificate"></a>Het CA-certificaat van de uitgever maken

Als u dit nog niet hebt gedaan, maakt u het CA-certificaat uitgever. Zie Het [emittentcertificaat voor OPC Vault maken en beheren voor](howto-opc-vault-manage.md)meer informatie.

## <a name="secure-opc-ua-applications"></a>Beveiligde OPC UA-toepassingen

### <a name="step-1-register-your-opc-ua-application"></a>Stap 1: Uw OPC UA-toepassing registreren 

> [!IMPORTANT]
> De rol Schrijver is vereist om een aanvraag te registreren.

1. Open uw certificaatservice bij `https://myResourceGroup-app.azurewebsites.net`en meld u aan.
2. Ga naar **Nieuw registreren**. Voor een toepassingsregistratie moet een gebruiker ten minste de rol Schrijver hebben toegewezen.
2. Het inschrijfformulier volgt naamgevingsconventies in OPC UA. In de volgende schermafbeelding worden bijvoorbeeld de instellingen voor het voorbeeld van de [OPC UA Reference Server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) in de OPC UA .NET Standard-stack weergegeven:

   ![Schermafbeelding van ua-referentieserverregistratie](media/howto-opc-vault-secure/reference-server-registration.png "UA-referentieserverregistratie")

5. Selecteer **Registreren** om de toepassing te registreren in de database met certificaatservicetoepassingen. De werkstroom leidt de gebruiker rechtstreeks naar de volgende stap om een ondertekend certificaat voor de toepassing aan te vragen.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Stap 2: Uw aanvraag beveiligen met een CA-ondertekend aanvraagcertificaat

Beveilig uw OPC UA-aanvraag door een ondertekend certificaat uit te geven op basis van een Csr (Certificate Signing Request). U ook een nieuw sleutelpaar aanvragen, dat een nieuwe privésleutel in PFX- of PEM-formaat bevat. Zie de documentatie van uw OPC UA-apparaat voor informatie over welke methode wordt ondersteund voor uw toepassing. In het algemeen wordt de MVO-methode aanbevolen, omdat er geen privésleutel over een draad hoeft te worden overgedragen.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Een nieuw certificaat aanvragen met een nieuw sleutelpaar

1. Ga naar **Toepassingen**.
3. Selecteer **Nieuwe aanvraag** voor een vermelde toepassing.

   ![Schermafbeelding van Nieuw certificaat aanvragen](media/howto-opc-vault-secure/request-new-certificate.png "Nieuw certificaat aanvragen")

3. Selecteer **Nieuw Sleutelpaar en certificaat aanvragen** om een privésleutel en een nieuw ondertekend certificaat met de openbare sleutel voor uw toepassing aan te vragen.

   ![Schermafbeelding van het genereren van een nieuw sleutelpaar en certificaat](media/howto-opc-vault-secure/generate-new-key-pair.png "Nieuwe sleutelpaar genereren")

4. Vul het formulier in met een onderwerp en de domeinnamen. Kies voor de privésleutel PEM of PFX met wachtwoord. Selecteer **Nieuwe KeyPair genereren** om de certificaataanvraag te maken.

   ![Schermafbeelding van details van certificaataanvraag weergeven](media/howto-opc-vault-secure/approve-reject.png "Certificaat goedkeuren")

5. Goedkeuring vereist een gebruiker met de rol Goedkeurende goedkeuring en met ondertekeningsmachtigingen in Azure Key Vault. In de typische werkstroom moeten de rollen Keuraar en Aanvrager aan verschillende gebruikers worden toegewezen. Selecteer **Goedkeuren** of **Weigeren** om de daadwerkelijke creatie van het sleutelpaar en de ondertekeningsbewerking te starten of te annuleren. Het nieuwe sleutelpaar wordt veilig gemaakt en opgeslagen in Azure Key Vault, totdat het wordt gedownload door de certificaataanvrager. Het resulterende certificaat met openbare sleutel wordt ondertekend door de CA. Deze bewerkingen kunnen enkele seconden duren om te voltooien.

   ![Schermafbeelding van details van certificaataanvraag weergeven, met goedkeuringsbericht onderaan](media/howto-opc-vault-secure/view-key-pair.png "Sleutelpaar weergeven")

7. De resulterende private key (PFX of PEM) en certificaat (DER) kunnen hier worden gedownload in het formaat dat is geselecteerd als binaire bestandsdownload. Er is ook een basisversie van 64 beschikbaar, bijvoorbeeld om het certificaat te kopiëren en te plakken naar een opdrachtregel of tekstvermelding. 
8. Nadat de privésleutel is gedownload en veilig is opgeslagen, u **Privésleutel verwijderen**selecteren. Het certificaat met de openbare sleutel blijft beschikbaar voor toekomstig gebruik.
9. Vanwege het gebruik van een CA ondertekend certificaat, moet de CA cert en Certificate Revocation List (CRL) hier ook worden gedownload.

Nu hangt het af van de OPC UA apparaat hoe het nieuwe sleutelpaar toe te passen. Doorgaans worden het CA-cert en CRL `trusted` gekopieerd naar een map, terwijl de openbare `own` en privésleutels van het toepassingscertificaat worden toegepast op een map in het certificaatarchief. Sommige apparaten ondersteunen mogelijk al serverpush voor certificaatupdates. Raadpleeg de documentatie van uw OPC UA-apparaat.

#### <a name="request-a-new-certificate-with-a-csr"></a>Een nieuw certificaat aanvragen met een MVO 

1. Ga naar **Toepassingen**.
3. Selecteer **Nieuwe aanvraag** voor een vermelde toepassing.

   ![Schermafbeelding van Nieuw certificaat aanvragen](media/howto-opc-vault-secure/request-new-certificate.png "Nieuw certificaat aanvragen")

3. Selecteer **Nieuw certificaat aanvragen met ondertekeningsaanvraag** om een nieuw ondertekend certificaat voor uw aanvraag aan te vragen.

   ![Schermafbeelding van Een nieuw certificaat genereren](media/howto-opc-vault-secure/generate-new-certificate.png "Nieuw certificaat genereren")

4. Upload CSR door een lokaal bestand te selecteren of door een base64-codeerde CSR in het formulier te plakken. Selecteer **Nieuw certificaat genereren**.

   ![Schermafbeelding van details van certificaataanvraag weergeven](media/howto-opc-vault-secure/approve-reject-csr.png "MVO goedkeuren")

5. Goedkeuring vereist een gebruiker met de rol Goedkeurende goedkeuring en met ondertekeningsmachtigingen in Azure Key Vault. Selecteer **Goedkeuren** of **Weigeren** om de werkelijke ondertekeningsbewerking te starten of te annuleren. Het resulterende certificaat met openbare sleutel wordt ondertekend door de CA. Deze bewerking kan enkele seconden duren.

   ![Schermafbeelding van details van certificaataanvraag weergeven, met goedkeuringsbericht onderaan](media/howto-opc-vault-secure/view-cert-csr.png "Certificaat weergeven")

6. Het resulterende certificaat (DER) kan vanaf hier worden gedownload als binair bestand. Er is ook een basisversie van 64 beschikbaar, bijvoorbeeld om het certificaat te kopiëren en te plakken naar een opdrachtregel of tekstvermelding. 
10. Nadat het certificaat is gedownload en veilig is opgeslagen, u **Certificaat verwijderen**selecteren.
11. Vanwege het gebruik van een CA ondertekend certificaat, moet de CA cert en CRL hier ook worden gedownload.

Nu hangt het af van het OPC UA-apparaat hoe het nieuwe certificaat toe te passen. Het CA-cert en CRL worden doorgaans `trusted` gekopieerd naar een map, `own` terwijl het toepassingscertificaat wordt toegepast op een map in het certificaatarchief. Sommige apparaten ondersteunen mogelijk al serverpush voor certificaatupdates. Raadpleeg de documentatie van uw OPC UA-apparaat.

### <a name="step-3-device-secured"></a>Stap 3: Apparaat beveiligd

Het OPC UA-apparaat is nu klaar om te communiceren met andere OPC UA-apparaten die zijn beveiligd met CA-ondertekende certificaten, zonder verdere configuratie.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC UA-apparaten beveiligen, u:

> [!div class="nextstepaction"]
> [Een beveiligde certificaatbeheerservice uitvoeren](howto-opc-vault-secure-ca.md)

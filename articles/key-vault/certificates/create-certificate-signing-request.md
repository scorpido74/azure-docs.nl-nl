---
title: CSR in Azure Key Vault maken en samenvoegen
description: CSR in Azure Key Vault maken en samenvoegen
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: ae2d6259bac6a2034edc98de9b0405f32f17fbc3
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85849494"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>CSR in Key Vault maken en samenvoegen

Azure Key Vault ondersteunt het opslaan van digitaal certificaat dat is uitgegeven door een certificeringsinstantie van uw keuze in uw sleutelkluis. Het biedt ondersteuning voor het maken van de aanvraag voor certificaatondertekening met een persoonlijk-openbaar sleutelpaar en kan worden ondertekend door elke gekozen certificeringsinstantie. Dit kan een interne ondernemings-CA of een externe openbare CA zijn. Een aanvraag voor certificaatondertekening (ook CSR of certificerings aanvraag) is een bericht dat door de gebruiker naar een certificeringsinstantie (CA) wordt verzonden om de uitgifte van een digitaal certificaat aan te vragen.

Zie [Azure Key Vault-certificaten](/azure/key-vault/certificates/about-certificates) voor meer algemene informatie over certificaten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="adding-certificate-in-key-vault-issued-by-a-non-trusted-ca"></a>Een certificaat toevoegen aan Key Vault dat is uitgegeven door een niet-vertrouwde certificeringsinstantie

De volgende stappen helpen u bij het maken van een certificaat van certificeringsinstanties die niet zijn gekoppeld aan Key Vault (GoDaddy is bijvoorbeeld is geen vertrouwde sleutelkluis-CA) 


### <a name="azure-powershell"></a>Azure PowerShell



1.  Begin met **Maak het certificaatbeleid**. Key Vault zal het certificaat van de uitgever niet inschrijven of vernieuwen namens de gebruiker, omdat de CA die in dit scenario is gekozen, niet wordt ondersteund, en daarom is de IssuerName ingesteld op onbekend.

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. Een **aanvraag voor certificaatondertekening** maken

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Het ophalen van de CSR-**aanvraag die is ondertekend door de CA**. De `$certificateOperation.CertificateSigningRequest` is de aanvraag voor het ondertekenen van versleuteld Base4-certificaat voor het certificaat. U kunt deze blob maken en dumpen in de certificaataanvraagwebsite van de verlener. Deze stap is afhankelijk van de certificeringsinstantie. De beste manier is om de richt lijnen van uw CA op te zoeken voor het uitvoeren van deze stap. U kunt ook hulpprogramma's zoals certreq of openssl gebruiken om de certificaataanvraag te ondertekenen en het proces voor het genereren van een certificaat te voltooien.


4. **Het samenvoegen van de ondertekende aanvraag** in Key Vault. Nadat de certificaataanvraag is ondertekend door de uitgever, kunt u het ondertekende certificaat terughalen en samenvoegen met het eerste persoonlijke openbare sleutelpaar dat is gemaakt in Azure Key Vault

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    De certificaataanvraag is nu succesvol samengevoegd.

### <a name="azure-portal"></a>Azure Portal

1.  Als u CSR wilt genereren voor de CA van uw keuze, gaat u naar de sleutelkluis die u wilt toevoegen van het certificaat.
2.  Selecteer op de eigenschappenpagina's van de sleutelkluis **Certificaten**.
3.  Selecteer tabblad **Genereren/importeren**.
4.  Kies op het scherm **Een certificaat maken** de volgende waarden:
    - **Methode voor het maken van certificaten:** Genereren.
    - **Naam van het certificaat:** ContosoManualCSRCertificate.
    - **Type certificeringsinstantie (CA):** Certificaat dat is uitgegeven door een niet-geïntegreerde certificeringsinstantie
    - **Onderwerp:** `"CN=www.contosoHRApp.com"`
    - Selecteer de andere waarden naar wens. Klik op **Create**.

    ![Certificaateigenschappen](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  U ziet dat het certificaat nu is toegevoegd in de lijst Certificaten. Selecteer dit nieuwe certificaat dat u zojuist hebt gemaakt. De huidige status van het certificaat is uitgeschakeld omdat het nog niet is uitgegeven door de CA.
7. Klik op tabblad **Certificaatbewerking** en selecteer **CSR downloaden**.
 ![Certificaateigenschappen](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  Breng het CSR-bestand naar de certificeringsinstantie om de aanvraag te kunnen ondertekenen.
9.  Zodra de aanvraag is ondertekend door de CA, gaat u terug naar het certificaatbestand om en kiest u **De ondertekende aanvraag samenvoegen** in hetzelfde Certificaatbewerkingsscherm.

De certificaataanvraag is nu succesvol samengevoegd.

## <a name="troubleshoot"></a>Problemen oplossen

Als het certificaat is uitgegeven in de status 'uitgeschakeld' in de Azure-portal, gaat u door met het weergeven van de **Certificaatbewerking** om het foutbericht voor dat certificaat te bekijken.

Raadpleeg de [Certificaatbewerkingen in de Key Vault REST API-referentie](/rest/api/keyvault) voor meer informatie. Raadpleeg [Kluizen: maken of bijwerken](/rest/api/keyvault/vaults/createorupdate) en [Kluizen: toegangsbeleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy) voor meer informatie over het instellen van machtigingen.

## <a name="next-steps"></a>Volgende stappen

- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)

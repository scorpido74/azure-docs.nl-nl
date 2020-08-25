---
title: Certificaat exporteren uit Azure Key Vault
description: Certificaat exporteren uit Azure Key Vault
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: afab65b22d9487f30da458346bf143a557bec0d8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588889"
---
# <a name="export-certificate-from-azure-key-vault"></a>Certificaat exporteren uit Azure Key Vault

Met Azure Key Vault kunt u eenvoudig digitale certificaten voor uw netwerk inrichten, beheren en implementeren en beveiligde communicatie mogelijk maken voor toepassingen. Zie [Azure Key Vault-certificaten](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates) voor meer algemene informatie over certificaten

## <a name="about-azure-key-vault-certificate"></a>Informatie over Azure-sleutelkluiscertificaten

### <a name="composition-of-certificate"></a>Samenstelling van certificaat
Wanneer er een Key Vault-certificaat wordt gemaakt, worden er ook een adresseerbare sleutel en een geheim gemaakt met dezelfde naam. Met de Key Vault sleutel kunnen sleutelbewerkingen worden uitgevoerd en met het Key Vault-geheim kan de certificaatwaarde als geheim worden opgehaald. Een Key Vault-certificaat bevat ook openbare metagegevens voor x509-certificaten. [Meer informatie](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>Exporteerbare of niet-exporteerbare sleutels
Wanneer een Key Vault-certificaat wordt gemaakt, kan het worden opgehaald uit het adresseerbare geheim met de privésleutel in een PFX- of PEM-indeling. Het beleid dat wordt gebruikt voor het maken van het certificaat moet aangeven dat de sleutel exporteerbaar is. Als het beleid aangeeft dat het niet kan worden geëxporteerd, is de privésleutel geen onderdeel van de waarde wanneer deze wordt opgehaald als geheim.

Er worden bij certificaten twee typen sleutels ondersteund: RSA- of RSA HSM. Exporteerbaar is alleen toegestaan met RSA, en wordt niet ondersteund door RSA HSM. [Meer informatie](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

Opgeslagen certificaat in Azure Key Vault kan worden geëxporteerd met behulp van Azure CLI, PowerShell of de portal.

> [!NOTE]
> Het is belangrijk te weten dat u alleen het wachtwoord van een certificaat nodig hebt als u het in de sleutelkluis importeert. In Key Vault wordt het bijbehorende wachtwoord niet opgeslagen, dus wanneer u het certificaat exporteert, is het wachtwoord leeg.

## <a name="exporting-certificate-using-cli"></a>Certificaat importeren met behulp van de CLI
Met de volgende opdracht kunt u het **openbare deel** van een Key Vault certificaat downloaden.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
[Kijk hier](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download) voor voorbeelden en parameterdefinities



Als u het hele certificaat wilt downloaden, dus **zowel het openbare als het persoonlijke deel van de van de samenstelling**, kunt u dit doen door het certificaat als een geheim te downloaden.

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
[Kijk hier](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download) voor parameterdefinities


## <a name="exporting-certificate-using-powershell"></a>Certificaat exporteren met PowerShell

Met deze opdracht wordt het certificaat met de naam TestCert01 opgehaald uit de sleutelkluis met de naam ContosoKV01. Voer de volgende opdracht uit om het certificaat als PFX-bestand te downloaden. Met deze opdrachten krijgt u toegang tot SecretId en kunt u de inhoud opslaan als een PFX-bestand.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
Hiermee wordt de hele keten met certificaten met een persoonlijke sleutel geëxporteerd. Dit certificaat wordt met een wachtwoord beveiligd.
Raadpleeg [voorbeeld 2](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0) voor meer informatie over opdracht ```Get-AzKeyVaultCertificate``` en parameters

## <a name="exporting-certificate-using-portal"></a>Certificaat exporteren met behulp van de portal

Wanneer u in de blade Certificaat een certificaat maakt/importeert, ontvangt u in de portal de melding dat het certificaat is gemaakt. Wanneer u het certificaat selecteert, kunt u op de huidige versie klikken. U ziet dan de optie om te downloaden.


Als u klikt op de knop “Downloaden in CER-indeling” of “Downloaden in PFX/PEM-indeling”, kunt u het certificaat downloaden.


![Certificaat downloaden](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>App Service Certificate exporteren vanuit de sleutelkluis

Azure App Service-certificaten bieden een handige manier om SSL-certificaten aan te schaffen en deze rechtstreeks vanuit de portal aan Azure-apps toe te wijzen. Deze certificaten kunnen ook als PFX-bestanden vanuit de portal worden geëxporteerd, zodat ze elders kunnen worden gebruikt.
Na het importeren kunnen de App Service-certificaten **zich onder geheimen bevinden**.

De stappen voor het exporteren van App Service-certificaten kunt u [hier lezen](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)

## <a name="read-more"></a>Meer informatie
* [Verschillende typen certificaatbestanden en -definities](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)
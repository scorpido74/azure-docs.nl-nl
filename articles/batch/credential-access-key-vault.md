---
title: Veilig toegang tot Key Vault met batch-Azure Batch
description: Meer informatie over hoe u via een programma toegang hebt tot uw referenties vanuit Key Vault met behulp van Azure Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192299"
---
# <a name="securely-access-key-vault-with-batch"></a>Key Vault veilig openen met Batch

In dit artikel leert u hoe u batch knooppunten instelt op veilige toegang tot referenties die zijn opgeslagen in Azure Key Vault. Het is niet mogelijk om uw beheerders referenties in Key Vault in te stellen en vervolgens de referenties voor het verkrijgen van een vaste code voor toegang tot Key Vault vanuit een script. De oplossing is het gebruik van een certificaat dat uw batch knooppunten toegang geeft tot Key Vault. Met een paar stappen kunnen we beveiligde sleutel opslag voor batch implementeren.

Als u wilt verifiëren bij Azure Key Vault van een batch-knoop punt, hebt u het volgende nodig:

- Een Azure Active Directory-Referentie (Azure AD)
- Een certificaat
- Een batch-account
- Een batch-pool met ten minste één knoop punt

## <a name="obtain-a-certificate"></a>Een certificaat verkrijgen

Als u nog geen certificaat hebt, kunt u er het beste een zelfondertekend certificaat genereren met behulp van het opdracht regel programma `makecert`.

`makecert` in dit pad vindt u meestal: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`. Open een opdracht prompt als beheerder en navigeer naar `makecert` met behulp van het volgende voor beeld.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Gebruik vervolgens het hulp programma `makecert` om zelfondertekende certificaat bestanden te maken met de naam `batchcertificate.cer` en `batchcertificate.pvk`. De gebruikte algemene naam (CN) is niet van belang voor deze toepassing, maar het is wel handig om er iets van te maken dat aangeeft waarvoor het certificaat wordt gebruikt.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Voor batch is een `.pfx`-bestand vereist. Gebruik het hulp programma [Pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) om de `.cer` en `.pvk` bestanden die door `makecert` zijn gemaakt, te converteren naar één `.pfx`-bestand.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Een service-principal maken

De toegang tot Key Vault wordt verleend aan een **gebruiker** of een **Service-Principal**. Als u toegang wilt krijgen tot Key Vault via een programma, gebruikt u een service-principal met het certificaat dat we de vorige stap hebben gemaakt.

Zie [toepassings-en Service-Principal-objecten in azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)voor meer informatie over Azure-service-principals.

> [!NOTE]
> De Service-Principal moet zich in dezelfde Azure AD-Tenant beKey Vault.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

De Url's voor de toepassing zijn niet belang rijk omdat we deze alleen gebruiken voor Key Vault toegang.

## <a name="grant-rights-to-key-vault"></a>Rechten toekennen aan Key Vault

De service-principal die in de vorige stap is gemaakt, heeft toestemming nodig om de geheimen op te halen uit Key Vault. Machtigingen kunnen worden verleend via de Azure Portal of met de Power shell-opdracht hieronder.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Een certificaat toewijzen aan een batch-account

Maak een batch-pool en ga naar het tabblad certificaat in de groep en wijs het certificaat toe dat u hebt gemaakt. Het certificaat bevindt zich nu op alle batch knooppunten.

We moeten het certificaat vervolgens toewijzen aan het batch-account. Als u het certificaat aan het account toewijst, kunnen we het toewijzen aan de groepen en vervolgens aan de knoop punten. De eenvoudigste manier om dit te doen is door naar uw batch-account in de portal te gaan, naar **certificaten**te gaan en **toevoegen**te selecteren. Upload het `.pfx` bestand dat we hebben gegenereerd in het [certificaat verkrijgen](#obtain-a-certificate) en geef het wacht woord op. Zodra het certificaat is voltooid, wordt het toegevoegd aan de lijst en kunt u de vinger afdruk controleren.

Wanneer u nu een batch-pool maakt, kunt u naar **certificaten** in de groep navigeren en het certificaat toewijzen dat u hebt gemaakt voor die groep. Wanneer u dit doet, moet u ervoor zorgen dat u **LocalMachine** selecteert voor de opslag locatie. Het certificaat wordt geladen op alle batch knooppunten in de groep.

## <a name="install-azure-powershell"></a>Azure PowerShell installeren

Als u van plan bent om toegang te krijgen tot Key Vault met behulp van Power shell-scripts op uw knoop punten, moet de Azure PowerShell-bibliotheek zijn geïnstalleerd. Er zijn enkele manieren om dit te doen als op uw knoop punten Windows Management Framework (WMF) 5 is geïnstalleerd, kunt u de opdracht install-module gebruiken om deze te downloaden. Als u knoop punten gebruikt die geen WMF 5 hebben, kunt u deze eenvoudig installeren door de Azure PowerShell `.msi` bestand te bundelen met uw batch-bestanden en vervolgens het installatie programma aan te roepen als eerste deel van het batch-opstart script. Zie dit voor beeld voor meer informatie:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Key Vault openen

Nu zijn alle instellingen voor toegang tot Key Vault in scripts die op batch knooppunten worden uitgevoerd. Als u toegang wilt krijgen tot Key Vault vanuit een script, hebt u uw script nodig om te verifiëren bij Azure AD met behulp van het certificaat. Als u dit wilt doen in Power shell, gebruikt u de volgende voorbeeld opdrachten. Geef de juiste GUID op voor de **vinger afdruk**, **App-ID** (de ID van uw service-principal) en **Tenant-id** (de Tenant waar uw Service-Principal zich bevindt).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Zodra de verificatie is uitgevoerd, hebt u toegang tot de sleutel kluis zoals u dat gewend bent.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Dit zijn de referenties die u in uw script kunt gebruiken.

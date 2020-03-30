---
title: Veilig toegang tot Key Vault met Batch - Azure Batch
description: Meer informatie over hoe u programmatisch toegang krijgt tot uw referenties vanuit Key Vault met Azure Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192299"
---
# <a name="securely-access-key-vault-with-batch"></a>Key Vault veilig openen met Batch

In dit artikel leert u hoe u Batch-knooppunten instelt om veilig toegang te krijgen tot referenties die zijn opgeslagen in Azure Key Vault. Het heeft geen zin om uw beheerdersreferenties in Key Vault te plaatsen en vervolgens instandaards te komen om toegang te krijgen tot Key Vault vanuit een script. De oplossing is om een certificaat te gebruiken waarmee uw Batch-knooppunten toegang krijgen tot Key Vault. Met een paar stappen kunnen we veilige sleutelopslag voor Batch implementeren.

Als u wilt verifiëren voor Azure Key Vault vanuit een batchknooppunt, moet u het volgende doen:

- Een Azure Active Directory-referentie (Azure AD)
- Een certificaat
- Een Batch-account
- Een batchpool met ten minste één knooppunt

## <a name="obtain-a-certificate"></a>Een certificaat verkrijgen

Als u nog geen certificaat hebt, u er een zo goed mogelijk `makecert` verkrijgen door een zelfondertekend certificaat te genereren met behulp van het opdrachtregelgereedschap.

U meestal `makecert` vinden in `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`dit pad:. Open een opdrachtprompt als beheerder `makecert` en navigeer naar het volgende voorbeeld.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Gebruik vervolgens `makecert` het hulpprogramma om zelfondertekende `batchcertificate.cer` certificaatbestanden te maken die worden aangeroepen en `batchcertificate.pvk`. De gebruikte algemene naam (CN) is niet belangrijk voor deze toepassing, maar het is handig om er iets van te maken dat u vertelt waarvoor het certificaat wordt gebruikt.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch vereist `.pfx` een bestand. Gebruik de [pvk2pfx-tool](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) om de `.cer` bestanden en `.pvk` bestanden die zijn gemaakt `makecert` in één `.pfx` bestand om te zetten.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Een service-principal maken

Toegang tot Key Vault wordt verleend aan een **gebruiker** of een **serviceprincipal.** Als u programmatisch toegang wilt krijgen tot Key Vault, gebruikt u een serviceprincipal met het certificaat dat we vorige stap hebben gemaakt.

Zie [Hoofdobjecten voor toepassingen en services in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)voor meer informatie over azure-serviceprincipals.

> [!NOTE]
> De serviceprincipal moet zich in dezelfde Azure AD-tenant bevinden als de Key Vault.

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

De URL's voor de toepassing zijn niet belangrijk omdat we ze alleen gebruiken voor Key Vault-toegang.

## <a name="grant-rights-to-key-vault"></a>Toekenning van rechten op Key Vault

De serviceprincipal die in de vorige stap is gemaakt, heeft toestemming nodig om de geheimen uit Key Vault op te halen. Toestemming kan worden verleend via de Azure-portal of met de powershell-opdracht hieronder.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Een certificaat toewijzen aan een Batch-account

Maak een batchgroep, ga vervolgens naar het certificaattabblad in de groep en wijs het certificaat toe dat u hebt gemaakt. Het certificaat bevindt zich nu op alle batchknooppunten.

Vervolgens moeten we het certificaat toewijzen aan de Batch-account. Door het certificaat toe te wijzen aan het account, kunnen we het toewijzen aan de groepen en vervolgens aan de knooppunten. De eenvoudigste manier om dit te doen is door naar uw Batch-account in de portal te gaan, naar **certificaten**te navigeren en **Toevoegen te selecteren.** Upload `.pfx` het bestand dat we hebben gegenereerd in [het verkrijgen van een certificaat](#obtain-a-certificate) en geef het wachtwoord. Zodra het certificaat is voltooid, wordt het certificaat aan de lijst toegevoegd en u de duimafdruk verifiëren.

Wanneer u nu een batchgroep maakt, u naar **Certificaten** in de groep navigeren en het certificaat dat u aan die groep hebt gemaakt toewijzen. Wanneer u dit doet, moet u ervoor zorgen dat u **LocalMachine** selecteert voor de winkellocatie. Het certificaat wordt geladen op alle batchknooppunten in de groep.

## <a name="install-azure-powershell"></a>Azure PowerShell installeren

Als u van plan bent toegang te krijgen tot Key Vault met PowerShell-scripts op uw knooppunten, moet de Azure PowerShell-bibliotheek worden geïnstalleerd. Er zijn een paar manieren om dit te doen, als uw knooppunten Windows Management Framework (WMF) 5 hebben geïnstalleerd, u de opdracht installatie-module gebruiken om het te downloaden. Als u knooppunten gebruikt die geen WMF 5 hebben, u het eenvoudigst `.msi` installeren door het Azure PowerShell-bestand te bundelen met uw Batch-bestanden en vervolgens het installatieprogramma te bellen als het eerste deel van uw batchopstartscript. Zie dit voorbeeld voor meer informatie:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Key Vault openen

Nu zijn we allemaal ingesteld om toegang te krijgen tot Key Vault in scripts die worden uitgevoerd op Batch-knooppunten. Als u Key Vault vanuit een script wilt openen, hoeft u alleen maar dat uw script zich met het certificaat verifiëren tegen Azure AD. Gebruik hiervoor de volgende voorbeeldopdrachten in PowerShell. Geef de juiste GUID op voor **Duimafdruk,** **App-id** (de id van uw serviceprincipal) en **tenant-id** (de tenant waar de serviceprincipal bestaat).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Zodra u bent geverifieerd, opent u KeyVault zoals u dat normaal zou doen.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Dit zijn de referenties om te gebruiken in uw script.

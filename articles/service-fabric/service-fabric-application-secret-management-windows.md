---
title: Een versleutelingscertificaat instellen op Windows-clusters
description: Meer informatie over het instellen van een versleutelingscertificaat en het versleutelen van geheimen op Windows-clusters.
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d9413a37be221adc375836719dc1f467a5571fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610179"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Een versleutelingscertificaat instellen en geheimen versleutelen op Windows-clusters
In dit artikel ziet u hoe u een versleutelingscertificaat instelt en gebruikt om geheimen op Windows-clusters te versleutelen. Zie Voor Linux-clusters [het instellen van een versleutelingscertificaat en het versleutelen van geheimen op Linux-clusters.][secret-management-linux-specific-link]

[Azure Key Vault][key-vault-get-started] wordt hier gebruikt als een veilige opslaglocatie voor certificaten en als een manier om certificaten te installeren op Service Fabric-clusters in Azure. Als u niet implementeert naar Azure, hoeft u Key Vault niet te gebruiken om geheimen in Service Fabric-toepassingen te beheren. Het *gebruik van* geheimen in een toepassing is echter cloudplatform-agnostisch om toepassingen te kunnen implementeren in een cluster dat overal wordt gehost. 

## <a name="obtain-a-data-encipherment-certificate"></a>Een certificaat voor gegevensvercijfering verkrijgen
Een certificaat voor gegevensversleuteling wordt strikt gebruikt voor versleuteling en decryptie van [parameters][parameters-link] in de instellingen.xml en [omgevingsvariabelen][environment-variables-link] van een service in serviceManifest.xml van een service. Het wordt niet gebruikt voor authenticatie of ondertekening van cijfertekst. Het certificaat moet aan de volgende eisen voldoen:

* Het certificaat moet een privésleutel bevatten.
* Het certificaat moet worden gemaakt voor sleuteluitwisseling, exporteerbaar naar een bestand voor persoonlijke informatie-uitwisseling (.pfx).
* Het gebruik van de certificaatsleutel moet gegevenscodering (10) bevatten en mag geen serververificatie of clientverificatie bevatten. 
  
  Wanneer u bijvoorbeeld een zelfondertekend certificaat maakt `KeyUsage` met PowerShell, `DataEncipherment`moet de vlag worden ingesteld op:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Het certificaat in uw cluster installeren
Dit certificaat moet op elk knooppunt in het cluster worden geïnstalleerd. Bekijk [hoe u een cluster maakt met Azure Resource Manager][service-fabric-cluster-creation-via-arm] voor installatie-instructies. 

## <a name="encrypt-application-secrets"></a>Toepassingsgeheimen versleutelen
De volgende PowerShell-opdracht wordt gebruikt om een geheim te versleutelen. Met deze opdracht wordt alleen de waarde versleuteld. het ondertekent **niet** de cijfertekst. U moet hetzelfde vercijferingscertificaat gebruiken dat in uw cluster is geïnstalleerd om cijfertekst voor geheime waarden te produceren:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

De resulterende basis-64 gecodeerde tekenreeks bevat zowel de geheime cijfertekst als informatie over het certificaat dat werd gebruikt om het te versleutelen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [opgeven van versleutelde geheimen in een toepassing.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application

---
title: Certificaat inhoud converteren naar base-64-Azure HDInsight
description: De inhoud van een Service-Principal-certificaat converteren naar een met base 64 gecodeerde teken reeks indeling in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f4974d9e9a2ff3b63b36e45d406a016078bb290
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008162"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>De inhoud van een Service-Principal-certificaat converteren naar een met base 64 gecodeerde teken reeks notatie in HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Er wordt een fout bericht weer gegeven met de melding dat de invoer geen geldige base-64-teken reeks is, omdat deze een niet-base-64 teken bevat, meer dan twee opvullings tekens of een niet-spatie teken tussen de opvul tekens.

## <a name="cause"></a>Oorzaak

Wanneer u een Power shell-of Azure-sjabloon implementatie gebruikt om clusters te maken met Data Lake als primaire of extra opslag ruimte, is de inhoud van het Service-Principal-certificaat voor toegang tot het Data Lake Storage-account in de basis-64-indeling. Een onjuiste conversie van de PFX-certificaat inhoud naar een met base 64 gecodeerde teken reeks kan leiden tot deze fout.

## <a name="resolution"></a>Oplossing

Als u het certificaat van de Service-Principal in PFX-indeling hebt (Zie [hier](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) voor voor beelden van de stappen voor het maken van een service-principal), gebruikt u de volgende Power shell-opdracht of C#-code om de certificaat inhoud te converteren naar de indeling base-64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).

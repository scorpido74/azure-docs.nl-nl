---
title: Certificaat-inhoud converteren naar basis-64 - Azure HDInsight
description: Inhoud van serviceprincipal-certificaat converteren naar basis-64 gecodeerde tekenreeksindeling in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: d6119e4f8c651ba482a24f46b44ff15f870858ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894175"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Inhoud van serviceprincipal certificaat converteren naar basis-64 gecodeerde tekenreeksindeling in HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

U ontvangt een foutbericht waarin staat dat de invoer geen geldige Base-64-tekenreeks is, omdat deze een niet-base 64-teken, meer dan twee opvullingstekens of een niet-wit spatieteken tussen de opvullingstekens bevat.

## <a name="cause"></a>Oorzaak

Wanneer u PowerShell- of Azure-sjabloonimplementatie gebruikt om clusters met Data Lake als primaire of aanvullende opslag te maken, bevindt de hoofdcertificaatinhoud van de service om toegang te krijgen tot het Data Lake-opslagaccount in de base-64-indeling. Onjuiste omzetting van de inhoud van het PFX-certificaat naar de basis-64 gecodeerde tekenreeks kan tot deze fout leiden.

## <a name="resolution"></a>Oplossing

Zodra u het serviceprincipal certificaat in pfx-indeling hebt (zie [hier](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) voor voorbeeldserviceprincipalcreatiestappen), gebruikt u de volgende PowerShell-opdracht of C#-fragment om de certificaatinhoud om te zetten in de base-64-indeling.

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

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).

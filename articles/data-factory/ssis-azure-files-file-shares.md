---
title: Bestanden openen en opslaan met SSIS-pakketten die zijn geïmplementeerd in Azure
description: Meer informatie over het openen en opslaan van bestanden op locatie en in Azure wanneer u SSIS-pakketten optilt en verplaatst die lokale bestandssystemen gebruiken in SSIS in Azure
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 630a8ead409ad60b9146838f062210a3301b43b5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760197"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Bestanden op locatie en in Azure openen en opslaan met SSIS-pakketten die zijn geïmplementeerd in Azure

In dit artikel wordt beschreven hoe u bestanden op locatie en in Azure openen en opslaan wanneer u SSIS-pakketten optilt en verplaatst die lokale bestandssystemen gebruiken in SSIS in Azure.

## <a name="save-temporary-files"></a>Tijdelijke bestanden opslaan

Als u tijdelijke bestanden moet opslaan en verwerken tijdens één pakketuitvoering,`.`kunnen pakketten`%TEMP%`de huidige werkmap ( ) of tijdelijke map ( ) van uw Azure-SSIS Integration Runtime-knooppunten gebruiken.

## <a name="use-on-premises-file-shares"></a>On-premises bestandsshares gebruiken

Ga als volgt te werk om **on-premises bestandsshares** te blijven gebruiken wanneer u pakketten opheft en verplaatst die lokale bestandssystemen gebruiken in SSIS in Azure, de volgende dingen:

1. Bestanden overbrengen van lokale bestandssystemen naar on-premises bestandsshares.

2. Neem deel aan de on-premises bestandsshares tot een virtueel Azure-netwerk.

3. Sluit uw Azure-SSIS IR aan op hetzelfde virtuele netwerk. Zie [Runtime van Azure-SSIS-integratie deelnemen aan een virtueel netwerk voor](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)meer informatie.

4. Verbind uw Azure-SSIS IR met de on-premises bestandsshares binnen hetzelfde virtuele netwerk door toegangsreferenties in te stellen die Windows-verificatie gebruiken. Zie [Verbinding maken met gegevens en bestandsshares met Windows Authentication voor](ssis-azure-connect-with-windows-auth.md)meer informatie.

5. Werk lokale bestandspaden in uw pakketten bij naar UNC-paden die verwijzen naar on-premises bestandsshares. Bijvoorbeeld bijwerken `C:\abc.txt` naar `\\<on-prem-server-name>\<share-name>\abc.txt`.

## <a name="use-azure-file-shares"></a>Azure-bestandsshares gebruiken

Ga als volgt te werk om **Azure Files** te gebruiken wanneer u pakketten opheft en verplaatst die lokale bestandssystemen gebruiken in SSIS in Azure:

1. Bestanden overbrengen van lokale bestandssystemen naar Azure-bestanden. Zie [Azure Files](https://azure.microsoft.com/services/storage/files/)voor meer informatie.

2. Verbind uw Azure-SSIS IR met Azure Files door toegangsreferenties in te stellen die windows-verificatie gebruiken. Zie [Verbinding maken met gegevens en bestandsshares met Windows Authentication voor](ssis-azure-connect-with-windows-auth.md)meer informatie.

3. Werk lokale bestandspaden in uw pakketten bij naar UNC-paden die naar Azure-bestanden verwijzen. Bijvoorbeeld bijwerken `C:\abc.txt` naar `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt`.

## <a name="next-steps"></a>Volgende stappen

- Zet uw pakketten in. Zie [Een SSIS-project implementeren naar Azure met SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)voor meer informatie.
- Voer je pakjes uit. Zie [SSIS-pakketten uitvoeren in Azure met SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)voor meer informatie.
- Plan uw pakketten. Zie [SSIS-pakketten plannen in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)voor meer informatie.

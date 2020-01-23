---
title: Ondersteuningsmatrix voor SAP HANA Backup
description: In dit artikel vindt u informatie over de ondersteunde scenario's en beperkingen wanneer u Azure Backup gebruikt om back-ups te maken van SAP HANA-data bases op Azure-Vm's.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a0a7c25ec718dcd6a903d2149a8b3930fb25941e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514287"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Ondersteuningsmatrix voor back-up van SAP HANA-databases in virtuele Azure-machines

Azure Backup ondersteunt de back-up van SAP HANA-data bases naar Azure. In dit artikel vindt u een overzicht van de scenario's die worden ondersteund en beperkingen die aanwezig zijn wanneer u Azure Backup gebruikt om back-ups te maken van SAP HANA-data bases op virtuele

## <a name="onboard-to-the-public-preview"></a>Onboarding voor de open bare preview

Onboarding voor de open bare preview als volgt:

* Registreer uw abonnements-ID bij de provider van de Recovery Services-service in de portal door [dit artikel te volgen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Voer deze cmdlet uit voor Power shell. Deze moet worden voltooid als ' geregistreerd '.

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> De frequentie van de back-uplogboeken kan nu worden ingesteld op mini maal 15 minuten. Logboek back-ups gaan alleen naar de stroom nadat een volledige back-up voor de data base is voltooid.

## <a name="scenario-support"></a>Scenario-ondersteuning

| **Scenario**               | **Ondersteunde configuraties**                                | **Niet-ondersteunde configuraties**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologie**               | Alleen SAP HANA die worden uitgevoerd in virtuele machines van Azure Linux                    | HANA grote instanties (HLI)                                   |
| **Geografische gebieden**                   | Australië Zuid-Oost, Oost-Australië Brazilië-zuid Canada-centraal, Canada-oost Zuid-Azië-oost, Azië-oost vs-Oost, VS-Oost 2, West-Centraal VS, VS-West, VS-West 2, Noord-Centraal VS, VS-midden, Zuid-Centraal VS India centraal, India Zuid Japan Oost, Japan St Korea-centraal, Korea-zuid Europa-noord, Europa-west UK-zuid, UK-west | Australië-centraal, Australië-centraal 2 China-oost, China-noord, China Oost2, China-noord 2 West-India Frankrijk-centraal, Frankrijk-zuid Duitsland-noord, Duitsland-west-centraal Zwitserland-noord, Zwitserland-west Zuid-Afrika-noord, Zuid-Afrika-west, UAE-noord, VAE Central l Azure Government regio's |
| **Versies van besturings systemen**            | SLES 12 met SP2, SP3 of SP4           | SLES 15, RHEL                                                |
| **HANA-versies**          | Dit SDC op HANA 1. x, MDC op HANA 2. x < = SPS04 Rev 44           | -                                                            |
| **HANA-implementaties**       | SAP HANA op één Azure VM-alleen omhoog schalen               | Uitschalen                                                    |
| **HANA-instanties**         | Eén SAP HANA-exemplaar op één Azure VM: alleen omhoog schalen | Meerdere exemplaren van SAP HANA op één virtuele machine                  |
| **HANA-database typen**    | Individuele database container (dit SDC) op 1. x, meerdere database container (MDC) op 2. x | MDC in HANA 1. x                                              |
| **HANA-database grootte**     | 2-TB volledige back-upgrootte zoals gerapporteerd door HANA) |                                                              |
| **Back-uptypen**           | Volledige, differentiële en logboek back-ups                           | Incrementeel, moment opnamen                                       |
| **Typen herstellen**          | Raadpleeg de SAP HANA opmerking [1642148](https://launchpad.support.sap.com/#/notes/1642148) voor meer informatie over de ondersteunde typen herstel bewerkingen |                                                              |
| **Back-uplimieten**          | Maxi maal 2 TB volledige back-upgrootte per SAP HANA-exemplaar  |                                                              |
| **Speciale configuraties** |                                                              | SAP HANA en dynamische lagen <br>  Klonen via LaMa            |

------

> [!NOTE]
> Back-up-en herstel bewerkingen van SAP HANA systeem eigen clients (SAP HANA Studio/cockpit/DBA-cockpit) worden momenteel niet ondersteund.



## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van back-ups SAP Hana data bases die worden uitgevoerd op virtuele machines](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Meer informatie over het [herstellen van SAP Hana-data bases die worden uitgevoerd op virtuele Azure-machines](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Meer informatie over het [beheren van SAP Hana databases waarvan een back-up is gemaakt met behulp van Azure backup](sap-hana-db-manage.md)
* Informatie over het [oplossen van veelvoorkomende problemen bij het maken van back-ups van SAP Hana-data bases](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)

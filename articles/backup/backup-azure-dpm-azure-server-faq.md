---
title: Veelgestelde vragen over Azure Backup Server en DPM
description: Ontdek in dit artikel antwoorden op veelgestelde vragen over de Microsoft Azure Backup Server (MABS) en DPM (Data Protection Manager).
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: 35957a1e8a3d6c3d9be06d9d44dbcd47efa0e6ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173165"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server en DPM - Veelgestelde vragen

## <a name="general-questions"></a>Algemene vragen

In dit artikel worden veelgestelde vragen beantwoord over de Azure Backup Server en DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Kan ik de Azure Backup-server gebruiken om een BMR-back-up (Bare Metal Recovery) te maken voor een fysieke server?

Ja.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Kan ik de server registreren bij meerdere kluizen?

Nee. Een DPM- of Azure Backup-server kan slechts op één kluis worden geregistreerd.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Kan ik DPM gebruiken om een back-up te maken van apps in Azure Stack?

Nee. U Azure Backup gebruiken om Azure Stack te beschermen, Azure Backup biedt geen ondersteuning voor het gebruik van DPM om een back-up te maken van apps in Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Als ik Azure Backup-agent heb geïnstalleerd om mijn bestanden en mappen te beveiligen, kan ik dan System Center DPM installeren om een back-up te maken van on-premises workloads naar Azure?

Ja. Maar u moet eerst DPM instellen en vervolgens de Azure Backup-agent installeren.  Het installeren van onderdelen in deze volgorde zorgt ervoor dat de Azure Backup-agent werkt met DPM. Het installeren van de agent voor het installeren van DPM wordt niet aangeraden of ondersteund.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Waarom kan ik geen externe DPM-server toevoegen na het installeren van UR7 en de nieuwste Azure Backup-agent?

Voor de DPM-servers met gegevensbronnen die zijn beveiligd naar de cloud (door een updaterollup eerder te gebruiken dan Update Rollup 7), moet u ten minste één dag wachten na het installeren van de UR7 en de nieuwste Azure Backup-agent om **externe DPM-server toevoegen**te starten. De periode van één dag is nodig om de metagegevens van de DPM-beveiligingsgroepen naar Azure te uploaden. Metagegevens van beveiligingsgroepen worden de eerste keer geüpload via een nachtelijke taak.

## <a name="vmware-and-hyper-v-backup"></a>VMware en Hyper-V back-up

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Kan ik een back-up maken van VMware vCenter-servers naar Azure?

Ja. U Azure Backup Server gebruiken om een back-up te maken van VMware vCenter Server- en ESXi-hosts in Azure.

- [Meer informatie](backup-mabs-protection-matrix.md) over ondersteunde versies.
- [Volg deze stappen](backup-azure-backup-server-vmware.md) om een back-up van een VMware-server te maken.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Heb ik een aparte licentie nodig om een volledig on-premises VMware/Hyper-V-cluster te herstellen?

U hebt geen aparte licenties nodig voor VMware/Hyper-V-beveiliging.

- Als u een system center-klant bent, gebruikt u System Center Data Protection Manager (DPM) om Vm's van VMware te beschermen.
- Als u geen System Center-klant bent, u Azure Backup Server (pay-as-you-go) gebruiken om VMware VM's te beschermen.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Kan ik een SharePoint-item herstellen naar de oorspronkelijke locatie als SharePoint is geconfigureerd met SQL AlwaysOn (met beveiliging op schijf)?

Ja, het item kan worden hersteld naar de oorspronkelijke SharePoint-site.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Kan ik een SharePoint-database herstellen naar de oorspronkelijke locatie als SharePoint is geconfigureerd met SQL AlwaysOn?

Omdat SharePoint-databases zijn geconfigureerd in SQL AlwaysOn, kunnen ze alleen worden gewijzigd als de beschikbaarheidsgroep is verwijderd. Als gevolg hiervan kan DPM een database niet herstellen naar de oorspronkelijke locatie. U een SQL Server-database herstellen naar een andere SQL Server-instantie.

## <a name="next-steps"></a>Volgende stappen

Lees de andere veelgestelde vragen:

- [Meer informatie](backup-support-matrix-mabs-dpm.md) over Azure Backup Server en DPM-ondersteuningsmatrix.
- [Meer informatie](backup-azure-mabs-troubleshoot.md) over de richtlijnen voor azure backup server en DPM-probleemoplossing.

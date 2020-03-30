---
title: Storage FSLogix-profielcontainer Windows Virtual Desktop - Azure
description: Opties voor het opslaan van uw Windows Virtual Desktop FSLogix-profiel op Azure Storage.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71ba24784dee7771acbe19bf0261c7dc02478b24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127515"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Opslagopties voor FSLogix-profielcontainers in Windows Virtual Desktop

Azure biedt meerdere opslagoplossingen die u gebruiken om uw FSLogix-profielcontainer op te slaan. In dit artikel worden opslagoplossingen vergeleken die Azure biedt voor windows Virtual Desktop FSLogix-gebruikersprofielcontainers.

Windows Virtual Desktop biedt FSLogix-profielcontainers als de aanbevolen gebruikersprofieloplossing. FSLogix is ontworpen om te roamen met profielen in externe computeromgevingen, zoals Windows Virtual Desktop. Bij aanmelding is deze container dynamisch verbonden met de computeromgeving met behulp van een native ondersteunde Virtual Hard Disk (VHD) en een Hyper-V Virtual Hard Disk (VHDX). Het gebruikersprofiel is onmiddellijk beschikbaar en verschijnt in het systeem precies als een native gebruikersprofiel.

In de volgende tabellen worden de opslagoplossingen vergeleken die Azure Storage biedt voor gebruikersprofielen van Windows Virtual Desktop FSLogix-profielcontainer.

## <a name="azure-platform-details"></a>Azure-platformgegevens

|Functies|Azure Files|Azure NetApp Files|Opslagruimten direct|
|--------|-----------|------------------|---------------------|
|Gebruiksvoorbeeld|Algemeen doel|Ultraprestaties of migratie van On-premises van NetApp|Platformoverschrijdend|
|Platformservice|Ja, Azure-native oplossing|Ja, Azure-native oplossing|Nee, zelfgestuurd|
|Regionale beschikbaarheid|Alle regio's|[Regio's selecteren](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Alle regio's|
|Redundantie|Lokaal overbodig/zone-redundant/geo-redundant|Lokaal overbodig|Lokaal overbodig/zone-redundant/geo-redundant|
|Lagen en prestaties|Standard<br>Premium<br>Tot max 100k IOPS per aandeel met 5 GBps per aandeel bij ongeveer 3 ms latency|Standard<br>Premium<br>Ultra<br>Tot 320k (16K) IOPS met 4,5 GBps per volume bij ongeveer 1 ms latency|Standaard HDD: tot 500 IOPS per schijflimieten<br>Standaard SSD: tot 4k IOPS per schijflimieten<br>Premium SSD: tot 20k IOPS per schijf limieten<br>We raden Premium-schijven aan voor Storage Spaces Direct|
|Capaciteit|100 TiB per aandeel|100 TiB per volume, tot 12,5 PiB per abonnement|Maximaal 32 TiB per schijf|
|Vereiste infrastructuur|Minimale aandelengrootte 1 GiB|Minimale capaciteit pool 4 TiB, min volume grootte 100 GiB|Twee VM's op Azure IaaS (+ Cloud Witness) of ten minste drie VM's zonder en kosten voor schijven|
|Protocollen|SMB 2.1/3. en REST|NFSv3, NFSv4.1 (voorbeeld), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Azure-beheergegevens

|Functies|Azure Files|Azure NetApp Files|Opslagruimten direct|
|--------|-----------|------------------|---------------------|
|Toegang|Cloud, on-premises en hybride (Azure-bestandssynchronisatie)|Cloud, on-premises (via ExpressRoute)|Cloud, on-premises|
|Back-up|Azure-back-upmomentopnameintegratie|Momentopnamen van Azure NetApp-bestanden|Azure-back-upmomentopnameintegratie|
|Beveiliging en naleving|[Alle door Azure ondersteunde certificaten](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO voltooid|[Alle door Azure ondersteunde certificaten](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integratie van Azure Active Directory|[Native Active Directory en Azure Active Directory Domain Services](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Azure Active Directory Domain Services en Native Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Alleen ondersteuning voor Native Active Directory of Azure Active Directory Domain Services|

Zodra u uw opslagmethode hebt gekozen, raadpleegt u [de prijzen van Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) voor informatie over onze prijsplannen.

## <a name="next-steps"></a>Volgende stappen

Zie de tabel in [FSLogix-profielcontainers en Azure-bestanden](fslogix-containers-azure-files.md)voor meer informatie over FSLogix-profielcontainers, gebruikersprofielschijven en andere gebruikersprofieltechnologieën.

Als je klaar bent om je eigen FSLogix-profielcontainers te maken, ga je aan de slag met een van deze tutorials:

- [Aan de slag met FSLogix-profielcontainers op Azure Files in Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Een FSLogix-profielcontainer maken voor een hostgroep met Azure NetApp-bestanden](create-fslogix-profile-container.md)
- De instructies in [Een direct scale-outbestandsserver met twee kopjes implementeren voor UPD-opslag in Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) zijn ook van toepassing wanneer u een FSLogix-profielcontainer gebruikt in plaats van een gebruikersprofielschijf

U ook vanaf het begin beginnen en uw eigen Windows Virtual Desktop-oplossing instellen bij [Een tenant maken in Windows Virtual Desktop.](tenant-setup-azure-active-directory.md)

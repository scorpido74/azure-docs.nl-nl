---
title: Beveiligings functies die kunnen worden gebruikt met Azure Storage | Microsoft Docs
description: Dit artikel bevat een overzicht van de belangrijkste Azure-beveiligings functies die kunnen worden gebruikt met Azure Storage.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: e273d913d07d8d7b77289695a4a28a478a64123a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350304"
---
# <a name="azure-storage-security-overview"></a>Overzicht van Azure Storage beveiliging

Dit artikel bevat een overzicht van de Azure-beveiligings functies die u kunt gebruiken met Azure Storage. Azure Storage is de oplossing voor opslag in de cloud voor moderne toepassingen die afhankelijk zijn van duurzaamheid, beschikbaarheid en schaalbaarheid om te voldoen aan de behoeften van klanten. Azure Storage biedt een uitgebreide reeks beveiligings mogelijkheden. U kunt:

* Beveilig het opslag account met behulp van op rollen gebaseerde Access Control (RBAC) en Azure Active Directory.
* Data-in-transit beveiligen tussen een toepassing en Azure met behulp van versleuteling aan de clientzijde, HTTPS of SMB 3.0.
* Stel in dat gegevens automatisch moeten worden versleuteld wanneer deze naar Azure Storage worden geschreven met behulp van Storage Service Encryption.
* Stel besturings systeem-en gegevens schijven die worden gebruikt door virtuele machines (Vm's) die moeten worden versleuteld met behulp van Azure Disk Encryption.
* Verleen toegang tot de gegevens objecten in Azure Storage met behulp van hand tekeningen voor gedeelde toegang (SASs).
* Gebruik Analytics om de verificatie methode bij te houden die door iemand wordt gebruikt bij het openen van opslag.

Raadpleeg de [Azure Storage-beveiligings handleiding](../../storage/common/storage-security-guide.md)voor een gedetailleerde beschrijving van de beveiliging in azure Storage. Deze hand leiding bevat een grondige uitleg van de beveiligings functies van Azure Storage. Deze functies omvatten opslag-account sleutels, gegevens versleuteling in overdracht en op rest en opslag analyse.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

U kunt uw opslag account beveiligen door gebruik te maken van Access Control op basis van rollen. Het beperken van de toegang op basis van de beveiligings principes van de [nood zaak om te kennen](https://en.wikipedia.org/wiki/Need_to_know) , is van cruciaal belang voor organisaties [die beveiligings beleid](https://en.wikipedia.org/wiki/Principle_of_least_privilege) voor gegevens toegang willen afdwingen. Deze toegangs rechten worden verleend door de juiste RBAC-rol toe te wijzen aan groepen en toepassingen bij een bepaald bereik. U kunt [Ingebouwde RBAC-rollen](/azure/role-based-access-control/built-in-roles), zoals Inzender voor opslag accounts, gebruiken om machtigingen toe te wijzen aan gebruikers.

Meer informatie:

* [Azure Active Directory op basis van rollen Access Control](/azure/role-based-access-control/role-assignments-portal)

## <a name="delegated-access-to-storage-objects"></a>Gedelegeerde toegang tot opslag objecten

Een shared access signature biedt gedelegeerde toegang tot resources in uw opslagaccount. De SAS betekent dat u een client beperkte machtigingen kunt verlenen voor objecten in uw opslag account voor een opgegeven periode en met een opgegeven set machtigingen. U kunt deze beperkte machtigingen verlenen zonder dat u de toegangs sleutels van uw account hoeft te delen.

De SAS is een URI die in de query parameters omvat alle informatie die nodig is voor geverifieerde toegang tot een opslag resource. Om toegang te krijgen tot opslag bronnen met de SAS, hoeft de client alleen de SAS te voorzien van de juiste constructor of methode.

Meer informatie:

* [Informatie over het SAS-model](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
* [Een SAS maken en gebruiken met Blob Storage](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

## <a name="encryption-in-transit"></a>Versleuteling in transit

Versleuteling in transit is een mechanisme voor het beveiligen van gegevens wanneer deze worden verzonden tussen netwerken. Met Azure Storage kunt u gegevens beveiligen met behulp van:

* [Versleuteling op transport niveau](/azure/storage/common/storage-security-guide#encryption-in-transit), zoals https, wanneer u gegevens overbrengt naar of van Azure Storage.
* [Wire-versleuteling](/azure/storage/common/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), zoals SMB 3,0-versleuteling, voor Azure-bestands shares.
* [Versleuteling aan de client zijde](/azure/storage/common/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), voor het versleutelen van de gegevens voordat deze naar de opslag wordt overgebracht en voor het ontsleutelen van de gegevens nadat deze buiten de opslag zijn overgedragen.

Meer informatie over versleuteling aan de client zijde:

* [Versleuteling aan client zijde voor Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* 0Cloud voor beveiligings controle van de @no__t: Gegevens in transit versleutelen @ no__t-0

## <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Voor veel organisaties is [gegevens versleuteling in rust](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) een verplichte stap naar de privacy, naleving en gegevens soevereiniteit van gegevens. Drie Azure-functies bieden versleuteling van gegevens die zich op rest bevinden:

* [Storage service Encryption](/azure/storage/common/storage-security-guide#encryption-at-rest) is altijd ingeschakeld en de gegevens van de opslag service worden automatisch versleuteld wanneer deze naar Azure Storage worden geschreven.
* [Versleuteling aan de client zijde](/azure/storage/common/storage-security-guide#client-side-encryption) biedt ook de mogelijkheid om op rest te versleutelen.
* Met [Azure Disk Encryption](/azure/storage/common/storage-security-guide#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) kunt u de besturingssysteem schijven en gegevens schijven versleutelen die door een virtuele IaaS-machine worden gebruikt.

Meer informatie over Storage Service Encryption:

* [Versleuteling](https://azure.microsoft.com/services/storage/) van de Azure Storage-service is beschikbaar voor [Azure Blob-opslag](https://azure.microsoft.com/services/storage/blobs/). Zie [Azure files](https://azure.microsoft.com/services/storage/files/), [Table Storage](https://azure.microsoft.com/services/storage/tables/)en [Queue Storage](https://azure.microsoft.com/services/storage/queues/)voor meer informatie over andere Azure-opslag typen.
* [Versleuteling van Azure Storage-service voor Data-at-rest](/azure/storage/common/storage-service-encryption)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption voor virtuele machines helpt u bij het oplossen van vereisten voor de beveiliging en naleving van organisaties. Uw VM-schijven (inclusief opstart-en gegevens schijven) worden versleuteld met behulp van sleutels en beleids regels die u in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)beheert.

Schijf versleuteling voor Vm's werkt voor Linux-en Windows-besturings systemen. Er wordt ook Key Vault gebruikt om u te helpen bij het beveiligen, beheren en controleren van het gebruik van uw schijf versleutelings sleutels. Alle gegevens in uw VM-schijven worden op rest versleuteld met behulp van de industrie standaard versleutelings technologie in uw Azure-opslag accounts. De oplossing voor schijf versleuteling voor Windows is gebaseerd op [micro soft BitLocker-stationsversleuteling](https://technet.microsoft.com/library/cc732774.aspx)en de Linux-oplossing is gebaseerd op [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Meer informatie

* [Overzicht van Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)

## <a name="firewalls-and-virtual-networks"></a>Firewalls en virtuele netwerken

Met Azure Storage kunt u firewall regels inschakelen voor uw opslag accounts. Zodra deze functie is ingeschakeld, worden binnenkomende aanvragen voor gegevens geblokkeerd, met inbegrip van aanvragen van andere Azure-Services. U kunt uitzonde ringen configureren om verkeer toe te staan. Firewall regels kunnen worden ingeschakeld op bestaande opslag accounts of tijdens de aanmaak tijd.

Gebruik deze functie om uw opslag accounts te beveiligen tot een specifieke set toegestane netwerken.

Voor meer informatie over Azure Storage-firewalls en virtuele netwerken raadpleegt u het artikel [Azure Storage firewalls en virtuele netwerken configureren](/azure/storage/common/storage-network-security)

## <a name="azure-data-box"></a>Azure Data Box

Met apparaten met Data Box, Data Box Disk of Data Box Heavy kunt u grote hoeveelheden gegevens overdragen naar Azure als overdracht via uw netwerk geen optie is. Deze apparaten voor offline gegevens overdracht worden verzonden tussen uw organisatie en het Azure-Data Center. De apparaten maken gebruik van AES-versleuteling om uw gegevens tijdens de overdracht te beveiligen en ondergaan na het uploaden een grondig opschoningsproces om uw gegevens van het apparaat te verwijderen.

Data Box Edge en Data Box Gateway zijn producten voor onlinegegevensoverdracht die fungeren als netwerkopslaggateways om gegevens tussen uw site en Azure te beheren. Met Data Box Edge, een on-premises netwerkapparaat, worden gegevens overdragen van en naar Azure en worden gegevens verwerkt met behulp van Edge-rekenprocessen met artificial intelligence (AI). Data Box Gateway is een virtueel apparaat met opslaggatewaymogelijkheden.

Meer informatie:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview)
* [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview)

## <a name="advanced-threat-protection"></a>Advanced Threat Protection

Azure Storage biedt geavanceerde beveiliging tegen bedreigingen voor een extra beveiligingslaag die ongebruikelijke en mogelijk schadelijke pogingen van toegang tot uw opslag account detecteert of misbruikt. Advanced Threat Protection bewaakt Azure Storage Diagnostische logboeken voor verdachte Lees-, schrijf-of verwijderings aanvragen voor Blob-opslag.

Geavanceerde beveiligings waarschuwingen voor bedreigingen kunnen worden weer gegeven vanuit [Azure Security Center](https://azure.microsoft.com/services/security-center/). Azure Security Center biedt Details over verdachte activiteiten die zijn gedetecteerd en raadt acties aan om de mogelijke dreiging te onderzoeken en te herstellen.

Meer informatie:

* [Overzicht van Azure Storage Advanced Threat Protection](/azure/storage/common/storage-advanced-threat-protection)

## <a name="azure-key-vault"></a>Azure Key Vault

Azure Disk Encryption gebruikt [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) om de coderings sleutels en geheimen van de schijf in uw sleutel kluis abonnement te controleren en te beheren. Het zorgt er ook voor dat alle gegevens in de virtuele-machine schijven op rest worden versleuteld in Azure Storage. U moet Key Vault gebruiken om sleutels en het gebruik van het beleid te controleren.

Meer informatie

* [Wat is Azure Key Vault?](/azure/key-vault/key-vault-overview)

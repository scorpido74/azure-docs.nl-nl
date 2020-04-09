---
title: Veelgestelde vragen (FAQ) voor Azure Files | Microsoft Documenten
description: Antwoorden vinden op veelgestelde vragen over Azure Files.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: c6062ec008a7a12c720cf28c2d79531e805ebba0
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984432"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Lees de veelgestelde vragen (FAQ) over Azure Files
[Azure Files](storage-files-introduction.md) biedt volledig beheerde bestandsshares in de cloud die toegankelijk zijn via het industriestandaard [Server Message Block (SMB)-protocol.](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) U Azure-bestandsshares gelijktijdig monteren op cloud- of on-premises implementaties van Windows, Linux en macOS. U azure-bestandsshares ook cacheopslaan op Windows Server-machines door Azure File Sync te gebruiken voor snelle toegang in de buurt van waar de gegevens worden gebruikt.

In dit artikel worden veelgestelde vragen beantwoord over de functies en functionaliteit van Azure Files, waaronder het gebruik van Azure File Sync met Azure Files. Als u het antwoord op uw vraag niet ziet, u contact met ons opnemen via de volgende kanalen (in escalerende volgorde):

1. De reacties sectie van dit artikel.
2. [Azure Storage Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [UserVoice van Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft-ondersteuning. Als u een nieuw ondersteuningsverzoek wilt maken, selecteert u in de Azure-portal op het tabblad **Help** de knop **Help + ondersteuning** en selecteert u Vervolgens Nieuw **ondersteuningsverzoek**.

## <a name="general"></a>Algemeen
* <a id="why-files-useful"></a>
  **Hoe is Azure Files nuttig?**  
   U Azure Files gebruiken om bestandsshares in de cloud te maken, zonder dat u verantwoordelijk bent voor het beheer van de overhead van een fysieke server, apparaat of toestel. Wij doen het monotone werk voor u, met inbegrip van het toepassen van OS-updates en het vervangen van slechte schijven. Zie [Waarom Azure-bestanden nuttig is](storage-files-introduction.md#why-azure-files-is-useful)voor meer informatie over de scenario's waarmee Azure Files u kan helpen.

* <a id="file-access-options"></a>
  **Wat zijn verschillende manieren om toegang te krijgen tot bestanden in Azure Files?**  
    U de bestandsshare op uw lokale machine monteren met behulp van het SMB 3.0-protocol, of u hulpprogramma's zoals [Storage Explorer](https://storageexplorer.com/) gebruiken om toegang te krijgen tot bestanden in uw bestandsshare. Vanuit uw toepassing u opslagclientbibliotheken, REST-API's, PowerShell of Azure CLI gebruiken om toegang te krijgen tot uw bestanden in het Azure-bestandsshare.

* <a id="what-is-afs"></a>
  **Wat is Azure File Sync?**  
    U Azure File Sync gebruiken om de bestandsshares van uw organisatie in Azure-bestanden te centraliseren, terwijl de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver behouden blijven. Azure File Sync transformeert uw Windows Server-machines in een snelle cache van uw Azure-bestandsshare. U elk protocol dat beschikbaar is op Windows Server gebruiken om lokaal toegang te krijgen tot uw gegevens, waaronder SMB, Network File System (NFS) en FtpS (File Transfer Protocol Service). Je zoveel caches hebben als je nodig hebt over de hele wereld.

* <a id="files-versus-blobs"></a>
  **Waarom zou ik een Azure-bestandsshare versus Azure Blob-opslag voor mijn gegevens gebruiken?**  
    Azure Files en Azure Blob-opslag bieden beide manieren om grote hoeveelheden gegevens in de cloud op te slaan, maar ze zijn handig voor iets andere doeleinden. 
    
    Azure Blob-opslag is handig voor grootschalige cloud-native toepassingen die ongestructureerde gegevens moeten opslaan. Om de prestaties en schaal te maximaliseren, is Azure Blob-opslag een eenvoudigere opslagabstractie dan een echt bestandssysteem. U hebt alleen toegang tot Azure Blob-opslag via restgebaseerde clientbibliotheken (of rechtstreeks via het REST-protocol).

    Azure Files is specifiek een bestandssysteem. Azure Files heeft alle bestandssamenvattingen die u kent en waar u van houdt uit jarenlange samenwerking met on-premises besturingssystemen. Net als Azure Blob-opslag biedt Azure Files een REST-interface en REST-gebaseerde clientbibliotheken. In tegenstelling tot Azure Blob-opslag biedt Azure Files SMB-toegang tot Azure-bestandsshares. Door SMB te gebruiken, u een Azure-bestandsshare rechtstreeks op Windows, Linux of macOS monteren, on-premises of in cloud-VM's, zonder code te schrijven of speciale stuurprogramma's aan het bestandssysteem te koppelen. U azure-bestandsshares ook cacheop on-premises bestandsservers met behulp van Azure File Sync voor snelle toegang, dicht bij waar de gegevens worden gebruikt. 
   
    Zie [Inleiding tot de belangrijkste Azure Storage-services](../common/storage-introduction.md)voor een meer diepgaande beschrijving van de verschillen tussen Azure Files en Azure Blob-opslag. Zie [Inleiding tot Blob-opslag voor](../blobs/storage-blobs-introduction.md)meer informatie over Azure Blob-opslag.

* <a id="files-versus-disks"></a>**Waarom zou ik een Azure-bestandsshare gebruiken in plaats van Azure Disks?**  
    Een schijf in Azure Disks is gewoon een schijf. Als u waarde wilt ophalen uit Azure Disks, moet u een schijf koppelen aan een virtuele machine die in Azure wordt uitgevoerd. Azure Disks kan worden gebruikt voor alles waarvoor u een schijf zou gebruiken op een on-premises server. U het gebruiken als een OS-systeemschijf, als ruilruimte voor een besturingssysteem of als speciale opslag voor een toepassing. Een interessant gebruik voor Azure Disks is het maken van een bestandsserver in de cloud om te gebruiken op dezelfde plaatsen waar u een Azure-bestandsshare gebruiken. Het implementeren van een bestandsserver in Azure Virtual Machines is een krachtige manier om bestandsopslag in Azure te krijgen wanneer u implementatieopties nodig hebt die momenteel niet worden ondersteund door Azure Files (zoals NFS-protocolondersteuning of premium opslag). 

    Het uitvoeren van een bestandsserver met Azure Disks als back-endopslag is echter doorgaans veel duurder dan het gebruik van een Azure-bestandsshare, om een aantal redenen. Ten eerste moet u niet alleen betalen voor schijfopslag, maar ook betalen voor de kosten van het uitvoeren van een of meer Azure VM's. Ten tweede moet u ook de VM's beheren die worden gebruikt om de bestandsserver uit te voeren. U bent bijvoorbeeld verantwoordelijk voor os-upgrades. Tot slot, als u uiteindelijk vereist dat gegevens on-premises in de cache worden opgeslagen, is het aan u om replicatietechnologieën in te stellen en te beheren, zoals DFSR (Distributed File System Replication), om dat mogelijk te maken.

    Een benadering om het beste uit zowel Azure Files als een bestandsserver te krijgen die wordt gehost in Azure Virtual Machines (naast het gebruik van Azure Disks als back-endopslag) is het installeren van Azure File Sync op een bestandsserver die wordt gehost op een cloud-vm. Als het Azure-bestandsaandeel zich in dezelfde regio bevindt als uw bestandsserver, u cloudlagen inschakelen en het percentage vrije ruimte instellen op maximaal (99%). Dit zorgt voor minimale duplicatie van gegevens. U ook alle toepassingen die u wilt gebruiken met uw bestandsservers, zoals toepassingen waarvoor NFS-protocolondersteuning vereist is.

    Zie [IaaS VM-gastclusters implementeren in Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)voor informatie over een optie voor het instellen van een krachtige en zeer beschikbare bestandsserver in Azure. Zie [Inleiding tot de belangrijkste Azure Storage-services](../common/storage-introduction.md)voor een meer diepgaande beschrijving van de verschillen tussen Azure-bestanden en Azure-schijven. Zie overzicht azure managed [disks](../../virtual-machines/windows/managed-disks-overview.md)voor meer informatie over Azure Disks.

* <a id="get-started"></a>
  **Hoe ga ik aan de slag met Azure Files?**  
   Aan de slag met Azure Files is eenvoudig. Maak [eerst een bestandsshare](storage-how-to-create-file-share.md)en monteer deze vervolgens in het besturingssysteem van uw voorkeur: 

  * [Monteren in Windows](storage-how-to-use-files-windows.md)
  * [Mount in Linux](storage-how-to-use-files-linux.md)
  * [Monteren in macOS](storage-how-to-use-files-mac.md)

    Zie [Plannen voor een Azure-bestandsimplementatie voor](storage-files-planning.md)een uitgebreidere handleiding voor het implementeren van een Azure-bestandsshare ter vervanging van productiebestandsshares in uw organisatie.

* <a id="redundancy-options"></a>
  **Welke opslagredundantieopties worden ondersteund door Azure Files?**  
    Momenteel ondersteunt Azure Files lokaal redundante opslag (LRS), zone redundante opslag (ZRS), georedundante opslag (GRS) en geo-zoneredundante opslag (GZRS) (preview). We zijn van plan om in de toekomst georedundante (RA-GRS)-opslag voor leestoegang te ondersteunen, maar we hebben op dit moment geen tijdlijnen om te delen.

* <a id="tier-options"></a>
  **Welke opslaglagen worden ondersteund in Azure Files?**  
    Azure Files ondersteunt twee opslaglagen: premium en standaard. Standaardbestandsshares worden gemaakt in opslagaccounts voor algemene doeleinden (GPv1 of GPv2) en premium bestandsshares worden gemaakt in opslagaccounts van FileStorage. Meer informatie over het maken van [standaard bestandsshares](storage-how-to-create-file-share.md) en [premium bestandsshares](storage-how-to-create-premium-fileshare.md). 
    
    > [!NOTE]
    > U geen Azure-bestandsshares maken van Blob-opslagaccounts of GPv1- of GPv2-opslagaccounts (Premium General Purpose) of GPv2.You cannot create Azure file shares from Blob storage accounts or *premium* general purpose (GPv1 or GPv2) storage accounts. Standaard Azure-bestandsshares moeten alleen in *standaardaccounts* voor algemeen gebruik worden gemaakt en premium Azure-bestandsshares mogen alleen worden gemaakt in opslagaccounts van FileStorage. *Opslagaccounts* voor algemeen gebruik (GPv1 en GPv2) zijn alleen voor premium paginablobs. 

* <a id="give-us-feedback"></a>
  **Ik wil echt een specifieke functie toegevoegd aan Azure Files zien. Kun je het toevoegen?**  
    Het Azure Files-team is geïnteresseerd in alle feedback die u hebt over onze service. Stem op functieverzoeken bij [Azure Files UserVoice!](https://feedback.azure.com/forums/217298-storage/category/180670-files) We kijken ernaar uit om u te verrassen met vele nieuwe functies.

  **Ondersteunt Azure Files het vergrendelen van bestanden?**  
    Ja, Azure Files ondersteunt volledig het vergrendelen van smb/Windows-stijl, [zie details](https://docs.microsoft.com/rest/api/storageservices/managing-file-locks). 
    
## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Welke regio's worden ondersteund voor Azure File Sync?**  
    De lijst met beschikbare regio's is te vinden in de [sectie Regiobeschikbaarheid](storage-sync-files-planning.md#azure-file-sync-region-availability) van de planningshandleiding voor Azure File Sync. We zullen voortdurend steun toevoegen aan extra regio's, waaronder niet-openbare regio's.

* <a id="cross-domain-sync"></a>
  **Kan ik met domeinen verbonden en niet-domeinverbonden servers in dezelfde synchronisatiegroep hebben?**  
    Ja. Een synchronisatiegroep kan servereindpunten bevatten met verschillende Active Directory-lidmaatschappen, zelfs als ze niet zijn gekoppeld aan het domein. Hoewel deze configuratie technisch werkt, raden we dit niet aan als een typische configuratie, omdat toegangscontrolelijsten (ACL's) die zijn gedefinieerd voor bestanden en mappen op één server mogelijk niet kunnen worden afgedwongen door andere servers in de synchronisatiegroep. Voor de beste resultaten raden we u aan te synchroniseren tussen servers die zich in hetzelfde Active Directory-forest bevinden, tussen servers die zich in verschillende Active Directory-forests bevinden, maar die vertrouwensrelaties hebben opgebouwd, of tussen servers die zich niet in een domein bevinden. We raden u aan een mix van deze configuraties niet te gebruiken.

* <a id="afs-change-detection"></a>
  **Ik heb een bestand rechtstreeks in mijn Azure-bestandsshare gemaakt met behulp van SMB of in de portal. Hoe lang duurt het voordat het bestand wordt gesynchroniseerd met de servers in de synchronisatiegroep?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Wat gebeurt er als hetzelfde bestand op twee servers op ongeveer hetzelfde moment wordt gewijzigd?**  
    Azure File Sync maakt gebruik van een eenvoudige strategie voor het oplossen van conflicten: we houden beide wijzigingen in bestanden die op twee servers tegelijk worden gewijzigd. De meest recent geschreven wijziging behoudt de oorspronkelijke bestandsnaam. Het oudere bestand heeft de "bron" machine en het conflictnummer toegevoegd aan de naam. Het volgt deze taxonomie: 
   
    \<BestandsnaamWithoutExtension\>-\<\>\[-#\]MachineName . \<ext\>  

    Het eerste conflict van CompanyReport.docx zou bijvoorbeeld CompanyReport-CentralServer.docx worden als CentralServer de plaats is waar het oudere schrijven heeft plaatsgevonden. Het tweede conflict zou companyReport-CentralServer-1.docx heten. Azure File Sync ondersteunt 100 conflictbestanden per bestand. Zodra het maximum aantal conflictbestanden is bereikt, wordt het bestand niet gesynchroniseerd totdat het aantal conflictbestanden kleiner is dan 100.

* <a id="afs-storage-redundancy"></a>
  **Wordt georedundante opslag ondersteund voor Azure File Sync?**  
    Ja, Azure Files ondersteunt zowel lokaal redundante opslag (LRS) als georedundante opslag (GRS). Als u een failovervan een opslagaccount initieert tussen gekoppelde regio's vanuit een account dat is geconfigureerd voor GRS, raadt Microsoft u aan het nieuwe gebied alleen te behandelen als een back-up van gegevens. Azure File Sync begint niet automatisch te synchroniseren met het nieuwe primaire gebied. 

* <a id="sizeondisk-versus-size"></a>
  **Waarom komt de *eigenschap Grootte op schijf* voor een bestand niet overeen met de eigenschap *Grootte* na het gebruik van Azure File Sync?**  
  Zie [Cloudtiering begrijpen](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  **Hoe weet ik of een bestand is trapsgewijs?**  
  Zie [Cloudtiering begrijpen](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Een bestand dat ik wil gebruiken is gelaagd. Hoe kan ik het bestand op schijf herinneren om het lokaal te gebruiken?**  
  Zie [Cloudtiering begrijpen](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **Hoe dwing ik een bestand of directory te laten tieren?**  
  Zie [Cloudtiering begrijpen](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  **Hoe wordt *volumevrije ruimte* geïnterpreteerd wanneer ik meerdere servereindpunten op een volume heb?**  
  Zie [Cloudtiering begrijpen](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
  **Welke bestanden of mappen worden automatisch uitgesloten door Azure File Sync?**  
  Zie [Bestanden overgeslagen](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Kan ik Azure File Sync gebruiken met Windows Server 2008 R2, Linux of mijn NAS-apparaat (Network-attached storage) ?**  
    Momenteel ondersteunt Azure File Sync alleen Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2. Op dit moment hebben we geen andere plannen die we kunnen delen, maar we staan open voor het ondersteunen van extra platforms op basis van de vraag van de klant. Laat ons op [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) weten welke platforms u wilt dat wij ondersteunen.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Waarom bestaan er gelaagde bestanden buiten de naamruimte van het servereindpunt?**  
    Voorafgaand aan Azure File Sync-agent versie 3 blokkeerde Azure File Sync de verplaatsing van gelaagde bestanden buiten het servereindpunt, maar op hetzelfde volume als het eindpunt van de server. Kopieerbewerkingen, verplaatsingen van niet-gelaagde bestanden en verplaatsingen van gelaagde naar andere volumes bleven ongewijzigd. De reden voor dit gedrag was de impliciete veronderstelling dat File Explorer en andere Windows API's hebben die verplaatsen bewerkingen op hetzelfde volume zijn (bijna) ogenblikkelijke hernoemen bewerkingen. Dit betekent dat met verplaatsingen Verkenner of andere verplaatsingsmethoden (zoals opdrachtregel of PowerShell) niet-reageren worden weergegeven terwijl Azure File Sync de gegevens uit de cloud weerroept. Vanaf [Azure File Sync-agent versie 3.0.12.0](storage-files-release-notes.md#supported-versions)u met Azure File Sync een gelaagd bestand buiten het servereindpunt verplaatsen. We vermijden de eerder genoemde negatieve effecten door toe te staan dat het gelaagde bestand bestaat als een gelaagd bestand buiten het servereindpunt en vervolgens het bestand op de achtergrond terugte roepen. Dit betekent dat bewegingen op hetzelfde volume onmiddellijk zijn, en we doen al het werk om het bestand terug te roepen naar de schijf nadat de verhuizing is voltooid. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Ik heb een probleem met Azure File Sync op mijn server (synchronisatie, cloudtiering, enz.). Moet ik mijn servereindpunt verwijderen en opnieuw maken?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Kan ik de opslagsynchronisatieservice en/of het opslagaccount verplaatsen naar een andere brongroep of -abonnement?**  
   Ja, de opslagsynchronisatieservice en/of het opslagaccount kunnen worden verplaatst naar een andere brongroep of -abonnement binnen de bestaande Azure AD-tenant. Als het opslagaccount wordt verplaatst, moet u de Hybrid File Sync Service toegang geven tot het opslagaccount (zie [Ervoor zorgen dat Azure File Sync toegang heeft tot het opslagaccount).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Azure File Sync biedt geen ondersteuning voor het verplaatsen van het abonnement naar een andere Azure AD-tenant.
    
* <a id="afs-ntfs-acls"></a>
  **Worden in Azure File Sync NTFS-ATFS's op map-/bestandsniveau bewaard, samen met gegevens die zijn opgeslagen in Azure-bestanden?**

    Vanaf 24 februari 2020 worden nieuwe en bestaande ACL's die zijn gelaagd door Azure-bestandssynchronisatie gehandhaafd in NTFS-indeling en worden ACL-wijzigingen die rechtstreeks in de Azure-bestandsshare zijn aangebracht, gesynchroniseerd met alle servers in de synchronisatiegroep. Wijzigingen in ACL's in Azure Files worden gesynchroniseerd via Azure-bestandssynchronisatie. Wanneer u gegevens kopieert naar Azure Files, controleert u of u SMB gebruikt om toegang te krijgen tot het aandeel en uw ACL's te behouden. Bestaande REST-gebaseerde hulpprogramma's, zoals AzCopy of Storage Explorer, blijven niet bestaan in ACL's.

    Als u Azure Backup hebt ingeschakeld voor de bestandssynchronisatiebeheerbeheerbestanden, kunnen bestandsacl.n.a.s. worden hersteld als onderdeel van de werkstroom voor back-upherstel. Dit werkt voor het gehele aandeel of individuele bestanden / mappen.

    Als u snapshots gebruikt als onderdeel van de zelfbeheerde back-upoplossing voor bestandsshares die worden beheerd door bestandssynchronisatie, worden uw ACL's mogelijk niet goed hersteld naar NTFS-ACL's als de momentopnamen vóór 24 februari 2020 zijn gemaakt. Als dit gebeurt, u contact opnemen met Azure Support.
    
## <a name="security-authentication-and-access-control"></a>Beveiliging, verificatie en toegangscontrole
* <a id="ad-support"></a>
**Wordt verificatie en toegangscontrole op basis van identiteit ondersteund door Azure Files?**  
    
    Ja, Azure Files ondersteunt verificatie op basis van identiteit en toegangscontrole. U een van de twee manieren kiezen om identiteitsgebaseerd toegangsbeheer te gebruiken: Active Directory (AD) (preview) of Azure Active Directory Domain Services (Azure AD DS) (GA). AD ondersteunt verificatie met behulp van AD-domein samengevoegde machines, on-premises of in Azure, om toegang te krijgen tot Azure-bestandsshares via SMB. Azure AD DS-verificatie via SMB voor Azure Files stelt Azure AD DS-domein-verbonden Windows VM's in staat om toegang te krijgen tot shares, mappen en bestanden met Azure AD-referenties. Zie [Overzicht van verificatieondersteuning voor azure files op basis van azure-bestanden voor SMB-toegang](storage-files-active-directory-overview.md)voor meer informatie. 

    Azure Files biedt twee extra manieren om toegangsbeheer te beheren:

    - U gedeelde toegangshandtekeningen (SAS) gebruiken om tokens te genereren met specifieke machtigingen en die geldig zijn voor een opgegeven tijdsinterval. U bijvoorbeeld een token genereren met alleen-lezen toegang tot een specifiek bestand met een vervaldatum van 10 minuten. Iedereen die het token bezit terwijl het token geldig is, heeft alleen-lezen toegang tot dat bestand gedurende die 10 minuten. Handtekeningsleutels voor gedeelde toegang worden alleen ondersteund via de REST API of in clientbibliotheken. U moet het Azure-bestandsaandeel over SMB monteren met behulp van de opslagaccountsleutels.

    - Azure File Sync behoudt en repliceert alle discretionaire ACL's of DacLs (of Active Directory-based of local) naar alle servereindpunten waarmee het synchroniseert. Omdat Windows Server al kan verifiëren met Active Directory, is Azure File Sync een effectieve stop-gap-optie totdat volledige ondersteuning voor Active Directory-gebaseerde verificatie en ACL-ondersteuning binnenkomt.
    
    U verwijzen naar [Het toestaan van toegang tot Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor een uitgebreide weergave van alle protocollen die worden ondersteund op Azure Storage-services. 

* <a id="ad-support-devices"></a>
**Ondersteunt Azure Files Azure Active Directory Domain Services (Azure AD DS) Authentication SMB-toegang met Azure AD-referenties van apparaten die zijn gekoppeld aan of geregistreerd bij Azure AD?**

    Nee, dit scenario wordt niet ondersteund.

* <a id="ad-support-rest-apis"></a>
**Zijn er REST-API's ter ondersteuning van Get/Set/Copy directory/file NTFS ASP's?**

    Ja, we ondersteunen REST-API's die NTFS-ACL's voor mappen of bestanden krijgen, instellen of kopiëren wanneer we de [REST-API 2019-07](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (of hoger) gebruiken.

* <a id="ad-vm-subscription"></a>
**Kan ik azure-bestanden openen met Azure AD-referenties van een VM onder een ander abonnement?**

    Als het abonnement waaronder de bestandsshare wordt geïmplementeerd, is gekoppeld aan dezelfde Azure AD-tenant als de Azure AD Domain Services-implementatie waaraan de VM is gekoppeld aan het domein, u azure-bestanden openen met dezelfde Azure AD-referenties. De beperking wordt niet opgelegd aan het abonnement, maar aan de bijbehorende Azure AD-tenant.
    
* <a id="ad-support-subscription"></a>
**Kan ik Azure Files Azure AD DS- of AD-verificatie inschakelen met een Azure AD-tenant die verschilt van de primaire tenant waaraan de bestandsshare is gekoppeld?**

    Nee, Azure Files ondersteunt alleen Azure AD DS- of AD-integratie met een Azure AD-tenant die zich in hetzelfde abonnement bevindt als de bestandsshare. Er kan slechts één abonnement worden gekoppeld aan een Azure AD-tenant. Deze beperking is van toepassing op zowel Azure AD DS- als AD-verificatiemethoden. Wanneer u AD gebruikt voor verificatie, moet de AD-referentie worden gesynchroniseerd met het Azure AD waaraan het opslagaccount is gekoppeld.

* <a id="ad-linux-vms"></a>
**Ondersteunt Azure Files Azure AD DS of AD-verificatie Linux VM's?**

    Nee, verificatie van Linux VM's wordt niet ondersteund.

* <a id="ad-multiple-forest"></a>
**Ondersteunt Azure Files AD-verificatie integratie met een AD-omgeving met meerdere forests?**    

    Azure Files AD-verificatie integreert alleen met het forest van de AD-domeinservice waaraan het opslagaccount is geregistreerd. Als u verificatie vanuit een ander AD-forest wilt ondersteunen, moet de forestvertrouwensrelatie op de juiste manier zijn geconfigureerd als u verificatie vanuit een ander AD-forest wilt ondersteunen. De manier waarop Azure Files zich registreert bij een AD-domeinservice is meestal hetzelfde als een gewone bestandsserver, waar het een identiteit (computer- of serviceaanmeldingsaccount) in AD maakt voor verificatie. Het enige verschil is dat de geregistreerde SPN van het opslagaccount eindigt met "file.core.windows.net" die niet overeenkomt met het domeinachtervoegsel. Raadpleeg uw domeinbeheerder om te zien of er een update van uw DNS-routeringsbeleid nodig is om meerdere forestverificatie in te schakelen vanwege het verschillende domeinachtervoegsel.

* <a id=""></a>
**Welke regio's zijn beschikbaar voor AD-verificatie van Azure Files (voorbeeld)?**

    Raadpleeg [de regionale beschikbaarheid van het AD](storage-files-identity-auth-active-directory-enable.md#regional-availability) voor meer informatie.

* <a id="ad-aad-smb-afs"></a>
**Kan ik gebruikmaken van Azure Files Azure AD DS-verificatie of AD-verificatie (preview) (Active Directory) op bestandsshares die worden beheerd door Azure File Sync?**

    Ja, u Azure AD DS- of AD-verificatie inschakelen op een bestandsshare die wordt beheerd door Azure-bestandssynchronisatie. Wijzigingen in de Directory/file NTFS ACL's op lokale bestandsservers worden gelaagd naar Azure-bestanden en vice versa.

* <a id="ad-aad-smb-files"></a>
**Hoe kan ik controleren of ik AD-verificatie heb ingeschakeld voor mijn opslagaccount en de AD-domeingegevens?**

    U verwijzen naar de instructies [die hier](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#enable-ad-authentication-for-your-account) worden aangeboden om te valideren of Azure Files AD-verificatie is ingeschakeld in uw opslagaccount en de AD-domeingegevens op te halen.
    
* <a id="encryption-at-rest"></a>
**Hoe kan ik ervoor zorgen dat mijn Azure-bestandsshare in rust wordt versleuteld?**  

    Ja. Zie Azure [Storage Service Encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie .

* <a id="access-via-browser"></a>
**Hoe kan ik toegang geven tot een specifiek bestand met behulp van een webbrowser?**  

    U gedeelde toegangshandtekeningen gebruiken om tokens te genereren met specifieke machtigingen en die geldig zijn voor een opgegeven tijdsinterval. U bijvoorbeeld een token genereren dat alleen-lezen toegang geeft tot een specifiek bestand, voor een bepaalde periode. Iedereen die de URL bezit, heeft rechtstreeks toegang tot het bestand vanuit elke webbrowser terwijl het token geldig is. U eenvoudig een handtekeningsleutel voor gedeelde toegang genereren vanuit een ui zoals Storage Explorer.

* <a id="file-level-permissions"></a>
**Is het mogelijk om alleen-lezen of alleen-schrijven machtigingen op mappen binnen het aandeel op te geven?**  

    Als u het bestandsshare monteert met SMB, hebt u geen controle over machtigingen op mapniveau. Als u echter een handtekening voor gedeelde toegang maakt met behulp van de REST API of clientbibliotheken, u alleen-lezen- of schrijfmachtigingen opgeven voor mappen in het aandeel.

* <a id="ip-restrictions"></a>
**Kan ik IP-beperkingen implementeren voor een Azure-bestandsshare?**  

    Ja. De toegang tot uw Azure-bestandsshare kan worden beperkt op het niveau van het opslagaccount. Zie [Azure Storage Firewalls en Virtual Networks configureren](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie.

* <a id="data-compliance-policies"></a>
**Welk beleid voor gegevensnaleving biedt ondersteuning voor Azure Files?**  

   Azure Files wordt uitgevoerd bovenop dezelfde opslagarchitectuur die wordt gebruikt in andere opslagservices in Azure Storage. Azure Files past hetzelfde beleid voor gegevensnaleving toe als in andere Azure-opslagservices. Voor meer informatie over de naleving van Azure Storage-gegevens u verwijzen naar [azure storage-nalevingsaanbiedingen](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)en naar het [Microsoft Trust Center](https://microsoft.com/trustcenter/default.aspx)gaan.

## <a name="on-premises-access"></a>Lokale toegang

* <a id="port-445-blocked"></a>
**Mijn ISP of IT blokkeert poort 445 die azure-bestanden niet monteren. Wat moet ik doen?**

    U hier meer te weten komen over [verschillende manieren om een tijdelijke oplossing te vinden voor geblokkeerde poort 445.](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked) Azure Files staat alleen verbindingen toe met SMB 3.0 (met versleutelingsondersteuning) van buiten de regio of het datacenter. SMB 3.0 protocol heeft veel beveiligingsfuncties geïntroduceerd, waaronder kanaalencryptie die zeer veilig is om te gebruiken via internet. Maar het is mogelijk dat poort 445 is geblokkeerd als gevolg van historische redenen van kwetsbaarheden gevonden in lagere SMB-versies. In het ideale geval moet de poort alleen worden geblokkeerd voor SMB 1.0-verkeer en moet SMB 1.0 worden uitgeschakeld op alle clients.

* <a id="expressroute-not-required"></a>
**Moet ik Azure ExpressRoute gebruiken om verbinding te maken met Azure Files of om Azure File Sync on-premises te gebruiken?**  

    Nee. ExpressRoute is niet vereist om toegang te krijgen tot een Azure-bestandsshare. Als u een Azure-bestandsshare direct on-premises monteert, hoeft u alleen poort 445 (TCP outbound) open te hebben voor internettoegang (dit is de poort die SMB gebruikt om te communiceren). Als u Azure File Sync gebruikt, is poort 443 (TCP outbound) poort 443 (TCP outbound) voor HTTPS-toegang (geen SMB vereist). U *kunt* ExpressRoute echter wel gebruiken met een van deze toegangsopties.

* <a id="mount-locally"></a>
**Hoe kan ik een Azure-bestandsshare op mijn lokale machine monteren?**  

    U het bestandsshare monteren met behulp van het SMB-protocol als poort 445 (TCP-uitgaande) is geopend en uw client het SMB 3.0-protocol ondersteunt (bijvoorbeeld als u Windows 10 of Windows Server 2016 gebruikt). Als poort 445 wordt geblokkeerd door het beleid van uw organisatie of door uw internetprovider, u Azure File Sync gebruiken om toegang te krijgen tot uw Azure-bestandsshare.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hoe maak ik een back-up van mijn Azure-bestandsshare?**  
    U [momentopnamen voor](storage-snapshots-files.md) periodieke delen gebruiken ter bescherming tegen onbedoelde verwijderingen. U ook AzCopy, Robocopy of een back-uptool van derden gebruiken waarmee een back-up van een opgezetbestandkan worden gemaakt. Azure Backup biedt back-ups van Azure Files. Meer informatie over [back-ups van Azure-bestandsshares door Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Momentopnamen van shares

### <a name="share-snapshots-general"></a>Momentopnamen delen: Algemeen
* <a id="what-are-snaphots"></a>
**Wat zijn momentopnamen voor bestandsshare?**  
    U Momentopnamen voor azure-bestandsdelen gebruiken om een alleen-lezen versie van uw bestandsshares te maken. U Azure Files ook gebruiken om een eerdere versie van uw inhoud terug te kopiëren naar hetzelfde aandeel, naar een alternatieve locatie in Azure of on-premises voor meer wijzigingen. Zie het [overzicht momentopnamen delen](storage-snapshots-files.md)voor meer informatie over momentopnamen voor delen .

* <a id="where-are-snapshots-stored"></a>
**Waar worden mijn share snapshots opgeslagen?**  
    Momentopnamen voor delen worden opgeslagen in hetzelfde opslagaccount als het bestandsshare.

* <a id="snapshot-consistency"></a>
**Zijn momentopnamen voor het delen van toepassingen consistent?**  
    Nee, momentopnamen voor delen zijn niet toepassingsconsistent. De gebruiker moet de schrijfbewerkingen van de toepassing naar het aandeel doorspoelen voordat de momentopname van het aandeel wordt gemaakt.

* <a id="snapshot-limits"></a>
**Zijn er beperkingen op het aantal momentopnamen voor delen dat ik kan gebruiken?**  
    Ja. Azure Files kunnen maximaal 200 momentopnamen voor delen behouden. Momentopnamen voor delen tellen niet mee voor het aandeelquotum, dus er is geen limiet per aandeel voor de totale ruimte die wordt gebruikt door alle momentopnamen van delen. Opslagaccountlimieten zijn nog steeds van toepassing. Na 200 momentopnamen voor delen, moet u oudere momentopnamen verwijderen om nieuwe momentopnamen voor delen te maken.

* <a id="snapshot-cost"></a>
**Hoeveel kosten momentopnamen voor delen?**  
    Standaardtransactie- en standaardopslagkosten zijn van toepassing op momentopnamen. Momentopnamen zijn incrementeel van aard. De basismomentopname is het aandeel zelf. Alle volgende momentopnamen zijn incrementeel en slaan alleen de diff van de vorige momentopname op. Dit betekent dat de deltawijzigingen die in de factuur worden weergegeven, minimaal zijn als uw werklast verloop minimaal is. Zie [prijspagina](https://azure.microsoft.com/pricing/details/storage/files/) voor prijsinformatie voor standaard Azure Files. Vandaag de dag is de manier om te kijken naar de grootte verbruikt door aandeel momentopname is door het vergelijken van de gefactureerde capaciteit met gebruikte capaciteit. We werken aan tooling om de rapportage te verbeteren.

* <a id="ntfs-acls-snaphsots"></a>
**Zijn NTFS ACL's op mappen en bestanden blijven bestaan in share snapshots?**  
    NTFS-ACL's op mappen en bestanden blijven bestaan in momentopnamen voor delen.

### <a name="create-share-snapshots"></a>Momentopnamen van shares maken
* <a id="file-snaphsots"></a>
**Kan ik een momentopname van afzonderlijke bestanden delen?**  
    Momentopnamen voor delen worden gemaakt op het niveau van de bestandsshare. U afzonderlijke bestanden herstellen uit de momentopname voor bestandsshare, maar u geen momentopnamen op bestandsniveau maken. Als u echter een momentopname op share-level hebt gemaakt en u momentopnamen voor delen wilt aanbieden waarin een specifiek bestand is gewijzigd, u dit doen onder **Vorige versies** op een door Windows gemonteerd aandeel. 
    
    Als u een bestandsmomentopnamefunctie nodig hebt, laat het ons dan weten bij [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Kan ik momentopnamen van een versleuteld bestand maken?**  
    U een momentopname van Azure-bestanden maken waarop versleuteling in rust is ingeschakeld. U bestanden van een momentopname voor delen herstellen naar een versleutelde bestandsshare. Als uw aandeel is versleuteld, wordt uw momentopname ook versleuteld.

* <a id="geo-redundant-snaphsots"></a>
**Zijn mijn share snapshots geo-redundant?**  
    Momentopnamen voor delen hebben dezelfde redundantie als het Azure-bestandsshare waarvoor ze zijn gemaakt. Als u georedundante opslag voor uw account hebt geselecteerd, wordt uw momentopname voor delen ook redundant opgeslagen in het gekoppelde gebied.

### <a name="manage-share-snapshots"></a>Momentopnamen voor delen beheren
* <a id="browse-snapshots-linux"></a>
**Kan ik bladeren door mijn share snapshots van Linux?**  
    U Azure CLI gebruiken om momentopnamen voor delen in Linux te maken, te vermelden, te bladeren en te herstellen.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Kan ik de momentopnamen voor delen kopiëren naar een ander opslagaccount?**  
    U bestanden kopiëren van momentopnamen delen naar een andere locatie, maar u de momentopnamen voor delen niet zelf kopiëren.

### <a name="restore-data-from-share-snapshots"></a>Gegevens herstellen uit momentopnamen van delen
* <a id="promote-share-snapshot"></a>
**Kan ik een momentopname voor delen promoten in het basisaandeel?**  
    U gegevens uit een momentopname voor delen naar een andere bestemming kopiëren. U een momentopname voor delen niet promoten in het basisaandeel.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Kan ik gegevens uit mijn momentopname voor delen herstellen naar een ander opslagaccount?**  
    Ja. Bestanden van een momentopname voor delen kunnen worden gekopieerd naar de oorspronkelijke locatie of naar een alternatieve locatie met hetzelfde opslagaccount of een ander opslagaccount, in dezelfde regio of in verschillende regio's. U bestanden ook kopiëren naar een on-premises locatie of naar een andere cloud.    
  
### <a name="clean-up-share-snapshots"></a>Momentopnamen voor delen opschonen
* <a id="delete-share-keep-snapshots"></a>
**Kan ik mijn share verwijderen, maar mijn momentopnamen voor delen niet verwijderen?**  
    Als je objecten met actieve delen op je aandeel hebt staan, kun je je deel niet verwijderen. U een API gebruiken om momentopnamen voor delen te verwijderen, samen met het aandeel. U ook zowel de momentopnamen voor delen als het aandeel in de Azure-portal verwijderen.

* <a id="delete-share-with-snapshots"></a>
**Wat gebeurt er met mijn momentopnamen voor delen als ik mijn opslagaccount verwijder?**  
    Als u uw opslagaccount verwijdert, worden de momentopnamen voor delen ook verwijderd.

## <a name="billing-and-pricing"></a>Facturering en prijzen
* <a id="vm-file-share-network-traffic"></a>
**Telt het netwerkverkeer tussen een Azure VM en een Azure-bestandsshare als externe bandbreedte die in rekening wordt gebracht voor het abonnement?**  
    Als de bestandsshare en VM zich in dezelfde Azure-regio bevinden, worden er geen extra kosten in rekening gebracht voor het verkeer tussen de bestandsshare en de VM. Als de bestandsshare en de VM zich in verschillende regio's bevinden, wordt het verkeer tussen deze regio's in rekening gebracht als externe bandbreedte.

* <a id="share-snapshot-price"></a>
**Hoeveel kosten momentopnamen voor delen?**  
     Tijdens de preview zijn er geen kosten verbonden aan de capaciteit van momentopnamen voor delen. Standaardopslag- en transactiekosten zijn van toepassing. Na algemene beschikbaarheid worden abonnementen in rekening gebracht voor capaciteit en transacties op momentopnamen van delen.
     
     Momentopnamen voor delen zijn incrementeel van aard. De momentopname van het basisaandeel is het aandeel zelf. Alle volgende momentopnamen voor delen zijn incrementeel en slaan alleen het verschil op met de vorige momentopname van het aandeel. U wordt alleen gefactureerd voor de gewijzigde inhoud. Als u een aandeel hebt met 100 GiB-gegevens, maar slechts 5 GiB is gewijzigd sinds uw laatste momentopname voor delen, verbruikt de momentopname van het aandeel slechts 5 extra GiB en wordt u gefactureerd voor 105 GiB. Zie de [pagina Prijzen voor](https://azure.microsoft.com/pricing/details/storage/files/)meer informatie over transactie- en standaardkosten.

## <a name="scale-and-performance"></a>Schaal en prestaties
* <a id="files-scale-limits"></a>
**Wat zijn de schaallimieten van Azure Files?**  
    Zie Schaalbaarheid en prestatiedoelen voor Azure Files voor informatie over schaalbaarheid en prestatiedoelen voor Azure [Files.](storage-files-scale-targets.md)

* <a id="need-larger-share"></a>
**Welke formaten zijn beschikbaar voor Azure-bestandsshares?**  
    Azure-bestandsgroottes (premium en standaard) kunnen worden opgeschaald tot 100 TiB. Zie het gedeelte [Onboard to larger file shares (standard tier)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) van de planningshandleiding voor onboarding-instructies voor de grotere bestandsshares voor de standaardlaag.

* <a id="lfs-performance-impact"></a>
**Heeft het uitbreiden van mijn bestandssharequotum gevolgen voor mijn workloads of Azure File Sync?**
    
    Nee. Het uitbreiden van het quotum heeft geen invloed op uw workloads of Azure File Sync.

* <a id="open-handles-quota"></a>
**Hoeveel clients hebben tegelijkertijd toegang tot hetzelfde bestand?**   
    Er is een quotum van 2.000 open handgrepen op één bestand. Wanneer u 2.000 geopende handgrepen hebt, wordt er een foutbericht weergegeven waarin staat dat het quotum is bereikt.

* <a id="zip-slow-performance"></a>
**Mijn prestaties zijn traag wanneer ik bestanden in Azure-bestanden openrits. Wat moet ik doen?**  
    Als u grote aantallen bestanden wilt overbrengen naar Azure Files, raden we u aan AzCopy (voor Windows; in preview voor Linux en UNIX) of Azure PowerShell te gebruiken. Deze tools zijn geoptimaliseerd voor netwerkoverdracht.

* <a id="slow-perf-windows-81-2012r2"></a>
**Waarom zijn mijn prestaties traag nadat ik mijn Azure-bestandsshare heb gemonteerd op Windows Server 2012 R2 of Windows 8.1?**  
    Er is een bekend probleem bij het monteren van een Azure-bestandsshare op Windows Server 2012 R2 en Windows 8.1. Het probleem is gepatcht in de cumulatieve update van april 2014 voor Windows 8.1 en Windows Server 2012 R2. Voor optimale prestaties moet u ervoor zorgen dat deze patch is toegepast op alle exemplaren van Windows Server 2012 R2 en Windows 8.1. (U moet altijd Windows-patches ontvangen via Windows Update.) Zie het bijbehorende Microsoft Knowledge Base-artikel [Trage prestaties wanneer u Azure-bestanden vanuit Windows 8.1 of Server 2012 R2 verkrijgt](https://support.microsoft.com/kb/3114025)voor meer informatie.

## <a name="features-and-interoperability-with-other-services"></a>Kenmerken en interoperabiliteit met andere diensten
* <a id="cluster-witness"></a>
**Kan ik mijn Azure-bestandsshare gebruiken als getuige voor *bestandsshare* voor mijn Failovercluster van Windows Server?**  
    Momenteel wordt deze configuratie niet ondersteund voor een Azure-bestandsshare. Zie [Een cloudgetuige voor een failovercluster implementeren voor](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)meer informatie over het instellen van dit voor Azure Blob-opslag.

* <a id="containers"></a>
**Kan ik een Azure-bestandsshare monteren op een Azure Container-exemplaar?**  
    Ja, Azure-bestandsshares zijn een goede optie wanneer u informatie wilt blijven gebruiken na de levensduur van een containerinstantie. Zie [Een Azure-bestandsshare met Azure Container-exemplaren monteren](../../container-instances/container-instances-mounting-azure-files-volume.md)voor meer informatie.

* <a id="rest-rename"></a>
**Is er een hernaambewerking in de REST API?**  
    Momenteel niet.

* <a id="nested-shares"></a>
**Kan ik geneste aandelen instellen? Met andere woorden, een aandeel onder een aandeel?**  
    Nee. Het bestandsaandeel *is* het virtuele stuurprogramma dat u monteren, zodat geneste shares niet worden ondersteund.

* <a id="ibm-mq"></a>
**Hoe gebruik ik Azure Files met IBM MQ?**  
    IBM heeft een document uitgebracht waarmee IBM MQ-klanten Azure Files kunnen configureren met de IBM-service. Zie [Een IBM MQ-wachtrijbeheer met meerdere instance instellen met Microsoft Azure Files-service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)voor meer informatie.

## <a name="see-also"></a>Zie ook
* [Azure-bestanden in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
* [Azure-bestanden in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
* [Problemen met Azure Files Sync oplossen](storage-sync-files-troubleshoot.md)

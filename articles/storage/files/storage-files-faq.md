---
title: Veelgestelde vragen over Azure Files | Microsoft Docs
description: Vind antwoorden op veelgestelde vragen over Azure Files.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 620628b244bf107ad0dc2e2242d174ef798450da
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235630"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Lees de veelgestelde vragen (FAQ) over Azure Files
[Azure files](storage-files-introduction.md) biedt volledig beheerde bestands shares in de cloud die toegankelijk zijn via het industrie standaard [SMB-protocol (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). U kunt Azure-bestands shares gelijktijdig koppelen aan Cloud-of on-premises implementaties van Windows, Linux en macOS. U kunt ook Azure-bestands shares op Windows Server-computers in de cache opslaan met behulp van Azure File Sync voor snelle toegang, waarbij de gegevens worden gebruikt.

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Files-functies en-functionaliteit, met inbegrip van het gebruik van Azure File Sync met Azure Files. Als u het antwoord op uw vraag niet ziet, kunt u contact met ons opnemen via de volgende kanalen (in de volg orde waarin ze worden doorzocht):

1. De sectie opmerkingen van dit artikel.
2. [Micro soft Q&een vraag pagina voor Azure Storage](https://docs.microsoft.com/answers/topics/azure-file-storage.html).
3. [Azure files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft Ondersteuning. Als u een nieuwe ondersteunings aanvraag wilt maken, selecteert u in het Azure Portal op het tabblad **Help** de knop **Help en ondersteuning** en selecteert u vervolgens **nieuwe ondersteunings aanvraag**.

## <a name="general"></a>Algemeen
* <a id="why-files-useful"></a>
  **Hoe is Azure Files nuttig?**  
   U kunt Azure Files gebruiken om bestands shares in de cloud te maken, zonder dat u verantwoordelijk bent voor het beheren van de overhead van een fysieke server, apparaat of toestel. We doen de monotone werkzaamheden voor u, inclusief het Toep assen van updates van het besturings systeem en het vervangen van beschadigde schijven. Zie [waarom Azure files nuttig is](storage-files-introduction.md#why-azure-files-is-useful)voor meer informatie over de scenario's waarmee u Azure files kunt helpen.

* <a id="file-access-options"></a>
  **Wat zijn verschillende manieren om toegang te krijgen tot bestanden in Azure Files?**  
    U kunt de bestands share op uw lokale machine koppelen met behulp van het SMB 3,0-protocol of u kunt hulpprogram ma's zoals [Storage Explorer](https://storageexplorer.com/) gebruiken om toegang te krijgen tot bestanden in uw bestands share. Vanuit uw toepassing kunt u opslag-client Bibliotheken, REST Api's, Power shell of Azure CLI gebruiken om toegang te krijgen tot uw bestanden in de Azure-bestands share.

* <a id="what-is-afs"></a>
  **Wat is Azure File Sync?**  
    U kunt Azure File Sync gebruiken om de bestands shares van uw organisatie in Azure Files te centraliseren, terwijl u de flexibiliteit, prestaties en compatibiliteit van een on-premises Bestands server bijhoudt. Azure File Sync worden uw Windows Server-machines getransformeerd naar een snelle cache van uw Azure-bestands share. U kunt elk protocol dat beschikbaar is op Windows Server gebruiken voor toegang tot uw gegevens lokaal, zoals SMB, Network File System (NFS) en File Transfer Protocol service (FTPS). U kunt zoveel caches hebben als u nodig hebt in de hele wereld.

* <a id="files-versus-blobs"></a>
  **Waarom zou ik een Azure-bestands share versus Azure Blob-opslag voor mijn gegevens gebruiken?**  
    Azure Files en Azure Blob Storage bieden beide manieren om grote hoeveel heden gegevens op te slaan in de Cloud, maar ze zijn handig voor iets andere doel einden. 
    
    Azure Blob-opslag is handig voor grootschalige, Cloud toepassingen die ongestructureerde gegevens moeten opslaan. Azure Blob-opslag is een eenvoudigere opslag abstractie dan een echt bestands systeem, om de prestaties en schaal baarheid te maximaliseren. U hebt alleen toegang tot Azure Blob-opslag via op REST gebaseerde client bibliotheken (of rechtstreeks via het REST-Protocol).

    Azure Files is speciaal een bestands systeem. Azure Files heeft alle samen vattingen van bestanden waarvan u weet dat ze van jaren werken met on-premises besturings systemen. Net als Azure Blob-opslag biedt Azure Files een REST-interface en REST-gebaseerde client bibliotheken. In tegens telling tot Azure Blob-opslag biedt Azure Files SMB-toegang tot Azure-bestands shares. Door SMB te gebruiken, kunt u een Azure-bestands share rechtstreeks koppelen aan Windows, Linux of macOS, on-premises of in virtuele machines in de Cloud, zonder dat u code hoeft te schrijven of speciale Stuur Programma's aan het bestands systeem hoeft toe te voegen. U kunt Azure-bestands shares ook opslaan in de cache van on-premises bestands servers met behulp van Azure File Sync voor snelle toegang, waarbij u sluit waar de gegevens worden gebruikt. 
   
    Zie [Introduction to the core Azure Storage services](../common/storage-introduction.md)(Engelstalig) voor een uitgebreide beschrijving van de verschillen tussen Azure files en Azure Blob-opslag. Zie [Inleiding tot Blob Storage](../blobs/storage-blobs-introduction.md)voor meer informatie over Azure Blob-opslag.

* <a id="files-versus-disks"></a>**Waarom zou ik een Azure-bestands share gebruiken in plaats van Azure-schijven?**  
    Een schijf in azure disks is gewoon een schijf. Als u de waarde van Azure-schijven wilt ophalen, moet u een schijf koppelen aan een virtuele machine die wordt uitgevoerd in Azure. Azure-schijven kunnen worden gebruikt voor alle items waarvoor u een schijf gebruikt voor op een on-premises server. U kunt deze gebruiken als een besturingssysteem schijf, als wissel ruimte voor een besturings systeem of als toegewezen opslag voor een toepassing. Een interessant gebruik van Azure-schijven is het maken van een bestands server in de Cloud voor gebruik op dezelfde locatie waar u een Azure-bestands share zou kunnen gebruiken. Het implementeren van een bestands server in azure Virtual Machines is een krachtige manier om bestands opslag in azure op te halen wanneer u implementatie opties nodig hebt die momenteel niet worden ondersteund door Azure Files (zoals NFS-protocol ondersteuning of Premium Storage). 

    Het uitvoeren van een bestands server met Azure-schijven als back-end-opslag is doorgaans veel duurder dan het gebruik van een Azure-bestands share, om een aantal redenen. Naast het betalen van schijf opslag moet u ook betalen voor de kosten van het uitvoeren van een of meer virtuele Azure-machines. Ten tweede moet u ook de virtuele machines beheren die worden gebruikt voor het uitvoeren van de bestands server. Stel dat u verantwoordelijk bent voor upgrades van het besturings systeem. Ten slotte, als u uiteindelijk gegevens moet opslaan in de cache op locatie, is het aan te raden om de replicatie technologieën, zoals Distributed File System replicatie (DFSR), in te stellen en te beheren.

    Een manier om het beste van zowel Azure Files als een bestands server op te halen die wordt gehost in azure Virtual Machines (naast het gebruik van Azure-schijven als back-end-opslag) is om Azure File Sync te installeren op een bestands server die wordt gehost op een virtuele machine in de Cloud. Als de Azure-bestands share zich in dezelfde regio bevindt als de bestands server, kunt u Cloud lagen inschakelen en het volume vrije ruimte percentage instellen op Maxi maal (99%). Dit zorgt voor minimale duplicatie van gegevens. U kunt ook alle gewenste toepassingen gebruiken met uw bestands servers, zoals toepassingen waarvoor NFS-protocol ondersteuning nodig is.

    Zie [IaaS VM-gast clusters implementeren in Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)voor meer informatie over een optie voor het instellen van een bestands server met hoge prestaties en Maxi maal Beschik baarheid in Azure. Zie [Introduction to the core Azure Storage services](../common/storage-introduction.md)(Engelstalig) voor een gedetailleerde beschrijving van de verschillen tussen Azure files en Azure-schijven. Zie [overzicht van azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md)voor meer informatie over Azure-schijven.

* <a id="get-started"></a>
  **Hoe kan ik aan de slag met Azure Files?**  
   Het is eenvoudig om aan de slag te gaan met Azure Files. Maak eerst [een bestands share](storage-how-to-create-file-share.md)en koppel deze vervolgens aan het besturings systeem van uw voor keur: 

  * [Koppelen in Windows](storage-how-to-use-files-windows.md)
  * [Koppelen in Linux](storage-how-to-use-files-linux.md)
  * [Koppelen in macOS](storage-how-to-use-files-mac.md)

    Zie [planning voor een Azure files-implementatie](storage-files-planning.md)voor een uitgebreidere hand leiding voor het implementeren van een Azure-bestands share voor het vervangen van productie bestands shares in uw organisatie.

* <a id="redundancy-options"></a>
  **Welke opties voor opslag redundantie worden ondersteund door Azure Files?**  
    Momenteel ondersteunt Azure Files lokaal redundante opslag (LRS), zone redundante opslag (ZRS), geografisch redundante opslag (GRS) en geo-zone-redundante opslag (GZRS). We gaan in de toekomst gebruikmaken van geografisch redundante opslag met lees toegang (RA-GRS), maar we hebben op dit moment geen tijd lijnen om te delen.

* <a id="tier-options"></a>
  **Welke opslag lagen worden ondersteund in Azure Files?**  
    Azure Files ondersteunt twee opslag lagen: Premium en Standard. Standaard bestands shares worden gemaakt in de opslag accounts voor algemeen gebruik (GPv1 of GPv2) en Premium-bestands shares worden gemaakt in FileStorage-opslag accounts. Meer informatie over het maken van [standaard bestands shares](storage-how-to-create-file-share.md) en [Premium-bestands shares](storage-how-to-create-premium-fileshare.md). 
    
    > [!NOTE]
    > U kunt geen Azure-bestands shares maken op basis van Blob Storage-accounts of *Premium* -opslag accounts voor algemeen gebruik (GPv1 of GPv2). Standaard-Azure-bestands shares moeten alleen worden gemaakt in *standaard* accounts voor algemeen gebruik en Premium Azure-bestands shares moeten alleen worden gemaakt in FileStorage-opslag accounts. *Premium* -opslag accounts voor algemeen gebruik (GPv1 en GPv2) zijn alleen voor Premium-pagina-blobs. 

* <a id="give-us-feedback"></a>
  **Ik wil een specifieke functie zien die is toegevoegd aan Azure Files. Kunt u deze toevoegen?**  
    Het Azure Files-team is geïnteresseerd bij het horen van alle feedback over onze service. Stem op de functie aanvragen op [Azure files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! We hopen dat u met veel nieuwe functies op zoek bent.

  **Ondersteunt Azure Files het vergren delen van bestanden?**  
    Ja, Azure Files volledig ondersteunt SMB/Windows-stijl bestands vergrendeling. [Zie de Details voor meer informatie](https://docs.microsoft.com/rest/api/storageservices/managing-file-locks). 
    
## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Welke regio's worden ondersteund voor Azure File Sync?**  
    De lijst met beschik bare regio's vindt u in de sectie [Beschik baarheid van regio's](storage-sync-files-planning.md#azure-file-sync-region-availability) van de Azure file sync plannings handleiding. Er wordt voortdurend ondersteuning toegevoegd voor extra regio's, waaronder niet-open bare regio's.

* <a id="cross-domain-sync"></a>
  **Kan ik servers die lid zijn van een domein en niet-domein lid zijn in dezelfde synchronisatie groep?**  
    Ja. Een synchronisatie groep kan server eindpunten met verschillende Active Directory lidmaatschappen bevatten, zelfs als ze geen lid van een domein zijn. Hoewel deze configuratie technisch kan worden gebruikt, wordt dit niet aanbevolen als een typische configuratie, omdat Acl's (toegangs beheer lijsten) die zijn gedefinieerd voor bestanden en mappen op één server mogelijk niet kunnen worden afgedwongen door andere servers in de synchronisatie groep. Voor de beste resultaten raden we u aan om te synchroniseren tussen servers die zich in hetzelfde Active Directory forest bevinden, tussen servers die zich in verschillende Active Directory forests bevinden, maar die vertrouwens relaties hebben ingesteld, of tussen servers die zich niet in een domein bevinden. U wordt aangeraden een combi natie van deze configuraties te gebruiken.

* <a id="afs-change-detection"></a>
  **Ik heb een bestand rechtstreeks in mijn Azure-bestands share gemaakt met behulp van SMB of in de portal. Hoe lang duurt het voordat het bestand is gesynchroniseerd met de servers in de synchronisatie groep?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Hoe lang duurt het voordat Azure File Sync 1TiB van gegevens uploadt?**
  
    Prestaties zijn afhankelijk van uw omgevings instellingen, configuratie en of dit een initiële synchronisatie of een voortdurende synchronisatie is. Zie voor meer informatie [Azure file sync metrische gegevens over prestaties](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**Als hetzelfde bestand op ongeveer hetzelfde moment op twee servers wordt gewijzigd, wat gebeurt er dan?**  
    Azure File Sync maakt gebruik van een eenvoudige strategie voor conflict oplossing: wijzigingen in bestanden die op twee servers tegelijk worden gewijzigd, worden bewaard. De meest recent geschreven wijziging behoudt de oorspronkelijke bestands naam. Het oudere bestand heeft de computer ' Bron ' en het conflict nummer dat is toegevoegd aan de naam. Deze is gebaseerd op deze taxonomie: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\<ext\>  

    Het eerste conflict van CompanyReport. docx wordt bijvoorbeeld CompanyReport-CentralServer. docx als CentralServer de locatie is waar de oudere schrijf bewerking plaatsvond. Het tweede conflict zou de naam CompanyReport-CentralServer-1. docx hebben. Azure File Sync ondersteunt 100-conflict bestanden per bestand. Zodra het maximum aantal conflict bestanden is bereikt, kan het bestand niet worden gesynchroniseerd totdat het aantal conflict bestanden kleiner is dan 100.

* <a id="afs-storage-redundancy"></a>
  **Wordt geografisch redundante opslag ondersteund voor Azure File Sync?**  
    Ja, Azure Files ondersteunt zowel lokaal redundante opslag (LRS) als geo-redundante opslag (GRS). Als u een failover van een opslag account initieert tussen gekoppelde regio's van een account dat is geconfigureerd voor GRS, raadt micro soft u aan de nieuwe regio alleen als back-up van gegevens te behandelen. Azure File Sync begint niet automatisch met het synchroniseren van de nieuwe primaire regio. 

* <a id="sizeondisk-versus-size"></a>
  **Waarom wordt de eigenschap *grootte op schijf* voor een bestand niet overeenkomen met de eigenschap *grootte* na gebruik van Azure file sync?**  
  Zie [Wat is Cloud lagen](storage-sync-cloud-tiering.md#sizeondisk-versus-size)?

* <a id="is-my-file-tiered"></a>
  **Hoe kan ik zien of een bestand is getierd?**  
  Zie [Wat is Cloud lagen](storage-sync-cloud-tiering.md#is-my-file-tiered)?

* <a id="afs-recall-file"></a>**Een bestand dat ik wil gebruiken, is gelaagd. Hoe kan ik het bestand intrekken op schijf om het lokaal te gebruiken?**  
  Zie [Wat is Cloud lagen](storage-sync-cloud-tiering.md#afs-recall-file)?

* <a id="afs-force-tiering"></a>
  **Hoe kan ik moet een bestand of map geforceerd worden getierd?**  
  Zie [Wat is Cloud lagen](storage-sync-cloud-tiering.md#afs-force-tiering)?

* <a id="afs-effective-vfs"></a>
  **Hoe wordt de *beschik bare volume ruimte* geïnterpreteerd wanneer ik meerdere server eindpunten op een volume heb?**  
  Zie [Wat is Cloud lagen](storage-sync-cloud-tiering.md#afs-effective-vfs)?

* <a id="afs-files-excluded"></a>
  **Welke bestanden of mappen worden automatisch uitgesloten door Azure File Sync?**  
  Bestanden weer geven die zijn [overgeslagen](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Kan ik Azure File Sync gebruiken met een Windows Server 2008 R2-, Linux-of mijn network-attached storage (NAS)-apparaat?**  
    Momenteel ondersteunt Azure File Sync alleen Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2. Op dit moment hebben we geen andere plannen die we kunnen delen, maar we zijn geopend voor het ondersteunen van extra platformen op basis van de vraag van de klant. Laat het ons weten om [Azure files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) welke platformen we willen ondersteunen.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Waarom bestaan er gelaagde bestanden buiten de naam ruimte van het server eindpunt?**  
    Vóór Azure File Sync agent versie 3, Azure File Sync de verplaatsing van gelaagde bestanden buiten het eind punt van de server geblokkeerd, maar op hetzelfde volume als het server eindpunt. Kopieer bewerkingen, verplaatsen van niet-gelaagde bestanden en verplaatsingen van gelaagde naar andere volumes werden niet beïnvloed. De reden voor dit gedrag is de impliciete veronderstelling dat bestanden Verkenner en andere Windows-Api's de bewerking voor het verplaatsen op hetzelfde volume (bijna) direct een andere naam hebben. Dit betekent dat bestanden Verkenner of andere methoden voor verplaatsen (zoals de opdracht regel of Power shell) niet meer reageert wanneer Azure File Sync de gegevens uit de Cloud aanroept. Als u begint met [Azure file sync agent versie 3.0.12.0](storage-files-release-notes.md#supported-versions), kunt u met Azure file sync een gelaagd bestand verplaatsen buiten het eind punt van de server. We vermijden de eerder genoemde negatieve effecten door toe te staan dat het gelaagde bestand bestaat als een gelaagd bestand buiten het eind punt van de server en vervolgens het bestand vervolgens op de achtergrond aanroept. Dit betekent dat het verplaatsen op hetzelfde volume onmiddellijk wordt uitgevoerd en dat alle werkzaamheden het bestand naar de schijf terughalen nadat de verplaatsing is voltooid. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Ik heb een probleem met Azure File Sync op mijn server (synchronisatie, Cloud lagen, enz.). Moet ik mijn server-eind punt verwijderen en opnieuw maken?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Kan ik de opslag synchronisatie service en/of het opslag account verplaatsen naar een andere resource groep of een ander abonnement?**  
   Ja, de opslag synchronisatie service en/of het opslag account kunnen worden verplaatst naar een andere resource groep of een ander abonnement binnen de bestaande Azure AD-Tenant. Als het opslag account is verplaatst, moet u de Hybrid File Sync-Service toegang tot het opslag account geven (Zie [controleren of Azure file sync toegang heeft tot het opslag account](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync biedt geen ondersteuning voor het verplaatsen van het abonnement naar een andere Azure AD-Tenant.
    
* <a id="afs-ntfs-acls"></a>
  **Behoudt Azure File Sync NTFS Acl's op Directory-en bestands niveau en gegevens die zijn opgeslagen in Azure Files?**

    Vanaf februari 2020 24 worden de nieuwe en bestaande Acl's die zijn gelaagd door Azure file sync bewaard in de NTFS-indeling en worden de ACL-wijzigingen die rechtstreeks aan de Azure-bestands share zijn aangebracht, gesynchroniseerd met alle servers in de synchronisatie groep. Eventuele wijzigingen aan de Acl's die zijn aangebracht in Azure Files worden via Azure file sync gesynchroniseerd. Wanneer u gegevens naar Azure Files kopieert, moet u ervoor zorgen dat u SMB gebruikt om toegang te krijgen tot de share en uw Acl's te bewaren. Bestaande op REST gebaseerde hulp middelen, zoals AzCopy of Storage Explorer, behouden geen Acl's.

    Als u Azure Backup hebt ingeschakeld op de bestands shares die door het bestand sync worden beheerd, kunnen Acl's van bestanden worden hersteld als onderdeel van de werk stroom voor het terugzetten van back-ups. Dit werkt voor de volledige share of afzonderlijke bestanden/directory's.

    Als u moment opnamen gebruikt als onderdeel van de zelf-beheerde back-upoplossing voor bestands shares die worden beheerd door bestands synchronisatie, worden uw Acl's mogelijk niet op de juiste wijze teruggezet naar NTFS-Acl's als de moment opnamen zijn gemaakt vóór februari 24, 2020. Als dit het geval is, kunt u contact opnemen met de ondersteuning van Azure.
    
## <a name="security-authentication-and-access-control"></a>Beveiliging, verificatie en toegangs beheer
* <a id="ad-support"></a>
**Wordt verificatie op basis van identiteit en toegangs beheer ondersteund door Azure Files?**  
    
    Ja, Azure Files ondersteunt verificatie en toegangs beheer op basis van een identiteit. U kunt een van de twee manieren kiezen voor het gebruik van toegangs beheer op basis van de identiteit: on-premises Active Directory Domain Services (preview) of Azure Active Directory Domain Services (Azure AD DS). On-premises Active Directory Domain Services (AD DS) ondersteunt verificatie met behulp van AD DS machines die lid zijn van een domein, on-premises of in azure, om toegang te krijgen tot Azure-bestands shares via SMB. Met Azure AD DS-verificatie via SMB voor Azure Files kunnen AD DS Azure-Windows-Vm's die lid zijn van een domein, toegang krijgen tot shares, directory's en bestanden met behulp van Azure AD-referenties. Zie [overzicht van Azure files op identiteit gebaseerde verificatie voor SMB-toegang](storage-files-active-directory-overview.md)voor meer informatie. 

    Azure Files biedt twee extra manieren om toegangs beheer te beheren:

    - U kunt Shared Access signatures (SAS) gebruiken voor het genereren van tokens met specifieke machtigingen en die geldig zijn voor een opgegeven tijds interval. U kunt bijvoorbeeld een token genereren met alleen-lezen toegang tot een specifiek bestand met een verval datum van 10 minuten. Iedereen die beschikt over het token terwijl het token geldig is heeft alleen-lezen toegang tot dat bestand voor die tien minuten. Sleutels voor gedeelde toegangs handtekeningen worden alleen ondersteund via de REST API of in client bibliotheken. U moet de Azure-bestands share via SMB koppelen met behulp van de sleutels voor het opslag account.

    - Azure File Sync behoudt en repliceert alle discretionaire Acl's, of DACL'S, (of Active Directory of lokaal) naar alle server eindpunten die worden gesynchroniseerd. 
    
    U kunt de [toegang tot Azure Storage machtigen](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor een uitgebreide weer gave van alle protocollen die worden ondersteund op Azure Storage services. 

* <a id="ad-support-devices"></a>
**Ondersteunt Azure Files verificatie van Azure Active Directory Domain Services (Azure AD DS) SMB-toegang via Azure AD-referenties van apparaten die zijn gekoppeld aan of zijn geregistreerd bij Azure AD?**

    Nee, dit scenario wordt niet ondersteund.

* <a id="ad-support-rest-apis"></a>
**Zijn er REST-Api's ter ondersteuning van Get/set/Copy map/file NTFS Acl's?**

    Ja, we ondersteunen REST-Api's waarmee NTFS-Acl's voor mappen of bestanden worden opgehaald, ingesteld of gekopieerd wanneer de [2019-07-07](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (of hoger) wordt gebruikt rest API.

* <a id="ad-vm-subscription"></a>
**Kan ik toegang krijgen tot Azure-bestands shares met Azure AD-referenties van een VM onder een ander abonnement?**

    Als het abonnement waarmee de bestands share is geïmplementeerd, is gekoppeld aan dezelfde Azure AD-Tenant als de Azure AD DS-implementatie waaraan de virtuele machine is toegevoegd aan een domein, kunt u toegang krijgen tot Azure-bestands shares met dezelfde Azure AD-referenties. De beperking wordt niet toegepast op het abonnement, maar op de gekoppelde Azure AD-Tenant.
    
* <a id="ad-support-subscription"></a>
**Kan ik Azure AD DS of on-premises AD DS-verificatie inschakelen voor Azure-bestands shares met behulp van een Azure AD-Tenant die verschilt van de primaire Tenant van de Azure-bestands share?**

    Nee, Azure Files ondersteunt alleen Azure AD DS of on-premises AD DS integratie met een Azure AD-Tenant die zich in hetzelfde abonnement als de bestands share bevindt. Er kan slechts één abonnement worden gekoppeld aan een Azure AD-Tenant. Deze beperking is van toepassing op Azure AD DS en on-premises AD DS-verificatie methoden. Wanneer u on-premises AD DS voor verificatie gebruikt, [moet de AD DS referentie worden gesynchroniseerd met de Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) waaraan het opslag account is gekoppeld.

* <a id="ad-linux-vms"></a>
**Ondersteunt Azure-AD DS of on-premises AD DS-verificatie voor Azure-bestands shares virtuele Linux-machines?**

    Nee, authenticatie vanuit Linux-Vm's wordt niet ondersteund.

* <a id="ad-aad-smb-afs"></a>
**Bieden bestands shares die worden beheerd door Azure File Sync de verificatie van Azure AD DS of on-premises AD DS (preview)?**

    Ja, u kunt Azure AD DS-of on-premises AD DS-verificatie inschakelen op een bestands share die wordt beheerd door Azure File Sync. Wijzigingen in de map/bestand NTFS Acl's op lokale bestands servers worden trapsgewijs gelaagd en Azure Files en vice versa.

* <a id="ad-aad-smb-files"></a>
**Hoe kan ik controleren of ik AD DS verificatie op mijn opslag account heb ingeschakeld en de domein gegevens ophaal?**

    Zie [hier](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#1-enable-ad-authentication-for-your-account)voor instructies.
    
* <a id="encryption-at-rest"></a>
**Hoe kan ik ervoor zorgen dat mijn Azure-bestands share op rest versleuteld is?**  

    Ja. Zie [Azure Storage-service versleuteling](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie.

* <a id="access-via-browser"></a>
**Hoe kan ik toegang bieden tot een specifiek bestand met behulp van een webbrowser?**  

    U kunt hand tekeningen voor gedeelde toegang gebruiken voor het genereren van tokens met specifieke machtigingen en die geldig zijn voor een opgegeven tijds interval. U kunt bijvoorbeeld een token genereren dat alleen-lezen toegang geeft tot een specifiek bestand, gedurende een bepaalde periode. Iedereen die de URL heeft, kan het bestand rechtstreeks vanuit elke webbrowser openen terwijl het token geldig is. U kunt eenvoudig een gedeelde toegangs handtekening sleutel genereren op basis van een gebruikers interface, zoals Storage Explorer.

* <a id="file-level-permissions"></a>
**Is het mogelijk om alleen-lezen of alleen-schrijven machtigingen op te geven voor mappen in de share?**  

    Als u de bestands share koppelt met behulp van SMB, hebt u geen beheer op mapniveau over machtigingen. Als u echter een Shared Access Signature maakt met behulp van de REST API of client Bibliotheken, kunt u alleen-lezen of alleen-schrijven machtigingen voor mappen in de share opgeven.

* <a id="ip-restrictions"></a>
**Kan ik IP-beperkingen implementeren voor een Azure-bestands share?**  

    Ja. Toegang tot uw Azure-bestands share kan worden beperkt op het niveau van het opslag account. Zie [Azure Storage firewalls en virtuele netwerken configureren](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie.

* <a id="data-compliance-policies"></a>
**Wat voor nalevings beleid voor gegevens Azure Files worden ondersteund?**  

   Azure Files wordt uitgevoerd boven op dezelfde opslag architectuur die wordt gebruikt in andere opslag Services in Azure Storage. Azure Files past hetzelfde beleid voor gegevens naleving toe dat in andere Azure Storage-services wordt gebruikt. Raadpleeg voor meer informatie over de naleving van Azure Storage gegevens [Azure Storage compliance-aanbiedingen](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)en ga naar het [vertrouwens centrum van micro soft](https://microsoft.com/trustcenter/default.aspx).
   
### <a name="ad-authentication"></a>AD-verificatie
* <a id=""></a>
**Ondersteunt Azure Files Azure AD-verificatie Linux Vm's?**

    Nee, authenticatie vanuit Linux-Vm's wordt niet ondersteund.

* <a id="ad-multiple-forest"></a>
**Ondersteunt on-premises AD DS verificatie voor Azure-bestands shares de integratie met een AD DS omgeving met meerdere forests?**    

    Azure Files on-premises AD DS authenticatie alleen worden geïntegreerd met het forest van de domein service waarop het opslag account is geregistreerd. Als u verificatie van een ander forest wilt ondersteunen, moet uw omgeving een forest-vertrouwens relatie juist hebben geconfigureerd. De manier waarop Azure Files zich in AD DS bijna hetzelfde bevinden als een gewone bestands server, waar het een identiteit (computer-of service-aanmeldings account) in AD DS voor verificatie maakt. Het enige verschil is dat de geregistreerde SPN van het opslag account eindigt op ' file.core.windows.net ', wat niet overeenkomt met het domein achtervoegsel. Neem contact op met uw domein beheerder om te controleren of er een update van uw DNS-routerings beleid is vereist om meerdere Forest-verificatie mogelijk te maken vanwege het andere domein achtervoegsel.

* <a id=""></a>
**Welke regio's zijn beschikbaar voor Azure Files AD DS-verificatie (preview)?**

    Raadpleeg [AD DS regionale Beschik baarheid](storage-files-identity-auth-active-directory-enable.md#regional-availability) voor meer informatie.
    
* <a id="ad-aad-smb-afs"></a>
**Kan ik gebruikmaken van Azure Files Active Directory (AD)-verificatie (preview) op bestands shares die worden beheerd door Azure File Sync?**

    Ja, u kunt AD-verificatie inschakelen op een bestands share die wordt beheerd door Azure file sync. Wijzigingen in de map/bestand NTFS Acl's op lokale bestands servers worden trapsgewijs gelaagd en Azure Files en vice versa.

* <a id="ad-aad-smb-files"></a>
**Hoe kan ik controleren of AD-verificatie is ingeschakeld voor mijn opslag account en de gegevens van het AD-domein?**

    Raadpleeg de instructies die u [hier](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#enable-ad-authentication-for-your-account) kunt vinden om te controleren of Azure files AD-verificatie is ingeschakeld voor uw opslag account en de gegevens van het AD-domein op te halen.

* <a id="ad-aad-smb-files"></a>
**Is er een verschil in het maken van een computer account of een service-aanmeldings account voor het weer geven van mijn opslag account in AD?**

    Het maken van een [computer account](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standaard) of een [service aanmeldings account](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) heeft geen verschil op de manier waarop de verificatie met Azure files zou werken. U kunt uw eigen keuze maken voor het weer geven van een opslag account als identiteit in uw AD-omgeving. De standaard DomainAccountType die in de cmdlet voor samen voegen AzStorageAccountForAuth is ingesteld, is het computer account. De leeftijd van het wacht woord die in uw AD-omgeving is geconfigureerd, kan echter verschillen voor de computer-of service-aanmeldings account en u moet rekening houden om [het wacht woord van de identiteit van uw opslag account in AD bij te werken](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#5-update-ad-account-password).

## <a name="on-premises-access"></a>Lokale toegang

* <a id="port-445-blocked"></a>
**Mijn Internet provider of poort 445 wordt geblokkeerd en kan niet Azure Files gekoppeld. Wat moet ik doen?**

    Hier vindt u meer informatie over de verschillende manieren waarop u de [tijdelijke oplossing poort 445 kunt blok keren](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked). Azure Files staat alleen verbindingen toe met behulp van SMB 3,0 (met ondersteuning voor versleuteling) van buiten de regio of het Data Center. Het SMB 3,0-protocol heeft veel beveiligings functies geïntroduceerd, waaronder Channel Encryption, die zeer veilig zijn voor gebruik via internet. Het is echter mogelijk dat poort 445 is geblokkeerd vanwege historische oorzaken van beveiligings problemen die in lagere SMB-versies zijn aangetroffen. In het ideale geval moet de poort alleen worden geblokkeerd voor SMB 1,0-verkeer en moet SMB 1,0 worden uitgeschakeld op alle clients.

* <a id="expressroute-not-required"></a>
**Moet ik Azure ExpressRoute gebruiken om verbinding te maken met Azure Files of Azure File Sync on-premises te gebruiken?**  

    Nee. ExpressRoute is niet vereist voor toegang tot een Azure-bestands share. Als u on-premises een Azure-bestands share koppelt, is het vereist dat poort 445 (TCP uitgaand) is geopend voor Internet toegang (dit is de poort die door SMB wordt gebruikt om te communiceren). Als u Azure File Sync gebruikt, is de vereiste poort 443 (TCP uitgaand) voor HTTPS-toegang (geen SMB vereist). U *kunt* ExpressRoute echter gebruiken met een van deze toegangs opties.

* <a id="mount-locally"></a>
**Hoe kan ik een Azure-bestands share koppelen op mijn lokale computer?**  

    U kunt de bestands share koppelen met behulp van het SMB-protocol als poort 445 (TCP uitgaand) is geopend en de client het SMB 3,0-protocol ondersteunt (bijvoorbeeld als u Windows 10 of Windows Server 2016 gebruikt). Als poort 445 wordt geblokkeerd door het beleid van uw organisatie of door uw Internet provider, kunt u Azure File Sync gebruiken om toegang te krijgen tot uw Azure-bestands share.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hoe kan ik back-up maken van mijn Azure-bestands share?**  
    U kunt [moment opnamen van periodieke shares](storage-snapshots-files.md) gebruiken voor beveiliging tegen onbedoeld verwijderen. U kunt ook AzCopy, Robocopy of een back-upprogramma van derden gebruiken dat een back-up kan maken van een gekoppelde bestands share. Azure Backup biedt back-up van Azure Files. Meer informatie over het [maken van back-ups van Azure-bestands shares per Azure backup](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Momentopnamen van shares

### <a name="share-snapshots-general"></a>Moment opnamen delen: algemeen
* <a id="what-are-snaphots"></a>
**Wat zijn moment opnamen van bestands shares?**  
    U kunt moment opnamen van Azure-bestands shares gebruiken om een alleen-lezen versie van uw bestands shares te maken. U kunt Azure Files ook gebruiken om een eerdere versie van uw inhoud terug te kopiëren naar dezelfde share, naar een alternatieve locatie in azure, of on-premises voor meer wijzigingen. Zie [overzicht van moment opnamen delen](storage-snapshots-files.md)voor meer informatie over moment opnamen van shares.

* <a id="where-are-snapshots-stored"></a>
**Waar worden mijn moment opnamen van mijn shares opgeslagen?**  
    Moment opnamen van shares worden opgeslagen in hetzelfde opslag account als de bestands share.

* <a id="snapshot-consistency"></a>
**Zijn share momentopnamen toepassing consistent?**  
    Nee, moment opnamen van shares zijn geen toepassings consistent. De gebruiker moet de schrijf bewerkingen van de toepassing naar de share leegmaken voordat de moment opname van de share wordt gemaakt.

* <a id="snapshot-limits"></a>
**Gelden er limieten voor het aantal moment opnamen van shares dat ik kan gebruiken?**  
    Ja. Azure Files kunnen Maxi maal 200 share-moment opnamen worden bewaard. Share-moment opnamen tellen niet mee voor het share quotum, dus er is geen limiet per deel voor de totale ruimte die wordt gebruikt door alle moment opnamen van shares. De limieten van het opslag account zijn nog steeds van toepassing. Na 200 moment opnamen delen, moet u oudere moment opnamen verwijderen om nieuwe share-moment opnamen te maken.

* <a id="snapshot-cost"></a>
**Wat zijn de kosten voor het delen van moment opnamen?**  
    Standaard kosten voor trans acties en standaard opslag zijn van toepassing op de moment opname. Moment opnamen zijn incrementeel. De basis momentopname is de share zelf. Alle volgende moment opnamen worden incrementeel gegenereerd en worden alleen het verschil van de vorige moment opname opgeslagen. Dit betekent dat de Delta wijzigingen die in de factuur worden weer gegeven, mini maal zijn als het verloop van de workload mini maal is. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/storage/files/) voor de prijs informatie voor Standard Azure files. Vandaag de manier om te kijken naar de grootte van de moment opname van de share, wordt de gefactureerde capaciteit vergeleken met de gebruikte capaciteit. Er wordt aan gewerkt om de rapportage te verbeteren.

* <a id="ntfs-acls-snaphsots"></a>
**Zijn NTFS-Acl's voor mappen en bestanden opgeslagen in moment opnamen van shares?**  
    NTFS-Acl's voor mappen en bestanden worden opgeslagen in moment opnamen van shares.

### <a name="create-share-snapshots"></a>Momentopnamen van shares maken
* <a id="file-snaphsots"></a>
**Kan ik een moment opname van een share maken voor afzonderlijke bestanden?**  
    Moment opnamen van shares worden gemaakt op het niveau van de bestands share. U kunt afzonderlijke bestanden herstellen vanuit de moment opname van de bestands share, maar u kunt geen moment opnamen van shares op bestands niveau maken. Als u echter een moment opname op share niveau delen hebt gemaakt en u moment opnamen van shares wilt weer geven waarin een specifiek bestand is gewijzigd, kunt u dit doen onder **eerdere versies** op een Windows-gekoppelde share. 
    
    Als u een functie voor moment opnamen van bestanden nodig hebt, laat het ons dan weten [Azure files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Kan ik moment opnamen van shares van een versleutelde bestands share maken?**  
    U kunt een moment opname van een share maken van Azure-bestands shares waarvoor versleuteling is ingeschakeld op rest. U kunt bestanden herstellen vanaf een moment opname van een share naar een versleutelde bestands share. Als uw share is versleuteld, wordt de moment opname van de share ook versleuteld.

* <a id="geo-redundant-snaphsots"></a>
**Zijn mijn moment opnamen van mijn share geo-redundant?**  
    Moment opnamen van shares hebben dezelfde redundantie als de Azure-bestands share waarvoor ze zijn gemaakt. Als u geografisch redundante opslag voor uw account hebt geselecteerd, wordt uw moment opname van de share ook overbodig opgeslagen in de gekoppelde regio.

### <a name="manage-share-snapshots"></a>Moment opnamen van shares beheren
* <a id="browse-snapshots-linux"></a>
**Kan ik bladeren door mijn moment opnamen van shares van Linux?**  
    U kunt Azure CLI gebruiken om moment opnamen van shares te maken, weer te geven, te zoeken en te herstellen in Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Kan ik de moment opnamen van shares naar een ander opslag account kopiëren?**  
    U kunt bestanden van moment opnamen van shares naar een andere locatie kopiëren, maar u kunt de moment opnamen van shares zelf niet kopiëren.

### <a name="restore-data-from-share-snapshots"></a>Gegevens herstellen vanaf moment opnamen van shares
* <a id="promote-share-snapshot"></a>
**Kan ik een moment opname van een share promo veren naar het basis share?**  
    U kunt gegevens van een moment opname van een share naar een andere bestemming kopiëren. U kunt een moment opname van een share niet promo veren naar het basis share.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Kan ik gegevens herstellen vanuit mijn moment opname van een share naar een ander opslag account?**  
    Ja. Bestanden van een moment opname van een share kunnen worden gekopieerd naar de oorspronkelijke locatie of naar een alternatieve locatie die hetzelfde opslag account of een ander opslag account bevat, in dezelfde regio of in verschillende regio's. U kunt ook bestanden kopiëren naar een on-premises locatie of naar een andere cloud.    
  
### <a name="clean-up-share-snapshots"></a>Moment opnamen van shares opruimen
* <a id="delete-share-keep-snapshots"></a>
**Kan ik mijn share verwijderen, maar niet mijn moment opnamen van shares verwijderen?**  
    Als u actieve moment opnamen van shares op uw share hebt, kunt u uw share niet verwijderen. U kunt een API gebruiken om moment opnamen van shares te verwijderen, samen met de share. U kunt ook de moment opnamen van de share en de share in de Azure Portal verwijderen.

* <a id="delete-share-with-snapshots"></a>
**Wat gebeurt er met moment opnamen van mijn share als ik mijn opslag account Verwijder?**  
    Als u uw opslag account verwijdert, worden ook de moment opnamen van de shares verwijderd.

## <a name="billing-and-pricing"></a>Facturering en prijzen
* <a id="vm-file-share-network-traffic"></a>
**Telt het netwerk verkeer tussen een virtuele machine van Azure en een Azure-bestands share zich als externe band breedte die wordt gefactureerd aan het abonnement?**  
    Als de bestands share en de virtuele machine zich in dezelfde Azure-regio bevinden, worden er geen extra kosten in rekening gebracht voor het verkeer tussen de bestands share en de virtuele machine. Als de bestands share en de virtuele machine zich in verschillende regio's bevinden, wordt het verkeer tussen hen in rekening gebracht als externe band breedte.

* <a id="share-snapshot-price"></a>
**Wat zijn de kosten voor het delen van moment opnamen?**  
     Tijdens de preview-periode worden er geen kosten in rekening gebracht voor de capaciteit van de share momentopname. De standaard opslag voor uitgaand verkeer en de transactie kosten zijn van toepassing. Na algemene Beschik baarheid worden abonnementen in rekening gebracht voor capaciteit en trans acties op moment opnamen van shares.
     
     Moment opnamen van shares zijn incrementeel. De moment opname van de basis share is de share zelf. Alle volgende moment opnamen van de share zijn incrementeel en slaan alleen het verschil op van de vorige moment opname van de share. Er worden alleen kosten in rekening gebracht voor de gewijzigde inhoud. Als u een share met 100 GiB van gegevens hebt, maar slechts 5 GiB is gewijzigd sinds de laatste share-moment opname, verbruikt de moment opname van de share slechts 5 extra GiB en wordt 105 GiB gefactureerd. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/storage/files/)voor meer informatie over kosten voor trans acties en standaard uitgaand verkeer.

## <a name="scale-and-performance"></a>Schaal en prestaties
* <a id="files-scale-limits"></a>
**Wat zijn de schaal limieten van Azure Files?**  
    Zie [Azure files schaal baarheid en prestatie doelen](storage-files-scale-targets.md)voor meer informatie over de schaalbaarheids-en prestatie doelen voor Azure files.

* <a id="need-larger-share"></a>
**Welke grootten zijn er beschikbaar voor Azure-bestands shares?**  
    Groottes van Azure-bestands shares (Premium en Standard) kunnen tot 100 TiB worden geschaald. Zie de sectie [onboarding to large file shares (Standard-laag)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) van de plannings handleiding voor onboarding-instructies voor de grotere bestands shares voor de laag standaard.

* <a id="lfs-performance-impact"></a>
**Breidt de quota van mijn bestands share-effect uit mijn workloads of Azure File Sync?**
    
    Nee. Het verg Roten van het quotum heeft geen invloed op uw werk belastingen of Azure File Sync.

* <a id="open-handles-quota"></a>
**Hoeveel clients hebben gelijktijdig toegang tot hetzelfde bestand?**   
    Er is een quotum van 2.000 open ingangen voor één bestand. Wanneer u 2.000 open ingangen hebt, wordt een fout bericht weer gegeven met de melding dat het quotum is bereikt.

* <a id="zip-slow-performance"></a>
**Mijn prestaties zijn traag wanneer ik bestanden uitpakt in Azure Files. Wat moet ik doen?**  
    Als u een groot aantal bestanden naar Azure Files wilt overdragen, raden we u aan om AzCopy (voor Windows; in Preview voor Linux en UNIX) of Azure PowerShell te gebruiken. Deze hulpprogram ma's zijn geoptimaliseerd voor netwerk overdracht.

* <a id="slow-perf-windows-81-2012r2"></a>
**Waarom is mijn prestaties traag nadat ik mijn Azure-bestands share heb gekoppeld op Windows Server 2012 R2 of Windows 8,1?**  
    Er is een bekend probleem bij het koppelen van een Azure-bestands share op Windows Server 2012 R2 en Windows 8,1. Het probleem is opgelost in de cumulatieve update van april 2014 voor Windows 8,1 en Windows Server 2012 R2. Voor optimale prestaties moet u ervoor zorgen dat deze patch wordt toegepast op alle exemplaren van Windows Server 2012 R2 en Windows 8,1. (U moet altijd Windows-patches ontvangen via Windows Update.) Voor meer informatie raadpleegt u het bijbehorende micro soft Knowledge Base-artikel [langzame prestaties wanneer u Azure files van Windows 8,1 of Server 2012 R2 opent](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Functies en interoperabiliteit met andere services
* <a id="cluster-witness"></a>
**Kan ik mijn Azure-bestands share gebruiken als een *Bestands share-Witness* voor mijn Windows Server-failovercluster?**  
    Deze configuratie wordt momenteel niet ondersteund voor een Azure-bestands share. Zie [een Cloudwitness implementeren voor een failovercluster](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)voor meer informatie over het instellen van dit voor Azure Blob Storage.

* <a id="containers"></a>
**Kan ik een Azure-bestands share koppelen aan een Azure-container exemplaar?**  
    Ja, Azure-bestands shares zijn een goede optie wanneer u gegevens wilt behouden die langer duren dan de levens duur van een container exemplaar. Zie [een Azure-bestands share koppelen met Azure container instances](../../container-instances/container-instances-mounting-azure-files-volume.md)voor meer informatie.

* <a id="rest-rename"></a>
**Is er een bewerking voor naam wijzigen in de REST API?**  
    Momenteel niet.

* <a id="nested-shares"></a>
**Kan ik geneste shares instellen? Met andere woorden, een share onder een share?**  
    Nee. De bestands share *is* het virtuele stuur programma dat u kunt koppelen, dus geneste shares worden niet ondersteund.

* <a id="ibm-mq"></a>
**Azure Files met IBM MQ Hoe kan ik gebruiken?**  
    IBM heeft een document uitgebracht waarmee klanten van IBM MQ Azure Files met de IBM-service kunnen configureren. Zie [How to set a IBM MQ multi-instance Queue Manager with Microsoft Azure files service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)(Engelstalig) voor meer informatie.

## <a name="see-also"></a>Zie ook
* [Problemen met Azure Files in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
* [Problemen met Azure Files in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
* [Problemen met Azure Files Sync oplossen](storage-sync-files-troubleshoot.md)

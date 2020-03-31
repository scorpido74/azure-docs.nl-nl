---
title: SAP LaMa-connector voor Azure | Microsoft Documenten
description: SAP-systemen beheren op Azure met SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: fda62ff0af29c7cf681d9438b02420d299535701
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293948"
---
# <a name="sap-lama-connector-for-azure"></a>SAP LaMa-connector voor Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Algemene ondersteuningsverklaring: Open altijd een incident met SAP op component BC-VCM-LVM-HYPERV als u ondersteuning nodig hebt voor SAP LaMa of de Azure-connector.

SAP LaMa wordt door veel klanten gebruikt om hun SAP-landschap te exploiteren en te monitoren. Sinds SAP LaMa 3.0 SP05 wordt deze standaard geleverd met een connector naar Azure. U deze connector gebruiken om virtuele machines te detoewijzen en te starten, beheerde schijven te kopiëren en te verplaatsen en beheerde schijven te verwijderen. Met deze basisbewerkingen u SAP-systemen verplaatsen, kopiëren, klonen en vernieuwen met SAP LaMa.

In deze handleiding wordt beschreven hoe u de Azure-connector voor SAP LaMa instelt, virtuele machines maakt die kunnen worden gebruikt om adaptieve SAP-systemen te installeren en hoe u deze configureren.

> [!NOTE]
> De connector is alleen beschikbaar in de SAP LaMa Enterprise Edition

## <a name="resources"></a>Resources

De volgende SAP Notes zijn gerelateerd aan het onderwerp SAP LaMa op Azure:

| Notitienummer | Titel |
| --- | --- |
| [2343511] |Microsoft Azure-connector voor SAP-landschapsbeheer (LaMa) |
| [2350235] |SAP Landscape Management 3.0 - Enterprise edition |

Lees ook de [SAP Help Portal voor SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Algemene opmerkingen

* Zorg ervoor dat *u Automatisch mountpoint maken* in setup -> Instellingen ->-engine inschakelt  
  Als SAP LaMa volumes monteert met de SAP Adaptive Extensions op een virtuele machine, moet het bevestigingspunt bestaan als deze instelling niet is ingeschakeld.

* Gebruik afzonderlijk subnet en gebruik geen dynamische IP-adressen om te voorkomen dat IP-adres wordt "gestolen" bij het implementeren van nieuwe VM's en dat ER GEEN SAP-exemplaren zijn voorbereid  
  Als u dynamische IP-adrestoewijzing gebruikt in het subnet, dat ook wordt gebruikt door SAP LaMa, kan het voorbereiden van een SAP-systeem met SAP LaMa mislukken. Als een SAP-systeem niet is voorbereid, worden de IP-adressen niet gereserveerd en kunnen ze worden toegewezen aan andere virtuele machines.

* Als u zich aanmeldt bij beheerde hosts, moet u ervoor zorgen dat bestandssystemen niet worden losgekoppeld  
  Als u zich aanmeldt bij een Virtuele Linux-machine en de werkmap wijzigt in een map in een bevestigingspunt, bijvoorbeeld /usr/sap/AH1/ASCS00/exe, kan het volume niet worden ontkoppeld en mislukt een verhuizing of onvoorbereiding.

* Zorg ervoor dat u CLOUD_NETCONFIG_MANAGE op virtuele sles linux machines van SUSE SLES uitschakelt. Zie [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633)voor meer informatie.

## <a name="set-up-azure-connector-for-sap-lama"></a>Azure-connector instellen voor SAP LaMa

De Azure-connector wordt verzonden vanaf SAP LaMa 3.0 SP05. We raden u aan altijd het nieuwste ondersteuningspakket en patch voor SAP LaMa 3.0 te installeren.

De Azure-connector gebruikt de Azure Resource Manager API om uw Azure-resources te beheren. SAP LaMa kan een Service Principal of een Managed Identity gebruiken om zich te authenticeren tegen deze API. Als uw SAP LaMa wordt uitgevoerd op een Azure VM, raden we u aan een beheerde identiteit te gebruiken zoals beschreven in hoofdstuk [Gebruik een beheerde identiteit om toegang te krijgen tot de Azure API.](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) Als u een Service Principal wilt gebruiken, volgt u de stappen in hoofdstuk [Gebruik een serviceprincipal om toegang te krijgen tot de Azure API.](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e)

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Een serviceprincipal gebruiken om toegang te krijgen tot de Azure API

De Azure-connector kan een Service Principal gebruiken om te autoriseren tegen Microsoft Azure. Volg deze stappen om een Service Principal voor SAP Landscape Management (LaMa) te maken.

1. Ga naar https://portal.azure.com
1. Het Azure Active Directory-blad openen
1. Klik op App-registraties
1. Klik op Nieuwe registratie
1. Voer een naam in en klik op Registreren
1. Selecteer de nieuwe app en klik op Certificaten & geheimen op het tabblad Instellingen
1. Maak een nieuw clientgeheim, voer een beschrijving in voor een nieuwe sleutel, selecteer wanneer het geheim verloopt en klik op Opslaan
1. Noteer de waarde. Het wordt gebruikt als wachtwoord voor de Service Principal
1. Noteer de toepassings-id. Het wordt gebruikt als gebruikersnaam van de Service Principal

De Serviceprincipal heeft standaard geen machtigingen om toegang te krijgen tot uw Azure-bronnen. U moet de Service Principal machtigingen geven om toegang te krijgen tot hen.

1. Ga naar https://portal.azure.com
1. Het blad resourcegroepen openen
1. Selecteer de resourcegroep die u wilt gebruiken
1. Klik op Toegangsbesturingselement (IAM)
1. Klik op Roltoewijzing toevoegen
1. Selecteer de rol inzender
1. Voer de naam in van de toepassing die u hierboven hebt gemaakt
1. Op Opslaan klikken
1. Herhaal stap 3 tot en met 8 voor alle resourcegroepen die u wilt gebruiken in SAP LaMa

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Een beheerde identiteit gebruiken om toegang te krijgen tot de Azure API

Als u een beheerde identiteit wilt gebruiken, moet uw SAP LaMa-exemplaar worden uitgevoerd op een Azure VM met een systeem of door de gebruiker toegewezen identiteit. Lees [Wat zijn beheerde identiteiten voor Azure-resources en](../../../active-directory/managed-identities-azure-resources/overview.md) [Beheerde identiteiten configureren voor Azure-resources op een VM met behulp van de Azure-portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)voor meer informatie over beheerde identiteiten.

De beheerde identiteit heeft standaard geen machtigingen voor toegang tot uw Azure-bronnen. Je moet het toestemming geven om toegang te krijgen.

1. Ga naar https://portal.azure.com
1. Het blad resourcegroepen openen
1. Selecteer de resourcegroep die u wilt gebruiken
1. Klik op Toegangsbesturingselement (IAM)
1. Klik op Toevoegen > Roltoewijzing toevoegen
1. Selecteer de rol inzender
1. Selecteer 'Virtuele machine' voor 'Toegang toewijzen tot'
1. Selecteer de virtuele machine waarop uw SAP LaMa-exemplaar wordt uitgevoerd
1. Op Opslaan klikken
1. Herhaal de stappen voor alle resourcegroepen die u wilt gebruiken in SAP LaMa

Selecteer in uw SAP LaMa Azure-connectorconfiguratie 'Beheerde identiteit gebruiken' om het gebruik van de beheerde identiteit mogelijk te maken. Als u een door het systeem toegewezen identiteit wilt gebruiken, moet u het veld Gebruikersnaam leeg laten. Als u een door een gebruiker toegewezen identiteit wilt gebruiken, voert u de door de gebruiker toegewezen identiteits-id in het veld Gebruikersnaam in.

### <a name="create-a-new-connector-in-sap-lama"></a>Maak een nieuwe connector in SAP LaMa

Open de SAP LaMa-website en navigeer naar Infrastructuur. Ga naar cloudmanagers op het tabblad En klik op Toevoegen. Selecteer de Microsoft Azure Cloud Adapter en klik op Volgende. Voer de volgende informatie in:

* Label: een naam kiezen voor de connectorinstantie
* Gebruikersnaam: Service Principal Application ID of ID van de door de gebruiker toegewezen identiteit van de virtuele machine. Zie [Een systeem of door een gebruiker toegewezen identiteit gebruiken] voor meer informatie
* Wachtwoord: Service Principal-sleutel/wachtwoord. U dit veld leeg laten als u een door het systeem of de door de gebruiker toegewezen identiteit gebruikt.
* URL: Standaard blijven`https://management.azure.com/`
* Monitoring interval (seconden): Moet ten minste 300
* Beheerde identiteit gebruiken: SAP LaMa kan een door een systeem of gebruiker toegewezen identiteit gebruiken om te verifiëren tegen de Azure API. Zie hoofdstuk [Gebruik een beheerde identiteit om toegang te krijgen tot de Azure API](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) in deze handleiding.
* Abonnements-id: Azure-abonnements-id
* Azure Active Directory Tenant ID: ID van de Active Directory-tenant
* Proxyhost: Hostname van de proxy als SAP LaMa een proxy nodig heeft om verbinding te maken met het internet
* Proxypoort: TCP-poort van de proxy
* Opslagtype wijzigen om kosten te besparen: schakel deze instelling in als de Azure-adapter het opslagtype van de beheerde schijven moet wijzigen om kosten te besparen wanneer de schijven niet in gebruik zijn. Voor gegevensschijven waarnaar wordt verwezen in een SAP-instantieconfiguratie, wijzigt de adapter het schijftype in Standaardopslag tijdens een instantie die zich niet voorbereidt en teruggaat naar het oorspronkelijke opslagtype tijdens een voorbereiding van een instantie. Als u een virtuele machine stopt in SAP LaMa, verandert de adapter het opslagtype van alle aangesloten schijven, inclusief de OS-schijf, in standaardopslag. Als u een virtuele machine start in SAP LaMa, verandert de adapter het opslagtype terug naar het oorspronkelijke opslagtype.

Klik op Testconfiguratie om uw invoer te valideren. Je zou moeten zien

Verbinding geslaagd: de verbinding met de Microsoft-cloud is geslaagd. Gevonden 7 brongroepen (slechts 10 groepen aangevraagd)

onderaan de website.

## <a name="provision-a-new-adaptive-sap-system"></a>Een nieuw adaptief SAP-systeem inrichten

U handmatig een nieuwe virtuele machine implementeren of een van de Azure-sjablonen gebruiken in de [quickstart-repository.](https://github.com/Azure/azure-quickstart-templates) Het bevat sjablonen voor [SAP NetWeaver ASCS,](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs) [SAP NetWeaver-toepassingsservers](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)en de [database](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). U deze sjablonen ook gebruiken om nieuwe hosts in te richten als onderdeel van een systeemkopie/kloon enz.

We raden u aan een apart subnet te gebruiken voor alle virtuele machines die u met SAP LaMa wilt beheren en geen dynamische IP-adressen te gebruiken om te voorkomen dat IP-adres wordt "gestolen" bij het implementeren van nieuwe virtuele machines en SAP-exemplaren niet zijn voorbereid.

> [!NOTE]
> Verwijder indien mogelijk alle virtuele machine-extensies, omdat deze lange looptijden kunnen veroorzaken voor het loskoppelen van schijven van een virtuele machine.

Zorg ervoor \<dat de gebruiker \<hanasid>adm, sapsid>adm en groep sapsys bestaan op de doelmachine met dezelfde ID en gid of gebruik LDAP. Schakel de NFS-server in en start deze op de virtuele machines die moeten worden gebruikt om de SAP NetWeaver (A)SCS uit te voeren.

### <a name="manual-deployment"></a>Handmatige implementatie

SAP LaMa communiceert met de virtuele machine via de SAP Host Agent. Als u de virtuele machines handmatig of niet implementeert met de sjabloon Azure Resource Manager uit de quickstart-opslagplaats, moet u de nieuwste SAP Host Agent en de SAP Adaptive Extensions installeren. Zie SAP Note [2343511]voor meer informatie over de vereiste patchniveaus voor Azure.

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Handmatige implementatie van een Linux Virtual Machine

Maak een nieuwe virtuele machine met een van de ondersteunde besturingssystemen in SAP Note [2343511]. Voeg extra IP-configuraties toe voor de SAP-exemplaren. Elke instantie moet ten minste op IP-adres en moet worden geïnstalleerd met behulp van een virtuele hostname.

De SAP NetWeaver ASCS-instantie heeft schijven\<nodig voor /sapmnt/\<SAPSID>, /usr/sap/ SAPSID>,\</usr/sap/trans en /usr/sap/sap/sapsid>adm. De SAP NetWeaver-toepassingsservers hebben geen extra schijven nodig. Alles wat met de SAP-instantie te maken heeft, moet op de ASCS worden opgeslagen en via NFS worden geëxporteerd. Anders is het momenteel niet mogelijk om extra applicatieservers toe te voegen met SAP LaMa.

![SAP NetWeaver ASCS op Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Handmatige implementatie voor SAP HANA

Maak een nieuwe virtuele machine met een van de ondersteunde besturingssystemen voor SAP HANA zoals vermeld in SAP Note [2343511]. Voeg één extra IP-configuratie toe voor SAP HANA en één per HANA-tenant.

SAP HANA heeft schijven nodig voor /hana/shared, /hana/backup, /hana/data en /hana/log

![SAP HANA op Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Handmatige implementatie voor Oracle Database op Linux

Maak een nieuwe virtuele machine met een van de ondersteunde besturingssystemen voor Oracle-databases zoals vermeld in SAP Note [2343511]. Voeg één extra IP-configuratie toe voor de Oracle-database.

De Oracle-database heeft schijven nodig voor /oracle, /home/oraod1 en /home/oracle

![Oracle-database op Linux](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Handmatige implementatie voor Microsoft SQL Server

Maak een nieuwe virtuele machine met een van de ondersteunde besturingssystemen voor Microsoft SQL Server zoals vermeld in SAP Note [2343511]. Voeg één extra IP-configuratie toe voor de SQL Server-instantie.

De SQL Server-databaseserver heeft schijven nodig voor de databasegegevens en logboekbestanden en -schijven voor c:\usr\sap.

![Oracle-database op Linux](media/lama/sap-lama-db-sql.png)

Zorg ervoor dat u een ondersteund Microsoft ODBC-stuurprogramma voor SQL Server installeert op een virtuele machine die u wilt gebruiken om een SAP NetWeaver-toepassingsserver te verplaatsen naar of als een systeemkopie-/kloondoel.

SAP LaMa kan SQL Server zelf niet verplaatsen, zodat een virtuele machine die u wilt gebruiken om een database-instantie te verplaatsen naar of als een systeem kopie / kloon doel sql server vooraf geïnstalleerd.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Virtuele machine implementeren met behulp van een Azure-sjabloon

Download de volgende beschikbare archieven van de [SAP Software Marketplace](https://support.sap.com/swdc) voor het besturingssysteem van de virtuele machines:

1. SAPCAR 7.21
1. SAP HOST AGENT 7.21
1. SAP ADAPTIVE EXTENSION 1.0 EXT

Download ook de volgende onderdelen uit het [Microsoft Download Center](https://www.microsoft.com/download)

1. Microsoft Visual C++ 2010 Redistributable Package (x64) (alleen Windows)
1. Microsoft ODBC-stuurprogramma voor SQL Server (alleen SQL Server)

De onderdelen zijn nodig om de sjabloon te implementeren. De eenvoudigste manier om ze beschikbaar te maken voor de sjabloon is door ze te uploaden naar een Azure-opslagaccount en een SAS (Shared Access Signature) te maken.

De sjablonen hebben de volgende parameters:

* sapSystemId: De SAP-systeem-ID. Het wordt gebruikt om de schijflay-out te\<maken (bijvoorbeeld /usr/sap/ sap/ sapsid>).

* computerNaam: de computernaam van de nieuwe virtuele machine. Deze parameter wordt ook gebruikt door SAP LaMa. Wanneer u deze sjabloon gebruikt om een nieuwe virtuele machine in te richten als onderdeel van een systeemkopie, wacht SAP LaMa tot de host met deze computernaam kan worden bereikt.

* osType: het type besturingssysteem dat u wilt implementeren.

* dbtype: het type van de database. Deze parameter wordt gebruikt om te bepalen hoeveel extra IP-configuraties moeten worden toegevoegd en hoe de schijfindeling eruit moet zien.

* sapSystemSize: de grootte van het SAP-systeem dat u wilt implementeren. Het wordt gebruikt om het type en de grootte van de virtuele machineinstantie te bepalen.

* adminGebruikersnaam: Gebruikersnaam voor de virtuele machine.

* adminPassword: Wachtwoord voor de virtuele machine. U ook een openbare sleutel voor SSH verstrekken.

* sshKeyData: Openbare SSH-toets voor de virtuele machines. Alleen ondersteund voor Linux-besturingssystemen.

* subnetId: de id van het subnet dat u wilt gebruiken.

* deployEmptyTarget: U een leeg doel implementeren als u de virtuele machine als doel wilt gebruiken voor een verplaatsing of iets dergelijks in een instantie. In dit geval zijn er geen extra schijven of IP-configuraties toegevoegd.

* sapcarLocatie: de locatie voor de sapcar-toepassing die overeenkomt met het besturingssysteem dat u implementeert. sapcar wordt gebruikt om de archieven die u opgeeft in andere parameters te extraheren.

* sapHostAgentArchiveLocatie: de locatie van het SAP Host Agent-archief. SAP Host Agent wordt geïmplementeerd als onderdeel van deze sjabloonimplementatie.

* sapacExtLocatie: De locatie van de SAP Adaptive Extensions. SAP Note [2343511] bevat het minimale patchniveau dat vereist is voor Azure.

* vcRedistLocatie: de locatie van de VC Runtime die nodig is om de SAP Adaptive Extensions te installeren. Deze parameter is alleen vereist voor Windows.

* odbcDriverLocatie: de locatie van het ODBC-stuurprogramma dat u wilt installeren. Alleen het Microsoft ODBC-stuurprogramma voor SQL Server wordt ondersteund.

* sapadmPassword: Het wachtwoord voor de sapadm-gebruiker.

* sapadmId: De Linux User ID van de sapadm gebruiker. Niet vereist voor Windows.

* sapsysGid: De Linux-groeps-ID van de sapsys-groep. Niet vereist voor Windows.

* _artifactsLocation: De basis URI, waar artefacten vereist door deze sjabloon zich bevinden. Wanneer de sjabloon wordt geïmplementeerd met behulp van de bijbehorende scripts, wordt een privélocatie in het abonnement gebruikt en wordt deze waarde automatisch gegenereerd. Alleen nodig als u de sjabloon niet vanuit GitHub implementeert.

* _artifactsLocationSasToken: De sasToken die nodig is om toegang te krijgen tot _artifactsLocation. Wanneer de sjabloon wordt geïmplementeerd met behulp van de bijbehorende scripts, wordt er automatisch een sasToken gegenereerd. Alleen nodig als u de sjabloon niet vanuit GitHub implementeert.

### <a name="sap-hana"></a>SAP HANA

In de onderstaande voorbeelden gaan we ervan uit dat u SAP HANA installeert met systeem-ID HN1 en het SAP NetWeaver-systeem met systeem-ID AH1. De virtuele hostnamen zijn hn1-db voor de HANA-instantie, ah1-db voor de HANA-tenant die wordt gebruikt door het SAP NetWeaver-systeem, ah1-ascs voor de SAP NetWeaver ASCS en ah1-di-0 voor de eerste SAP NetWeaver-toepassingsserver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>SAP NetWeaver ASCS voor SAP HANA installeren met Azure Managed Disks

Voordat u de SAP Software Provisioning Manager (SWPM) start, moet u het IP-adres van virtuele hostname van de ASCS monteren. De aanbevolen manier is om sapacext te gebruiken. Als u het IP-adres monteert met sapacext, moet u het IP-adres opnieuw monteren na een herstart.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Voer SWPM uit en gebruik *ah1-ascs* voor de *HOSTName van DE ASCS Instance*.

![Linux][Logo_Linux] Linux  
Voeg de volgende profielparameter toe aan het SAP Host Agent-profiel, dat zich bevindt op /usr/sap/hostctrl/exe/host_profile. Zie SAP Note [2628497]voor meer informatie.
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>SAP NetWeaver ASCS voor SAP HANA installeren op Azure NetAppFiles (ANF) BETA

> [!NOTE]
> Deze functionaliteit is noch GA nog niet. Zie voor meer informatie SAP Note [2815988] (alleen zichtbaar voor preview-klanten).
Open een SAP-incident op component BC-VCM-LVM-HYPERV en vraag om lid te worden van de LaMa-opslagadapter voor azure NetApp Files preview

ANF biedt NFS voor Azure. In het kader van SAP LaMa vereenvoudigt dit de creatie van de ABAP Central Services (ASCS) exemplaren en de daaropvolgende installatie van applicatieservers. Voorheen moest de ASCS-instantie ook als NFS-server optreden en moest de parameter acosprep/nfs_paths worden toegevoegd aan de host_profile van de SAP Hostagent.

#### <a name="anf-is-currently-available-in-these-regions"></a>ANF is momenteel beschikbaar in deze regio's:

Australië Oost, Centraal VS, Oost-VS, Oost-VS 2, Noord-Europa, Zuid-Centraal VS, West-Europa en West-VS 2.

#### <a name="network-requirements"></a>Netwerkvereisten

ANF vereist een gedelegeerd subnet dat deel moet uitmaken van dezelfde VNET als de SAP-servers. Hier is een voorbeeld voor een dergelijke configuratie.
Dit scherm toont de creatie van het VNET en het eerste subnet:

![SAP LaMa maakt virtueel netwerk voor Azure ANF ](media/lama/sap-lama-createvn-50.png)

Met de volgende stap wordt het gedelegeerde subnet voor Microsoft.NetApp/volumes gedeisd.

![SAP LaMa voegt gedelegeerd subnet toe ](media/lama/sap-lama-addsubnet-50.png)

![SAP LaMa lijst van subnetten ](media/lama/sap-lama-subnets.png)

Nu moet er een NetApp-account worden gemaakt binnen de Azure-portal:

![SAP LaMa maken NetApp account ](media/lama/sap-lama-create-netappaccount-50.png)

![SAP LaMa NetApp account aangemaakt ](media/lama/sap-lama-netappaccount.png)

Binnen het NetApp-account geeft de capaciteitsgroep de grootte en het type schijven voor elke groep aan:

![SAP LaMa creëert NetApp capaciteitspool ](media/lama/sap-lama-capacitypool-50.png)

![SAP LaMa NetApp capaciteitspool gemaakt ](media/lama/sap-lama-capacitypool-list.png)

De NFS-volumes kunnen nu worden gedefinieerd. Aangezien er volumes voor meerdere systemen in één pool zullen zijn, moet een zelfverklarend naamgevingsschema worden gekozen. Het toevoegen van de SID helpt om gerelateerde volumes samen te groeperen. Voor de ASCS en de AS instance zijn de volgende mounts nodig: */sapmnt/\<SID\>*, */usr/sap/\<SID\>*, *en/home/sid\<\>adm*. Optioneel is */usr/sap/trans* nodig voor de centrale transportmap, die in ieder geval door alle systemen van één landschap wordt gebruikt.

> [!NOTE]
> Tijdens de BÈTAfase moet de naam van de volumes uniek zijn binnen het abonnement.

![SAP LaMa maakt een volume 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa creëert een volume 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa maakt een volume 3 ](media/lama/sap-lama-createvolume3-80.png)

Deze stappen moeten ook voor de andere volumes worden herhaald.

![SAP LaMa lijst van gecreëerde volumes ](media/lama/sap-lama-volumes.png)

Nu moeten deze volumes worden gemonteerd op de systemen waar de eerste installatie met de SAP SWPM zal worden uitgevoerd.

Eerst moeten de bevestigingspunten worden gemaakt. In dit geval is de SID AN1, zodat de volgende opdrachten moeten worden uitgevoerd:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Vervolgens worden de ANF-volumes gemonteerd met de volgende opdrachten:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
De mount commando's kunnen ook worden afgeleid uit het portaal. De lokale bevestigingspunten moeten worden aangepast.

Gebruik de opdracht df -h om te verifiëren.

![SAP LaMa mount punten OS niveau ](media/lama/sap-lama-mounts.png)

Nu moet de installatie met SWPM worden uitgevoerd.

Dezelfde stappen moeten worden uitgevoerd voor ten minste één AS-instantie.

Na de succesvolle installatie moet het systeem ontdekt worden binnen SAP LaMa.

De bevestigingspunten moeten er als volgt uitzien voor de ASCS en de AS-instantie:

![SAP LaMa mount punten ](media/lama/sap-lama-ascs.png) in LaMa (Dit is een voorbeeld. De IP-adressen en het exportpad verschillen van de eerder gebruikte ADRESSEN)


#### <a name="install-sap-hana"></a>SAP HANA installeren

Als u SAP HANA installeert met de commandline tool hdblcm, gebruikt u parameter -hostname om een virtuele hostname te geven. U moet het IP-adres van de virtuele hostnaam van de database toevoegen aan een netwerkinterface. De aanbevolen manier is om sapacext te gebruiken. Als u het IP-adres monteert met sapacext, moet u het IP-adres opnieuw monteren na een herstart.

Voeg een andere virtuele hostnaam en IP-adres toe voor de naam die door de toepassingsservers wordt gebruikt om verbinding te maken met de HANA-tenant.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Voer de installatie van swpm in de database-instantie uit op de virtuele machine van de toepassingsserver, niet op de virtuele HANA-machine. Gebruik *ah1-db* voor de *databasehost* in *de dialoog Database voor SAP-systeem*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>SAP NetWeaver-toepassingsserver voor SAP HANA installeren

Voordat u de SAP Software Provisioning Manager (SWPM) start, moet u het IP-adres van virtuele hostname van de toepassingsserver monteren. De aanbevolen manier is om sapacext te gebruiken. Als u het IP-adres monteert met sapacext, moet u het IP-adres opnieuw monteren na een herstart.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Het wordt aanbevolen om SAP NetWeaver-profielparameter dbs/hdb/hdb_use_ident te gebruiken om de identiteit in te stellen die wordt gebruikt om de sleutel in de HDB-gebruikerswinkel te vinden. U deze parameter handmatig toevoegen na de installatie van de databaseinstantie met SWPM of SWPM uitvoeren met

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Als u het handmatig instelt, moet u ook nieuwe HDB-gebruikerswinkels maken.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Gebruik *ah1-di-0* voor de *hostnaam PAS-instantie* in het *dialoogvenster Primaire toepassingsserverinstantie*.

#### <a name="post-installation-steps-for-sap-hana"></a>Stappen na installatie voor SAP HANA

Zorg ervoor dat u een back-up maakt van de SYSTEMDB en alle tenantdatabases voordat u een tenantkopie probeert uit te voeren, te verplaatsen of een systeemreplicatie te maken.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

In de onderstaande voorbeelden gaan we ervan uit dat u het SAP NetWeaver-systeem met systeem-ID AS1 installeert. De virtuele hostnamen zijn as1-db voor de SQL Server-instantie die wordt gebruikt door het SAP NetWeaver-systeem, as1-ascs voor de SAP NetWeaver ASCS en as1-di-0 voor de eerste SAP NetWeaver-toepassingsserver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>SAP NetWeaver ASCS voor SQL Server installeren

Voordat u de SAP Software Provisioning Manager (SWPM) start, moet u het IP-adres van virtuele hostname van de ASCS monteren. De aanbevolen manier is om sapacext te gebruiken. Als u het IP-adres monteert met sapacext, moet u het IP-adres opnieuw monteren na een herstart.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Voer SWPM uit en gebruik *as1-ascs* voor de *HOSTName van ASCS Instance*.

#### <a name="install-sql-server"></a>SQL Server installeren

U moet het IP-adres van de virtuele hostnaam van de database toevoegen aan een netwerkinterface. De aanbevolen manier is om sapacext te gebruiken. Als u het IP-adres monteert met sapacext, moet u het IP-adres opnieuw monteren na een herstart.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Voer de installatie van swpm in de databaseinstantie uit op de virtuele SQL-server. Gebruik SAPINST_USE_HOSTNAME=*as1-db* om de hostnaam te overschrijven die wordt gebruikt om verbinding te maken met SQL Server. Als u de virtuele machine hebt geïmplementeerd met de sjabloon Azure Resource Manager, moet u de map die wordt gebruikt voor de databasegegevensbestanden instellen op *C:\sql\gegevens* en databaselogboekbestand op *C:\sql\log*.

Zorg ervoor dat de gebruiker *NT AUTHORITY\SYSTEM* toegang heeft tot de SQL Server en de serverrol *sysadmin*heeft. Zie SAP Note [1877727] en [2562184]voor meer informatie.

#### <a name="install-sap-netweaver-application-server"></a>SAP NetWeaver-toepassingsserver installeren

Voordat u de SAP Software Provisioning Manager (SWPM) start, moet u het IP-adres van virtuele hostname van de toepassingsserver monteren. De aanbevolen manier is om sapacext te gebruiken. Als u het IP-adres monteert met sapacext, moet u het IP-adres opnieuw monteren na een herstart.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Gebruik *as1-di-0* voor de *hostnaam PAS-instantie* in het *dialoogvenster Primaire toepassingsserverinstantie*.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="errors-and-warnings-during-discover"></a>Fouten en waarschuwingen tijdens Discover

* De SELECT-toestemming is geweigerd
  * [Microsoft] [ODBC SQL Server-stuurprogramma] [SQL Server] De SELECT-toestemming werd geweigerd op het object 'log_shipping_primary_databases', database 'msdb', schema 'dbo'. [SOAPFaultException]  
  De SELECT-toestemming werd geweigerd op het object 'log_shipping_primary_databases', database 'msdb', schema 'dbo'.
  * Oplossing  
    Controleer of *NT AUTHORITY\SYSTEM* toegang heeft tot de SQL Server. Zie SAP Note [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Fouten en waarschuwingen voor bijvoorbeeld validatie

* Er is een uitzondering gemaakt bij de validatie van de HDB userstore  
  * zie Logboekviewer  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: Uitzondering in validator met ID 'RuntimeHDBConnectionValidator' (Validatie: 'VALIDATION_HDB_USERSTORE'): Kon de hdbuserstore niet ophalen  
    HANA userstore is niet op de juiste locatie
  * Oplossing  
    Zorg ervoor dat /usr/sap/AH1/hdbclient/install/installation.ini correct is

### <a name="errors-and-warnings-during-a-system-copy"></a>Fouten en waarschuwingen tijdens een systeemkopie

* Er is een fout opgetreden bij het valideren van de systeeminrichtingsstap
  * Veroorzaakt door: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50\;-h\;hn1-db -o level=0 status=5 port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R-T dev_lvminfo-u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db\;-o level=0 status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R-T dev_lvminfo-u SYSTEM -p hook -r
  * Oplossing  
    Maak een back-up van alle databases in het HANA-systeem van de bron

* Start van *de* database-instantie voor systeemkopie
  * Host Agent Operation '000D3A282BC91EE8A1D76CF1F92E2944' is mislukt (OperationException. FaultCode: '127', Bericht: 'Command execution failed. : [Microsoft][ODBC SQL Server Driver][SQL Server]Gebruiker heeft geen toestemming om database 'AS2' te wijzigen, de database bestaat niet of de database is niet in een staat die toegangscontroles toestaat.".
  * Oplossing  
    Controleer of *NT AUTHORITY\SYSTEM* toegang heeft tot de SQL Server. Zie SAP Note [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Fouten en waarschuwingen tijdens een systeemkloon

* Er is een fout opgetreden bij het registreren van instantieagent in stap *Gedwongen register en Instantie startagent* van toepassingsserver of ASCS
  * Er is een fout opgetreden bij het registreren van instantieagent. (RemoteException: 'Kan instantiegegevens uit\\profiel niet laden' as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Kan geen profiel openen '\\as1-ascs\sapmnt\AS1\SYS\profiel\AS1_D00_as1-di-0': geen dergelijk bestand of directory.')
  * Oplossing  
   Zorg ervoor dat het sapmnt-aandeel op de ASCS/SCS volledige toegang heeft voor SAP_AS1_GlobalAdmin

* Fout in stap *Opstartbeveiliging inschakelen voor kloon*
  * Kan bestand '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' Oorzaak niet openen: geen dergelijk bestand of map
  * Oplossing  
    Het computeraccount van de toepassingsserver moet toegang tot het profiel schrijven

### <a name="errors-and-warnings-during-create-system-replication"></a>Fouten en waarschuwingen tijdens Systeemreplicatie maken

* Uitzondering wanneer u klikt op Systeemreplicatie maken
  * Veroorzaakt door: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50\;-h\;hn1-db -o level=0 status=5 port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R-T dev_lvminfo-u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db\;-o level=0 status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R-T dev_lvminfo-u SYSTEM -p hook -r
  * Oplossing  
    Test of sapacext kan `<hanasid` worden uitgevoerd als>adm

* Fout wanneer volledige kopie niet is ingeschakeld in opslagstap
  * Er is een fout opgetreden bij het melden van een contextkenmerkbericht voor pad IStorageCopyData.storageVolumeCopyList:1 en veldtargetStorageSystemId
  * Oplossing  
    Waarschuwingen in stap negeren en probeer het opnieuw. Dit probleem wordt opgelost in een nieuw ondersteuningspakket/patch van SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Fouten en waarschuwingen tijdens verhuizen

* Pad '/usr/sap/AH1' is niet toegestaan voor nfs reexports.
  * Kijk op SAP Note [2628497] voor meer informatie.
  * Oplossing  
    Ascs-export toevoegen aan ASCS HostAgent-profiel. Zie SAP Note [2628497]

* Functie niet geïmplementeerd bij verhuizing ASCS
  * Opdrachtuitvoer: exportfs: host:/usr/sap/AX1: Functie niet geïmplementeerd
  * Oplossing  
    Zorg ervoor dat de NFS-serverservice is ingeschakeld op de virtuele machine voor het verplaatsen van het doel

### <a name="errors-and-warnings-during-application-server-installation"></a>Fouten en waarschuwingen tijdens de installatie van de toepassingsserver

* Fout bij het uitvoeren van SAPinst stap: getProfileDir
  * FOUT: (Laatste fout gemeld door de stap: Gevangen ESAPinstException in module aanroep: Validator van stap '| NW_DI|ind|ind|ind|ind|0|0|0| NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0| NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' heeft een fout \\gemeld: Node \as1-ascs\sapmnt\AS1\SYS\profile bestaat niet. Start SAPinst in interactieve modus om dit probleem op te lossen)
  * Oplossing  
    Zorg ervoor dat SWPM wordt uitgevoerd met een gebruiker die toegang heeft tot het profiel. Deze gebruiker kan worden geconfigureerd in de wizard Installatie van toepassingsserver

* Fout uitvoeren van SAPinst stap: askUnicode
  * FOUT: (Laatste fout gemeld door de stap: Gevangen ESAPinstException in module aanroep: Validator van stap '| NW_DI|ind|ind|ind|ind|0|0|0| NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0| NW_getUnicode|ind|ind|ind|ind|unicode|0|askUnicode' heeft een fout gemeld: Start SAPinst in interactieve modus om dit probleem op te lossen)
  * Oplossing  
    Als u een recente SAP-kernel gebruikt, kan SWPM niet meer bepalen of het systeem een unicode-systeem is met behulp van de berichtenserver van de ASCS. Zie SAP Note [2445033] voor meer informatie.  
    Dit probleem wordt opgelost in een nieuw ondersteuningspakket/patch van SAP LaMa.  
    Stel profielparameter OS_UNICODE=uc in het standaardprofiel van uw SAP-systeem in om dit probleem te omzeilen.

* Fout bij het uitvoeren van SAPinst-stap: dCheckGivenServer
  * Fout uitvoeren SAPinst stap: dCheckGivenServer" versie="1.0" FOUT: (Laatste \<fout gemeld door de stap: p> Installatie is geannuleerd door de gebruiker. \</p>
  * Oplossing  
    Zorg ervoor dat SWPM wordt uitgevoerd met een gebruiker die toegang heeft tot het profiel. Deze gebruiker kan worden geconfigureerd in de wizard Installatie van toepassingsserver

* Fout uitvoeren van SAPinst stap: checkClient
  * Fout uitvoeren SAPinst stap: checkClient" version="1.0" ERROR: (Laatste \<fout gemeld door de stap: p> Installatie is geannuleerd door de gebruiker. \</p>)
  * Oplossing  
    Controleer of het Microsoft ODBC-stuurprogramma voor SQL Server is geïnstalleerd op de virtuele machine waarop u de toepassingsserver wilt installeren

* Fout bij het uitvoeren van SAPinst-stap: copyScripts
  * Laatste fout gemeld door de stap: Systeemaanroep is mislukt. BIJZONDERHEDEN: Fout 13 (0x000000d) (Geweigerde toestemming) bij uitvoering van\\systeemoproep 'fopenU' met parameter ( \as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), regel (494) in dossier (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), stack trace:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute:execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction(iastring const& naam, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,isyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl(const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Oplossing  
    Zorg ervoor dat SWPM wordt uitgevoerd met een gebruiker die toegang heeft tot het profiel. Deze gebruiker kan worden geconfigureerd in de wizard Installatie van toepassingsserver

* Fout uitvoeren van SAPinst stap: askPasswords
  * Laatste fout gemeld door de stap: Systeemaanroep is mislukt. DETAILS: Fout 5 (0x0000005) (Toegang wordt geweigerd.) bij uitvoering van systeemoproep 'NetValidatePasswordPolicy' met parameter (...), regel (359) in bestand (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), stacktrace:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute:execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction(iastring const& naam, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * Oplossing  
    Zorg ervoor dat u een hostregel toevoegt in *stapisolatie* om communicatie van de VM naar de domeincontroller toe te staan

## <a name="next-steps"></a>Volgende stappen
* [Bedieningsgids voor SAP HANA op Azure][hana-ops-guide]
* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]

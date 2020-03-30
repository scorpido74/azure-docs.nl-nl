---
title: Azure VMware-oplossing door CloudSimple - Private Cloud-machtigingsmodel
description: Beschrijft het CloudSimple Private Cloud-machtigingsmodel, -groepen en -categorieën
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 28c4dc7831f97d66eb4d47f08e640344d5cca0d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014943"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>CloudSimple Private Cloud-machtigingsmodel van VMware vCenter

CloudSimple behoudt volledige administratieve toegang tot de Private Cloud-omgeving. Elke CloudSimple-klant krijgt voldoende beheerdersrechten om de virtuele machines in zijn omgeving te kunnen implementeren en beheren.  Indien nodig u uw bevoegdheden om administratieve functies uit te voeren tijdelijk escaleren.

## <a name="cloud-owner"></a>Cloud-eigenaar

Wanneer u een Private Cloud maakt, wordt een **CloudOwner-gebruiker** gemaakt in het vCenter Single Sign-On-domein, met toegang tot **cloud-eigenaar-rol** om objecten in de private cloud te beheren. Deze gebruiker kan ook extra [vCenter-identiteitsbronnen](set-vcenter-identity.md)en andere gebruikers instellen voor het VCenter private cloud.

> [!NOTE]
> Standaardgebruiker voor uw CloudSimple Private cloudowner@cloudsimple.local Cloud vCenter is wanneer een Private Cloud wordt gemaakt.

## <a name="user-groups"></a>Gebruikersgroepen

Een groep genaamd **Cloud-Owner-Group** wordt gemaakt tijdens de implementatie van een Private Cloud. Gebruikers in deze groep kunnen verschillende delen van de vSphere-omgeving beheren op de Private Cloud. Deze groep krijgt automatisch bevoegdheden **voor Cloud-Owner-Role** en de **CloudOwner-gebruiker** wordt toegevoegd als lid van deze groep.  CloudSimple maakt extra groepen met beperkte bevoegdheden voor het gemak van beheer.  U elke gebruiker toevoegen aan deze vooraf gemaakte groepen en de onderstaande bevoegdheden worden automatisch toegewezen aan de gebruikers in de groepen.

### <a name="pre-created-groups"></a>Vooraf gemaakte groepen

| Groepsnaam | Doel | Rol |
| -------- | ------- | ------ |
| Cloud-Owner-Group | Leden van deze groep hebben beheerdersrechten voor de Private Cloud vCenter | [Cloud-Owner-Rol](#cloud-owner-role) |
| Cloud-Global-Cluster-Admin-Group | Leden van deze groep hebben beheerdersrechten op het VCenter-cluster Private Cloud | [Cloud-Cluster-Admin-Role](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-Admin-Group | Leden van deze groep kunnen opslag beheren in het VCenter Private Cloud | [Cloud-Storage-Admin-Role](#cloud-storage-admin-role) |
| Cloud-Global-Network-Admin-Group | Leden van deze groep kunnen netwerk- en gedistribueerde poortgroepen beheren in het VCenter Private Cloud | [Cloud-Network-Admin-Role](#cloud-network-admin-role) |
| Cloud-Global-VM-Admin-Group | Leden van deze groep kunnen virtuele machines beheren op het Private Cloud vCenter | [Cloud-VM-Admin-Rol](#cloud-vm-admin-role) |

Als u individuele gebruikers machtigingen wilt verlenen voor het beheer van de Private Cloud, voegt u gebruikersaccounts toe aan de juiste groepen.

> [!CAUTION]
> Nieuwe gebruikers mogen alleen worden toegevoegd aan *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* of *Cloud-Global-VM-Admin-Group*.  Gebruikers die zijn toegevoegd aan de groep *Administrators,* worden automatisch verwijderd.  Alleen serviceaccounts mogen worden toegevoegd aan de groep *Administrators* en serviceaccounts mogen niet worden gebruikt om zich aan te melden bij de vSphere-webgebruikersinterface.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Lijst met vCenter-bevoegdheden voor standaardrollen

### <a name="cloud-owner-role"></a>Cloud-Owner-Rol

| **Categorie** | **Bevoegdheid** |
|----------|-----------|
| **Waarschuwingen** | Waarschuwing bevestigen <br> Alarm maken <br> Alarmactie uitschakelen <br> Alarm wijzigen <br> Alarm verwijderen <br> Alarmstatus instellen |
| **Machtigingen** | Machtigingen wijzigen |
| **Inhoudsbibliotheek** | Bibliotheekitem toevoegen <br> Lokale bibliotheek maken <br> Geabonneerde bibliotheek maken <br> Bibliotheekitem verwijderen <br> Lokale bibliotheek verwijderen <br> Geabonneerde bibliotheek verwijderen <br> Bestanden downloaden <br> Bibliotheekitem verwijderen <br> Geabonneerde bibliotheek uitzetten <br> Opslag importeren <br> Abonnementsgegevens van Probe <br> Opslag lezen <br> Bibliotheekitem synchroniseren <br> Geabonneerde bibliotheek synchroniseren <br> Type introspectie <br> Configuratie-instellingen bijwerken <br> Bestanden bijwerken <br> Bibliotheek bijwerken <br> Bibliotheekitem bijwerken <br> Lokale bibliotheek bijwerken <br> Geabonneerde bibliotheek bijwerken <br> Configuratie-instellingen weergeven |
| **Cryptografische bewerkingen** | Schijf toevoegen <br> Klonen <br> Ontsleutelen <br> Directe toegang <br> Versleutelen <br> Nieuw versleutelen <br> KM's beheren <br> Versleutelingsbeleid beheren <br> Sleutels beheren <br> Migreren <br> Opnieuw coderen <br> VM registreren <br> Host registreren |
| **dvPort-groep** | Maken <br> Verwijderen <br> Wijzigen <br> Beleidsbewerking <br> Scope-bewerking |
| **Gegevensarchief** | Ruimte toewijzen <br> Bladeren door datastore <br> Gegevensarchief configureren <br> Bestandsbewerkingen op laag niveau <br> Gegevensarchief verplaatsen <br> Gegevensarchief verwijderen <br> Bestand verwijderen <br> De naam van datastore wijzigen <br> Virtuele machinebestanden bijwerken <br> Metagegevens van virtuele machines bijwerken |
| **ESX Agent Manager** | Configureren <br> Wijzigen <br> Weergave |
| **Toestelnummer** | Extensie registreren <br> Extensie uitschrijven <br> Uitbreiding bijwerken |
| **Externe statistieken provider**| Registreren <br> Registratie ongedaan maken <br> Update |
| **Map** | Map maken <br> Map verwijderen <br> Map verplaatsen <br> Naam van map wijzigen |
| **Wereldwijd** | Taak annuleren <br> Capaciteitsplanning <br> Diagnostiek <br> Methoden uitschakelen <br> Methoden inschakelen <br> Globale tag <br> Status <br> Licenties <br> Logboekgebeurtenis <br> Aangepaste kenmerken beheren <br> Proxy <br> Scriptactie <br> Servicemanagers <br> Aangepast kenmerk instellen <br> Systeemtag |
| **Leverancier van statusupdates** | Registreren <br> Registratie ongedaan maken <br> Update |
| **Host > Configuratie** | Configuratie van opslagpartitie |
| **Voorraad host->** | Cluster wijzigen |
| **vSphere-labelen** | VSphere-tag toewijzen of niet toewijzen <br> VSphere-tag maken <br> Categorie vSphere-tag maken <br> VSphere-tag verwijderen <br> VSphere-tagcategorie verwijderen <br> VSphere-tag bewerken <br> Categorie vSphere-tag bewerken <br> Usedby-veld wijzigen voor categorie <br> UsedBy-veld wijzigen voor tag |
| **Netwerk** | Netwerk toewijzen <br> Configureren <br> Netwerk verplaatsen <br> Verwijderen |
| **Prestaties** | Intervallen wijzigen |
| **Hostprofiel** | Weergave |
| **Resource** | Aanbeveling toepassen <br> VApp toewijzen aan resourcegroep <br> Virtuele machine toewijzen aan resourcegroep <br> Resourcegroep maken <br> Uitgeschakelde virtuele machine migreren <br> Migreren op virtuele machine <br> Resourcegroep wijzigen <br> Resourcegroep verplaatsen <br> Query vMotion <br> Resourcegroep verwijderen <br> De naam van resourcegroep wijzigen |
| **Geplande taak** | Taken maken <br> Taak wijzigen <br> Taak verwijderen <br> Taak uitvoeren |
| **Sessies** | Gebruiker imiteren <br> Bericht <br> Sessie valideren <br> Sessies weergeven en stoppen |
| **Datastore-cluster** | Een datastore-cluster configureren |
| **Profielgestuurde opslag** | Profielgestuurde opslagupdate <br> Profielgestuurde opslagweergave |
| **Opslagweergaven** | Service configureren <br> Weergave |
| **Taken** | Taak maken <br> Taak bijwerken |
| **Overdrachtsservice**| Beheren <br> Controleren |
| **vApp** | Virtuele machine toevoegen <br> Resourcegroep toewijzen <br> VApp toewijzen <br> Klonen <br> Maken <br> Verwijderen <br> Exporteren <br> Importeren <br> Verplaatsen <br> Uitschakelen <br> Inschakelen <br> Naam wijzigen <br> Onderbreken <br> Registratie ongedaan maken <br> OVF-omgeving bekijken <br> vApp-toepassingsconfiguratie <br> vApp-instantieconfiguratie <br> vApp beheerdDoor configuratie <br> vApp-bronconfiguratie |
| **VRMBeleid** | VRM-beleid voor query's <br> VRM-beleid bijwerken |
| **Configuratie van virtuele machines >** | Bestaande schijf toevoegen <br> Nieuwe schijf toevoegen <br> Apparaat toevoegen of verwijderen <br> Geavanceerd <br> CPU-telling wijzigen <br> Bron wijzigen <br> ManagedBy configureren <br> Schijfwijzigingstracking <br> Schijflease <br> Verbindingsinstellingen weergeven <br> Virtuele schijf uitbreiden <br> USB-apparaat hosten <br> Geheugen <br> Apparaatinstellingen wijzigen <br> Compatibiliteit met fouttolerantie voor query's <br> Niet-eigendombestanden opvragen <br> Raw-apparaat <br> Herladen vanaf pad <br> Schijf verwijderen <br> Naam wijzigen <br> Gastgegevens opnieuw instellen <br> Annotatie instellen <br> Instellingen <br> Plaatsing van swapbestanden <br> Voorvork bovenliggende wisselknop <br> Virtuele machine ontgrendelen <br> Compatibiliteit met virtuele machines bijwerken |
| **Virtuele machine > gastbewerkingen** | Wijziging gastbewerkingalias <br> Aliasquery gastbewerking <br> Wijzigingen in gastbewerking <br> Uitvoering gastprogramma <br> Query's voor gastbewerking |
| **Virtuele machine > interactie** | Antwoordvraag <br> Back-upbewerking op virtuele machine <br> CD-media configureren <br> Diskettemedia configureren <br> Consoleinteractie <br> Schermafbeelding maken <br> Alle schijven defragmenteren <br> Apparaatverbinding <br> Slepen en neerzetten <br> Guest operating system management door VIX API <br> USB HID-scancodes injecteren <br> Onderbreken of onderbreken <br> Veeg- of krimpbewerkingen uitvoeren <br> Uitschakelen <br> Inschakelen <br> Sessie opnemen op virtuele machine <br> Replay sessie op virtuele machine <br> Opnieuw instellen <br> Fouttolerantie hervatten <br> Onderbreken <br> Fouttolerantie onderbreken <br> Testfailover <br> Test opnieuw opstarten Secundaire VM <br> Fouttolerantie uitschakelen <br> Fouttolerantie inschakelen <br> VMware Tools installeren |
| **Virtuele machine > inventaris** | Maken op uit bestaande <br> Nieuwe maken <br> Verplaatsen <br> Registreren <br> Verwijderen <br> Registratie ongedaan maken |
| **Virtuele machine > provisioning** | Schijftoegang toestaan <br> Bestandstoegang toestaan <br> Alleen-lezen schijftoegang toestaan <br> Virtuele machine downloaden toestaan <br> Virtuele machinebestanden uploaden toestaan <br> Kloonsjabloon <br> Kloon virtuele machine <br> Sjabloon maken op virtuele machine <br> Aanpassen <br> Sjabloon implementeren <br> Markeren als sjabloon <br> Markeren als virtuele machine <br> Aanpassingsspecificatie wijzigen <br> Schijven promoten <br> Lees de specificaties voor aanpassingen |
| **Configuratie van virtuele machine > Service** | Meldingen toestaan <br> Polling van wereldwijde gebeurtenismeldingen toestaan <br> Serviceconfiguraties beheren <br> Serviceconfiguratie wijzigen <br> Queryserviceconfiguraties <br> Serviceconfiguratie lezen |
| **Beheer van virtuele machines > momentopname** | Momentopname maken <br> Momentopname verwijderen <br> De naam van momentopname wijzigen <br> Terugkeren naar momentopname |
| **Virtuele machine > vSphere-replicatie** | Replicatie configureren <br> Replicatie beheren <br> Replicatie controleren |
| **vService** | Afhankelijkheid maken <br> Afhankelijkheid vernietigen <br> Afhankelijkheidsconfiguratie opnieuw configureren <br> Afhankelijkheid bijwerken |

### <a name="cloud-cluster-admin-role"></a>Cloud-Cluster-Admin-Role

| **Categorie** | **Bevoegdheid** |
|----------|-----------|
| **Gegevensarchief** | Ruimte toewijzen <br> Bladeren door datastore <br> Gegevensarchief configureren <br> Bestandsbewerkingen op laag niveau <br> Gegevensarchief verwijderen <br> De naam van datastore wijzigen <br> Virtuele machinebestanden bijwerken <br> Metagegevens van virtuele machines bijwerken |
| **Map** | Map maken <br> Map verwijderen <br> Map verplaatsen <br> Naam van map wijzigen |
| **Host > Configuratie**  | Configuratie van opslagpartitie |
| **vSphere-labelen** | VSphere-tag toewijzen of niet toewijzen <br> VSphere-tag maken <br> Categorie vSphere-tag maken <br> VSphere-tag verwijderen <br> VSphere-tagcategorie verwijderen <br> VSphere-tag bewerken <br> Categorie vSphere-tag bewerken <br> Usedby-veld wijzigen voor categorie <br> UsedBy-veld wijzigen voor tag |
| **Netwerk** | Netwerk toewijzen |
| **Resource** | Aanbeveling toepassen <br> VApp toewijzen aan resourcegroep <br> Virtuele machine toewijzen aan resourcegroep <br> Resourcegroep maken <br> Uitgeschakelde virtuele machine migreren <br> Migreren op virtuele machine <br> Resourcegroep wijzigen <br> Resourcegroep verplaatsen <br> Query vMotion <br> Resourcegroep verwijderen <br> De naam van resourcegroep wijzigen |
| **vApp** | Virtuele machine toevoegen <br> Resourcegroep toewijzen <br> VApp toewijzen <br> Klonen <br> Maken <br> Verwijderen <br> Exporteren <br> Importeren <br> Verplaatsen <br> Uitschakelen <br> Inschakelen <br> Naam wijzigen <br> Onderbreken <br> Registratie ongedaan maken <br> OVF-omgeving bekijken <br> vApp-toepassingsconfiguratie <br> vApp-instantieconfiguratie <br> vApp beheerdDoor configuratie <br> vApp-bronconfiguratie |
| **VRMBeleid** | VRM-beleid voor query's <br> VRM-beleid bijwerken |
| **Configuratie van virtuele machines >** | Bestaande schijf toevoegen <br> Nieuwe schijf toevoegen <br> Apparaat toevoegen of verwijderen <br> Geavanceerd <br> CPU-telling wijzigen <br> Bron wijzigen <br> ManagedBy configureren <br> Schijfwijzigingstracking <br> Schijflease <br> Verbindingsinstellingen weergeven <br> Virtuele schijf uitbreiden <br> USB-apparaat hosten <br> Geheugen <br> Apparaatinstellingen wijzigen <br> Compatibiliteit met fouttolerantie voor query's <br> Niet-eigendombestanden opvragen <br> Raw-apparaat <br> Herladen vanaf pad <br> Schijf verwijderen <br> Naam wijzigen <br> Gastgegevens opnieuw instellen <br> Annotatie instellen <br> Instellingen <br> Plaatsing van swapbestanden <br> Voorvork bovenliggende wisselknop <br> Virtuele machine ontgrendelen <br> Compatibiliteit met virtuele machines bijwerken |
| **Virtuele machine > gastbewerkingen** | Wijziging gastbewerkingalias <br> Aliasquery gastbewerking <br> Wijzigingen in gastbewerking <br> Uitvoering gastprogramma <br> Query's voor gastbewerking |
| **Virtuele machine > interactie** | Antwoordvraag <br> Back-upbewerking op virtuele machine <br> CD-media configureren <br> Diskettemedia configureren <br> Consoleinteractie <br> Schermafbeelding maken <br> Alle schijven defragmenteren <br> Apparaatverbinding <br> Slepen en neerzetten <br> Guest operating system management door VIX API <br> USB HID-scancodes injecteren <br> Onderbreken of onderbreken <br> Veeg- of krimpbewerkingen uitvoeren <br> Uitschakelen <br> Inschakelen <br> Sessie opnemen op virtuele machine <br> Replay sessie op virtuele machine <br> Opnieuw instellen <br> Fouttolerantie hervatten <br> Onderbreken <br> Fouttolerantie onderbreken <br> Testfailover <br> Test opnieuw opstarten Secundaire VM <br> Fouttolerantie uitschakelen <br> Fouttolerantie inschakelen <br> VMware Tools installeren
| **Virtuele machine > inventaris** | Maken op uit bestaande <br> Nieuwe maken <br> Verplaatsen <br> Registreren <br> Verwijderen <br> Registratie ongedaan maken |
| **Virtuele machine > provisioning** | Schijftoegang toestaan <br> Bestandstoegang toestaan <br> Alleen-lezen schijftoegang toestaan <br> Virtuele machine downloaden toestaan <br> Virtuele machinebestanden uploaden toestaan <br> Kloonsjabloon <br> Kloon virtuele machine <br> Sjabloon maken op virtuele machine <br> Aanpassen <br> Sjabloon implementeren <br> Markeren als sjabloon <br> Markeren als virtuele machine <br> Aanpassingsspecificatie wijzigen <br> Schijven promoten  <br> Lees de specificaties voor aanpassingen |
| **Configuratie van virtuele machine > Service** | Meldingen toestaan <br> Polling van wereldwijde gebeurtenismeldingen toestaan <br> Serviceconfiguraties beheren <br> Serviceconfiguratie wijzigen <br> Queryserviceconfiguraties <br> Serviceconfiguratie lezen
| **Beheer van virtuele machines > momentopname** | Momentopname maken <br> Momentopname verwijderen <br> De naam van momentopname wijzigen <br> Terugkeren naar momentopname |
| **Virtuele machine > vSphere-replicatie** | Replicatie configureren <br> Replicatie beheren <br> Replicatie controleren |
| **vService** | Afhankelijkheid maken <br> Afhankelijkheid vernietigen <br> Afhankelijkheidsconfiguratie opnieuw configureren <br> Afhankelijkheid bijwerken |

### <a name="cloud-storage-admin-role"></a>Cloud-Storage-Admin-Role

| **Categorie** | **Bevoegdheid** |
|----------|-----------|
| **Gegevensarchief** | Ruimte toewijzen <br> Bladeren door datastore <br> Gegevensarchief configureren <br> Bestandsbewerkingen op laag niveau <br> Gegevensarchief verwijderen <br> De naam van datastore wijzigen <br> Virtuele machinebestanden bijwerken <br> Metagegevens van virtuele machines bijwerken |
| **Host > Configuratie** | Configuratie van opslagpartitie |
| **Datastore-cluster** | Een datastore-cluster configureren |
| **Profielgestuurde opslag** | Profielgestuurde opslagupdate <br> Profielgestuurde opslagweergave |
| **Opslagweergaven** | Service configureren <br> Weergave |

### <a name="cloud-network-admin-role"></a>Cloud-Network-Admin-Role

| **Categorie** | **Bevoegdheid** |
|----------|-----------|
| **dvPort-groep** | Maken <br> Verwijderen <br> Wijzigen <br> Beleidsbewerking <br> Scope-bewerking |
| **Netwerk** | Netwerk toewijzen <br> Configureren <br> Netwerk verplaatsen <br> Verwijderen |
| **Configuratie van virtuele machines >** | Apparaatinstellingen wijzigen |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-Admin-Rol

| **Categorie** | **Bevoegdheid** |
|----------|-----------|
| **Gegevensarchief** | Ruimte toewijzen <br> Bladeren door datastore |
| **Netwerk** | Netwerk toewijzen |
| **Resource** | Virtuele machine toewijzen aan resourcegroep <br> Uitgeschakelde virtuele machine migreren <br> Migreren op virtuele machine
| **vApp** | Exporteren <br> Importeren |
| **Configuratie van virtuele machines >** | Bestaande schijf toevoegen <br> Nieuwe schijf toevoegen <br> Apparaat toevoegen of verwijderen <br> Geavanceerd <br> CPU-telling wijzigen <br> Bron wijzigen <br> ManagedBy configureren <br> Schijfwijzigingstracking <br> Schijflease <br> Verbindingsinstellingen weergeven <br> Virtuele schijf uitbreiden <br> USB-apparaat hosten <br> Geheugen <br> Apparaatinstellingen wijzigen <br> Compatibiliteit met fouttolerantie voor query's <br> Niet-eigendombestanden opvragen <br> Raw-apparaat <br> Herladen vanaf pad <br> Schijf verwijderen <br> Naam wijzigen <br> Gastgegevens opnieuw instellen <br> Annotatie instellen <br> Instellingen <br> Plaatsing van swapbestanden <br> Voorvork bovenliggende wisselknop <br> Virtuele machine ontgrendelen <br> Compatibiliteit met virtuele machines bijwerken |
| **Virtuele machine >gastbewerkingen** | Wijziging gastbewerkingalias <br> Aliasquery gastbewerking <br> Wijzigingen in gastbewerking <br> Uitvoering gastprogramma <br> Query's voor gastbewerking    |
| **Virtuele machine >interactie** | Antwoordvraag <br> Back-upbewerking op virtuele machine <br> CD-media configureren <br> Diskettemedia configureren <br> Consoleinteractie <br> Schermafbeelding maken <br> Alle schijven defragmenteren <br> Apparaatverbinding <br> Slepen en neerzetten <br> Guest operating system management door VIX API <br> USB HID-scancodes injecteren <br> Onderbreken of onderbreken <br> Veeg- of krimpbewerkingen uitvoeren <br> Uitschakelen <br> Inschakelen <br> Sessie opnemen op virtuele machine <br> Replay sessie op virtuele machine <br> Opnieuw instellen <br> Fouttolerantie hervatten <br> Onderbreken <br> Fouttolerantie onderbreken <br> Testfailover <br> Test opnieuw opstarten Secundaire VM <br> Fouttolerantie uitschakelen <br> Fouttolerantie inschakelen <br> VMware Tools installeren |
| **Inventaris virtuele machine >** | Maken op uit bestaande <br> Nieuwe maken <br> Verplaatsen <br> Registreren <br> Verwijderen <br> Registratie ongedaan maken |
| **Virtuele machine >provisioning** | Schijftoegang toestaan <br> Bestandstoegang toestaan <br> Alleen-lezen schijftoegang toestaan <br> Virtuele machine downloaden toestaan <br> Virtuele machinebestanden uploaden toestaan <br> Kloonsjabloon <br> Kloon virtuele machine <br> Sjabloon maken op virtuele machine <br> Aanpassen <br> Sjabloon implementeren <br> Markeren als sjabloon <br> Markeren als virtuele machine <br> Aanpassingsspecificatie wijzigen <br> Schijven promoten <br> Lees de specificaties voor aanpassingen |
| **Configuratie van de >virtuele machine** | Meldingen toestaan <br> Polling van wereldwijde gebeurtenismeldingen toestaan <br> Serviceconfiguraties beheren <br> Serviceconfiguratie wijzigen <br> Queryserviceconfiguraties <br> Serviceconfiguratie lezen
| **Beheer van virtuele >** | Momentopname maken <br> Momentopname verwijderen <br> De naam van momentopname wijzigen <br> Terugkeren naar momentopname |
| **Virtuele machine >vSphere-replicatie** | Replicatie configureren <br> Replicatie beheren <br> Replicatie controleren |
| **vService** | Afhankelijkheid maken <br> Afhankelijkheid vernietigen <br> Afhankelijkheidsconfiguratie opnieuw configureren <br> Afhankelijkheid bijwerken |

---
title: Azure VMware-oplossingen (AVS)-machtigings model voor de Privécloud-Cloud
description: Hierin worden het machtigings model, groepen en categorieën van de AVS-Privécloud beschreven
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9488c59ead23fb68633ccc56a0df905ebfeea079
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014943"
---
# <a name="avs-private-cloud-permission-model-of-vmware-vcenter"></a>Machtigings model voor de Privécloud van de cloud van VMware vCenter

AVS behoudt volledige beheerders toegang tot de cloud omgeving van de AVS-Privécloud. Aan elke AVS-klant worden voldoende beheerders bevoegdheden verleend om de virtuele machines in hun omgeving te kunnen implementeren en beheren. Als dat nodig is, kunt u uw bevoegdheden tijdelijk escaleren om beheer functies uit te voeren.

## <a name="cloud-owner"></a>Cloud eigenaar

Wanneer u een Privécloud maakt, wordt er een **CloudOwner** -gebruiker gemaakt in het vCenter-domein voor eenmalige aanmelding, met toegang tot de **rol van Cloud-eigenaar** om objecten in de AVS-privécloud te beheren. Deze gebruiker kan ook extra VCenter- [identiteits bronnen](set-vcenter-identity.md)instellen, en andere gebruikers naar de AVS-Privécloud voor persoonlijke Clouds.

> [!NOTE]
> De standaard gebruiker voor uw AVS-Privécloud is cloudowner@AVS.local wanneer er een Privécloud wordt gemaakt.

## <a name="user-groups"></a>Gebruikersgroepen

Een groep met de naam **Cloud-eigenaar-groep** wordt gemaakt tijdens de implementatie van een privécloud in de Cloud. Gebruikers in deze groep kunnen verschillende onderdelen van de vSphere omgeving beheren in de Privécloud van de AVS. Deze groep krijgt automatisch machtigingen voor de **rol van Cloud-eigenaar** en de **CloudOwner** -gebruiker wordt toegevoegd als lid van deze groep. AVS maakt extra groepen met beperkte bevoegdheden voor eenvoudig beheer. U kunt elke wille keurige gebruiker toevoegen aan deze vooraf gemaakte groepen en de bevoegdheden die hieronder zijn gedefinieerd, worden automatisch toegewezen aan de gebruikers in de groepen.

### <a name="pre-created-groups"></a>Vooraf gemaakte groepen

| Groepsnaam | Doel | Rol |
| -------- | ------- | ------ |
| Cloud-eigenaar-groep | Leden van deze groep hebben beheerders bevoegdheden voor de automatische AVS-Cloud-vCenter | [Cloud-eigenaar-rol](#cloud-owner-role) |
| Cloud-globaal-Cluster-beheer groep | Leden van deze groep hebben beheerders bevoegdheden op de AVS Private Cloud vCenter-cluster | [Cloud-cluster-beheerder-rol](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-admin-Group | Leden van deze groep kunnen opslag in de automatische AVS-Cloud-vCenter beheren | [Cloud-opslag-beheerder-rol](#cloud-storage-admin-role) |
| Cloud-globaal-netwerk-beheerder-groep | Leden van deze groep kunnen netwerk-en gedistribueerde poort groepen beheren in de automatische AVS-Cloud-vCenter | [Cloud-netwerk-admin-rol](#cloud-network-admin-role) |
| Cloud-Global-VM-beheerder-groep | Leden van deze groep kunnen virtuele machines op de AVS-Privécloud beheren | [Cloud-VM-beheerder-rol](#cloud-vm-admin-role) |

Als u afzonderlijke gebruikers machtigingen wilt verlenen voor het beheren van de automatische AVS-Cloud, maakt u gebruikers accounts toevoegen aan de juiste groepen.

> [!CAUTION]
> Nieuwe gebruikers moeten alleen worden toegevoegd aan de *Cloud-eigenaar-groep*, *Cloud-Global-cluster-admin groep*, *Cloud-Global-Storage-admin-Group*, Cloud-Global: *Network-Administrator-* Group of Cloud-Global:- *beheer groep*.  Gebruikers die zijn toegevoegd aan de groep *Administrators* , worden automatisch verwijderd.  Alleen service accounts moeten worden toegevoegd aan de groep *Administrators* en service accounts moeten worden gebruikt om u aan te melden bij de vSphere-webgebruikersinterface.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Lijst met vCenter-bevoegdheden voor standaard rollen

### <a name="cloud-owner-role"></a>Cloud-eigenaar-rol

| **Categorie** | **Bevoegdheden** |
|----------|-----------|
| **Waarschuwingen** | Alarm bevestigen <br> Alarm maken <br> Waarschuwings actie uitschakelen <br> Waarschuwing wijzigen <br> Waarschuwing verwijderen <br> Waarschuwings status instellen |
| **Machtigingen** | Machtiging wijzigen |
| **Inhouds bibliotheek** | Bibliotheek item toevoegen <br> Lokale bibliotheek maken <br> Geabonneerde bibliotheek maken <br> Bibliotheek item verwijderen <br> Lokale bibliotheek verwijderen <br> Geabonneerde bibliotheek verwijderen <br> Bestanden downloaden <br> Bibliotheek item verwijderen <br> Geabonneerde bibliotheek verwijderen <br> Opslag importeren <br> Abonnements gegevens testen <br> Opslag lezen <br> Bibliotheek item synchroniseren <br> Geabonneerde bibliotheek synchroniseren <br> Type introspectie <br> Configuratie-instellingen bijwerken <br> Update bestanden <br> Bibliotheek bijwerken <br> Bibliotheek item bijwerken <br> Lokale bibliotheek bijwerken <br> Geabonneerde bibliotheek bijwerken <br> Configuratie-instellingen weer geven |
| **Cryptografische bewerkingen** | Schijf toevoegen <br> Kloon <br> Ontsleutelen <br> Directe toegang <br> Versleutelen <br> Nieuwe versleutelen <br> KMS beheren <br> Versleutelings beleid beheren <br> Sleutels beheren <br> Migreren <br> Hercryptografie <br> VM registreren <br> Host registreren |
| **dvPort-groep** | Create <br> Verwijderen <br> Wijzigen <br> Beleids bewerking <br> Bereik bewerking |
| **Gegevens opslag** | Ruimte toewijzen <br> Door gegevens opslag bladeren <br> Gegevens opslag configureren <br> Bestands bewerkingen op laag niveau <br> Gegevens opslag verplaatsen <br> Gegevens opslag verwijderen <br> Bestand verwijderen <br> Naam gegevens opslag wijzigen <br> Bestanden van de virtuele machine bijwerken <br> Meta gegevens van de virtuele machine bijwerken |
| **ESX-agent beheer** | Config <br> Wijzigen <br> Weergeven |
| **Switch** | Extensie registreren <br> Registratie uitbrei ding opheffen <br> Extensie bijwerken |
| **Externe statistieken provider**| Registreren <br> Registratie ongedaan maken <br> Update |
| **Map** | Map maken <br> Map verwijderen <br> Map verplaatsen <br> Mapnaam wijzigen |
| **Internationaal** | Taak annuleren <br> Capaciteitsplanning <br> Diagnostics <br> Methoden uitschakelen <br> Methoden inschakelen <br> Global-Tag <br> Gezondheid <br> Licenties <br> Logboek gebeurtenis <br> Aangepaste kenmerken beheren <br> Proxy <br> Script actie <br> Service managers <br> Aangepast kenmerk instellen <br> Systeem label |
| **Status update provider** | Registreren <br> Registratie ongedaan maken <br> Update |
| **Configuratie van > host** | Configuratie van de opslag partitie |
| **Host > inventaris** | Cluster wijzigen |
| **Tags voor vSphere** | VSphere-tag toewijzen of de toewijzing ervan opheffen <br> VSphere-tag maken <br> VSphere-label categorie maken <br> VSphere-tag verwijderen <br> VSphere-label categorie verwijderen <br> VSphere-tag bewerken <br> Categorie vSphere-code bewerken <br> Het veld UsedBy voor categorie wijzigen <br> UsedBy veld voor label wijzigen |
| **Netwerk** | Netwerk toewijzen <br> Configureren <br> Netwerk verplaatsen <br> Verwijderen |
| **Prestaties** | Intervallen wijzigen |
| **Host-profiel** | Weergeven |
| **Resource** | Aanbeveling Toep assen <br> VApp toewijzen aan resource groep <br> Virtuele machine aan resource groep toewijzen <br> Resource groep maken <br> Virtuele machine die is uitgeschakeld migreren <br> De ingeschakelde virtuele machine wordt gemigreerd <br> Resource groep wijzigen <br> Resource groep verplaatsen <br> Query's van vMotion <br> Resource groep verwijderen <br> Naam van resource groep wijzigen |
| **Geplande taak** | Taken maken <br> Taak wijzigen <br> Taak verwijderen <br> Taak uitvoeren |
| **Sessies** | Gebruiker imiteren <br> Bericht <br> Sessie valideren <br> Sessies weer geven en stoppen |
| **Data Store-cluster** | Een Data Store-cluster configureren |
| **Op profielen gebaseerde opslag** | Update voor profiel gerichte opslag <br> Beschik bare opslag weergave voor profielen |
| **Opslag weergaven** | Service configureren <br> Weergeven |
| **Taken** | Taak maken <br> Taak bijwerken |
| **Service overdragen**| Beheer <br> Controleren |
| **vApp** | Virtuele machine toevoegen <br> Resource groep toewijzen <br> VApp toewijzen <br> Kloon <br> Create <br> Verwijderen <br> Exporteren <br> Importeren <br> Verplaatsen <br> Uitschakelen <br> Inschakelen <br> Naam wijzigen <br> Tijdelijk intrekken <br> Registratie ongedaan maken <br> OVF-omgeving weer geven <br> configuratie van vApp-toepassing <br> configuratie van vApp-exemplaar <br> configuratie van vApp managedBy <br> vApp-resource configuratie |
| **VRMPolicy** | Query VRMPolicy <br> VRMPolicy bijwerken |
| **Configuratie van virtuele-machine >** | Bestaande schijf toevoegen <br> Nieuwe schijf toevoegen <br> Apparaat toevoegen of verwijderen <br> Geavanceerd <br> CPU-aantal wijzigen <br> Resource wijzigen <br> ManagedBy configureren <br> Bijhouden van schijf wijzigingen <br> Lease van schijf <br> Verbindings instellingen weer geven <br> Virtuele schijf uitbreiden <br> Host-USB-apparaat <br> Geheugen <br> Apparaatinstellingen wijzigen <br> Compatibiliteit van query fout tolerantie <br> Niet-eigendoms bestanden opvragen <br> Onbewerkt apparaat <br> Opnieuw laden vanaf pad <br> Schijf verwijderen <br> Naam wijzigen <br> Gast gegevens opnieuw instellen <br> Aantekening instellen <br> Instellingen <br> Swapfile-plaatsing <br> Bovenliggende Fork in-/uitschakelen <br> Virtuele machine ontgrendelen <br> Compatibiliteit van virtuele machines bijwerken |
| **>-Gast bewerkingen voor virtuele machines** | Wijziging alias voor gast bewerkingen <br> Alias query voor gast bewerkingen <br> Wijzigingen in de gast bewerking <br> Uitvoering van gast bewerkings programma <br> Query's voor gast bewerkingen |
| **Interactie van de virtuele machine >** | Vraag beantwoorden <br> Back-upbewerking op virtuele machine <br> CD-media configureren <br> Diskette media configureren <br> Interactie met de console <br> Scherm opname maken <br> Alle schijven defragmenteren <br> Apparaat-verbinding <br> Slepen en neerzetten <br> Beheer van gast besturingssystemen met VIX-API <br> USB HID-scan codes invoeren <br> Onderbreken of onderbreken <br> Bewerkingen voor wissen of verkleinen uitvoeren <br> Uitschakelen <br> Inschakelen <br> Sessie op virtuele machine opnemen <br> Sessie opnieuw afspelen op virtuele machine <br> Opnieuw instellen <br> Fout tolerantie hervatten <br> Tijdelijk intrekken <br> Fout tolerantie opschorten <br> Testfailover <br> De secundaire virtuele machine opnieuw opstarten testen <br> Fout tolerantie uitschakelen <br> Fout tolerantie inschakelen <br> VMware-Hulpprogram Ma's installeren |
| **> Inventaris van virtuele machine** | Maken op basis van bestaande <br> Nieuwe maken <br> Verplaatsen <br> Registreren <br> Verwijderen <br> Registratie ongedaan maken |
| **Inrichting van de virtuele machine >** | Schijf toegang toestaan <br> Toegang tot bestanden toestaan <br> Alleen-lezen schijf toegang toestaan <br> Downloaden van virtuele machines toestaan <br> Uploaden van virtuele machine bestanden toestaan <br> Sjabloon klonen <br> Virtuele machine klonen <br> Een sjabloon maken op basis van de virtuele machine <br> Aanpassen <br> Sjabloon implementeren <br> Als sjabloon markeren <br> Als virtuele machine markeren <br> Aanpassings specificatie wijzigen <br> Schijven promo veren <br> Aanpassings specificaties lezen |
| **Configuratie van de virtuele-machine >-Service** | Meldingen toestaan <br> Navragen van globale gebeurtenis meldingen toestaan <br> Service configuraties beheren <br> Service configuratie wijzigen <br> Query service configuraties <br> Service configuratie lezen |
| **Beheer van > moment opnamen van virtuele machines** | Momentopname maken <br> Moment opname verwijderen <br> Naam van moment opname wijzigen <br> Herstellen naar moment opname |
| **> VSphere-replicatie voor virtuele machines** | Replicatie configureren <br> Replicatie beheren <br> Replicatie controleren |
| **vService** | Afhankelijkheid maken <br> Afhankelijkheid vernietigen <br> Afhankelijkheids configuratie opnieuw configureren <br> Afhankelijkheid bijwerken |

### <a name="cloud-cluster-admin-role"></a>Cloud-cluster-beheerder-rol

| **Categorie** | **Bevoegdheden** |
|----------|-----------|
| **Gegevens opslag** | Ruimte toewijzen <br> Door gegevens opslag bladeren <br> Gegevens opslag configureren <br> Bestands bewerkingen op laag niveau <br> Gegevens opslag verwijderen <br> Naam gegevens opslag wijzigen <br> Bestanden van de virtuele machine bijwerken <br> Meta gegevens van de virtuele machine bijwerken |
| **Map** | Map maken <br> Map verwijderen <br> Map verplaatsen <br> Mapnaam wijzigen |
| **Configuratie van > host**  | Configuratie van de opslag partitie |
| **Tags voor vSphere** | VSphere-tag toewijzen of de toewijzing ervan opheffen <br> VSphere-tag maken <br> VSphere-label categorie maken <br> VSphere-tag verwijderen <br> VSphere-label categorie verwijderen <br> VSphere-tag bewerken <br> Categorie vSphere-code bewerken <br> Het veld UsedBy voor categorie wijzigen <br> UsedBy veld voor label wijzigen |
| **Netwerk** | Netwerk toewijzen |
| **Resource** | Aanbeveling Toep assen <br> VApp toewijzen aan resource groep <br> Virtuele machine aan resource groep toewijzen <br> Resource groep maken <br> Virtuele machine die is uitgeschakeld migreren <br> De ingeschakelde virtuele machine wordt gemigreerd <br> Resource groep wijzigen <br> Resource groep verplaatsen <br> Query's van vMotion <br> Resource groep verwijderen <br> Naam van resource groep wijzigen |
| **vApp** | Virtuele machine toevoegen <br> Resource groep toewijzen <br> VApp toewijzen <br> Kloon <br> Create <br> Verwijderen <br> Exporteren <br> Importeren <br> Verplaatsen <br> Uitschakelen <br> Inschakelen <br> Naam wijzigen <br> Tijdelijk intrekken <br> Registratie ongedaan maken <br> OVF-omgeving weer geven <br> configuratie van vApp-toepassing <br> configuratie van vApp-exemplaar <br> configuratie van vApp managedBy <br> vApp-resource configuratie |
| **VRMPolicy** | Query VRMPolicy <br> VRMPolicy bijwerken |
| **Configuratie van virtuele-machine >** | Bestaande schijf toevoegen <br> Nieuwe schijf toevoegen <br> Apparaat toevoegen of verwijderen <br> Geavanceerd <br> CPU-aantal wijzigen <br> Resource wijzigen <br> ManagedBy configureren <br> Bijhouden van schijf wijzigingen <br> Lease van schijf <br> Verbindings instellingen weer geven <br> Virtuele schijf uitbreiden <br> Host-USB-apparaat <br> Geheugen <br> Apparaatinstellingen wijzigen <br> Compatibiliteit van query fout tolerantie <br> Niet-eigendoms bestanden opvragen <br> Onbewerkt apparaat <br> Opnieuw laden vanaf pad <br> Schijf verwijderen <br> Naam wijzigen <br> Gast gegevens opnieuw instellen <br> Aantekening instellen <br> Instellingen <br> Swapfile-plaatsing <br> Bovenliggende Fork in-/uitschakelen <br> Virtuele machine ontgrendelen <br> Compatibiliteit van virtuele machines bijwerken |
| **>-Gast bewerkingen voor virtuele machines** | Wijziging alias voor gast bewerkingen <br> Alias query voor gast bewerkingen <br> Wijzigingen in de gast bewerking <br> Uitvoering van gast bewerkings programma <br> Query's voor gast bewerkingen |
| **Interactie van de virtuele machine >** | Vraag beantwoorden <br> Back-upbewerking op virtuele machine <br> CD-media configureren <br> Diskette media configureren <br> Interactie met de console <br> Scherm opname maken <br> Alle schijven defragmenteren <br> Apparaat-verbinding <br> Slepen en neerzetten <br> Beheer van gast besturingssystemen met VIX-API <br> USB HID-scan codes invoeren <br> Onderbreken of onderbreken <br> Bewerkingen voor wissen of verkleinen uitvoeren <br> Uitschakelen <br> Inschakelen <br> Sessie op virtuele machine opnemen <br> Sessie opnieuw afspelen op virtuele machine <br> Opnieuw instellen <br> Fout tolerantie hervatten <br> Tijdelijk intrekken <br> Fout tolerantie opschorten <br> Testfailover <br> De secundaire virtuele machine opnieuw opstarten testen <br> Fout tolerantie uitschakelen <br> Fout tolerantie inschakelen <br> VMware-Hulpprogram Ma's installeren
| **> Inventaris van virtuele machine** | Maken op basis van bestaande <br> Nieuwe maken <br> Verplaatsen <br> Registreren <br> Verwijderen <br> Registratie ongedaan maken |
| **Inrichting van de virtuele machine >** | Schijf toegang toestaan <br> Toegang tot bestanden toestaan <br> Alleen-lezen schijf toegang toestaan <br> Downloaden van virtuele machines toestaan <br> Uploaden van virtuele machine bestanden toestaan <br> Sjabloon klonen <br> Virtuele machine klonen <br> Een sjabloon maken op basis van de virtuele machine <br> Aanpassen <br> Sjabloon implementeren <br> Als sjabloon markeren <br> Als virtuele machine markeren <br> Aanpassings specificatie wijzigen <br> Schijven promo veren  <br> Aanpassings specificaties lezen |
| **Configuratie van de virtuele-machine >-Service** | Meldingen toestaan <br> Navragen van globale gebeurtenis meldingen toestaan <br> Service configuraties beheren <br> Service configuratie wijzigen <br> Query service configuraties <br> Service configuratie lezen
| **Beheer van > moment opnamen van virtuele machines** | Momentopname maken <br> Moment opname verwijderen <br> Naam van moment opname wijzigen <br> Herstellen naar moment opname |
| **> VSphere-replicatie voor virtuele machines** | Replicatie configureren <br> Replicatie beheren <br> Replicatie controleren |
| **vService** | Afhankelijkheid maken <br> Afhankelijkheid vernietigen <br> Afhankelijkheids configuratie opnieuw configureren <br> Afhankelijkheid bijwerken |

### <a name="cloud-storage-admin-role"></a>Cloud-opslag-beheerder-rol

| **Categorie** | **Bevoegdheden** |
|----------|-----------|
| **Gegevens opslag** | Ruimte toewijzen <br> Door gegevens opslag bladeren <br> Gegevens opslag configureren <br> Bestands bewerkingen op laag niveau <br> Gegevens opslag verwijderen <br> Naam gegevens opslag wijzigen <br> Bestanden van de virtuele machine bijwerken <br> Meta gegevens van de virtuele machine bijwerken |
| **Configuratie van > host** | Configuratie van de opslag partitie |
| **Data Store-cluster** | Een Data Store-cluster configureren |
| **Op profielen gebaseerde opslag** | Update voor profiel gerichte opslag <br> Beschik bare opslag weergave voor profielen |
| **Opslag weergaven** | Service configureren <br> Weergeven |

### <a name="cloud-network-admin-role"></a>Cloud-netwerk-admin-rol

| **Categorie** | **Bevoegdheden** |
|----------|-----------|
| **dvPort-groep** | Create <br> Verwijderen <br> Wijzigen <br> Beleids bewerking <br> Bereik bewerking |
| **Netwerk** | Netwerk toewijzen <br> Configureren <br> Netwerk verplaatsen <br> Verwijderen |
| **Configuratie van virtuele-machine >** | Apparaatinstellingen wijzigen |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-beheerder-rol

| **Categorie** | **Bevoegdheden** |
|----------|-----------|
| **Gegevens opslag** | Ruimte toewijzen <br> Door gegevens opslag bladeren |
| **Netwerk** | Netwerk toewijzen |
| **Resource** | Virtuele machine aan resource groep toewijzen <br> Virtuele machine die is uitgeschakeld migreren <br> De ingeschakelde virtuele machine wordt gemigreerd
| **vApp** | Exporteren <br> Importeren |
| **Configuratie van virtuele-machine >** | Bestaande schijf toevoegen <br> Nieuwe schijf toevoegen <br> Apparaat toevoegen of verwijderen <br> Geavanceerd <br> CPU-aantal wijzigen <br> Resource wijzigen <br> ManagedBy configureren <br> Bijhouden van schijf wijzigingen <br> Lease van schijf <br> Verbindings instellingen weer geven <br> Virtuele schijf uitbreiden <br> Host-USB-apparaat <br> Geheugen <br> Apparaatinstellingen wijzigen <br> Compatibiliteit van query fout tolerantie <br> Niet-eigendoms bestanden opvragen <br> Onbewerkt apparaat <br> Opnieuw laden vanaf pad <br> Schijf verwijderen <br> Naam wijzigen <br> Gast gegevens opnieuw instellen <br> Aantekening instellen <br> Instellingen <br> Swapfile-plaatsing <br> Bovenliggende Fork in-/uitschakelen <br> Virtuele machine ontgrendelen <br> Compatibiliteit van virtuele machines bijwerken |
| **>-Gast bewerkingen voor virtuele machines** | Wijziging alias voor gast bewerkingen <br> Alias query voor gast bewerkingen <br> Wijzigingen in de gast bewerking <br> Uitvoering van gast bewerkings programma <br> Query's voor gast bewerkingen    |
| **Interactie van de virtuele machine >** | Vraag beantwoorden <br> Back-upbewerking op virtuele machine <br> CD-media configureren <br> Diskette media configureren <br> Interactie met de console <br> Scherm opname maken <br> Alle schijven defragmenteren <br> Apparaat-verbinding <br> Slepen en neerzetten <br> Beheer van gast besturingssystemen met VIX-API <br> USB HID-scan codes invoeren <br> Onderbreken of onderbreken <br> Bewerkingen voor wissen of verkleinen uitvoeren <br> Uitschakelen <br> Inschakelen <br> Sessie op virtuele machine opnemen <br> Sessie opnieuw afspelen op virtuele machine <br> Opnieuw instellen <br> Fout tolerantie hervatten <br> Tijdelijk intrekken <br> Fout tolerantie opschorten <br> Testfailover <br> De secundaire virtuele machine opnieuw opstarten testen <br> Fout tolerantie uitschakelen <br> Fout tolerantie inschakelen <br> VMware-Hulpprogram Ma's installeren |
| **> Inventaris van virtuele machine** | Maken op basis van bestaande <br> Nieuwe maken <br> Verplaatsen <br> Registreren <br> Verwijderen <br> Registratie ongedaan maken |
| **Inrichting van de virtuele machine >** | Schijf toegang toestaan <br> Toegang tot bestanden toestaan <br> Alleen-lezen schijf toegang toestaan <br> Downloaden van virtuele machines toestaan <br> Uploaden van virtuele machine bestanden toestaan <br> Sjabloon klonen <br> Virtuele machine klonen <br> Een sjabloon maken op basis van de virtuele machine <br> Aanpassen <br> Sjabloon implementeren <br> Als sjabloon markeren <br> Als virtuele machine markeren <br> Aanpassings specificatie wijzigen <br> Schijven promo veren <br> Aanpassings specificaties lezen |
| **Configuratie van de virtuele-machine >-Service** | Meldingen toestaan <br> Navragen van globale gebeurtenis meldingen toestaan <br> Service configuraties beheren <br> Service configuratie wijzigen <br> Query service configuraties <br> Service configuratie lezen
| **Beheer van > moment opnamen van virtuele machines** | Momentopname maken <br> Moment opname verwijderen <br> Naam van moment opname wijzigen <br> Herstellen naar moment opname |
| **> VSphere-replicatie voor virtuele machines** | Replicatie configureren <br> Replicatie beheren <br> Replicatie controleren |
| **vService** | Afhankelijkheid maken <br> Afhankelijkheid vernietigen <br> Afhankelijkheids configuratie opnieuw configureren <br> Afhankelijkheid bijwerken |

---
title: Algemene VM-fout codes in azure | Microsoft Docs
description: Meer informatie over enkele veelvoorkomende fout codes die worden aangetroffen bij het inrichten en beheren van virtuele machines in azure
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: f5639d1cf94c77d699dc6de9841698b045ac1f96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76543015"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Veelvoorkomende foutberichten begrijpen tijdens het beheer van virtuele machines in Azure

In dit artikel worden enkele van de meest voorkomende fout codes en-berichten beschreven die u kunt tegen komen wanneer u virtuele machines (Vm's) in azure maakt of beheert.

>[!NOTE]
> U kunt opmerkingen op deze pagina achterlaten voor feedback of via [Azure feedback](https://feedback.azure.com/forums/216843-virtual-machines) met #azerrormessage-tag.

## <a name="error-response-format"></a>Indeling van fout bericht 
Virtuele Azure-machines gebruiken de volgende JSON-indeling voor fout Reacties:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner level error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Een fout bericht bevat altijd een status code en een fout object. Elk fout object bevat altijd een fout code en een bericht. Als de virtuele machine is gemaakt met een sjabloon, bevat het fout object ook een sectie met details die een intern niveau aan fout codes en het bericht bevat. Normaal gesp roken is het meest interne niveau van fout berichten de hoofd fout.


## <a name="common-virtual-machine-management-errors"></a>Veelvoorkomende fouten bij het beheer van virtuele machines

In deze sectie vindt u de algemene fout berichten die u kunt tegen komen bij het beheren van virtuele machines:

|  Foutcode  |  Foutbericht  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Kan geen lease verkrijgen tijdens het maken van{0}de schijf ' ' met {1}een blob met Uri. De BLOB wordt al gebruikt.  |  
|  AllocationFailed  |  Toewijzing is mislukt. Verklein de VM-grootte of het aantal Vm's, probeer het later opnieuw of probeer te implementeren naar een andere Beschikbaarheidsset of een andere Azure-locatie.  |  
|  AllocationFailed  |  De VM-toewijzing is mislukt vanwege een interne fout. Probeer het later opnieuw of probeer de implementatie uit te voeren op een andere locatie.  |
|  ArtifactNotFound  |  De VM-extensie met de{0}Uitgever en het type{1}' ' is niet gevonden op de locatie{2}.  |
|  ArtifactNotFound  |  De extensie met de{0}Publisher, het type{1}en de type-handler-versie{2}kan niet worden gevonden in de uitbreidings opslagplaats.  |
|  ArtifactVersionNotFound  |  Er is{0}geen versie gevonden in de artefact opslagplaats die voldoet aan de aangevraagde versie.  |
|  ArtifactVersionNotFound  |  Er is geen versie gevonden in de artefact opslagplaats die voldoet aan de{0}aangevraagde versie ' ' voor{1}de VM-extensie{2}met de uitgever ' ' en het type ' '.  |
|  AttachDiskWhileBeingDetached  |  Kan de gegevens schijf{0}niet koppelen aan de virtuele{1}machine omdat de schijf momenteel wordt ontkoppeld. Wacht tot de schijf volledig is losgekoppeld en probeer het vervolgens opnieuw.  |
|  BadRequest  |  De afgevulde beschikbaarheids sets worden nog niet ondersteund in deze regio.  |
|  BadRequest  |  Het toevoegen van een virtuele machine met beheerde schijven aan een niet-beheerde Beschikbaarheidsset of het toevoegen van een virtuele machine met op blobs gebaseerde schijven naar een beheerde Beschikbaarheidsset wordt niet ondersteund. Maak een Beschikbaarheidsset met de eigenschap Managed om een virtuele machine met Managed disks aan de set toe te voegen.  |
|  BadRequest  |  Managed Disks worden niet ondersteund in deze regio.  |
|  BadRequest  |  Meerdere VMExtensions per handler worden niet ondersteund voor het type{0}besturings systeem. {1}De VMExtension met de handler{2}is al toegevoegd of opgegeven in de invoer.  |
|  BadRequest  |  Bewerking '{0}' wordt niet ondersteund voor resource '{1}' met beheerde schijven.  |
|  CertificateImproperlyFormatted  |  De JSON-weer gave van het geheim {0} die is opgehaald van heeft een gegevens veld dat geen correct opgemaakt pfx-bestand is, of het pfx-bestand wordt niet correct gedecodeerd met het opgegeven wacht woord.  |
|  CertificateImproperlyFormatted  |  De gegevens die worden opgehaald {0} uit, kunnen niet worden GEDESERIALISEERD in JSON.  |
|  Conflict  |  Het wijzigen van de grootte van de schijf is alleen toegestaan bij het maken van een VM of wanneer de toewijzing van de virtuele machine ongedaan wordt gemaakt  |
|  ConflictingUserInput  |  {0}De schijf kan niet worden gekoppeld omdat de schijf al eigendom is van de VM{1}.  |
|  ConflictingUserInput  |  Bron-en doel resource groepen zijn hetzelfde.  |
|  ConflictingUserInput  |  De bron-en doel opslag accounts {0} voor de schijf verschillen.  |
|  ContainerAlreadyOnLease  |  Er bevindt zich al een lease op de opslag container die de {0}blob met URI bevat.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  De aanvraag voor het verplaatsen van resources bevat de bron kluis resources waarnaar wordt verwezen door {0}een of meer s in de aanvraag. Dit wordt momenteel niet ondersteund tijdens het verplaatsen van cross-abonnementen. Raadpleeg de fout Details voor de resource-Id's van de sleutel kluis.  |
|  DiagnosticsOperationInternalError  |  Er is een interne fout opgetreden tijdens het verwerken van {0}het diagnostische Profiel van de VM.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  De {0} BLOB wordt al gebruikt door een andere schijf die deel uitmaakt{1}van de VM. U kunt de BLOB-meta gegevens voor de schijf referentie gegevens controleren.  |
|  DiskBlobNotFound  |  Kan de VHD-blob met de {0} URI voor de{1}schijf niet vinden.  |
|  DiskBlobNotFound  |  Kan de VHD-blob met URI {0}niet vinden.  |
|  DiskEncryptionKeySecretMissingTags  |  {0}het {1} geheim heeft geen tags. Werk de geheime versie bij, voeg de vereiste labels toe en probeer het opnieuw.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Het teruglopen van {0} de geheime waarde {1} met de sleutel is mislukt.  |
|  DiskImageNotReady  |  De schijf {0} installatie kopie {1} heeft de status. Probeer het opnieuw wanneer de installatie kopie gereed is.  |
|  DiskPreparationError  |  Er zijn een of meer fouten opgetreden bij het voorbereiden van VM-schijven. Zie weer gave van schijf instantie voor meer informatie.  |
|  DiskProcessingError  |  De schijf verwerking is gestopt omdat de virtuele machine andere schijven heeft dan de schijven waarop de fout is opgetreden.  |
|  ImageBlobNotFound  |  Kan de VHD-blob met de {0} URI voor de{1}schijf niet vinden.  |
|  ImageBlobNotFound  |  Kan de VHD-blob met URI {0}niet vinden.  |
|  IncorrectDiskBlobType  |  Schijf-blobs kunnen alleen van het type pagina-BLOB zijn. De {0} BLOB voor de{1}schijf is van het type blok-blob.  |
|  IncorrectDiskBlobType  |  Schijf-blobs kunnen alleen van het type pagina-BLOB zijn. BLOB {0} is van het type{1}' '.  |
|  IncorrectImageBlobType  |  Schijf-blobs kunnen alleen van het type pagina-BLOB zijn. De {0} BLOB voor de{1}schijf is van het type blok-blob.  |
|  IncorrectImageBlobType  |  Schijf-blobs kunnen alleen van het type pagina-BLOB zijn. BLOB {0} is van het type{1}' '.  |
|  InternalOperationError  |  Kan het opslag account {0}niet omzetten. Zorg ervoor dat het is gemaakt via de opslag Resource provider op dezelfde locatie als de reken resource.  |
|  InternalOperationError  |  {0}het doel van het zoeken naar taken is mislukt.  |
|  InternalOperationError  |  Er is een fout opgetreden bij het valideren van het{0}netwerk profiel van de VM.  |
|  InvalidAccountType  |  Het account {0} type is ongeldig.  |
|  InvalidParameter  |  De waarde van de {0} para meter is ongeldig.  |
|  InvalidParameter  |  Het opgegeven beheerders wachtwoord is niet toegestaan.  |
|  InvalidParameter  |  "Het opgegeven wacht woord moet tussen {0}-{1} tekens lang zijn en moet voldoen aan {2} ten minste de vereisten voor wachtwoord complexiteit van het volgende: <ol><li> Bevat een hoofd letter</li><li>Bevat een kleine letter</li><li>Bevat een numeriek cijfer</li><li>Bevat een speciaal teken.</li></ol>  |
|  InvalidParameter  |  De opgegeven gebruikers naam voor de beheerder is niet toegestaan.  |
|  InvalidParameter  |  Kan geen bestaande besturingssysteem schijf koppelen als de virtuele machine is gemaakt op basis van een platform of een gebruikers installatie kopie.  |
|  InvalidParameter  |  De container {0} naam is ongeldig. Container namen moeten 3-63 tekens lang zijn en mogen alleen alfanumerieke tekens met kleine letters en afbreek streepjes bevatten. Het afbreek streepje moet worden voorafgegaan en gevolgd door een alfanumeriek teken.  |
|  InvalidParameter  |  De container {0} naam in {1} de URL is ongeldig. Container namen moeten 3-63 tekens lang zijn en mogen alleen alfanumerieke tekens met kleine letters en afbreek streepjes bevatten. Het afbreek streepje moet worden voorafgegaan en gevolgd door een alfanumeriek teken.  |
|  InvalidParameter  |  De BLOB-naam in {0} de URL bevat een slash. Dit wordt momenteel niet ondersteund voor schijven.  |
|  InvalidParameter  |  De URI {0} ziet er niet uit de juiste BLOB-URI.  |
|  InvalidParameter  |  Een schijf met de{0}naam ' ' maakt al gebruik van {1}dezelfde LUN:.  |
|  InvalidParameter  |  Er bestaat al een{0}schijf met de naam.  |
|  InvalidParameter  |  Kan geen overschrijvingen voor gebruikers afbeeldingen opgeven voor een schijf die al is gedefinieerd in de opgegeven installatie kopie verwijzing.  |
|  InvalidParameter  |  Een schijf met de{0}naam ' ' maakt al gebruik van {1}dezelfde VHD-URL.  |
|  InvalidParameter  |  Het opgegeven aantal {0} fout domeinen moet in het bereik {1} vallen. {2}  |
|  InvalidParameter  |  Het licentie type {0} is ongeldig. Geldige licentie typen zijn: Windows_Client of Windows_Server, hoofdletter gevoelig.  |
|  InvalidParameter  |  De naam van een Linux {0} -host mag niet langer zijn dan de tekens {1}of de volgende tekens bevatten:.  |
|  InvalidParameter  |  Doelpad voor open bare SSH-sleutels is momenteel beperkt tot de standaard {0} waarde vanwege een bekend probleem in de Linux-inrichtings agent.  |
|  InvalidParameter  |  Er bestaat al een {0} schijf op LUN.  |
|  InvalidParameter  |  Het {0} abonnement van de aanvraag moet overeenkomen {1} met het abonnement dat is opgenomen in de id van de beheerde schijf.  |
|  InvalidParameter  |  Aangepaste gegevens in OSProfile moeten een base64-code ring hebben en een maximum lengte {0} van tekens bevatten.  |
|  InvalidParameter  |  De BLOB-naam {0} in de URL moet{1}eindigen op de extensie.  |
|  InvalidParameter  |  {0}' is geen geldig voor voegsel van de VHD-BLOB-naam vastgelegd. Een geldig voor voegsel komt overeen{1}met regex.  |
|  InvalidParameter  |  Er kunnen geen certificaten aan uw virtuele machine worden toegevoegd als de VM-agent niet is ingericht.  |
|  InvalidParameter  |  Er bestaat al een {0} schijf op LUN.  |
|  InvalidParameter  |  De virtuele machine kan niet worden gemaakt, omdat {0} de aangevraagde grootte niet beschikbaar is in het cluster met de beschikbaarheidsset die momenteel is toegewezen. De beschik bare grootten zijn: {1}. Meer informatie over de strategie voor het wijzigen https://aka.ms/azure-resizevmvan de grootte van VM op.  |
|  InvalidParameter  |  De aangevraagde {0} VM-grootte is niet beschikbaar in de huidige regio. De beschik bare grootten in de huidige {1}regio zijn:. Meer informatie over de beschik bare VM-grootten in https://aka.ms/azure-regionselke regio op.  |
|  InvalidParameter  |  De aangevraagde {0} VM-grootte is niet beschikbaar in de huidige regio. Meer informatie over de beschik bare VM-grootten in https://aka.ms/azure-regionselke regio op.  |
|  InvalidParameter  |  De gebruikers naam van de Windows-beheerder {0} mag niet langer zijn dan tekens lang, eindigen met een punt (.) of de {1}volgende tekens bevatten:.  |
|  InvalidParameter  |  De naam van een Windows-computer {0} mag niet langer zijn dan tekens, volledig numeriek zijn of de volgende {1}tekens bevatten:.  |
|  MissingMoveDependentResources  |  De aanvraag voor het verplaatsen van resources bevat niet alle afhankelijke resources. Raadpleeg de fout Details voor ontbrekende resource-id's.  |
|  MoveResourcesHaveInvalidState  |  De aanvraag voor het verplaatsen van resources bevat Vm's die zijn gekoppeld aan ongeldige opslag accounts. Controleer de Details voor deze bron-id's en de namen van de opslag accounts waarnaar wordt verwezen.  |
|  MoveResourcesHavePendingOperations  |  De aanvraag voor het verplaatsen van resources bevat resources waarvoor een bewerking in behandeling is. Controleer de Details voor deze resource-id's. Voer de bewerking opnieuw uit nadat de in behandeling zijnde bewerkingen zijn voltooid.  |
|  MoveResourcesNotFound  |  De aanvraag voor het verplaatsen van resources bevat resources die niet kunnen worden gevonden. Controleer de Details voor deze resource-id's.  |
|  NetworkingInternalOperationError  |  Onbekende netwerk toewijzings fout.  |
|  NetworkingInternalOperationError  |  Fout bij onbekende netwerk toewijzing  |
|  NetworkingInternalOperationError  |  Er is een interne fout opgetreden bij het verwerken van het netwerk profiel van de virtuele machine.  |
|  NotFound  |  De Beschikbaarheidsset {0} is niet gevonden.  |
|  NotFound  |  De virtuele bron machine{0}' ' die in de aanvraag is opgegeven, bestaat niet op deze Azure-locatie.  |
|  NotFound  |  De Tenant met {0} de id is niet gevonden.  |
|  NotFound  |  De installatie {0} kopie is niet gevonden.  |
|  NotSupported  |  Het licentie type is {0}, maar de afbeeldings {1} -blob is niet van on-premises.  |
|  OperationNotAllowed  |  De beschikbaarheidsset {0} kan niet worden verwijderd. Voordat u een Beschikbaarheidsset verwijdert, moet u ervoor zorgen dat deze geen virtuele machine bevat.  |
|  OperationNotAllowed  |  Het wijzigen van de SKU van de beschikbaarheidsset van ' uitgelijnd ' in ' klassiek ' is niet toegestaan.  |
|  OperationNotAllowed  |  Kan uitbrei dingen in de virtuele machine niet wijzigen wanneer de virtuele machine niet wordt uitgevoerd.  |
|  OperationNotAllowed  |  De opname actie wordt alleen ondersteund op een virtuele machine met schijven op basis van blobs. Gebruik de bron-Api's van de installatie kopie om een installatie kopie van een beheerde virtuele machine te maken.  |
|  OperationNotAllowed  |  De resource {0} kan niet worden gemaakt op {1} basis van de installatie kopie totdat de installatie kopie is gemaakt.  |
|  OperationNotAllowed  |  Updates voor encryptionSettings zijn niet toegestaan wanneer de virtuele machine is toegewezen. Probeer het opnieuw nadat de toewijzing van de VM ongedaan is gemaakt  |
|  OperationNotAllowed  |  Het toevoegen van een beheerde schijf aan een virtuele machine met op blobs gebaseerde schijven wordt niet ondersteund.  |
|  OperationNotAllowed  |  Het maximum aantal gegevens schijven dat kan worden gekoppeld aan een virtuele machine van deze grootte is {0}.  |
|  OperationNotAllowed  |  Het toevoegen van een BLOB op basis van een schijf naar een VM met beheerde schijven wordt niet ondersteund.  |
|  OperationNotAllowed  |  {0}De bewerking is niet toegestaan voor de installatie kopie{1}omdat de installatie kopie is gemarkeerd voor verwijdering. U kunt de Verwijder bewerking alleen opnieuw uitvoeren (of wachten totdat het proces is voltooid).  |
|  OperationNotAllowed  |  {0}Bewerking is niet toegestaan op de virtuele machine{1}omdat de VM is gegeneraliseerd.  |
|  OperationNotAllowed  |  {0}Bewerking is niet toegestaan omdat de herstel punt verzameling{1}is gemarkeerd voor verwijdering.  |
|  OperationNotAllowed  |  {0}De bewerking is niet toegestaan voor de VM-extensie{1}, omdat deze is gemarkeerd voor verwijdering. U kunt de Verwijder bewerking alleen opnieuw uitvoeren (of wachten totdat het proces is voltooid).  |
|  OperationNotAllowed  |  {0}Bewerking is niet toegestaan omdat virtual machines{1}worden ingericht met behulp van de installatie kopie{2}.  |
|  OperationNotAllowed  |  {0}Bewerking is niet toegestaan omdat de installatie kopie{2}van de virtuele-machine schaalset{1}momenteel wordt gebruikt.  |
|  OperationNotAllowed  |  {0}Bewerking is niet toegestaan op de virtuele machine{1}omdat de VM is gemarkeerd voor verwijdering. U kunt de Verwijder bewerking alleen opnieuw uitvoeren (of wachten totdat het proces is voltooid).  |
|  OperationNotAllowed  |  Bewerking '{0}' is niet toegestaan op de virtuele{1}machine ' ' omdat de toewijzing van de virtuele machine ongedaan is gemaakt of is gemarkeerd voor het ongedaan maken van de toewijzing.  |
|  OperationNotAllowed  |  {0}De bewerking is niet toegestaan op de virtuele machine{1}omdat de VM actief is. Schakel expliciet uit voor het geval u de virtuele machine afsluit vanuit het gast besturingssysteem.  |
|  OperationNotAllowed  |  {0}Bewerking is niet toegestaan op de virtuele machine{1}omdat de toewijzing van de virtuele machine niet ongedaan is gemaakt.  |
|  OperationNotAllowed  |  Bewerking '{0}' is niet toegestaan op de virtuele{1}machine ' ' omdat de VM{2}een extensie heeft met de status mislukt.  |
|  OperationNotAllowed  |  {0}Bewerking is niet toegestaan op de virtuele machine{1}omdat er een andere bewerking wordt uitgevoerd.  |
|  OperationNotAllowed  |  Voor de bewerking{0}is vereist dat de virtuele machine{1}wordt gegeneraliseerd.  |
|  OperationNotAllowed  |  Voor de bewerking moet de virtuele machine worden uitgevoerd (of ingesteld om te worden uitgevoerd).  |
|  OperationNotAllowed  |  De schijf met {0}een grootte van GB, die kleiner is {1}dan de grootte GB van de overeenkomstige schijf in de installatie kopie, is niet toegestaan.  |
|  OperationNotAllowed  |  De uitbrei dingen van de VM{0}-schaalset van de handler kunnen alleen worden toegevoegd op het moment dat de VM-schaalset wordt gemaakt.  |
|  OperationNotAllowed  |  De uitbrei dingen van de VM{0}-schaalset van de handler kunnen alleen worden verwijderd op het moment dat de VM-schaalset wordt verwijderd.  |
|  OperationNotAllowed  |  {0}De VM maakt al gebruik van beheerde schijven.  |
|  OperationNotAllowed  |  {0}VM hoort bij de klassieke beschikbaarheidsset{1}. Werk de beschikbaarheidsset bij om de ' uitgelijnde ' SKU te gebruiken en voer de conversie vervolgens opnieuw uit.  |
|  OperationNotAllowed  |  Een VM die is gemaakt op basis van een installatie kopie kan geen schijven op basis van BLOB hebben Alle schijven moeten beheerde schijven zijn.  |
|  OperationNotAllowed  |  De opname bewerking kan niet worden voltooid omdat de virtuele machine niet is gegeneraliseerd.  |
|  OperationNotAllowed  |  Beheer bewerkingen op de virtuele{0}machine zijn niet toegestaan omdat de VM-schijven worden geconverteerd naar Managed disks.  |
|  OperationNotAllowed  |  Bij een actieve bewerking wordt de energie status van de {0} virtuele {1}machine gewijzigd in. Voer de bewerking {2} na enige tijd uit.  |
|  OperationNotAllowed  |  De virtuele machine kan niet worden toegevoegd of bijgewerkt. De aangevraagde {0} VM-grootte is mogelijk niet beschikbaar in de bestaande toewijzings eenheid. Meer informatie over de strategie voor het wijzigen https://aka.ms/azure-resizevmvan de grootte van VM op.  |
|  OperationNotAllowed  |  Kan de grootte van de virtuele machine niet wijzigen {0} omdat de aangevraagde grootte niet beschikbaar is in het cluster waar de beschikbaarheidsset momenteel is toegewezen. De beschik bare grootten zijn: {1}. Meer informatie over de strategie voor het wijzigen https://aka.ms/azure-resizevmvan de grootte van VM op.  |
|  OperationNotAllowed  |  Kan de grootte van de virtuele machine niet wijzigen {0} omdat de aangevraagde grootte niet beschikbaar is in het cluster waar de virtuele machine momenteel is toegewezen. Als u het formaat van {1} de virtuele machine wilt wijzigen, moet u de toewijzing ongedaan maken (dit is een stop bewerking in de Azure Portal) en de grootte van de bewerking opnieuw proberen. Meer informatie over de strategie voor het wijzigen https://aka.ms/azure-resizevmvan de grootte van VM op.  |
|  OSProvisioningClientError  |  Het inrichten van het besturings systeem is{0}mislukt voor de virtuele machine omdat het gast besturingssysteem momenteel wordt ingericht.  |
|  OSProvisioningClientError  |  Het inrichten van het besturings systeem{0}voor de virtuele machine is mislukt. Fout Details: {1} Controleer of de installatie kopie juist is voor bereid (gegeneraliseerd). <ul><li>Instructies voor Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Genereren van SSH-host is mislukt. Fout Details: {0}. Controleer of de Linux-agent correct is ingesteld om dit probleem op te lossen. <ul><li>U kunt de instructies controleren op:https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  De gebruikers naam die voor de virtuele machine is opgegeven, is ongeldig voor deze Linux-distributie. Fout Details: {0}.  |
|  OSProvisioningInternalError  |  Het inrichten van het besturings systeem voor{0}de virtuele machine is mislukt vanwege een interne fout.  |
|  OSProvisioningTimedOut  |  Het inrichten van het besturings systeem{0}voor de VM is niet voltooid binnen de toegewezen tijd. De inrichting van de virtuele machine kan nog steeds worden voltooid. Controleer de inrichtings status later.  |
|  OSProvisioningTimedOut  |  Het inrichten van het besturings systeem{0}voor de VM is niet voltooid binnen de toegewezen tijd. De inrichting van de virtuele machine kan nog steeds worden voltooid. Controleer de inrichtings status later. Zorg er ook voor dat de installatie kopie op de juiste wijze is voor bereid (gegeneraliseerd).   <ul><li>Instructies voor Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instructies voor Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Het inrichten van het besturings systeem{0}voor de VM is niet voltooid binnen de toegewezen tijd. De VM-gast agent is echter wel uitgevoerd. Dit geeft aan dat het gast besturingssysteem niet goed is voor bereid om te worden gebruikt als een VM-installatie kopie (met CreateOption = FromImage). Om dit probleem op te lossen, gebruikt u de VHD als is met CreateOption = koppelen of voorbereiden op de juiste manier voor gebruik als een installatie kopie:   <ul><li>Instructies voor Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instructies voor Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  De vereiste VM-grootte is momenteel niet beschikbaar op de geselecteerde locatie.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  De resource kan op dit moment niet worden bijgewerkt vanwege een voortdurende platform update. Probeer het later opnieuw.  |
|  StorageAccountLimitation  |  Het opslag account{0}biedt geen ondersteuning voor pagina-blobs die vereist zijn voor het maken van schijven.  |
|  StorageAccountLimitation  |  Het opslag account{0}heeft de toegewezen quota overschreden.  |
|  StorageAccountLocationMismatch  |  Kan het opslag account {0}niet omzetten. Zorg ervoor dat het is gemaakt via de opslag Resource provider op dezelfde locatie als de reken resource.  |
|  StorageAccountNotFound  |  Kan het {0} opslag account niet vinden. Zorg ervoor dat het opslag account niet is verwijderd en tot dezelfde Azure-locatie behoort als de VM.  |
|  StorageAccountNotRecognized  |  Gebruik een opslag account dat wordt beheerd door de provider van de opslag resource. Het gebruik {0} van wordt niet ondersteund.  |
|  StorageAccountOperationInternalError  |  Er is een interne fout opgetreden {0}tijdens het openen van het opslag account.  |
|  StorageAccountSubscriptionMismatch  |  Het opslag {0} account behoort niet tot {1}het abonnement.  |
|  StorageAccountTooBusy  |  Het opslag account{0}is momenteel bezet. Overweeg het gebruik van een ander account.  |
|  StorageAccountTypeNotSupported  |  Schijf {0} maakt {1} gebruik van een Blob Storage-account. Probeer het opnieuw met een opslag account voor algemene doel einden.  |
|  StorageAccountTypeNotSupported  |  Het opslag {0} account is {1} van het type. Diagnostische gegevens over {2} opstarten ondersteunt typen opslag accounts.  <ul><li>Deze fout treedt op als u het Premium-opslag account gebruikt voor diagnostische gegevens over opstarten. Zie voor meer informatie [over het gebruik van diagnostische gegevens over opstarten](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Het abonnement is niet geautoriseerd.  |
|  TargetDiskBlobAlreadyExists  |  De {0} BLOB bestaat al. Geef een andere blob-URI op om een nieuwe lege gegevens schijf{1}te maken.  |
|  TargetDiskBlobAlreadyExists  |  De vastleg bewerking kan niet worden voortgezet omdat {0} de doel afbeelding-BLOB al bestaat en de vlag voor het OVERSCHRIJVEN van VHD-blobs niet is ingesteld. Verwijder de BLOB of stel de vlag in op het overschrijven van de VHD-blobs en probeer het opnieuw.  |
|  TargetDiskBlobAlreadyExists  |  De vastleg bewerking kan niet worden voortgezet omdat {0} er een actieve lease voor de blob van de doel afbeelding is.   |
|  TargetDiskBlobAlreadyExists  |  De {0} BLOB bestaat al. Geef een andere blob-URI op als doel voor de{1}schijf.  |
|  TooManyVMRedeploymentRequests  |  Er zijn te veel aanvragen voor herimplementaties ontvangen voor{0}de virtuele machine ' ' of de virtuele machines in dezelfde beschikbaarheidsset met deze virtuele machine. Probeer het later opnieuw.  |
|  VHDSizeInvalid  |  De opgegeven waarde voor de schijf {0} grootte van voor{1}de schijf met {2} de blob is ongeldig. De schijf grootte moet tussen {3} en {4}liggen.  |
|  VMAgentStatusCommunicationError  |  {0}De virtuele machine heeft geen status gerapporteerd voor de VM-agent of de uitbrei dingen. Controleer of de virtuele machine een actieve VM-agent heeft en of er uitgaande verbindingen met Azure Storage tot stand kunnen worden gebracht.  |
|  VMArtifactRepositoryInternalError  |  Er is een fout opgetreden tijdens de communicatie met de artefact opslagplaats om VM-artefact gegevens op te halen.  |
|  VMArtifactRepositoryInternalError  |  Er is een interne fout opgetreden tijdens het ophalen van de VM-artefact gegevens uit de artefact opslagplaats.  |
|  VMExtensionHandlerNonTransientError  |  Handler '{0}' heeft een fout gerapporteerd voor de VM{1}-extensie ' ' met de{2}Terminal fout code ' ' en{3}het fout bericht: ' '  |
|  VMExtensionManagementInternalError  |  Er is een interne fout opgetreden tijdens{0}het verwerken van de VM-extensie.  |
|  VMExtensionManagementInternalError  |  Er zijn meerdere fouten opgetreden bij het voorbereiden van de VM-extensies. Zie de weer gave van de VM-extensie-instantie voor meer informatie.  |
|  VMExtensionProvisioningError  |  De virtuele machine heeft een fout gerapporteerd bij het{0}verwerken van de extensie. Fout bericht: "{1}".  |
|  VMExtensionProvisioningError  |  Meerdere VM-extensies kunnen niet worden ingericht op de VM. Raadpleeg de weer gave van de VM-extensie-instantie voor meer informatie.  |
|  VMExtensionProvisioningTimeout  |  Er is een time-out{0}opgetreden tijdens het inrichten van de VM-extensie. De installatie van de extensie kan te lang duren of de status van de extensie kan niet worden opgehaald.  |
|  VMMarketplaceInvalidInput  |  Het maken van een virtuele machine vanuit een niet-Marketplace-installatie kopie vereist geen plan gegevens, verwijder de plan gegevens in de aanvraag. De naam van de {0}besturingssysteem schijf is.  |
|  VMMarketplaceInvalidInput  |  De inkoop gegevens komen niet overeen. Kan niet implementeren vanuit de Marketplace-installatie kopie. De naam van de {0}besturingssysteem schijf is.  |
|  VMMarketplaceInvalidInput  |  Voor het maken van een virtuele machine vanuit een Marketplace-installatie kopie zijn plan gegevens in de aanvraag vereist. De naam van de {0}besturingssysteem schijf is.  |
|  VMNotFound  |  Kan de virtuele{0}machine niet vinden.  |
|  VMRedeploymentFailed  |  {0}De implementatie van de VM is mislukt vanwege een interne fout. Probeer het later opnieuw.  |
|  VMRedeploymentTimedOut  |  Het opnieuw implementeren van de{0}VM is niet voltooid binnen de toegewezen tijd. Het kan nu ook worden voltooid. Anders kunt u de aanvraag opnieuw proberen.  |
|  VMStartTimedOut  |  {0}De VM is niet gestart binnen de toegewezen tijd. De VM kan nog steeds worden gestart. Controleer de energie status op een later tijdstip.  |


## <a name="next-steps"></a>Volgende stappen
Als u meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.

---
title: Algemene VM-foutcodes in Azure | Microsoft Documenten
description: Een aantal van de veelvoorkomende foutcodes begrijpen die worden aangetroffen bij het inrichten en beheren van virtuele machines in Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543015"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Veelvoorkomende foutberichten begrijpen tijdens het beheer van virtuele machines in Azure

In dit artikel worden enkele van de meest voorkomende foutcodes en berichten beschreven die u tegenkomen wanneer u virtuele machines (VM's) maakt of beheert in Azure.

>[!NOTE]
> U opmerkingen achterlaten op deze pagina voor feedback of via [Azure-feedback](https://feedback.azure.com/forums/216843-virtual-machines) met #azerrormessage-tag.

## <a name="error-response-format"></a>Foutantwoordnotatie 
Azure VM's gebruiken de volgende JSON-indeling voor foutrespons:

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

Een foutantwoord bevat altijd een statuscode en een foutobject. Elk foutobject bevat altijd een foutcode en een bericht. Als de vm is gemaakt met een sjabloon, bevat het foutobject ook een detailsectie met een innerlijk niveau van foutcodes en een bericht. Normaal gesproken is het meest innerlijke niveau van foutmelding de root-fout.


## <a name="common-virtual-machine-management-errors"></a>Veelvoorkomende fouten in het beheer van virtuele machines

In deze sectie worden de veelvoorkomende foutmeldingen weergegeven die u tegenkomen bij het beheren van VM's:

|  Foutcode  |  Foutbericht  |  
|  :------| :-------------|  
|  AcquireDiskLease mislukt  |  Kan geen lease verkrijgen{0}tijdens het maken {1}van schijf ' ' met behulp van blob met URI . Blob is al in gebruik.  |  
|  Toewijzing mislukt  |  Toewijzing is mislukt. Probeer de VM-grootte of het aantal VM's te verkleinen, probeer het later opnieuw of probeer het te implementeren op een andere beschikbaarheidsset of andere Azure-locatie.  |  
|  Toewijzing mislukt  |  De VM-toewijzing is mislukt vanwege een interne fout. Probeer het later opnieuw of probeer het opnieuw uit te rollen naar een andere locatie.  |
|  Artefactniet gevonden  |  De VM-extensie{0}met uitgever{1}' en type '{2}was niet te vinden op locatie ' '.  |
|  Artefactniet gevonden  |  Extensie met{0}uitgever '{1}', typ '{2}en type handler versie ' kon niet worden gevonden in de extensie repository.  |
|  Artefactversieniet gevonden  |  Geen versie gevonden in de artefact repository die{0}voldoet aan de gevraagde versie ''.  |
|  Artefactversieniet gevonden  |  Geen versie gevonden in de artefact repository die{0}voldoet aan de{1}gevraagde versie{2}' ' voor VM extensie met uitgever ' en typ '.  |
|  AttachDiskWhileBeingLosgekoppeld  |  Kan de gegevensschijf '{0}{1}' ' niet aan VM ' koppelen omdat de schijf momenteel wordt losgekoppeld. Wacht tot de schijf volledig is losgemaakt en probeer het dan opnieuw.  |
|  BadRequest  |  Beschikbaarheidssets op uitgelijnde lijnen worden nog niet ondersteund in deze regio.  |
|  BadRequest  |  Toevoeging van een VM met beheerde schijven aan niet-beheerde beschikbaarheidsset of toevoeging van een VM met blobgebaseerde schijven aan beheerde beschikbaarheidsset wordt niet ondersteund. Maak een beschikbaarheidsset met 'beheerde' eigenschapset om er een VM met beheerde schijven aan toe te voegen.  |
|  BadRequest  |  Beheerde schijven worden niet ondersteund in deze regio.  |
|  BadRequest  |  Meerdere VMExtensions per handler worden niet{0}ondersteund voor het type BE ' '. VMExtension{1}' met{2}handler ' ' al toegevoegd of opgegeven in input.  |
|  BadRequest  |  Operation{0}' wordt niet ondersteund{1}op Resource ' ' met beheerde schijven.  |
|  CertificaatOnjuist opgemaakt  |  De JSON-vertegenwoordiging van {0} het geheim die is opgehaald, heeft een gegevensveld dat geen correct geformatteerd PFX-bestand is, of het opgegeven wachtwoord decodeert het PFX-bestand niet correct.  |
|  CertificaatOnjuist opgemaakt  |  De gegevens die {0} worden opgehaald, worden niet gedeserialiseerd in JSON.  |
|  Conflict  |  Het aanpassen van de grootte van de schijf is alleen toegestaan bij het maken van een vm of wanneer de vm wordt toegewezen.  |
|  Conflicterende gebruikersinvoer  |  Disk{0}' ' kan niet worden bevestigd omdat{1}de schijf al eigendom is van VM ' '.  |
|  Conflicterende gebruikersinvoer  |  Bron- en bestemmingsbrongroepen zijn hetzelfde.  |
|  Conflicterende gebruikersinvoer  |  Bron- en doelopslagaccounts voor schijf {0} zijn verschillend.  |
|  ContainerAlonLease  |  Er is al een lease op de {0}opslag container die de blob met URI .  |
|  CrosssubscriptionMoveWithKeyVaultResources  |  De aanvraag voor resources verplaatsen bevat KeyVault-bronnen {0}waarnaar wordt verwezen door een of meer s in de aanvraag. Dit wordt momenteel niet ondersteund in Cross-abonnement Move. Controleer de foutgegevens voor de KeyVault-bron-id's.  |
|  DiagnosticsOperationInternalError  |  Er is een interne fout {0}opgetreden tijdens het verwerken van het diagnoseprofiel van VM.  |
|  DiskBlobAlreadyinusebyAnotherDisk  |  Blob {0} is al in gebruik door{1}een andere schijf die behoort tot VM '. U de blobmetagegevens voor de referentiegegevens van de schijf onderzoeken.  |
|  DiskBlobNotFound  |  Niet in staat om {0} VHD{1}blob te vinden met URI voor schijf '.  |
|  DiskBlobNotFound  |  Kan vhd-blob niet {0}vinden met URI .  |
|  DiskEncryptionKeySecretMissingTags  |  {0}Secret heeft de {1} tags niet. Werk de geheime versie bij, voeg de vereiste tags toe en probeer het opnieuw.  |
|  DiskEncryptionKeySecretUnwrap Failed  |  Uitpakken van {0} geheime {1} waarde met behulp van sleutel is mislukt.  |
|  DiskimageNotReady  |  Schijfafbeelding {0} is {1} in staat. Probeer het opnieuw wanneer de afbeelding klaar is.  |
|  DiskPreparationError  |  Er zijn een of meer fouten opgetreden tijdens het voorbereiden van VM-schijven. Zie schijfinstantieweergave voor meer informatie.  |
|  DiskProcessingFout  |  Schijfverwerking gestopt omdat de VM andere schijven in mislukte schijven heeft.  |
|  ImageBlobNotFound  |  Niet in staat om {0} VHD{1}blob te vinden met URI voor schijf '.  |
|  ImageBlobNotFound  |  Kan vhd-blob niet {0}vinden met URI .  |
|  OnjuistDiskBlobType  |  Schijfblobs kunnen alleen van typepaginablob zijn. Blob {0} voor{1}schijf ' ' is van het type blok blob.  |
|  OnjuistDiskBlobType  |  Schijfblobs kunnen alleen van typepaginablob zijn. Blob {0} is van{1}het type ' '.  |
|  OnjuistImageBlobType  |  Schijfblobs kunnen alleen van typepaginablob zijn. Blob {0} voor{1}schijf ' ' is van het type blok blob.  |
|  OnjuistImageBlobType  |  Schijfblobs kunnen alleen van typepaginablob zijn. Blob {0} is van{1}het type ' '.  |
|  InternalOperationError InternalOperationError InternalOperationError InternalOperation  |  Kan het opslagaccount {0}niet oplossen . Controleer of het is gemaakt via de Storage Resource Provider op dezelfde locatie als de compute resource.  |
|  InternalOperationError InternalOperationError InternalOperationError InternalOperation  |  {0}doelzoekende taken is mislukt.  |
|  InternalOperationError InternalOperationError InternalOperationError InternalOperation  |  Er is fout opgetreden bij het{0}valideren van het netwerkprofiel van VM ' '.  |
|  OngeldigAccountType  |  Het AccountType {0} is ongeldig.  |
|  OngeldigParameter  |  De waarde {0} van de parameter is ongeldig.  |
|  OngeldigParameter  |  Het opgegeven beheerderswachtwoord is niet toegestaan.  |
|  OngeldigParameter  |  "Het opgegeven wachtwoord moet {0}{1} tussen - tekens lang {2} en moet voldoen aan ten minste van het wachtwoord complexiteit eisen van de volgende: <ol><li> Bevat een hoofdletterteken</li><li>Bevat een kleine letters</li><li>Bevat een numeriek cijfer</li><li>Bevat een speciaal karakter.</li></ol>  |
|  OngeldigParameter  |  De opgegeven gebruikersnaam beheerder is niet toegestaan.  |
|  OngeldigParameter  |  Kan geen bestaande osschijf koppelen als de VM is gemaakt op basis van een platform of gebruikersafbeelding.  |
|  OngeldigParameter  |  De {0} naam van de container is ongeldig. Containernamen moeten 3-63 tekens lang zijn en mogen alleen alfanumerieke tekens en koppeltekens in kleine letters bevatten. Koppelteken moet worden voorafgegaan en gevolgd door een alfanumeriek teken.  |
|  OngeldigParameter  |  De {0} naam {1} van de container in URL is ongeldig. Containernamen moeten 3-63 tekens lang zijn en mogen alleen alfanumerieke tekens en koppeltekens in kleine letters bevatten. Koppelteken moet worden voorafgegaan en gevolgd door een alfanumeriek teken.  |
|  OngeldigParameter  |  De blobnaam {0} in URL bevat een slash. Dit wordt momenteel niet ondersteund voor schijven.  |
|  OngeldigParameter  |  De {0} URI ziet er niet te kloppen blob URI.  |
|  OngeldigParameter  |  Een schijf{0}met de naam ' {1}gebruikt al dezelfde LUN: .  |
|  OngeldigParameter  |  Er bestaat{0}al een schijf met de naam ' '  |
|  OngeldigParameter  |  Kan de overschrijvingen van gebruikersafbeeldingen niet opgeven voor een schijf die al is gedefinieerd in de opgegeven afbeeldingsverwijzing.  |
|  OngeldigParameter  |  Een schijf{0}met de naam ' {1}gebruikt al dezelfde VHD-URL .  |
|  OngeldigParameter  |  Het aantal {0} opgegeven foutdomeinen moet {1} {2}in het bereik vallen tot .  |
|  OngeldigParameter  |  Het licentietype {0} is ongeldig. Geldige licentietypen zijn: Windows_Client of Windows_Server, hoofdlettergevoelig.  |
|  OngeldigParameter  |  De naam van {0} de Linux-host mag {1}de tekens niet langer overschrijden of de volgende tekens bevatten: .  |
|  OngeldigParameter  |  Bestemmingspad voor Openbare SSH-sleutels is {0} momenteel beperkt tot de standaardwaarde vanwege een bekend probleem in de Linux-inrichtingsagent.  |
|  OngeldigParameter  |  Een schijf {0} bij LUN bestaat al.  |
|  OngeldigParameter  |  Het {0} abonnement op de {1} aanvraag moet overeenkomen met het abonnement in de beheerde schijf-id.  |
|  OngeldigParameter  |  Aangepaste gegevens in OSProfile moeten zich in Base64-codering bevinden en met een maximale lengte van {0} tekens.  |
|  OngeldigParameter  |  Blob naam {0} in URL{1}moet eindigen met ' extensie.  |
|  OngeldigParameter  |  {0}' is geen geldig vastgelegd VHD-blobnaamvoorvoegsel. Een geldig voorvoegsel{1}komt overeen met regex ' '.  |
|  OngeldigParameter  |  Certificaten kunnen niet aan uw vm worden toegevoegd als de VM-agent niet is ingericht.  |
|  OngeldigParameter  |  Een schijf {0} bij LUN bestaat al.  |
|  OngeldigParameter  |  Kan de vm niet maken {0} omdat de gevraagde grootte niet beschikbaar is in het cluster waar de beschikbaarheidsset momenteel is toegewezen. De beschikbare maten {1}zijn: . Lees meer over vm-hergroottestrategie op https://aka.ms/azure-resizevm.  |
|  OngeldigParameter  |  De gevraagde VM-grootte {0} is niet beschikbaar in de huidige regio. De maten die beschikbaar zijn {1}in de huidige regio zijn: . Lees meer over de beschikbare VM-formaten in elke regio op https://aka.ms/azure-regions.  |
|  OngeldigParameter  |  De gevraagde VM-grootte {0} is niet beschikbaar in de huidige regio. Lees meer over de beschikbare VM-formaten in elke regio op https://aka.ms/azure-regions.  |
|  OngeldigParameter  |  De gebruikersnaam van windows-beheerders mag niet langer zijn dan {0} tekens, eindigt {1}met een periode(.), of de volgende tekens bevatten: .  |
|  OngeldigParameter  |  De naam van de {0} Windows-computer mag niet langer zijn dan {1}tekens, kan volledig numeriek zijn of de volgende tekens bevatten: .  |
|  MissingMoveDependentResources MissingMoveDependentResources MissingMoveDependentResources MissingMove  |  De aanvraag voor verplaatsingsresources bevat niet alle afhankelijke resources. Controleer foutgegevens op ontbrekende resource-id's.  |
|  MoveResourcesHaveInvalidState  |  De aanvraag Resources verplaatsen bevat VM's die zijn gekoppeld aan ongeldige opslagaccounts. Controleer de details voor deze bron-id's en namen van het opslagaccount waarnaar wordt verwezen.  |
|  MoveResourcesHavePendingOperations  |  De aanvraag voor verplaatsingsresources bevat resources waarvoor een bewerking in behandeling is. Controleer de details voor deze resource-id's. Probeer uw bewerking opnieuw uit zodra de bewerkingen in behandeling zijn voltooid.  |
|  MoveResourcesNotFound  |  De aanvraag voor verplaatsingsresources bevat resources die niet kunnen worden gevonden. Controleer de details voor deze resource-id's.  |
|  NetworkingInternalOperationError  |  Onbekende fout bij netwerktoewijzing.  |
|  NetworkingInternalOperationError  |  Fout bij onbekende netwerktoewijzing  |
|  NetworkingInternalOperationError  |  Er is een interne fout opgetreden bij het verwerken van het netwerkprofiel van de VM.  |
|  NotFound  |  De beschikbaarheidsset {0} kan niet worden gevonden.  |
|  NotFound  |  Bron Virtual{0}Machine ' ' ' opgegeven in de aanvraag bestaat niet in deze Azure-locatie.  |
|  NotFound  |  Huurder met {0} id niet gevonden.  |
|  NotFound  |  De {0} afbeelding kan niet worden gevonden.  |
|  Niet ondersteund  |  Het licentietype {0}is , {1} maar de afbeeldingblob is niet van on-premises.  |
|  OperationNotA toegestaan  |  Beschikbaarheidsset {0} kan niet worden verwijderd. Voordat u een beschikbaarheidsset verwijderd, moet u ervoor zorgen dat deze geen VM bevat.  |
|  OperationNotA toegestaan  |  Het wijzigen van beschikbaarheidsset SKU van 'Uitgelijnd' naar 'Klassiek' is niet toegestaan.  |
|  OperationNotA toegestaan  |  Kan extensies in de virtuele machine niet wijzigen wanneer de virtuele machine niet wordt uitgevoerd.  |
|  OperationNotA toegestaan  |  De actie Vastleggen wordt alleen ondersteund op een virtuele machine met blobgebaseerde schijven. Gebruik de bron-API's 'Image' om een afbeelding te maken van een beheerde virtuele machine.  |
|  OperationNotA toegestaan  |  De {0} bron kan niet {1} worden gemaakt vanuit Image totdat Image is gemaakt.  |
|  OperationNotA toegestaan  |  Updates voor versleutelingInstellingen zijn niet toegestaan wanneer VM is toegewezen, Probeer opnieuw nadat VM is deald  |
|  OperationNotA toegestaan  |  Toevoeging van een beheerde schijf aan een VM met blobgebaseerde schijven wordt niet ondersteund.  |
|  OperationNotA toegestaan  |  Het maximum aantal gegevensschijven dat aan een VM van {0}deze grootte mag worden gekoppeld, is .  |
|  OperationNotA toegestaan  |  Toevoeging van een blob gebaseerde schijf aan VM met beheerde schijven wordt niet ondersteund.  |
|  OperationNotA toegestaan  |  Bewerking{0}' ' is niet{1}toegestaan op Image ' ' omdat de afbeelding is gemarkeerd voor verwijdering. U de bewerking Verwijderen alleen opnieuw proberen (of wachten tot een bewerking is voltooid).  |
|  OperationNotA toegestaan  |  Bewerking{0}' ' is niet{1}toegestaan op VM ' ' omdat de VM is gegeneraliseerd.  |
|  OperationNotA toegestaan  |  Bewerking{0}' is niet toegestaan omdat{1}restore point collection ' is gemarkeerd voor verwijdering.  |
|  OperationNotA toegestaan  |  Bewerking{0}' ' is niet toegestaan{1}op VM-extensie ' ' omdat deze is gemarkeerd voor verwijdering. U de bewerking Verwijderen alleen opnieuw proberen (of wachten tot een bewerking is voltooid).  |
|  OperationNotA toegestaan  |  Bediening{0}' is niet toegestaan omdat{1}de Virtuele Machines ' worden{2}ingericht met behulp van de Afbeelding ''.  |
|  OperationNotA toegestaan  |  Bediening{0}' ' is niet toegestaan, omdat{1}de Virtual Machine{2}ScaleSet ' momenteel gebruik maakt van de Afbeelding ''.  |
|  OperationNotA toegestaan  |  Bewerking{0}' ' is niet{1}toegestaan op VM ' ' omdat de VM is gemarkeerd voor verwijdering. U de bewerking Verwijderen alleen opnieuw proberen (of wachten tot een bewerking is voltooid).  |
|  OperationNotA toegestaan  |  Operatie{0}' ' is niet{1}toegestaan op VM ' omdat de VM is ofwel deallocated of gemarkeerd te worden deallocated.  |
|  OperationNotA toegestaan  |  Bewerking{0}' ' is niet{1}toegestaan op VM ' ' omdat de VM wordt uitgevoerd. Schakel expliciet uit voor het geval u de VM vanuit het gastbesturingssysteem uitschakelt.  |
|  OperationNotA toegestaan  |  Operatie{0}' ' is niet{1}toegestaan op VM ' ' omdat de VM niet is deallocated.  |
|  OperationNotA toegestaan  |  Bewerking{0}' ' is niet{1}toegestaan op VM{2}' ' omdat VM extensie heeft ' ' in mislukte staat.  |
|  OperationNotA toegestaan  |  Bewerking{0}' is niet toegestaan{1}op VM ' omdat er een andere bewerking aan de gang is.  |
|  OperationNotA toegestaan  |  De operatie{0}' ' vereist{1}dat de Virtuele Machine ' wordt gegeneraliseerd.  |
|  OperationNotA toegestaan  |  De bewerking vereist dat de VM wordt uitgevoerd (of ingesteld om uit te voeren).  |
|  OperationNotA toegestaan  |  Schijf met {0}formaat GB, die {1}kleiner is dan de grootte GB van de bijbehorende schijf in Image, is niet toegestaan.  |
|  OperationNotA toegestaan  |  VM Scale Set-extensies{0}van handler ' ' kunnen alleen worden toegevoegd op het moment van het maken van VM-schaalset.  |
|  OperationNotA toegestaan  |  VM Scale Set-extensies{0}van handler ' ' kunnen alleen worden verwijderd op het moment dat vm-schaalset wordt verwijderd.  |
|  OperationNotA toegestaan  |  VM{0}' ' is al met behulp van beheerde schijven.  |
|  OperationNotA toegestaan  |  VM{0}' ' behoort tot 'Classic' beschikbaarheid set '.{1} Werk de beschikbaarheidsset bij om 'Aligned' SKU te gebruiken en probeer vervolgens de conversie opnieuw.  |
|  OperationNotA toegestaan  |  VM die is gemaakt met Image kan geen blobgebaseerde schijven hebben. Alle schijven moeten worden beheerd schijven.  |
|  OperationNotA toegestaan  |  De opnamebewerking kan niet worden voltooid omdat de vm niet is gegeneraliseerd.  |
|  OperationNotA toegestaan  |  Beheerbewerkingen op{0}VM ' ' zijn niet toegestaan omdat VM-schijven worden geconverteerd naar beheerde schijven.  |
|  OperationNotA toegestaan  |  Een voortdurende bewerking verandert de energiestatus van Virtual Machine {0} naar {1}. Voer de {2} werking na enige tijd uit.  |
|  OperationNotA toegestaan  |  Kan de vm niet toevoegen of bijwerken. De gevraagde VM-grootte {0} is mogelijk niet beschikbaar in de bestaande toewijzingseenheid. Lees meer over vm-hergroottestrategie op https://aka.ms/azure-resizevm.  |
|  OperationNotA toegestaan  |  Het formaat van de virtuele machine {0} kan niet worden wijzigen omdat de gevraagde grootte niet beschikbaar is in het cluster waar de beschikbaarheidsset momenteel is toegewezen. De beschikbare maten {1}zijn: . Lees meer over vm-hergroottestrategie op https://aka.ms/azure-resizevm.  |
|  OperationNotA toegestaan  |  Het formaat van de virtuele machine {0} kan niet worden wijzigen omdat de gevraagde grootte niet beschikbaar is in het cluster waarin de virtuele machine momenteel is toegewezen. Als u het {1} formaat van uw VM wilt wijzigen om de toewijzing (dit is De bewerking stoppen in de Azure-portal) te wijzigen en de bewerking opnieuw te wijzigen. Lees meer over vm-hergroottestrategie op https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  OS Provisioning is{0}mislukt voor VM ' ' omdat het gastbesturingssysteem momenteel wordt ingericht.  |
|  OSProvisioningClientError  |  OS provisioning voor{0}VM ' ' mislukt. Foutgegevens: {1} zorg ervoor dat de afbeelding goed is voorbereid (gegeneraliseerd). <ul><li>Instructies voor Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  SSH host key generation failed. Foutgegevens: {0}. Om dit probleem op te lossen, controleert u of de Linux-agent correct is ingesteld. <ul><li>U de instructies bekijken op:https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  De gebruikersnaam die is opgegeven voor de VM is ongeldig voor deze Linux-distributie. Foutgegevens: {0}.  |
|  OSProvisioningInternalError  |  OS Provisioning is{0}mislukt voor VM ' ' vanwege een interne fout.  |
|  OSProvisioningTimedOut  |  OS Provisioning for{0}VM ' ' eindigde niet in de toegewezen tijd. De VM kan de inrichting nog steeds voltooien. Controleer de inleveringsstatus later.  |
|  OSProvisioningTimedOut  |  OS Provisioning for{0}VM ' ' eindigde niet in de toegewezen tijd. De VM kan de inrichting nog steeds voltooien. Controleer de inleveringsstatus later. Zorg er ook voor dat de afbeelding goed is voorbereid (gegeneraliseerd).   <ul><li>Instructies voor Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instructies voor Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  OS Provisioning for{0}VM ' ' eindigde niet in de toegewezen tijd. De VM-gastagent is echter gedetecteerd als actief. Dit suggereert dat het gastbesturingssysteem niet goed is voorbereid om te worden gebruikt als een VM-afbeelding (met CreateOption=FromImage). Als u dit probleem wilt oplossen, gebruikt u de VHD zoals met CreateOption=Attach of bereid u het goed voor op gebruik als afbeelding:   <ul><li>Instructies voor Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instructies voor Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverconstrainedAllocationRequest  |  De vereiste VM-grootte is momenteel niet beschikbaar op de geselecteerde locatie.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Resource kan op dit moment niet worden bijgewerkt vanwege de lopende platformupdate. Probeer het later opnieuw.  |
|  StorageAccountLimitation StorageAccountLimitation  |  Opslagaccount{0}' ' ondersteunt geen paginablobs die nodig zijn om schijven te maken.  |
|  StorageAccountLimitation StorageAccountLimitation  |  De opslagrekening '{0}heeft het toegewezen quotum overschreden.  |
|  StorageAccountLocationMismatch  |  Kan het opslagaccount {0}niet oplossen . Controleer of het is gemaakt via de Storage Resource Provider op dezelfde locatie als de compute resource.  |
|  StorageAccountNotFound  |  Opslagaccount {0} niet gevonden. Zorg ervoor dat het opslagaccount niet wordt verwijderd en tot dezelfde Azure-locatie behoort als de VM.  |
|  StorageAccountNotRecognized  |  Gebruik een opslagaccount dat wordt beheerd door Storage Resource Provider. Het {0} gebruik van wordt niet ondersteund.  |
|  StorageAccountOperationInternalError  |  Interne fout is opgetreden {0}tijdens het openen van een opslagaccount .  |
|  StorageAccountSubscriptionMismatch  |  Opslagaccount {0} behoort niet tot {1}het abonnement.  |
|  StorageAccountTooBusy  |  Opslagaccount{0}' is momenteel te druk. Overweeg een ander account te gebruiken.  |
|  StorageAccountTypeNotSupported  |  {0} Schijfwordt {1} gebruikt, wat een Blob-opslagaccount is. Probeer het opnieuw met een opslagaccount voor algemeen gebruik.  |
|  StorageAccountTypeNotSupported  |  Opslagaccount {0} is {1} van type. Boot Diagnostics {2} ondersteunt opslagaccounttypen.  <ul><li>Deze fout treedt op als u het premium opslagaccount gebruikt voor opstartdiagnoses. Zie [Opstartdiagnose gebruiken](boot-diagnostics.md)voor meer informatie. </li></ul> |
|  AbonnementnietgeautoriseerdForImage  |  Het abonnement is niet geautoriseerd.  |
|  TargetDiskBlobBestaat al  |  Blob {0} bestaat al. Geef een andere blob URI op om{1}een nieuwe lege gegevensschijf te maken ' '.  |
|  TargetDiskBlobBestaat al  |  De opnamebewerking kan niet {0} worden voortgezet omdat de blob van de doelafbeelding al bestaat en de vlag om VHD-blobs te overschrijven niet is ingesteld. Verwijder de blob of stel de vlag in om VHD-blobs te overschrijven en probeer het opnieuw.  |
|  TargetDiskBlobBestaat al  |  De opnamebewerking kan niet {0} worden voortgezet omdat de blob van de doelafbeelding een actieve lease heeft.   |
|  TargetDiskBlobBestaat al  |  Blob {0} bestaat al. Geef een andere blob URI als{1}doel voor schijf '' '.  |
|  TeManyVMRedeploymentRequests  |  Er zijn te veel herplaatsingsaanvragen ontvangen voor VM '{0}of de VM's in dezelfde beschikbaarheidsset met deze VM. Probeer het later opnieuw.  |
|  VHDSizeOngeldig  |  De opgegeven schijfgroottewaarde {0} van{1}voor {2} schijf ' met blob is ongeldig. De schijfgrootte {3} moet {4}tussen en .  |
|  VMAgentStatusCommunicationFout  |  VM{0}' ' heeft geen status gemeld voor VM-agent of extensies. Controleer of de VM een draaiende VM-agent heeft en uitgaande verbindingen met Azure-opslag kan maken.  |
|  VMArtefactRepositoryInternalError  |  Er is een fout opgetreden tijdens het communiceren met de artefact-opslagplaats om VM-artefactgegevens op te halen.  |
|  VMArtefactRepositoryInternalError  |  Er is een interne fout opgetreden tijdens het ophalen van de VM-artefactgegevens uit de artefact-opslagplaats.  |
|  VMExtensionHandlerNonTransientError  |  Handler{0}' heeft melding gemaakt{1}van een storing{2}voor VM Extension{3}' met terminalfoutcode ' en foutmelding: '  |
|  VMExtensionManagementInternalError  |  Interne fout opgetreden tijdens{0}de verwerking vm extensie '' ' .  |
|  VMExtensionManagementInternalError  |  Er zijn meerdere fouten opgetreden tijdens de voorbereiding van de VM-extensies. Zie voorbeeldweergave VM-extensie voor meer informatie.  |
|  VMExtensionProvisioningError  |  VM heeft een storing gemeld{0}bij het verwerken van extensie ' '. Foutmelding: "{1}".  |
|  VMExtensionProvisioningError  |  Meerdere VM-extensies kunnen niet op de VM worden ingericht. Zie de voorbeeldweergave vm-extensie voor meer informatie.  |
|  VMExtensionProvisioningTime-out  |  De inrichting van{0}VM-extensie ' ' heeft een time-out. De installatie van de uitbreiding kan te lang duren, of de uitbreidingsstatus kon niet worden verkregen.  |
|  VMMarketplaceOngeldigInput  |  Als u een virtuele machine maakt op basis van een niet-Marketplace-afbeelding, hoeft u geen plangegevens te gebruiken en verwijdert u de plangegevens in de aanvraag. OS-schijfnaam {0}is .  |
|  VMMarketplaceOngeldigInput  |  De aankoopgegevens komen niet overeen. Kan niet worden geïmplementeerd vanuit de Marketplace-afbeelding. OS-schijfnaam {0}is .  |
|  VMMarketplaceOngeldigInput  |  Voor het maken van een virtuele machine op Marketplace-afbeelding moet u informatie plannen in de aanvraag. OS-schijfnaam {0}is .  |
|  VMNotFound  |  De VM{0}' ' kan niet worden gevonden.  |
|  VMRedeploymentfailed  |  De{0}herschikking van VM ' is mislukt vanwege een interne fout. Probeer het later opnieuw.  |
|  VMRedeploymentTimedOut  |  Herschikking van VM{0}' ' is niet voltooid in de toegewezen tijd. Het zou kunnen eindigen met succes in een keer. Anders u het verzoek opnieuw proberen.  |
|  VMStartTimedOut  |  VM{0}' ' begon niet in de toegewezen tijd. De VM kan nog steeds met succes worden gestart. Controleer de stroomstatus later.  |


## <a name="next-steps"></a>Volgende stappen
Als u meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de MSDN Azure- en Stack Overflow-forums.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.

---
title: Veelgestelde vragen over Windows virtueel bureau blad-Azure
description: Veelgestelde vragen en aanbevolen procedures voor virtueel bureau blad van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b7ab9e63bfe92967eca22b60dceec0de882768a6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531867"
---
# <a name="windows-virtual-desktop-faq"></a>Veelgestelde vragen over Windows virtueel bureau blad

In dit artikel vindt u antwoorden op veelgestelde vragen en worden aanbevolen procedures beschreven voor virtuele Windows-Bureau bladen.

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>Wat zijn de minimale beheerders machtigingen die ik nodig heb om objecten te beheren?

Als u hostgroepen en andere objecten wilt maken, moet u de rol Inzender toewijzen aan het abonnement of de resource groep waarmee u werkt.

U moet de rol beheerder voor gebruikers toegang toewijzen aan een app-groep om app-groepen te kunnen publiceren naar gebruikers of gebruikers groepen.

Als u een beheerder wilt beperken om alleen gebruikers sessies te beheren, zoals het verzenden van berichten aan gebruikers, het afmelden van gebruikers, enzovoort, kunt u aangepaste rollen maken. Bijvoorbeeld: 

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/write",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>Ondersteunt Windows Virtual Desktop Azure Active Directory modellen?

Wanneer een gebruiker is toegewezen aan een app-groep, voert de service een eenvoudige functie toewijzing op basis van op rollen gebaseerde toegangs beheer (RBAC) van Azure uit. Als gevolg hiervan moeten de Azure Active Directory van de gebruiker (AD) en de Azure AD van de app-groep zich op dezelfde locatie bestaan. Alle service objecten, zoals hostgroepen, app-groepen en werk ruimten, moeten ook zich in dezelfde Azure AD bevinden als de gebruiker.

U kunt virtuele machines (Vm's) in een andere Azure AD maken, zolang u de Active Directory synchroniseert met de Azure AD van de gebruiker in hetzelfde virtuele netwerk (VNET).

Azure Lighthouse biedt geen volledige ondersteuning voor het beheren van de virtuele Windows-desktop omgeving. Omdat Lighthouse momenteel geen cross-Azure AD-Tenant gebruikers beheer ondersteunt, moeten Lighthouse-klanten zich nog steeds aanmelden bij de Azure AD die klanten gebruiken om gebruikers te beheren.

## <a name="what-are-location-restrictions"></a>Wat zijn locatie beperkingen?

Aan alle service resources is een locatie gekoppeld. De locatie van een hostgroep bepaalt op welke geografie de meta gegevens van de service voor de hostgroep worden opgeslagen. Er kan geen app-groep bestaan zonder een hostgroep. Als u apps toevoegt aan een groep RemoteApp-apps, hebt u ook een sessiehost nodig om de apps in het menu Start te bepalen. Voor elke actie van een app-groep hebt u ook een gerelateerde gegevens toegang nodig voor de hostgroep. Om ervoor te zorgen dat er geen gegevens worden overgedragen tussen meerdere locaties, moet de locatie van de app-groep gelijk zijn aan die van de hostgroep.

Werk ruimten moeten ook zich op dezelfde locatie bevinden als de app-groepen. Telkens wanneer de werk ruimte wordt bijgewerkt, worden de gerelateerde app-groepen samen bijgewerkt. Net als bij app-groepen vereist de service dat alle werk ruimten zijn gekoppeld aan app-groepen die zijn gemaakt op dezelfde locatie.

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>Hoe vouwt u de eigenschappen van een object uit in Power shell?

Wanneer u een Power shell-cmdlet uitvoert, worden alleen de resource naam en-locatie weer geven.

Bijvoorbeeld:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

Als u alle eigenschappen van een resource wilt weer geven, voegt u `format-list` of `fl` toe aan het einde van de cmdlet.

Bijvoorbeeld:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

Als u specifieke eigenschappen wilt weer geven, voegt u de specifieke eigenschaps namen toe na `format-list` of `fl` .

Bijvoorbeeld:

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>Ondersteunt Windows virtueel bureau blad gast gebruikers?

Windows virtueel bureau blad biedt geen ondersteuning voor Azure AD-gast gebruikers accounts. Stel bijvoorbeeld dat een groep gast gebruikers Microsoft 365 E3 per gebruiker, Windows E3 per gebruiker of WIN VDA-licenties in hun eigen bedrijf hebben, maar gast gebruikers in de Azure AD van een ander bedrijf. Het andere bedrijf beheert de gebruikers objecten van de gast gebruikers in zowel Azure AD als Active Directory als lokale accounts.

U kunt uw eigen licenties niet gebruiken voor het voor deel van een derde partij. Windows virtueel bureau blad biedt momenteel geen ondersteuning voor micro soft-account (MSA).

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>Waarom zie ik het client-IP-adres niet in de tabel WVDConnections?

Er is momenteel geen betrouw bare manier om de IP-adressen van de webclient te verzamelen. deze waarde wordt niet in de tabel vermeld.

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Hoe worden back-ups door Windows Virtual Desktop afgehandeld?

Er zijn meerdere opties in azure voor het verwerken van back-ups. U kunt Azure backup, Site Recovery en moment opnamen gebruiken.

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>Ondersteunt Windows virtueel bureau blad samenwerkings-apps van derden?

Virtueel bureau blad van Windows is momenteel geoptimaliseerd voor teams. Micro soft biedt momenteel geen ondersteuning voor samenwerkings-apps van derden, zoals zoomen. Organisaties van derden zijn verantwoordelijk voor het verstrekken van compatibiliteits richtlijnen aan hun klanten. Virtueel bureau blad van Windows biedt ook geen ondersteuning voor Skype voor bedrijven.

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>Kan ik overschakelen van groep naar persoonlijke hostgroepen?

Wanneer u een hostgroep hebt gemaakt, kunt u het type niet meer wijzigen. U kunt echter alle Vm's die u registreert voor een hostgroep verplaatsen naar een ander type hostgroep.

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>Wat is de grootste profiel grootte die FSLogix kan verwerken?

Beperkingen of quota's in FSLogix zijn afhankelijk van de opslag-Fabric die wordt gebruikt voor het opslaan van VHD (X)-bestanden van het gebruikers profiel.

De volgende tabel bevat een voor beeld van hoe resources een FSLogix-profiel nodig heeft om elke gebruiker te ondersteunen. De vereisten kunnen sterk variëren, afhankelijk van de gebruiker, de toepassingen en de activiteit voor elk profiel. 

| Resource | Vereiste |
|---|---|
| IOPS met stabiele status | 10 |
| IOPS voor aanmelden/afmelden | 5 |

Het voor beeld in deze tabel is van één gebruiker, maar kan worden gebruikt voor het schatten van de vereisten voor het totale aantal gebruikers in uw omgeving. U hebt bijvoorbeeld ongeveer 1.000 IOPS nodig voor 100 gebruikers, en rond 5.000 IOPS tijdens het aanmelden en afmelden.

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>Is er een schaal limiet voor de hostgroepen die zijn gemaakt in de Azure Portal?

Deze factoren kunnen invloed hebben op de schaal limiet voor hostgroepen:

- De Azure-sjabloon is beperkt tot 800-objecten. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits)voor meer informatie. Elke VM maakt ook ongeveer zes objecten, wat betekent dat u elke keer dat u de sjabloon uitvoert rondom 132 Vm's kunt maken.

- Er gelden beperkingen voor het aantal kernen dat u per regio en per abonnement kunt maken. Als u bijvoorbeeld een Enterprise Agreement-abonnement hebt, kunt u 350 kernen maken. U moet 350 delen met behulp van het standaard aantal kernen per VM of uw eigen kern limiet om te bepalen hoeveel Vm's u kunt maken telkens wanneer u de sjabloon uitvoert. Meer informatie over [virtual machines limieten-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager).

- De naam van het VM-voor voegsel en het aantal Vm's zijn minder dan 15 tekens. Zie [naamgevings regels en beperkingen voor Azure-resources](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute)voor meer informatie.
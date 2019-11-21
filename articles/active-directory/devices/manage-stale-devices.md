---
title: Verlopen apparaten beheren in Azure AD | Microsoft Docs
description: Learn how to remove stale devices from your database of registered devices in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1829c56f9804c5aa808461db98a5048d63f55446
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207286"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>How To: Manage stale devices in Azure AD

In het ideale geval moet de levenscyclus van geregistreerde apparaten worden voltooid door de registratie van de apparaten ongedaan te maken op het moment dat ze niet meer nodig zijn. Als gevolg van bijvoorbeeld verloren, gestolen of defecte apparaten, of herinstallaties van het besturingssysteem, zijn er meestal echter nog wel verlopen apparaten aanwezig in uw omgeving. Als IT-beheerder is het prettig om te beschikken over een methode voor het verwijderen van verlopen apparaten, zodat u zich kunt richten op het beheren van apparaten die nog echt in gebruik zijn.

In dit artikel leert u hoe u op een efficiënte manier verlopen apparaten in uw omgeving kunt beheren.
  

## <a name="what-is-a-stale-device"></a>Wat is een verlopen apparaat?

Een verlopen apparaat is een apparaat dat is geregistreerd bij Azure AD, maar dat gedurende een bepaalde periode niet is gebruikt voor toegang tot cloud-apps. Verlopen apparaten hebben vanwege de volgende redenen invloed op het efficiënt beheren en ondersteunen van apparaten en gebruikers in de tenant: 

- Dubbele apparaten kunnen het lastig maken voor de helpdesk om te bepalen welk apparaat momenteel actief is.
- An increased number of devices creates unnecessary device writebacks increasing the time for Azure AD connect syncs.
- Als een algemene maatregel en om te voldoen aan nalevingsvereisten, kan het prettig zijn om alleen met actieve apparaten te werken. 

Verlopen apparaten in Azure AD kunnen van invloed zijn op het algemene levenscyclusbeleid voor apparaten in uw organisatie.

## <a name="detect-stale-devices"></a>Verlopen apparaten detecteren

De definitie van een verlopen apparaat is een geregistreerd apparaat dat gedurende een bepaalde periode niet is gebruikt om toegang te krijgen tot cloud-apps. Om die reden vereist het detecteren van verlopen apparaten een eigenschap waarin een tijdstempel wordt opgeslagen. In Azure AD heeft deze eigenschap de naam **ApproximateLastLogonTimestamp** (ook wel **tijdstempel van activiteit** genoemd). Als het verschil tussen nu en de waarde van de **tijdstempel van activiteit** groter is dan de periode die u hebt gedefinieerd voor actieve apparaten, wordt een apparaat beschouwd als verlopen. Deze **tijdstempel van activiteit** is nu in openbare preview.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Hoe wordt de waarde van de tijdstempel van activiteit beheerd?  

De evaluatie van de tijdstempel van activiteit wordt geactiveerd door een poging tot verificatie van een apparaat. In de volgende gevallen wordt de tijdstempel van activiteit geëvalueerd door Azure AD:

- A Conditional Access policies requiring [managed devices](../conditional-access/require-managed-devices.md) or [approved client apps](../conditional-access/app-based-conditional-access.md) has been triggered.
- Windows 10-apparaten die zijn toegevoegd aan Azure AD of hybride Azure AD zijn actief in het netwerk. 
- Met Intune beheerde apparaten zijn ingecheckt bij de service.

If the delta between the existing value of the activity timestamp and the current value is more than 14 days (+/-5 day variance), the existing value is replaced with the new value.

## <a name="how-do-i-get-the-activity-timestamp"></a>Hoe kom ik aan het tijdstempel van activiteit?

Er zijn twee manieren om de waarde van de tijdstempel van activiteit te bepalen:

- Via de kolom **Activiteit** op de [pagina Apparaten](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) in de Azure-portal

    ![Tijdstempel van activiteit](./media/manage-stale-devices/01.png)

- Via de cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0)

    ![Tijdstempel van activiteit](./media/manage-stale-devices/02.png)

## <a name="plan-the-cleanup-of-your-stale-devices"></a>Opschonen van verlopen apparaten plannen

Om verlopen apparaten op een efficiënte manier op te schonen in uw omgeving, moet u een opschoningsbeleid definiëren. Dit beleid zorgt ervoor dat u rekening houdt met alle overwegingen met betrekking tot verlopen apparaten. In de volgende gedeelten vindt u voorbeelden van algemene beleidsoverwegingen. 

### <a name="cleanup-account"></a>Opschoningsaccount

Als u een apparaat wilt bijwerken in Azure AD, hebt u een account nodig waaraan een van de volgende rollen is toegewezen:

- Globale beheerder
- Cloud Device Administrator
- Intune-servicebeheerder

Selecteer in uw opschoningsbeleid accounts die over de vereiste rollen beschikken. 

### <a name="timeframe"></a>Tijdsbestek

Definieer de periode die u als indicator wilt gebruiken voor een verlopen apparaat. When defining your timeframe, factor the window noted for updating the activity timestamp into your value. For example, you shouldn't consider a timestamp that is younger than 21 days (includes variance) as an indicator for a stale device. Er zijn scenario's waarin het lijkt alsof een apparaat verlopen is terwijl dat niet het geval is. Zo is het bijvoorbeeld mogelijk dat de eigenaar van het apparaat op vakantie is of ziek is en  hierdoor de periode voor verlopen apparaten wordt overschreden.

### <a name="disable-devices"></a>Apparaten uitschakelen

Het wordt afgeraden om een apparaat dat lijkt te zijn verlopen direct te verwijderen. Het is namelijk niet mogelijk om het apparaat weer terug te halen mocht het toch nog in gebruik zijn. De aanbevolen procedure is om een apparaat gedurende een respijtperiode uit te schakelen voordat u het definitief verwijdert. Definieer in uw beleid een tijdsbestek waarna u een apparaat uitschakelt voordat het definitief wordt verwijderd.

### <a name="mdm-controlled-devices"></a>Met MDM beheerde apparaten

Als een apparaat onder beheer staat van Intune of een andere MDM-oplossing, stelt u het apparaat in het beheersysteem buiten gebruik voordat u het uitschakelt of verwijdert.

### <a name="system-managed-devices"></a>Door systeem beheerde apparaten

Verwijder geen apparaten die door het systeem worden beheerd. Dit zijn meestal apparaten zoals auto-pilot. Once deleted, these devices can't be reprovisioned. Door het systeem beheerde apparaten worden standaard uitgesloten door de nieuwe cmdlet `get-msoldevice`. 

### <a name="hybrid-azure-ad-joined-devices"></a>Hybride Azure AD-gekoppelde apparaten

Apparaten die zijn gekoppeld aan hybride Azure AD moeten het beleid volgen voor het beheer van on-premises verlopen apparaten. 

Azure AD opschonen:

- **Windows 10-apparaten**: schakel Windows 10-apparaten uit in uw on-premises AD of verwijder ze, en laat Azure AD Connect de gewijzigde apparaatstatus synchroniseren naar Azure AD.
- **Windows 7/8** - Disable or delete Windows 7/8 devices in your on-premises AD first. U kunt Azure AD Connect niet gebruiken voor het uitschakelen of verwijderen van Windows 7/8-apparaten in Azure AD. Instead, when you make the change in your on-premises, you must disable/delete in Azure AD.

> [!NOTE]
>* Deleting devices in your on-premises AD or Azure AD does not remove registration on the client. It will only prevent access to resources using device as an identity (e.g. Conditional Access). Read additional information on how to [remove registration on the client](faq.md#hybrid-azure-ad-join-faq).
>* Deleting a Windows 10 device only in Azure AD will re-synchronize the device from your on-premises using Azure AD connect but as a new object in "Pending" state. A re-registration is required on the device.
>* Removing the device from sync scope for Windows 10/Server 2016 devices will delete the Azure AD device. Adding it back to sync scope will place a new object in "Pending" state. A re-registration of the device is required.
>* If you not using Azure AD Connect for Windows 10 devices to synchronize (e.g. ONLY using AD FS for registration), you must manage lifecycle similar to Windows 7/8 devices.


### <a name="azure-ad-joined-devices"></a>Azure AD-gekoppelde apparaten

Apparaten die zijn gekoppeld aan Azure AD kunt u uitschakelen of verwijderen in Azure AD.

> [!NOTE]
>* Deleting an Azure AD device does not remove registration on the client. It will only prevent access to resources using device as an identity (e.g Conditional Access). 
>* Read more on [how to unjoin on Azure AD](faq.md#azure-ad-join-faq) 

### <a name="azure-ad-registered-devices"></a>Azure AD-geregistreerde apparaten

Apparaten die zijn geregistreerd in Azure AD kunt u uitschakelen of verwijderen in Azure AD.

> [!NOTE]
>* Deleting an Azure AD registered device in Azure AD does not remove registration on the client. It will only prevent access to resources using device as an identity (e.g. Conditional Access).
>* Read more on [how to remove a registration on the client](faq.md#azure-ad-register-faq)

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Verlopen apparaten opschonen in de Microsoft Azure-portal  

Hoewel het mogelijk is om verlopen apparaten op te schonen in de Azure-portal, is het efficiënter om hiervoor een PowerShell-script te gebruiken. Gebruik de meest recente PowerShell V1-module om met behulp van het timestamp-filter apparaten uit te filteren die worden beheerd door het systeem, zoals auto-pilot. Op dit moment wordt PowerShell V2 niet aanbevolen.

Een typische routine bestaat uit de volgende stappen:

1. Verbinding maken met Azure Active Directory met behulp van de cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0)
1. De lijst met apparaten opvragen
1. Schakel het apparaat uit met de cmdlet [Disable-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/disable-msoldevice?view=azureadps-1.0). 
1. Wacht tot de respijtperiode (d.w.z. het aantal dagen dat u hiervoor hebt gekozen) is verlopen voordat u het apparaat verwijdert.
1. Verwijder het apparaat met de cmdlet [Remove-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/remove-msoldevice?view=azureadps-1.0).

### <a name="get-the-list-of-devices"></a>De lijst met apparaten opvragen

Alle apparaten opvragen en de geretourneerde gegevens opslaan in een CSV-bestand:

```PowerShell
Get-MsolDevice -all | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, Approxi
mateLastLogonTimestamp | export-csv devicelist-summary.csv
```

If you have a large number of devices in your directory, use the timestamp filter to narrow down the number of returned devices. Alle apparaten met een tijdstempel ouder dan een specifieke datum opvragen en de geretourneerde gegevens opslaan in een CSV-bestand: 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-MsolDevice -all -LogonTimeBefore $dt | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>Wat u moet weten

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Waarom wordt de tijdstempel niet vaker bijgewerkt?

De tijdstempel wordt bijgewerkt ter ondersteuning van scenario's voor de levenscyclus van apparaten. Dit is geen controlefunctie. Gebruik de auditlogboeken voor aanmelden voor meer frequent updates over het apparaat.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>Waarom moet ik mij zorgen maken over mijn BitLocker-sleutels?

Als dit is geconfigureerd, worden BitLocker-sleutels voor Windows 10-apparaten opgeslagen in het apparaatobject in Azure AD. Als u een verlopen apparaat verwijdert, verwijdert u ook de BitLocker-sleutels die op het apparaat zijn opgeslagen. U moet bepalen of uw opschoningsbeleid overeenkomt met de werkelijke levenscyclus van uw apparaat voordat u een verlopen apparaat verwijdert. 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>Why should I worry about Windows Autopilot devices?

When a Azure AD device was associated with a Windows Autopilot object the following three scenarios can occur if the device will be repurposed in future:
- With Windows Autopilot user-driven deployments without using white glove, a new Azure AD device will be created, but it won’t be tagged with the ZTDID.
- With Windows Autopilot self-deploying mode deployments, they will fail because an associate Azure AD device cannot be found.  (This is a security mechanism to make sure that no “imposter” devices try to join Azure AD with no credentials.) The failure will indicate a ZTDID mismatch.
- With Windows Autopilot white glove deployments, they will fail because an associated Azure AD device cannot be found. (Behind the scenes, white glove deployments use the same self-deploying mode process, so they enforce the same security mechanisms.)

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Hoe weet ik om wat voor type gekoppelde apparaten het gaat?

Zie [Overzicht van apparaatbeheer](overview.md) voor meer informatie over de verschillende typen.

### <a name="what-happens-when-i-disable-a-device"></a>Wat gebeurt er wanneer ik een apparaat uitschakel?

Verificatie waarbij een apparaat wordt gebruikt om te verifiëren bij Azure AD wordt geweigerd. Enkele typische voorbeelden:

- **Apparaat dat is gekoppeld aan hybride Azure AD**: gebruikers kunnen zich mogelijk met het apparaat aanmelden bij hun on-premises domein. Ze hebben echter geen toegang tot Azure AD-resources, zoals Office 365.
- **Apparaat dat is gekoppeld aan Azure AD**: gebruikers kunnen het apparaat niet gebruiken om zich aan te melden. 
- **Mobiele apparaten**: gebruikers hebben geen toegang tot Azure AD-resources, zoals Office 365. 

## <a name="next-steps"></a>Volgende stappen

Zie [Apparaten beheren met behulp van de Azure-portal](device-management-azure-portal.md) voor een overzicht van hoe u apparaten in de Azure-portal kunt beheren.

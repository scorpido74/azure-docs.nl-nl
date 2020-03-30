---
title: Gecontroleerde validatie van hybride Azure AD join - Azure AD
description: Meer informatie over het uitvoeren van een gecontroleerde validatie van hybride Azure AD-join voordat u deze in de hele organisatie tegelijk inschakelt
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43db805ccbb7d4e546c51bbe39350f4bbba2efb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049983"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Gecontroleerde validatie van hybride Azure AD-deelname

Wanneer alle vereisten zijn ingevoerd, registreren Windows-apparaten zich automatisch als apparaten in uw Azure AD-tenant. De status van deze apparaatidentiteiten in Azure AD wordt hybride Azure AD-lid genoemd. Meer informatie over de concepten in dit artikel vindt u in de artikelen [Inleiding tot apparaatbeheer in Azure Active Directory](overview.md) en Plan uw hybride Azure Active Directory [join-implementatie](hybrid-azuread-join-plan.md).

Organisaties willen mogelijk een gecontroleerde validatie van hybride Azure AD-join uitvoeren voordat ze deze in hun hele organisatie tegelijk inschakelen. In dit artikel wordt uitgelegd hoe u een gecontroleerde validatie van hybride Azure AD-join uitvoeren.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Gecontroleerde validatie van hybride Azure AD-join op windows huidige apparaten

Voor apparaten waarop het Windows-bureaubladbesturingssysteem wordt uitgevoerd, is de ondersteunde versie de Windows 10 Anniversary Update (versie 1607) of hoger. Als een aanbevolen praktijk, upgrade naar de nieuwste versie van Windows 10.

Als u een gecontroleerde validatie wilt uitvoeren van hybride Azure AD-join op de huidige Windows-apparaten, moet u het:

1. Scp-vermelding (Service Connection Point) wissen vanuit Active Directory (AD) als deze bestaat
1. Registerinstelling clientzijde configureren voor SCP op uw computers die zijn verbonden met een domein met behulp van een groepsbeleidsobject (GPO)
1. Als u AD FS gebruikt, moet u ook de client-side registry-instelling voor SCP configureren op uw AD FS-server met behulp van een GPO  
1. Mogelijk moet u ook [synchronisatieopties aanpassen](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) in Azure AD Connect om apparaatsynchronisatie in te schakelen. 


### <a name="clear-the-scp-from-ad"></a>Het SCP wissen van AD

Gebruik de Active Directory Services Interfaces Editor (ADSI Edit) om de SCP-objecten in AD te wijzigen.

1. Start de **ADSI-bureaubladtoepassing Bewerken** vanaf en het administratieve werkstation of een domeincontroller als Enterprise Administrator.
1. Maak verbinding met de **configuratienaamgevingscontext** van uw domein.
1. Blader naar **CN=Configuration,DC=contoso,DC=com** > **CN=Services** > **CN=Device Registration Configuration Configuration**
1. Klik met de rechtermuisknop op het bladobject **CN=62a0ff2e-97b9-4513-943f-0d221bd30080** en selecteer **Eigenschappen**
   1. Trefwoorden **selecteren** in het venster **Kenmerkeditor** en klik op **Bewerken**
   1. Selecteer de waarden **azureADId** en **azureADName** (één voor één) en klik op **Verwijderen**
1. **ADSI Bewerken sluiten**


### <a name="configure-client-side-registry-setting-for-scp"></a>Registerinstelling clientzijde configureren voor SCP

Gebruik het volgende voorbeeld om een Groepsbeleidsobject (GPO) te maken om een registerinstelling te implementeren die een SCP-vermelding configureert in het register van uw apparaten.

1. Open een console groepsbeleidsbeheer en maak een nieuw groepsbeleidsobject in uw domein.
   1. Geef uw nieuw gemaakte GPO een naam (bijvoorbeeld ClientSideSCP).
1. De GPO bewerken en het volgende pad zoeken: > **Preferences** > **Windows-instellingenregister** > **Registry** **voor computerconfiguratie**
1. Klik met de rechtermuisknop op het register en selecteer **Nieuw** > **registeritem**
   1. Configureer op het tabblad **Algemeen** het volgende
      1. Actie: **Bijwerken**
      1. Bijenkorf: **HKEY_LOCAL_MACHINE**
      1. Sleutelpad: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Waardenaam: **TenantId**
      1. Waardetype: **REG_SZ**
      1. Waardegegevens: de GUID- of **Directory-id** van uw Azure AD-exemplaar (deze waarde is te vinden in de **Azure Portal** > **Azure Active Directory** > **Properties** > **Directory ID)**
   1. Klik **op OK**
1. Klik met de rechtermuisknop op het register en selecteer **Nieuw** > **registeritem**
   1. Configureer op het tabblad **Algemeen** het volgende
      1. Actie: **Bijwerken**
      1. Bijenkorf: **HKEY_LOCAL_MACHINE**
      1. Sleutelpad: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Waardenaam: **TenantName**
      1. Waardetype: **REG_SZ**
      1. Waardegegevens: uw geverifieerde **domeinnaam** als u een federatieve omgeving gebruikt, zoals AD FS. Uw geverifieerde **domeinnaam** of uw onmicrosoft.com domeinnaam `contoso.onmicrosoft.com` bijvoorbeeld als u beheerde omgeving gebruikt
   1. Klik **op OK**
1. Sluit de editor voor de nieuw gecreëerde GPO
1. Koppel de nieuw gecreëerde GPO aan de gewenste organisatie-eenheid met domeinbeheercomputers die behoren tot uw gecontroleerde implementatiepopulatie

### <a name="configure-ad-fs-settings"></a>AD FS-instellingen configureren

Als u AD FS gebruikt, moet u SCP aan clientzijde eerst configureren met behulp van de hierboven genoemde instructies door het GPO te koppelen aan uw AD FS-servers. Het SCP-object definieert de bron van autoriteit voor apparaatobjecten. Het kan on-premises of Azure AD zijn. Wanneer SCP aan clientzijde is geconfigureerd voor AD FS, wordt de bron voor apparaatobjecten ingesteld als Azure AD.

> [!NOTE]
> Als u SCP aan clientzijde niet configureren op uw AD FS-servers, wordt de bron voor apparaatidentiteiten als on-premises beschouwd. ADFS zal vervolgens apparaatobjecten uit on-premises directory verwijderen na de bepaalde periode die is gedefinieerd in het kenmerk "MaximumInactiveDays" van de ADFS-apparaatregistratie. ADFS-apparaatregistratieobjecten zijn te vinden met de [cmdlet Get-AdfsDeviceRegistration](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps).

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Gecontroleerde validatie van hybride Azure AD-lid op Apparaten op Windows-niveau

Als u apparaten op downniveau van Windows wilt registreren, moeten organisaties Microsoft Workplace Join installeren [voor niet-Windows 10-computers](https://www.microsoft.com/download/details.aspx?id=53554) die beschikbaar zijn in het Microsoft Downloadcentrum.

U het pakket implementeren met behulp van een softwaredistributiesysteem zoals [Microsoft Endpoint Configuration Manager.](/configmgr/) Het pakket ondersteunt de standaard stille installatieopties met de stille parameter. De huidige tak van Configuration Manager biedt voordelen ten opzichte van eerdere versies, zoals de mogelijkheid om voltooide registraties bij te houden.

Het installatieprogramma maakt een geplande taak op het systeem dat wordt uitgevoerd in de gebruikerscontext. De taak wordt geactiveerd wanneer de gebruiker zich aanmeldt bij Windows. De taak voegt het apparaat in stilte samen met Azure AD met de gebruikersreferenties nadat deze is geauthenticerd met Azure AD.

Als u de apparaatregistratie wilt beheren, moet u het Windows Installer-pakket implementeren in uw geselecteerde groep apparaten op downniveau van Windows.

> [!NOTE]
> Als een SCP niet is geconfigureerd in AD, moet u dezelfde benadering volgen als beschreven bij [Het configureren van de registerinstelling clientzijde voor SCP)](#configure-client-side-registry-setting-for-scp)op uw computers die zijn verbonden met een domein met behulp van een Groepsbeleidsobject (GPO).


Nadat u hebt gecontroleerd of alles werkt zoals verwacht, u automatisch de rest van uw Windows-apparaten op stroom- en downniveau registreren bij Azure AD door [SCP te configureren met Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Volgende stappen

[De implementatie van uw hybride Azure Active Directory-deelname plannen](hybrid-azuread-join-plan.md)

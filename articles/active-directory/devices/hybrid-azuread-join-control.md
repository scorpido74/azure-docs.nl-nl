---
title: Gecontroleerde validatie van hybride Azure AD-deelname-Azure AD
description: Meer informatie over het uitvoeren van een gecontroleerde validatie van de deelname aan hybride Azure AD voordat u deze in de hele organisatie in één keer inschakelt
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
ms.openlocfilehash: dc5c85aaa3c2128b10ba2e6f9c45a66b44593202
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809220"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Gecontroleerde validatie van hybride Azure AD-deelname

Wanneer alle vereisten aanwezig zijn, worden Windows-apparaten automatisch geregistreerd als apparaten in uw Azure AD-Tenant. De status van deze apparaat-id's in azure AD wordt aangeduid als hybride Azure AD-deelname. Meer informatie over de concepten die in dit artikel worden behandeld, vindt u in de artikelen [Inleiding tot Apparaatbeheer in azure Active Directory](overview.md) en [de implementatie van uw hybride Azure Active Directory-deelname plannen](hybrid-azuread-join-plan.md).

Organisaties willen mogelijk een gecontroleerde validatie van hybride Azure AD-deelname uitvoeren voordat ze in hun hele organisatie allemaal tegelijk worden ingeschakeld. In dit artikel wordt uitgelegd hoe u een gecontroleerde validatie van hybride Azure AD-deelname uitvoert.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Gecontroleerde validatie van hybride Azure AD-deelname op Windows huidige apparaten

Voor apparaten met het besturings systeem Windows Desktop is de ondersteunde versie de update van Windows 10 jubileum (versie 1607) of hoger. Voer een upgrade uit naar de nieuwste versie van Windows 10 als best practice.

Voor een gecontroleerde validatie van hybride Azure AD-deelname op Windows huidige apparaten moet u het volgende doen:

1. Wis de SCP-vermelding (Service Connection Point) van Active Directory (AD) als deze bestaat
1. Register instelling aan client zijde voor SCP op computers die lid zijn van een domein configureren met behulp van een groepsbeleid-object (GPO)
1. Als u AD FS gebruikt, moet u ook de register instelling aan de client zijde voor SCP op uw AD FS-server configureren met behulp van een groeps beleidsobject  



### <a name="clear-the-scp-from-ad"></a>De SCP uit AD wissen

Gebruik de Active Directory Services Interfaces editor (ADSI bewerken) om de SCP-objecten in AD te wijzigen.

1. Start de **ADSI** -module voor het bewerken van het bureau blad van en het beheer werkstation of een domein controller als een ondernemings beheerder.
1. Maak verbinding met de **configuratie naamgevings context** van uw domein.
1. Blader naar **CN = Configuration, DC = contoso, DC = com** > **CN = Services** > **CN = Configuratie van apparaatregistratie**
1. Klik met de rechter muisknop op het blad object onder **CN = Configuratie van apparaatregistratie** en selecteer **Eigenschappen**
   1. Selecteer **tref woorden** in het venster **kenmerk editor** en klik op **bewerken**
   1. Selecteer de waarden van **azureADId** en **azureADName** (een voor een tegelijk) en klik op **verwijderen**
1. **ADSI bewerken** sluiten


### <a name="configure-client-side-registry-setting-for-scp"></a>Register instelling aan client zijde voor SCP configureren

Gebruik het volgende voor beeld om een groepsbeleid-object (GPO) te maken om een register instelling te implementeren voor het configureren van een SCP-vermelding in het REGI ster van uw apparaten.

1. Open een groepsbeleid-beheer console en maak een nieuw groepsbeleid-object in uw domein.
   1. Geef een naam op voor het nieuwe groeps beleidsobject dat u hebt gemaakt (bijvoorbeeld ClientSideSCP).
1. Bewerk het groeps beleidsobject en zoek het volgende pad: **computer configuratie** > **voor keuren** > **Windows-instellingen** > **REGI ster**
1. Klik met de rechter muisknop op het REGI ster en selecteer **nieuw** > **register item**
   1. Configureer op het tabblad **Algemeen** de volgende instellingen:
      1. Actie: **bijwerken**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Sleutelpad: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Waardenaam: **TenantId**
      1. Waardetype: **REG_SZ**
      1. Waardegegevens: de GUID of **Directory-id** van uw Azure AD-exemplaar (deze waarde is te vinden in de **Azure Portal** > **Azure Active Directory** > **Eigenschappen** > **Directory-id**)
   1. Klik op **OK**
1. Klik met de rechter muisknop op het REGI ster en selecteer **nieuw** > **register item**
   1. Configureer op het tabblad **Algemeen** de volgende instellingen:
      1. Actie: **bijwerken**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Sleutelpad: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Waardenaam: **Tenant** naam
      1. Waardetype: **REG_SZ**
      1. Waardegegevens: uw geverifieerde **domein naam** als u een federatieve omgeving gebruikt, zoals AD FS. Uw geverifieerde **domein naam** of uw onmicrosoft.com-domein naam bijvoorbeeld `contoso.onmicrosoft.com` als u een beheerde omgeving gebruikt
   1. Klik op **OK**
1. De editor voor het zojuist gemaakte groeps beleidsobject sluiten
1. Koppel het zojuist gemaakte groeps beleidsobject aan de gewenste organisatie-eenheid met computers die lid zijn van een domein en die deel uitmaken van uw geïmplementeerde implementatie populatie

### <a name="configure-ad-fs-settings"></a>AD FS-instellingen configureren

Als u AD FS gebruikt, moet u eerst SCP aan de client zijde configureren met behulp van de bovenstaande instructies, maar het groeps beleidsobject koppelen aan uw AD FS-servers. Het SCP-object definieert de bron van de autoriteit voor object apparaatobject. Het kan on-premises of Azure AD zijn. Wanneer dit is geconfigureerd voor AD FS, wordt de bron voor object apparaten ingesteld als Azure AD.

> [!NOTE]
> Als u het SCP aan client zijde op uw AD FS-servers niet configureert, wordt de bron voor apparaat-id's als on-premises beschouwd en als u het apparaat terugschrijft, wordt AD FS gestart met het verwijderen van Device-objecten uit de on-premises geregistreerde container van het apparaat na een vastgestelde periode.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Gecontroleerde validatie van hybride Azure AD-deelname op Windows-apparaten op het lagere niveau

Voor het registreren van Windows-apparaten op een lager niveau moeten organisaties [micro soft Workplace join installeren voor niet-Windows 10-computers](https://www.microsoft.com/download/details.aspx?id=53554) die beschikbaar zijn via het micro soft Download centrum.

U kunt het pakket implementeren met behulp van een software distributiesysteem als [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Het pakket ondersteunt de standaard opties voor installatie op de achtergrond met de Stille para meter. De huidige vertakking van Configuration Manager biedt voor delen ten opzichte van eerdere versies, zoals de mogelijkheid om voltooide registraties bij te houden.

Het installatie programma maakt een geplande taak op het systeem dat wordt uitgevoerd in de gebruikers context. De taak wordt geactiveerd wanneer de gebruiker zich aanmeldt bij Windows. De taak wordt tijdens de verificatie met Azure AD met de gebruikers referenties op de achtergrond gekoppeld aan het apparaat met Azure AD.

Als u de apparaatregistratie wilt beheren, moet u het Windows Installer-pakket implementeren op de geselecteerde groep apparaten van het lagere niveau Windows.

> [!NOTE]
> Als een SCP niet is geconfigureerd in AD, moet u dezelfde benadering volgen als beschreven voor het configureren van de [register instelling aan client zijde voor SCP](#configure-client-side-registry-setting-for-scp)) op computers die lid zijn van een domein met behulp van een Groepsbeleid-object (GPO).


Nadat u hebt gecontroleerd of alles werkt zoals verwacht, kunt u de rest van uw Windows-apparaten voor huidige en lagere niveaus automatisch registreren met Azure AD door [SCP te configureren met Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Volgende stappen

[De implementatie van uw hybride Azure Active Directory-deelname plannen](hybrid-azuread-join-plan.md)

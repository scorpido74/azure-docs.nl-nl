---
title: Aanmelden bij windows virtuele machine in Azure met Azure Active Directory (Preview)
description: Azure AD meldt zich aan bij een Azure VM met Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88ae3c45126403161e35ec46e5ccc2666c3edb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050066"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Aanmelden bij windows virtuele machine in Azure met Azure Active Directory-verificatie (Preview)

Organisaties kunnen nu Ad-verificatie (Azure Active Directory) gebruiken voor hun Virtual Azure-machines (VM's) met **Windows Server 2019 Datacenter-editie** of **Windows 10 1809** en hoger. Het gebruik van Azure AD om te verifiëren aan VM's biedt u een manier om beleid centraal te beheren en af te dwingen. Met hulpprogramma's zoals RBAC (Azure Role-Based Access Control) en Voorwaardelijke toegang tot Azure AD u bepalen wie toegang heeft tot een vm. In dit artikel ziet u hoe u een Windows Server 2019-vm maakt en configureert om Azure AD-verificatie te gebruiken.

|     |
| --- |
| Azure AD-aanmelding voor Azure Windows VM's is een openbare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) meer informatie over voorvertoningen|
|     |

Er zijn veel voordelen van het gebruik van Azure AD-verificatie om u aan te melden bij Windows VM's in Azure, waaronder:

- Gebruik dezelfde federatieve of beheerde Azure AD-referenties die u normaal gesproken gebruikt.
- U hoeft geen accounts van lokale beheerders meer te beheren.
- Met Azure RBAC u de juiste toegang tot VM's verlenen op basis van behoefte en deze verwijderen wanneer deze niet meer nodig is.
- Voordat u toegang tot een vm toestaat, kan Azure AD Conditional Access aanvullende vereisten afdwingen, zoals: 
   - Multi-Factor Authentication
   - Risicocontrole bij het aanmelden
- Automatiseer en schaal Azure AD-lid van Azure Windows VM's die deel uitmaken van uw VDI-implementaties.

> [!NOTE]
> Zodra u deze mogelijkheid hebt ingeschakeld, zijn uw Windows VM's in Azure Azure nl/' toegetreden. U het niet deelnemen aan andere domeinen, zoals on-premises AD of Azure AD DS. Als u dit moet doen, moet u de VM loskoppelen van uw Azure AD-tenant door de extensie te verwijderen.

## <a name="requirements"></a>Vereisten

### <a name="supported-azure-regions-and-windows-distributions"></a>Ondersteunde Azure-regio's en Windows-distributies

De volgende Windows-distributies worden momenteel ondersteund tijdens de preview van deze functie:

- Windows Server 2019 Datacenter
- Windows 10 1809 en hoger

> [!IMPORTANT]
> Verbinding op afstand met VM's die zijn aangesloten op Azure AD is alleen toegestaan vanaf Windows 10-pc's waarvan Azure AD is samengevoegd of hybride Azure AD is samengevoegd tot **dezelfde** map als de VM. 

De volgende Azure-regio's worden momenteel ondersteund tijdens de preview van deze functie:

- Alle globale Azure-regio's

> [!IMPORTANT]
> Als u deze voorbeeldfunctie wilt gebruiken, implementeert u alleen een ondersteunde Windows-distributie en in een ondersteunde Azure-regio. De functie wordt momenteel niet ondersteund in Azure Government of sovereign clouds.

### <a name="network-requirements"></a>Netwerkvereisten

Als u Azure AD-verificatie voor uw Windows VM's in Azure wilt inschakelen, moet u ervoor zorgen dat uw VM's-netwerkconfiguratie uitgaande toegang tot de volgende eindpunten via TCP-poort 443 mogelijk maken:

- https:\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https:\//device.login.microsoftonline.com
- https:\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Azure AD-aanmelding inschakelen voor Windows VM in Azure

Als u Azure AD-aanmelding voor Windows VM in Azure wilt gebruiken, moet u eerst de azure AD-aanmeldingsoptie voor uw Windows-vm inschakelen en vervolgens RBAC-roltoewijzingen configureren voor gebruikers die gemachtigd zijn om in te loggen op de VM.
Er zijn meerdere manieren waarop u Azure AD-aanmelding voor uw Windows-vm inschakelen:

- De Azure-portalervaring gebruiken bij het maken van een Windows-vm
- De Azure Cloud Shell-ervaring gebruiken bij het maken van een Windows-vm **of voor een bestaande Windows-VM**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Azure-portal gebruiken om VM-ervaring te maken om Azure AD-aanmelding in te schakelen

U Azure AD-aanmelding inschakelen voor Windows Server 2019 Datacenter of Windows 10 1809 en later VM-afbeeldingen. 

Ga als lid van het nieuwe bedrijf naar een VM voor Windows Server 2019-datacenter in Azure met Azure AD-aanmelding: 

1. Meld u aan bij de [Azure-portal,](https://portal.azure.com)met een account dat toegang heeft tot het maken van VM's en selecteer **+ Een resource maken.**
1. Typ **Windows Server** in Zoeken op de zoekbalk van Marketplace.
   1. Klik op **Windows Server** en kies Windows **Server 2019 Datacenter** in Een vervolgkeuzelijst voor een softwareplan selecteren.
   1. Klik op **Maken**.
1. Schakel op het tabblad 'Beheer' de optie inschakelen om in te **loggen met AAD-referenties (Voorbeeld)** onder de sectie Azure Active Directory van Uit naar **Aan**.
1. Controleer of **De door het systeem toegewezen beheerde identiteit** onder de sectie Identiteit is ingesteld op **Aan**. Deze actie moet automatisch plaatsvinden zodra u Aanmelding met Azure AD-referenties inschakelt.
1. Ga door de rest van de ervaring van het creëren van een virtuele machine. Tijdens deze preview moet u een gebruikersnaam en wachtwoord voor de vm maken.

![Als u aanmeldingmaakt met Azure AD-referenties, wordt een VM gemaakt](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Als u zich wilt aanmelden bij de VM met uw Azure AD-referentie, moet u eerst roltoewijzingen voor de VM configureren, zoals beschreven in een van de onderstaande secties.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>De Azure Cloud Shell-ervaring gebruiken om Azure AD-aanmelding in te schakelen

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. Er zijn vooraf algemene Azure-hulpprogramma's geïnstalleerd en geconfigureerd in Cloud Shell die u kunt gebruiken bij uw account. Selecteer de knop Kopiëren om de code te kopiëren, plak deze in Cloud Shell en druk op Enter om de code uit te voeren. U kunt Cloud Shell op verschillende manieren openen:

Selecteer Nu proberen in de rechterbovenhoek van een codeblok.
Open Cloud Shell in uw browser.
Klik op de knop Cloud Shell in het menu in de hoek rechtsboven in de [Azure Portal](https://portal.azure.com).

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, moet u in dit artikel de Azure CLI-versie 2.0.31 of hoger uitvoeren. Voer az --version uit om de versie te zoeken. Als u moet installeren of upgraden, [raadpleegt](/cli/azure/install-azure-cli)u het artikel Azure CLI installeren .

1. Maak een resourcegroep maken met [az group create](/cli/azure/group#az-group-create). 
1. Maak een VM met [az vm maken](/cli/azure/vm#az-vm-create) met behulp van een ondersteunde distributie in een ondersteunde regio. 
1. Installeer de VM-extensie azure AD-aanmelding. 

In het volgende voorbeeld wordt een VM met de naam myVM geïmplementeerd die Win2019Datacenter gebruikt, in een resourcegroep met de naam myResourceGroup, in de regio southcentralus. In de volgende voorbeelden u indien nodig uw eigen resourcegroep en VM-namen opgeven.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Het is vereist dat u systeemtoegewezen beheerde identiteit inschakelt op uw virtuele machine voordat u de AZURE AD-inlogVM-extensie installeert.

Het maken van de virtuele machine en de ondersteunende resources duurt enkele minuten.

Installeer ten slotte de VM-extensie Azure AD-aanmelding om Azure AD-aanmelding voor Windows VM in te schakelen. VM-extensies zijn kleine toepassingen die configuratie- en automatiseringstaken na implementatie bieden op virtuele Azure-machines. Gebruik [de AZ VM-extensieset](/cli/azure/vm/extension#az-vm-extension-set) om de AADLoginForWindows-extensie te installeren op de VM met de naam myVM in de brongroep myResourceGroup:

> [!NOTE]
> U AADLoginForWindows-extensie installeren op een bestaande Windows Server 2019 of Windows 10 1809 en later VM om deze in te schakelen voor Azure AD-verificatie. Een voorbeeld van AZ CLI is hieronder te zien.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Het `provisioningState` `Succeeded` van wordt weergegeven, zodra de extensie is geïnstalleerd op de VM.

## <a name="configure-role-assignments-for-the-vm"></a>Roltoewijzingen configureren voor de VM

Nu u de VM hebt gemaakt, moet u azure RBAC-beleid configureren om te bepalen wie zich kan aanmelden bij de VM. Twee RBAC-rollen worden gebruikt om VM-aanmelding te autoriseren:

- **Inloggen voor virtuele machinebeheerder**: Gebruikers met deze toegewezen rol kunnen zich aanmelden bij een virtuele Azure-machine met beheerdersbevoegdheden.
- **Virtual Machine User Login**: Gebruikers met deze toegewezen rol kunnen zich aanmelden bij een virtuele Azure-machine met regelmatige gebruikersrechten.

> [!NOTE]
> Als u wilt toestaan dat een gebruiker zich via RDP bij de VM aanmeldt, moet u de rol Voor het inloggen van de virtuele machinebeheerder of de rol Van de gebruiker van de virtuele machine toewijzen. Een Azure-gebruiker met de eigenaar- of inzenderrollen die zijn toegewezen voor een vm, heeft niet automatisch bevoegdheden om zich aan te melden bij de VM via RDP. Dit is om gecontroleerde scheiding tussen de set van mensen die virtuele machines ten opzichte van de set van mensen die toegang hebben tot virtuele machines.

Er zijn meerdere manieren waarop u roltoewijzingen voor VM configureren:

- De Azure AD-portal-ervaring gebruiken
- De Azure Cloud Shell-ervaring gebruiken

### <a name="using-azure-ad-portal-experience"></a>Azure AD-portal-ervaring gebruiken

Roltoewijzingen configureren voor uw Azure AD-vm's met Windows Server 2019-datacenter:

1. Naar de specifieke overzichtspagina voor virtuele machines navigeren
1. **Selecteer Toegangsbeheer (IAM)** in de menuopties
1. Selecteer **Toevoegen**, **Roltoewijzing toevoegen** om het deelvenster Roltoewijzing toevoegen te openen.
1. Selecteer **in** de vervolgkeuzelijst Rol een rol zoals **Inloggen van virtuele machinebeheerder** of **inloggen met virtuele machinegebruiker**.
1. Selecteer **in** het veld Selecteren een gebruiker, groep, serviceprincipal of beheerde identiteit. Als u de beveiligings-principal niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om te zoeken naar weergavenamen, e-mailadressen en object-id's.
1. Selecteer **Opslaan**om de rol toe te wijzen.

Na enkele ogenblikken krijgt de beveiligingsprincipal de toegewezen rol op het geselecteerde bereik.

![Rollen toewijzen aan gebruikers die toegang krijgen tot de VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>De Azure Cloud Shell-ervaring gebruiken

In het volgende voorbeeld wordt [az-toewijzingstoewijzing gebruikt](/cli/azure/role/assignment#az-role-assignment-create) om de inlogrol voor virtuele machinebeheerder toe te wijzen aan de VM voor uw huidige Azure-gebruiker. De gebruikersnaam van uw actieve Azure-account wordt verkregen met [de AZ-account show](/cli/azure/account#az-account-show)en het bereik is ingesteld op de VM die is gemaakt in een eerdere stap met [de AZ VM-show.](/cli/azure/vm#az-vm-show) Het bereik kan ook worden toegewezen op resourcegroep- of abonnementsniveau en normale RBAC-overervingsmachtigingen zijn van toepassing. Zie [Op rollen gebaseerde toegangsbesturingselementen](../../virtual-machines/linux/login-using-aad.md)voor meer informatie .

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Als uw AAD-domein en aanmeldingsgebruikersnaamdomein niet overeenkomen, moet u `--assignee-object-id`de object-id `--assignee`van uw gebruikersaccount opgeven met de , niet alleen de gebruikersnaam voor . U de object-ID voor uw gebruikersaccount verkrijgen met [de az-advertentiegebruikerslijst.](/cli/azure/ad/user#az-ad-user-list)

Zie de volgende artikelen voor meer informatie over het gebruik van RBAC om toegang tot uw Azure-abonnementsbronnen te beheren:

- [Toegang tot Azure-bronnen beheren met RBAC en Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [Toegang tot Azure-resources beheren met op rollen gebaseerd toegangsbeheer en de Azure-portal](/azure/role-based-access-control/role-assignments-portal)
- [Beheer de toegang tot Azure-bronnen met RBAC en Azure PowerShell.](/azure/role-based-access-control/role-assignments-powershell)

## <a name="using-conditional-access"></a>Voorwaardelijke toegang gebruiken

U beleid voor voorwaardelijke toegang afdwingen, zoals meervoudige verificatie of aanmeldingsrisicocontrole door gebruikers voordat u toegang tot Windows VM's in Azure machtigt die zijn ingeschakeld met Azure AD-aanmelding. Als u het beleid voor voorwaardelijke toegang wilt toepassen, moet u de 'Azure Windows VM Sign-In'-app selecteren vanuit de optie voor toewijzing van cloud-apps of acties en vervolgens aanmeldingsrisico als voorwaarde gebruiken en/of multi-factor authenticatie vereisen als subsidietoegangscontrole. 

> [!NOTE]
> Als u 'Multi-factor authentication' gebruiken als een subsidietoegangscontrole voor het aanvragen van toegang tot de app 'Azure Windows VM Sign-In', moet u multifactorauthenticatieclaim leveren als onderdeel van de client die de RDP-sessie initieert naar de beoogde Windows VM in Azure. De enige manier om dit te bereiken op een Windows 10-client is het gebruik van Windows Hello for Business PIN of biometrische authenication met de RDP-client. Ondersteuning voor biometrische authenticatie is toegevoegd aan de RDP-client in Windows 10 versie 1809. Extern bureaublad met Windows Hello voor Bedrijven-verificatie is alleen beschikbaar voor implementaties die gebruikmaken van het vertrouwensmodel van Cert en momenteel niet beschikbaar zijn voor het vertrouwensmodel voor belangrijke instellingen.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Inloggen met Azure AD-referenties bij een Windows-vm

> [!IMPORTANT]
> Verbinding op afstand met VM's die zijn aangesloten op Azure AD is alleen toegestaan vanaf Windows 10-pc's waarvan Azure AD is samengevoegd of hybride Azure AD is samengevoegd tot **dezelfde** map als de VM. Bovendien moet de gebruiker voor RDP met Azure AD-referenties deel uitmaken van een van de twee RBAC-rollen, Virtual Machine Administrator Login of Virtual Machine User Login. Op dit moment kan Azure Bastion niet worden gebruikt om in te loggen met Azure Active Directory-verificatie met de AADLoginForWindows-extensie. Alleen directe RDP wordt ondersteund.

Inloggen op uw virtuele windows server 2019 met Azure AD: 

1. Navigeer naar de overzichtspagina van de virtuele machine die is ingeschakeld met Azure AD-aanmelding.
1. Selecteer **Verbinding maken** om het connect to virtual machine blade te openen.
1. Selecteer **RDP-bestand downloaden**.
1. Selecteer **Openen** om de client Extern bureaublad-verbinding te starten.
1. Selecteer **Verbinding maken** om het aanmeldingsdialoogvenster van Windows te starten.
1. Aanmelding met uw Azure AD-referenties.

U bent nu aangemeld bij de virtuele windows server 2019 Azure-machine met de toegewezen rolmachtigingen, zoals VM-gebruiker of VM-beheerder. 

> [!NOTE]
> U de. RDP-bestand lokaal op uw computer om toekomstige externe bureaubladverbindingen met uw virtuele machine te starten in plaats van te hoeven navigeren naar de overzichtspagina van virtuele machines in de Azure-portal en de verbindingsoptie te gebruiken.

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="troubleshoot-deployment-issues"></a>Oplossen van implementatieproblemen

De AADLoginForWindows-extensie moet met succes worden geïnstalleerd om ervoor te zorgen dat de VM het Azure AD-joinproces kan voltooien. Voer de volgende stappen uit als de VM-extensie niet correct is geïnstalleerd.

1. RDP naar de VM met behulp van het lokale beheerdersaccount en bekijk het CommandExecuti'n.log onder  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Als de extensie opnieuw wordt opgestart na de eerste fout, wordt het logboek met de implementatiefout opgeslagen als CommandExecution_YYYYMMDDHHMMSSSSS.log. "
1. Open een opdrachtprompt op de VM en verifieer deze query's op het IMDS-eindpunt (Instance Metadata Service) dat wordt uitgevoerd op de Azure-hostretouren:

   | Opdracht om uit te voeren | Verwachte uitvoer |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Juiste informatie over de Azure VM |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Geldige tenant-id die is gekoppeld aan het Azure-abonnement |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Geldig toegangstoken uitgegeven door Azure Active Directory voor de beheerde identiteit die aan deze VM is toegewezen |

   > [!NOTE]
   > Het toegangstoken kan worden gedecodeerd met behulp van een tool zoals [http://calebb.net/](http://calebb.net/). Controleer of de 'appid' in het toegangstoken overeenkomt met de beheerde identiteit die aan de VM is toegewezen.

1. Controleer of de vereiste eindpunten toegankelijk zijn vanaf de VM met behulp van de opdrachtregel:
   
   - krul https:\//login.microsoftonline.com/ -D –
   - krul https:\/`<TenantID>`/login.microsoftonline.com/ / -D –

   > [!NOTE]
   > Vervang `<TenantID>` de Azure AD-tenant-id die is gekoppeld aan het Azure-abonnement.

   - krul https:\//enterpriseregistration.windows.net/ -D -
   - krul https:\//device.login.microsoftonline.com/ -D -
   - krul https:\//pas.windows.net/ -D -

1. De apparaatstatus kan worden `dsregcmd /status`bekeken door het uitvoeren van . Het doel is dat apparaatstatus wordt weergegeven als `AzureAdJoined : YES`.

   > [!NOTE]
   > Azure AD-deelnameactiviteit wordt vastgelegd in logboekviewer onder het logboek gebruikersapparaatregistratie\Beheerlogboek.

Als aadloginvoorWindows-extensie mislukt met bepaalde foutcode, u de volgende stappen uitvoeren:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Probleem 1: AADLoginForWindows-extensie wordt niet geïnstalleerd met terminalfoutcode '1007' en Exit-code: -2145648574.

Deze afsluitcode vertaalt zich naar DSREG_E_MSI_TENANTID_UNAVAILABLE omdat de extensie de Azure AD-tenantgegevens niet kan opvragen.

1. Controleer of de Azure VM de TenantID kan ophalen uit de Instantie Metadata Service.

   - RDP naar de VM als lokale beheerder en controleer het eindpunt als geldige tenant-id door deze opdracht uit te voeren vanaf een opdrachtregel met verhoogde verhoogde functie op de VM:
      
      - curl -H Metadata:truehttp://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. De VM-beheerder probeert de AADLoginForWindows-extensie te installeren, maar een beheerde identiteit met systeemtoegewezen systeem heeft de VM niet als eerste ingeschakeld. Navigeer naar het identiteitsblad van de vm. Controleer op het tabblad Systeem toegewezen of de status is ingeschakeld voor Aan.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Probleem 2: AADLoginForWindows-extensie wordt niet geïnstalleerd met exitcode: -2145648607

Deze exitcode vertaalt naar DSREG_AUTOJOIN_DISC_FAILED omdat de extensie `https://enterpriseregistration.windows.net` het eindpunt niet kan bereiken.

1. Controleer of de vereiste eindpunten toegankelijk zijn vanaf de VM met behulp van de opdrachtregel:

   - krul https:\//login.microsoftonline.com/ -D –
   - krul https:\/`<TenantID>`/login.microsoftonline.com/ / -D –
   
   > [!NOTE]
   > Vervang `<TenantID>` de Azure AD-tenant-id die is gekoppeld aan het Azure-abonnement. Als u de tenant-id wilt vinden, u de plaats boven uw accountnaam zetten om de adreslijst/ tenant-id op te halen of Azure Active Directory > Eigenschappen > Directory-id in de Azure-portal selecteren.

   - krul https:\//enterpriseregistration.windows.net/ -D -
   - krul https:\//device.login.microsoftonline.com/ -D -
   - krul https:\//pas.windows.net/ -D -

1. Als een van de opdrachten mislukt met `<URL>`'Kon host niet oplossen', probeer dan deze opdracht uit te voeren om de DNS-server te bepalen die door de VM wordt gebruikt.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Vervang `<URL>` de volledig gekwalificeerde domeinnamen die door de eindpunten worden gebruikt, zoals 'login.microsoftonline.com'.

1. Bekijk vervolgens of het opgeven van een openbare DNS-server de opdracht mogelijk maakt:

   `nslookup <URL> 208.67.222.222`

1. Wijzig indien nodig de DNS-server die is toegewezen aan de netwerkbeveiligingsgroep waartoe de Azure VM behoort.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Probleem 3: AADLoginForWindows-extensie wordt niet geïnstalleerd met Exit-code: 51

Exit code 51 vertaalt naar "Deze extensie wordt niet ondersteund op het besturingssysteem van de VM".

Bij Public Preview is de AADLoginForWindows-extensie alleen bedoeld om te worden geïnstalleerd op Windows Server 2019 of Windows 10 (Build 1809 of hoger). Zorg ervoor dat de versie van Windows wordt ondersteund. Als de build van Windows niet wordt ondersteund, verwijdert u de VM-extensie.

### <a name="troubleshoot-sign-in-issues"></a>Aanmeldingsproblemen oplossen

Enkele veelvoorkomende fouten wanneer u rdp probeert te gebruiken met Azure AD-referenties, bevatten geen RBAC-rollen die zijn toegewezen, ongeautoriseerde client of 2FA-aanmeldingsmethode vereist. Gebruik de volgende informatie om deze problemen te verhelpen.

De status Apparaat en SSO `dsregcmd /status`kan worden bekeken door het uitvoeren van . Het doel is dat apparaatstatus `SSO State` wordt `AzureAdPrt : YES`weergegeven als `AzureAdJoined : YES` en weer te geven.

Rdp-aanmelding met Azure AD-accounts wordt ook vastgelegd in gebeurtenisviewer onder de AAD\Operationele gebeurtenislogboeken.

#### <a name="rbac-role-not-assigned"></a>RBAC-rol niet toegewezen

Als u het volgende foutbericht ziet wanneer u een verbinding met een extern bureaublad met uw vm start: 

- Uw account is geconfigureerd om te voorkomen dat u dit apparaat gebruikt. Neem voor meer informatie contact op met uw systeembeheerder

![Uw account is geconfigureerd om te voorkomen dat u dit apparaat gebruikt.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Controleer of u [RBAC-beleidsregels](../../virtual-machines/linux/login-using-aad.md) hebt geconfigureerd voor de VM waarmee de gebruiker de rol Aanmelding van virtuele machinebeheerder of bijmeldingsfunctie voor virtuele machines wordt toegekend:
 
#### <a name="unauthorized-client"></a>Onbevoegde client

Als u het volgende foutbericht ziet wanneer u een verbinding met een extern bureaublad met uw vm start: 

- Uw referenties werkte niet

![Uw referenties werkte niet](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Controleer of de Windows 10-pc die u gebruikt om de verbinding met extern bureaublad te starten, is verbonden met Azure AD of dat hybride Azure AD is samengevoegd met dezelfde Azure AD-map waaruw VM is verbonden. Zie het artikel Wat is [een apparaatidentiteit](/azure/active-directory/devices/overview)voor meer informatie over de identiteit van het apparaat.

> [!NOTE]
> Windows 10 20H1 voegt ondersteuning toe voor Azure AD Registered PC om een externe desktopverbinding met uw VM te starten. Word lid van het Windows Insider-programma om dit uit te proberen en nieuwe functies van Windows 10 te verkennen.

Controleer ook of de AADLoginForWindows-extensie niet is verwijderd nadat Azure AD-join is voltooid.
 
#### <a name="mfa-sign-in-method-required"></a>MFA-aanmeldingsmethode vereist

Als u het volgende foutbericht ziet wanneer u een verbinding met een extern bureaublad met uw vm start: 

- De aanmeldingsmethode die u probeert te gebruiken, is niet toegestaan. Probeer een andere aanmeldingsmethode of neem contact op met uw systeembeheerder.

![De aanmeldingsmethode die u probeert te gebruiken, is niet toegestaan.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Als u een beleid voor voorwaardelijke toegang hebt geconfigureerd waarvoor multi-factor authenticatie (MFA) vereist is voordat u toegang hebt tot de bron, moet u ervoor zorgen dat de Windows 10-pc de verbinding met uw vm-koppeling initieert met een sterke verificatiemethode zoals Windows Hello. Als u geen sterke verificatiemethode gebruikt voor uw verbinding met extern bureaublad, ziet u de vorige fout.

Als u Windows Hello voor Bedrijven niet hebt geïmplementeerd en als dat voorlopig geen optie is, u mfa-vereisten uitsluiten door het beleid voor voorwaardelijke toegang te configureren dat de app 'Azure Windows VM Sign-In' uitsluit van de lijst met cloud-apps waarvoor MFA vereist is. Zie Overzicht van Windows Hello voor Bedrijven voor meer informatie over Windows Hello voor [Bedrijven.](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

> [!NOTE]
> Windows Hello for Business PIN-verificatie met RDP is ondersteund door Windows 10 voor verschillende versies, maar ondersteuning voor biometrische authenticatie met RDP is toegevoegd in Windows 10 versie 1809. Het gebruik van Windows Hello for Business auth tijdens RDP is alleen beschikbaar voor implementaties die gebruik maken van het cert-vertrouwensmodel en momenteel niet beschikbaar zijn voor het sleutelvertrouwensmodel.
 
## <a name="preview-feedback"></a>Feedback voor de preview-versie

Deel uw feedback over deze voorbeeldfunctie of meld problemen met deze functie op het [Azure AD-feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) voor meer informatie over Azure Active Directory

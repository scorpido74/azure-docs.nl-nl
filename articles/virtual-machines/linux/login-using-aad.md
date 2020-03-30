---
title: Inloggen bij een Linux-vm met Azure Active Directory-referenties
description: Meer informatie over het maken en configureren van een Linux-vm om u aan te melden met Azure Active Directory-verificatie.
author: iainfoulds
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: iainfou
ms.openlocfilehash: 2731693667d2129a72da72455c6bbdd74c277697
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366483"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Voorbeeld: Inloggen bij een virtuele Linux-machine in Azure met Azure Active Directory-verificatie

Als u de beveiliging van Virtuele Linux-machines (VM's) in Azure wilt verbeteren, u integreren met Azure Active Directory (AD)-verificatie. Wanneer u Azure AD-verificatie voor Linux-VM's gebruikt, beheert en handhaaft u beleid dat toegang tot de VM's toestaat of weigert. In dit artikel ziet u hoe u een Linux-vm maakt en configureert om Azure AD-verificatie te gebruiken.


> [!IMPORTANT]
> Azure Active Directory-verificatie bevindt zich momenteel in een openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
> Gebruik deze functie op een virtuele testmachine die u na het testen verwacht te verwijderen.
>


Er zijn veel voordelen van het gebruik van Azure AD-verificatie om in te loggen op Linux-VM's in Azure, waaronder:

- **Verbeterde beveiliging:**
  - U uw bedrijfsAD-referenties gebruiken om u aan te melden bij Azure Linux VM's. Het is niet nodig om lokale beheerdersaccounts te maken en de levensduur van de referenties te beheren.
  - Door het verminderen van uw afhankelijkheid van lokale beheerdersaccounts, hoeft u zich geen zorgen te maken over verlies van referenties/ diefstal, gebruikers die zwakke referenties configureren enz.
  - De wachtwoordcomplexiteit en het wachtwoordlevenslangbeleid dat is geconfigureerd voor uw Azure AD-map, helpen ook Linux VM's te beveiligen.
  - Als u de aanmelding bij virtuele Azure-machines verder wilt beveiligen, u meervoudige verificatie configureren.
  - De mogelijkheid om in te loggen op Linux VM's met Azure Active Directory werkt ook voor klanten die [Federation Services](../../active-directory/hybrid/how-to-connect-fed-whatis.md)gebruiken.

- **Naadloze samenwerking:** Met RBAC (Role-Based Access Control) u opgeven wie zich als gewone gebruiker of met beheerdersrechten kan aanmelden bij een bepaalde VM. Wanneer gebruikers lid worden van of uw team verlaten, u het RBAC-beleid bijwerken dat de VM toegang verleent. Deze ervaring is veel eenvoudiger dan vm's te moeten schrobben om onnodige SSH-openbare sleutels te verwijderen. Wanneer werknemers uw organisatie verlaten en hun gebruikersaccount is uitgeschakeld of verwijderd uit Azure AD, hebben ze geen toegang meer tot uw bronnen.

## <a name="supported-azure-regions-and-linux-distributions"></a>Ondersteunde Azure-regio's en Linux-distributies

De volgende Linux-distributies worden momenteel ondersteund tijdens de preview van deze functie:

| Distributie | Versie |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| Red Hat Enterprise Linux | RHEL 6. | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 en Ubuntu Server 18.04 |


De volgende Azure-regio's worden momenteel ondersteund tijdens de preview van deze functie:

- Alle wereldwijde Azure-regio's

>[!IMPORTANT]
> Als u deze voorbeeldfunctie wilt gebruiken, implementeert u alleen een ondersteunde Linux-distro en in een ondersteunde Azure-regio. De functie wordt niet ondersteund in Azure Government of sovereign clouds.
>
> Deze extensie wordt niet ondersteund voor het gebruik van deze extensie op AKS-clusters (Azure Kubernetes Service). Zie [Ondersteuningsbeleid voor AKS voor](../../aks/support-policies.md)meer informatie .


Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, vereist deze zelfstudie dat u de Azure CLI-versie 2.0.31 of hoger uitvoert. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Netwerkvereisten

Als u Azure AD-verificatie voor uw Linux VM's in Azure wilt inschakelen, moet u ervoor zorgen dat uw VM's-netwerkconfiguratie uitgaande toegang tot de volgende eindpunten via TCP-poort 443 mogelijk maken:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https:\//device.login.microsoftonline.com
* https:\//pas.windows.net
* https:\//management.azure.com
* https:\//packages.microsoft.com

> [!NOTE]
> Op dit moment kunnen Azure-netwerkbeveiligingsgroepen niet worden geconfigureerd voor VM's die zijn ingeschakeld met Azure AD-verificatie.

## <a name="create-a-linux-virtual-machine"></a>Een virtuele Linux-machine maken

Maak een resourcegroep met [de az-groep maken,](/cli/azure/group#az-group-create)maak vervolgens een VM met [az vm maken](/cli/azure/vm#az-vm-create) met behulp van een ondersteunde distro en in een ondersteunde regio. In het volgende voorbeeld wordt een VM met de naam *myVM* geïmplementeerd die *Ubuntu 16.04 LTS* gebruikt in een resourcegroep met de naam *myResourceGroup* in de *regio southcentralus.* In de volgende voorbeelden u indien nodig uw eigen resourcegroep en VM-namen opgeven.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Het maken van de virtuele machine en de ondersteunende resources duurt enkele minuten.

## <a name="install-the-azure-ad-login-vm-extension"></a>De VM-extensie Azure AD-aanmelding installeren

> [!NOTE]
> Als het implementeren van deze extensie naar een eerder gemaakte VM ervoor zorgt dat de machine ten minste 1 GB geheugen heeft toegewezen, anders wordt de extensie niet geïnstalleerd

Als u zich wilt aanmelden bij een Linux-VM met Azure AD-referenties, installeert u de VM-extensie Azure Active Directory.To log in to be in to an Linux VM with Azure AD credentials, install the Azure Active Directory login VM extension. VM-extensies zijn kleine toepassingen die configuratie- en automatiseringstaken na implementatie bieden op virtuele Azure-machines. Gebruik [de AZ VM-extensieset](/cli/azure/vm/extension#az-vm-extension-set) om de *AADLoginForLinux-extensie* te installeren op de VM met de naam *myVM* in de *brongroep myResourceGroup:*

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

De *provisioningState* *of Succeeded wordt* weergegeven zodra de extensie met succes is geïnstalleerd op de VM. De VM heeft een draaiende VM-agent nodig om de extensie te installeren. Zie [VM-agentoverzicht](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)voor meer informatie.

## <a name="configure-role-assignments-for-the-vm"></a>Roltoewijzingen configureren voor de VM

Het RBAC-beleid (Azure Role-Based Access Control) bepaalt wie zich kan aanmelden bij de VM. Twee RBAC-rollen worden gebruikt om VM-aanmelding te autoriseren:

- **Inloggen voor virtuele machinebeheerder**: Gebruikers met deze toegewezen rol kunnen zich aanmelden bij een virtuele Azure-machine met Windows Administrator- of Linux-rootgebruikersrechten.
- **Virtual Machine User Login**: Gebruikers met deze toegewezen rol kunnen zich aanmelden bij een virtuele Azure-machine met regelmatige gebruikersrechten.

> [!NOTE]
> Als u wilt toestaan dat een gebruiker zich via SSH aanmeldt bij de VM, moet u de *rol Voor het inloggen van de virtuele machinebeheerder* of de rol Van de gebruiker van de virtuele machine *toewijzen.* Een Azure-gebruiker met de *eigenaar-* of *inzenderrollen die* zijn toegewezen voor een vm, heeft niet automatisch bevoegdheden om zich aan te melden bij de VM via SSH.

In het volgende voorbeeld wordt [az-toewijzingstoewijzing gebruikt](/cli/azure/role/assignment#az-role-assignment-create) om de *inlogrol voor virtuele machinebeheerder* toe te wijzen aan de VM voor uw huidige Azure-gebruiker. De gebruikersnaam van uw actieve Azure-account wordt verkregen met [de AZ-account show](/cli/azure/account#az-account-show)en het *bereik* is ingesteld op de VM die is gemaakt in een eerdere stap met de [AZ VM-show.](/cli/azure/vm#az-vm-show) Het bereik kan ook worden toegewezen op resourcegroep- of abonnementsniveau en normale RBAC-overervingsmachtigingen zijn van toepassing. Zie [Toegangsbesturingselementen op basis van rollen](../../role-based-access-control/overview.md) voor meer informatie

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Als uw AAD-domein en aanmeldingsgebruikersnaam domein niet overeenkomen, moet u de object-ID van uw gebruikersaccount opgeven met de *--assignee-object-id,* niet alleen de gebruikersnaam voor *--assignee.* U de object-ID voor uw gebruikersaccount verkrijgen met [de az-advertentiegebruikerslijst.](/cli/azure/ad/user#az-ad-user-list)

Zie voor meer informatie over het gebruik van RBAC voor het beheren van toegang tot uw Azure-abonnementsbronnen de [Azure CLI,](../../role-based-access-control/role-assignments-cli.md) [Azure-portal](../../role-based-access-control/role-assignments-portal.md)of [Azure PowerShell.](../../role-based-access-control/role-assignments-powershell.md)

U Azure AD ook configureren om multifactorauthenticatie voor een specifieke gebruiker te vereisen om zich aan te melden bij de virtuele Linux-machine. Zie [Aan de slag met Azure Multi-Factor Authentication in de cloud](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)voor meer informatie.

## <a name="log-in-to-the-linux-virtual-machine"></a>Log in op de Virtuele Linux machine

Bekijk eerst het openbare IP-adres van uw VM met [az vm show:](/cli/azure/vm#az-vm-show)

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Meld u aan bij de virtuele Azure Linux-machine met uw Azure AD-referenties. Met `-l` de parameter u uw eigen Azure AD-accountadres opgeven. Vervang het voorbeeldaccount door het eigen account. Accountadressen moeten in alle kleine letters worden ingevoerd. Vervang het voorbeeld-IP-adres door het openbare IP-adres van uw vm uit de vorige opdracht.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

U wordt gevraagd zich aan te melden bij Azure [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)AD met een eenmalige gebruikscode op . Kopieer en plak de eenmalige gebruikscode naar de inlogpagina van het apparaat.

Voer desgevraagd uw Azure AD-inloggegevens in op de aanmeldingspagina. 

Het volgende bericht wordt weergegeven in de webbrowser wanneer u zich hebt geverifieerd:`You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Sluit het browservenster, ga terug naar de SSH-prompt en druk op **enter.** 

U bent nu aangemeld bij de virtuele Azure Linux-machine met de toegewezen rolmachtigingen, zoals *VM-gebruiker* of *VM-beheerder.* Als aan uw gebruikersaccount de *rol Voor het inloggen van de beheerder van virtuele machines* is toegewezen, kunt `sudo` u opdrachten uitvoeren waarvoor hoofdbevoegdheden nodig zijn.

## <a name="sudo-and-aad-login"></a>Sudo en AAD inloggen

De eerste keer dat u sudo uitvoert, wordt u gevraagd om een tweede keer te verifiëren. Als u niet opnieuw wilt verifiëren om sudo uit te voeren, `/etc/sudoers.d/aad_admins` u uw sudoersbestand bewerken en deze regel vervangen:

```bash
%aad_admins ALL=(ALL) ALL
```

Met deze regel:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Aanmeldingsproblemen oplossen

Enkele veelvoorkomende fouten wanneer u ssh probeert te gebruiken met Azure AD-referenties, bevatten geen Toegewezen RBAC-rollen en herhaalde aanwijzingen om u aan te melden. Gebruik de volgende secties om deze problemen te verhelpen.

### <a name="access-denied-rbac-role-not-assigned"></a>Toegang geweigerd: RBAC-rol niet toegewezen

Als u de volgende fout op uw SSH-prompt ziet, controleert u of u RBAC-beleidsregels hebt geconfigureerd voor de VM waarmee de gebruiker de *rol Aanmelding van virtuele machinebeheerder* of *bijmelding van virtuele machinegebruikers toekent:*

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Vervolg SSH aanmeldingsprompts

Als u de verificatiestap in een webbrowser met succes voltooit, wordt u mogelijk onmiddellijk gevraagd om opnieuw in te loggen met een nieuwe code. Deze fout wordt meestal veroorzaakt door een mismatch tussen de aanmeldingsnaam die u hebt opgegeven bij de SSH-prompt en het account waarmee u zich hebt aangemeld bij Azure AD. Ga als volgt te werk om dit probleem te verhelpen:

- Controleer of de aanmeldingsnaam die u bij de SSH-prompt hebt opgegeven, juist is. Een typfout in de aanmeldingsnaam kan leiden tot een mismatch tussen de aanmeldingsnaam die u bij de SSH-prompt hebt opgegeven en het account waarmee u zich hebt aangemeld bij Azure AD. U hebt bijvoorbeeld *azuresuer\@contoso.onmicrosoft.com* getypt in plaats van *azureuser-contoso.onmicrosoft.com\@*.
- Als u meerdere gebruikersaccounts hebt, moet u ervoor zorgen dat u geen ander gebruikersaccount in het browservenster opgeeft wanneer u zich aanmeldt bij Azure AD.
- Linux is een case-gevoelig besturingssysteem. Er is eenAzureuser@contoso.onmicrosoft.comverschil tussenazureuser@contoso.onmicrosoft.com' en ' ', wat een mismatch kan veroorzaken. Zorg ervoor dat u de UPN opgeeft met de juiste hoofdlettergevoeligheid bij de SSH-prompt.

### <a name="other-limitations"></a>Andere beperkingen

Gebruikers die toegangsrechten overnemen via geneste groepen of roltoewijzingen, worden momenteel niet ondersteund. De gebruiker of groep moet direct de [vereiste roltoewijzingen](#configure-role-assignments-for-the-vm)toegewezen krijgen. Het gebruik van beheergroepen of geneste groepsroltoewijzingen geeft bijvoorbeeld niet de juiste machtigingen om de gebruiker toe te staan zich aan te melden.

## <a name="preview-feedback"></a>Feedback voor de preview-versie

Uw feedback over deze voorbeeldfunctie delen of problemen melden op het [Feedbackforum van Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) voor meer informatie over Azure Active Directory

---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d1ec61bf18248ea56c8ee5e430a671af7f39d732
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458660"
---
Het is belangrijk om uw virtuele machine (VM) veilig te houden voor de toepassingen die u uitvoert. Het beveiligen van uw VM's kan een of meer Azure-services en -functies bevatten die betrekking hebben op veilige toegang tot uw VM's en veilige opslag van uw gegevens. Dit artikel bevat informatie waarmee u uw VM en toepassingen veilig houden.

## <a name="antimalware"></a>Antimalware

Het moderne dreigingslandschap voor cloudomgevingen is dynamisch en verhoogt de druk om effectieve bescherming te behouden om te voldoen aan de nalevings- en beveiligingsvereisten. [Microsoft Antimalware voor Azure](../articles/security/fundamentals/antimalware.md) is een gratis real-time beveiligingsmogelijkheid die helpt bij het identificeren en verwijderen van virussen, spyware en andere schadelijke software. Waarschuwingen kunnen worden geconfigureerd om u op de hoogte te stellen wanneer bekende schadelijke of ongewenste software zichzelf probeert te installeren of op uw vm probeert uit te voeren. Het wordt niet ondersteund op VM's met Linux of Windows Server 2008.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md) helpt u bedreigingen voor uw VM's te voorkomen, detecteren en erop te reageren. Security Center biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden kunnen blijven en werkt met een breed ecosysteem van beveiligingsoplossingen.

De just-in-time-toegang van het Security Center kan worden toegepast op uw VM-implementatie om binnenkomend verkeer naar uw Azure VM's te vergrendelen, waardoor de blootstelling aan aanvallen wordt verminderd en u gemakkelijk toegang hebt tot vm's wanneer dat nodig is. Wanneer just-in-time is ingeschakeld en een gebruiker toegang tot een VM vraagt, controleert beveiligingscentrum welke machtigingen de gebruiker heeft voor de VM. Als ze over de juiste machtigingen beschikken, wordt de aanvraag goedgekeurd en configureert het Beveiligingscentrum automatisch de Network Security Groups (NSG's) om binnenkomend verkeer naar de geselecteerde poorten voor een beperkte tijd toe te staan. Nadat de tijd is verstreken, herstelt Security Center de NSG's naar hun vorige staten. 

## <a name="encryption"></a>Versleuteling

Er worden twee versleutelingsmethoden aangeboden voor beheerde schijven. Versleuteling op OS-niveau, azure-schijfversleuteling, en versleuteling op platformniveau, dat is server-side encryptie.

### <a name="server-side-encryption"></a>Versleuteling aan de serverzijde

Azure-beheerde schijven versleutelen uw gegevens standaard automatisch wanneer deze in de cloud blijven staan. Server-side encryptie beschermt uw gegevens en helpt u om te voldoen aan uw organisatorische beveiligings- en nalevingsverplichtingen. Gegevens in azure beheerde schijven worden transparant versleuteld met behulp van 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de sterkste blokcijfers die beschikbaar zijn, en is FIPS 140-2-compatibel.

Versleuteling heeft geen invloed op de prestaties van beheerde schijven. Er zijn geen extra kosten voor de encryptie.

U vertrouwen op door het platform beheerde sleutels voor de versleuteling van uw beheerde schijf, of u versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling met uw eigen sleutels te beheren, u een *door de klant beheerde sleutel* opgeven die u moet gebruiken voor het versleutelen en decoderen van alle gegevens in beheerde schijven. 

Voor meer informatie over server-side encryptie, verwijzen naar de artikelen voor [Windows](../articles/virtual-machines/windows/disk-encryption.md) of [Linux](../articles/virtual-machines/linux/disk-encryption.md).

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Voor verbeterde [windows VM-](../articles/virtual-machines/windows/disk-encryption-overview.md) en [Linux VM-beveiliging](../articles/virtual-machines/linux/disk-encryption-overview.md) en -naleving kunnen virtuele schijven in Azure worden versleuteld. Virtuele schijven op Windows VM's worden in rust versleuteld met BitLocker. Virtuele schijven op Linux VM's worden in rust versleuteld met behulp van dm-crypt. 

Er zijn geen kosten verbonden aan het versleutelen van virtuele schijven in Azure. Cryptografische sleutels worden opgeslagen in Azure Key Vault met behulp van softwarebeveiliging, of u uw sleutels importeren of genereren in Hardware Security Modules (HSM's) gecertificeerd volgens FIPS 140-2 niveau 2-standaarden. Deze cryptografische sleutels worden gebruikt om virtuele schijven die aan uw VM zijn gekoppeld, te versleutelen en te decoderen. U behoudt de controle over deze cryptografische sleutels en het gebruik ervan controleren. Een Azure Active Directory-serviceprincipal biedt een beveiligd mechanisme voor het uitgeven van deze cryptografische sleutels, omdat VM's zijn ingeschakeld en uitgeschakeld.

## <a name="key-vault-and-ssh-keys"></a>Key Vault- en SSH-sleutels

Geheimen en certificaten kunnen worden gemodelleerd als resources en worden geleverd door [Key Vault.](../articles/key-vault/key-vault-whatis.md) U Azure PowerShell gebruiken om belangrijke kluizen voor [Windows VM's](../articles/virtual-machines/windows/key-vault-setup.md) en de Azure CLI voor [Linux VM's](../articles/virtual-machines/linux/key-vault-setup.md)te maken. U ook sleutels voor versleuteling maken.

Het toegangsbeleid voor sleutelkluizen verleent afzonderlijk machtigingen voor sleutels, geheimen en certificaten. U kunt bijvoorbeeld een gebruiker toegang geven tot sleutels, maar geen machtigingen voor geheimen geven. Machtigingen voor toegang tot sleutels, geheimen of certificaten bevinden zich echter op het niveau van de Key Vault. Met andere woorden, [het toegangsbeleid voor sleutelkluizen](../articles/key-vault/key-vault-secure-your-key-vault.md) biedt geen ondersteuning voor machtigingen op objectniveau.

Wanneer u verbinding maakt met VM's, moet u cryptografie met openbare sleutels gebruiken om een veiligere manier te bieden om u bij hen aan te melden. Dit proces omvat een openbare en private sleuteluitwisseling met behulp van de secure shell (SSH) opdracht om jezelf te verifiëren in plaats van een gebruikersnaam en wachtwoord. Wachtwoorden zijn kwetsbaar voor brute-force aanvallen, vooral op internet-facing VM's zoals webservers. Met een secure shell (SSH) sleutelpaar u een [Linux-VM](../articles/virtual-machines/linux/mac-create-ssh-keys.md) maken die SSH-sleutels gebruikt voor verificatie, waardoor wachtwoorden niet meer hoeven aan te melden. U ook SSH-toetsen gebruiken om verbinding te maken van een [Windows-vm](../articles/virtual-machines/linux/ssh-from-windows.md) naar een Linux-vm.

## <a name="managed-identities-for-azure-resources"></a>Beheerde identiteiten voor Azure-resources

Een veelvoorkomende uitdaging bij het bouwen van cloud-apps is het beheren van de referenties in uw code voor verificatie bij cloudservices. Het is belangrijk dat de referenties veilig worden bewaard. In het ideale geval worden de referenties nooit weergegeven op werkstations van ontwikkelaars en niet ingecheckt in broncodebeheer. Azure Key Vault biedt een manier voor het veilig opslaan van referenties, geheimen en andere sleutels, maar uw code moet worden geverifieerd bij Key Vault om ze op te halen. 

Dit probleem wordt opgelost met de functie Beheerde identiteiten voor Azure-resources in Azure Active Directory (Azure AD). De functie biedt Azure-services met een automatisch beheerde identiteit in Azure AD. U kunt de identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder referenties in de code.  Uw code die op een VM wordt uitgevoerd, kan een token aanvragen bij twee eindpunten die alleen toegankelijk zijn vanuit de VM. Bekijk de [overzichtspagina beheerde identiteiten voor Azure-bronnen voor](../articles/active-directory/managed-identities-azure-resources/overview.md) meer gedetailleerde informatie over deze service.   

## <a name="policies"></a>Beleidsregels

[Azure-beleid](../articles/azure-policy/azure-policy-introduction.md) kan worden gebruikt om het gewenste gedrag voor de [Windows VM's](../articles/virtual-machines/windows/policy.md) en [Linux VM's](../articles/virtual-machines/linux/policy.md)van uw organisatie te definiëren. Door beleid te gebruiken, kan een organisatie verschillende conventies en regels in de hele onderneming afdwingen. Handhaving van het gewenste gedrag kan helpen risico's te beperken en tegelijkertijd bij te dragen aan het succes van de organisatie.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Met behulp van [op rollen gebaseerde toegangscontrole (RBAC)](../articles/role-based-access-control/overview.md)u taken binnen uw team scheiden en alleen de hoeveelheid toegang verlenen aan gebruikers op uw VM die ze nodig hebben om hun taken uit te voeren. In plaats van iedereen onbeperkte machtigingen op de VM te geven, u alleen bepaalde acties toestaan. U toegangsbeheer voor de VM configureren in de [Azure-portal,](../articles/role-based-access-control/role-assignments-portal.md)met behulp van Azure [CLI](https://docs.microsoft.com/cli/azure/role)of[Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Volgende stappen
- Loop door de stappen om de beveiliging van virtuele machines te controleren met Azure Security Center voor [Linux](../articles/security/fundamentals/overview.md) of [Windows.](../articles/virtual-machines/windows/tutorial-azure-security.md)

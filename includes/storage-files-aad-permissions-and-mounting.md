---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 23550c83e76631e44d5036e0a038f01b61a79f1b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208233"
---
## <a name="assign-access-permissions-to-an-identity"></a>Toegangs machtigingen toewijzen aan een identiteit

Om toegang te krijgen tot Azure Files bronnen met verificatie op basis van de identiteit, moet een identiteit (een gebruiker, groep of Service-Principal) de benodigde machtigingen op het share niveau hebben. Dit proces is vergelijkbaar met het opgeven van machtigingen voor Windows share, waar u het type toegang opgeeft dat een bepaalde gebruiker heeft voor een bestands share. De richt lijnen in deze sectie laten zien hoe u machtigingen voor lezen, schrijven of verwijderen voor een bestands share toewijst aan een identiteit.

We hebben drie ingebouwde rollen geïntroduceerd voor het verlenen van machtigingen op share niveau voor gebruikers:

- Met de **SMB-share lezer van het opslag bestand** wordt lees toegang tot Azure Storage bestands shares via SMB toegestaan.
- **Gegevens van het opslag bestand SMB delen Inzender** maakt lees-, schrijf-en verwijder toegang mogelijk in azure Storage bestands shares via SMB.
- **Opslag bestands gegevens SMB-share met verhoogde bevoegdheid** is voor lezen, schrijven, verwijderen en wijzigen van NTFS-machtigingen in azure Storage bestands shares via SMB toegestaan.

> [!IMPORTANT]
> Volledig beheer van een bestands share, inclusief de mogelijkheid om eigenaar te worden van een bestand, vereist het gebruik van de sleutel van het opslag account. Beheer beheer wordt niet ondersteund met Azure AD-referenties.

U kunt de Azure Portal, Power shell of Azure CLI gebruiken om de ingebouwde rollen toe te wijzen aan de Azure AD-identiteit van een gebruiker voor het verlenen van machtigingen op share niveau.

> [!NOTE]
> Vergeet niet om uw AD-referenties te synchroniseren met Azure AD als u van plan bent om uw AD te gebruiken voor verificatie. Wachtwoord-hash-synchronisatie van AD naar Azure AD is optioneel. Machtigingen op share niveau worden verleend aan de Azure AD-identiteit die is gesynchroniseerd met AD.

#### <a name="azure-portal"></a>Azure-portal
Voer de volgende stappen uit om een RBAC-rol toe te wijzen aan een Azure AD-identiteit met behulp van de [Azure Portal](https://portal.azure.com):

1. Ga in het Azure Portal naar de bestands share of [Maak een bestands share](../articles/storage/files/storage-how-to-create-file-share.md).
2. Selecteer **Access Control (IAM)** .
3. Selecteer **een roltoewijzing toevoegen**
4. Selecteer in de Blade **roltoewijzing toevoegen** de geschikte ingebouwde rol (opslag BESTANDS gegevens SMB-share, opslag BESTANDS gegevens SMB delen Inzender) uit de lijst met **functies** . Zorg ervoor dat er geen **toegang wordt toegewezen aan** de standaard instelling: **Azure AD-gebruiker,-groep of Service-Principal**. Selecteer de Azure AD-doel-id op naam of e-mail adres.
5. Selecteer **Opslaan** om de roltoewijzings bewerking te volt ooien.

#### <a name="powershell"></a>PowerShell

In het volgende Power shell-voor beeld ziet u hoe u een RBAC-rol toewijst aan een Azure AD-identiteit, op basis van aanmeldings naam. Zie [toegang beheren met RBAC en Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md)voor meer informatie over het toewijzen van RBAC-rollen met Power shell.

Voordat u het volgende voorbeeld script uitvoert, moet u de waarden van de tijdelijke aanduidingen vervangen door uw eigen waarden.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
De volgende CLI 2,0-opdracht laat zien hoe u een RBAC-rol toewijst aan een Azure AD-identiteit, op basis van aanmeldings naam. Zie [toegang beheren met RBAC en Azure cli](../articles/role-based-access-control/role-assignments-cli.md)voor meer informatie over het toewijzen van RBAC-rollen met Azure cli. 

Voordat u het volgende voorbeeld script uitvoert, moet u de waarden van de tijdelijke aanduidingen vervangen door uw eigen waarden.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>NTFS-machtigingen configureren via SMB 
Nadat u machtigingen op share niveau hebt toegewezen met RBAC, moet u de juiste NTFS-machtigingen toewijzen op het niveau root, Directory of file. U kunt machtigingen op share niveau beschouwen als de gate keeper op hoog niveau die bepaalt of een gebruiker toegang heeft tot de share. Terwijl NTFS-machtigingen op een meer gedetailleerd niveau handelen om te bepalen welke bewerkingen de gebruiker kan uitvoeren op het niveau van de map of het bestand.

Azure Files ondersteunt de volledige set met NTFS Basic-en geavanceerde machtigingen. U kunt NTFS-machtigingen voor mappen en bestanden in een Azure-bestands share weer geven en configureren door de share te koppelen en vervolgens Windows Verkenner of de opdracht Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) of [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) uit te voeren. 

Als u NTFS wilt configureren met Gebruikersaccountbeheer, moet u de share koppelen met behulp van de sleutel van uw opslag account van de virtuele machine die lid is van het domein. Volg de instructies in de volgende sectie om een Azure-bestands share te koppelen vanaf de opdracht prompt en de NTFS-machtigingen dienovereenkomstig te configureren.

De volgende sets machtigingen worden ondersteund in de hoofdmap van een bestands share:

- BUILTIN\Administrators: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (i/o) (GR, GE)
- NT AUTHORITY\Authenticated-gebruikers: (OI) (CI) (M)
- NT AUTHORITY\SYSTEM: (F)
- MAKER EIGENAAR: (OI) (CI) (I/O) (F)

### <a name="configure-ntfs-permissions-with-icacls"></a>NTFS-machtigingen configureren met icacls
Gebruik de volgende Windows-opdracht om volledige machtigingen te verlenen aan alle mappen en bestanden onder de bestands share, met inbegrip van de hoofdmap. Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Zie voor meer informatie over het gebruik van icacls voor het instellen van NTFS-machtigingen en voor de verschillende typen ondersteunde machtigingen [de opdracht regel verwijzing voor icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="mount-a-file-share-from-the-command-prompt"></a>Een bestands share koppelen vanaf de opdracht prompt

Gebruik de Windows **net use** -opdracht om de Azure-bestands share te koppelen. Vergeet niet om de waarden van de tijdelijke aanduidingen in het volgende voor beeld te vervangen door uw eigen waarden. Zie [een Azure-bestands share gebruiken met Windows](../articles/storage/files/storage-how-to-use-files-windows.md)voor meer informatie over het koppelen van bestands shares.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>NTFS-machtigingen configureren met Windows Verkenner
Gebruik Windows Verkenner om volledige machtigingen te verlenen aan alle mappen en bestanden onder de bestands share, met inbegrip van de hoofdmap.

1. Open Windows Verkenner, klik met de rechter muisknop op het bestand/de map en selecteer **Eigenschappen**
2. Klik op het tabblad **beveiliging**
3. Klik op **bewerken..** . knop om machtigingen te wijzigen
4. U kunt de machtiging van bestaande gebruikers wijzigen of klikken op **toevoegen...** om machtigingen toe te kennen aan nieuwe gebruikers
5. In het prompt venster voor het toevoegen van nieuwe gebruikers typt u de naam van de doel gebruiker waaraan u machtigingen wilt verlenen in het vak **Geef de object namen op** en klikt u op **Namen controleren** om de volledige UPN-naam van de doel gebruiker te zoeken.
7.  Klik op **OK**
8.  Selecteer op het tabblad Beveiliging alle machtigingen die u wilt verlenen aan de nieuwe gebruiker toevoegen
9.  Klik op **Toep assen**

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Een bestands share koppelen vanaf een virtuele machine die lid is van een domein

Met het volgende proces wordt gecontroleerd of uw bestands share en toegangs machtigingen juist zijn ingesteld en dat u toegang hebt tot een Azure-bestands share vanaf een VM die is toegevoegd aan een domein:

Meld u aan bij de virtuele machine met behulp van de Azure AD-identiteit waaraan u machtigingen hebt verleend, zoals wordt weer gegeven in de volgende afbeelding. Als u AD-verificatie voor Azure Files hebt ingeschakeld, gebruikt u de AD-referentie. Voor Azure AD DS-verificatie meldt u zich aan met de Azure AD-referentie.

![Scherm afbeelding van het aanmeldings scherm van Azure AD voor gebruikers verificatie](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Gebruik de volgende opdracht om de Azure-bestands share te koppelen. Vergeet niet om de waarden van de tijdelijke aanduidingen te vervangen door uw eigen waarden. Omdat u bent geverifieerd, hoeft u de sleutel voor het opslag account, de AD-referenties of de Azure AD-referenties niet op te geven. De ervaring voor eenmalige aanmelding wordt ondersteund voor verificatie met AD of Azure AD DS.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

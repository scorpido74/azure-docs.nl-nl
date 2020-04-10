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
ms.openlocfilehash: c88f5a4dd4f2997ce01b1f6a3ae192c62f530e76
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011414"
---
## <a name="2-assign-access-permissions-to-an-identity"></a>2. Toegangsmachtigingen toewijzen aan een identiteit

Om toegang te krijgen tot Azure Files-bronnen met verificatie op basis van identiteit, moet een identiteit (een gebruiker, groep of serviceprincipal) over de vereiste machtigingen op shareniveau beschikken. Dit proces is vergelijkbaar met het opgeven van machtigingen voor delen van Windows, waarbij u het type toegang opgeeft dat een bepaalde gebruiker heeft om een bestandsshare te delen. De richtlijnen in deze sectie laten zien hoe u lees-, schrijf- of verwijdermachtigingen voor een bestandsshare aan een identiteit toewijzen. 

We hebben drie azure-ingebouwde rollen geïntroduceerd voor het verlenen van machtigingen op share-level aan gebruikers:

- **Storage File Data SMB Share Reader** biedt leestoegang in Azure Storage-bestandsshares via SMB.
- **Met SMB Share Contributor voor opslagbestandsgegevens** kan toegang worden gelezen, geschreven en verwijderd in Azure Storage-bestandsshares via SMB.
- **Met Opslagbestandsgegevens SMB Contribuant met verhoogde bijdrager** kan NTFS-machtigingen in Azure Storage-bestandsshares via SMB worden gelezen, geschreven, verwijderd en gewijzigd.

> [!IMPORTANT]
> Volledige administratieve controle over een bestandsshare, inclusief de mogelijkheid om eigenaar te worden van een bestand, vereist het gebruik van de opslagaccountsleutel. Beheerbeheer wordt niet ondersteund met Azure AD-referenties.

U de Azure-portal, PowerShell of Azure CLI gebruiken om de ingebouwde rollen toe te wijzen aan de Azure AD-identiteit van een gebruiker voor het verlenen van machtigingen op share-level.

> [!NOTE]
> Vergeet niet uw AD-referenties te synchroniseren met Azure AD als u van plan bent uw AD te gebruiken voor verificatie. Wachtwoordhashsynchronisatie van AD naar Azure AD is optioneel. Toestemming op sharelevel wordt verleend voor de Azure AD-identiteit die vanuit AD wordt gesynchroniseerd.

De algemene aanbeveling is om toestemming op shareniveau te gebruiken voor toegangsbeheer op hoog niveau voor een AD-groep die een groep gebruikers en identiteiten vertegenwoordigt, en vervolgens NTFS-machtigingen te gebruiken voor gedetailleerd toegangsbeheer op map-/bestandsniveau. 

#### <a name="azure-portal"></a>Azure Portal
Als u een RBAC-rol wilt toewijzen aan een Azure AD-identiteit, voert u [de](https://portal.azure.com)volgende stappen uit:

1. Ga in de Azure-portal naar uw bestandsshare of [Maak een bestandsshare](../articles/storage/files/storage-how-to-create-file-share.md).
2. Selecteer **Toegangsbeheer (IAM)**.
3. Een **roltoewijzing toevoegen selecteren**
4. Selecteer in het **hoofdmodel Roltoewijzing toevoegen** de juiste ingebouwde rol (SMB Share Reader voor opslagbestanden, smb-bijdrager voor opslagbestandgegevens) in de lijst **Met rollen.** Laat **Toegang toewijzen aan** de standaardinstelling: Azure **AD-gebruiker, groep of serviceprincipal**. Selecteer de doel-Azure AD-identiteit op naam of e-mailadres.
5. Selecteer **Opslaan** om de taakbewerking voor roltoewijzing te voltooien.

#### <a name="powershell"></a>PowerShell

In het volgende PowerShell-voorbeeld ziet u hoe u een RBAC-rol toewijst aan een Azure AD-identiteit op basis van aanmeldingsnaam. Zie [Toegang beheren met RBAC en Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md)voor meer informatie over het toewijzen van RBAC-rollen met PowerShell.

Voordat u het volgende voorbeeldscript uitvoert, moet u rekening houden met het vervangen van tijdelijke aanduidingswaarden, inclusief haakjes, door uw eigen waarden.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Met de volgende opdracht CLI 2.0 ziet u hoe u een RBAC-rol toewijst aan een Azure AD-identiteit op basis van aanmeldingsnaam. Zie [Toegang beheren met RBAC en Azure CLI](../articles/role-based-access-control/role-assignments-cli.md)voor meer informatie over het toewijzen van RBAC-rollen met Azure CLI. 

Voordat u het volgende voorbeeldscript uitvoert, moet u rekening houden met het vervangen van tijdelijke aanduidingswaarden, inclusief haakjes, door uw eigen waarden.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="3-configure-ntfs-permissions-over-smb"></a>3. NTFS-machtigingen configureren via SMB 
Nadat u machtigingen op share-level hebt toegewezen aan RBAC, moet u de juiste NTFS-machtigingen toewijzen op root-, directory- of bestandsniveau. Denk aan machtigingen op share-level als de poortwachter op hoog niveau die bepaalt of een gebruiker toegang heeft tot het aandeel. Terwijl NTFS-machtigingen op een gedetailleerder niveau werken om te bepalen welke bewerkingen de gebruiker kan uitvoeren op map- of bestandsniveau.

Azure Files ondersteunt de volledige set NTFS-basis- en geavanceerde machtigingen. U NTFS-machtigingen voor mappen en bestanden in een Azure-bestandsshare weergeven en configureren door het aandeel te monteren en vervolgens Windows File Explorer te gebruiken of de opdracht Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) of [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) uit te voeren. 

Als u NTFS wilt configureren met superusermachtigingen, moet u het aandeel monteren met behulp van uw opslagaccountsleutel van uw vm met domeinverbinding. Volg de instructies in de volgende sectie om een Azure-bestandsshare te monteren op basis van de opdrachtprompt en ntfs-machtigingen dienovereenkomstig te configureren.

De volgende sets machtigingen worden ondersteund in de hoofdmap van een bestandsshare:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Gebruikers:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Geverifieerde gebruikers:(OI)(CI)(M)
- NT AUTHORITY\SYSTEEM:(F)
- EIGENAAR VAN DE MAKER:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>NTFS-machtigingen configureren met icacls
Gebruik de volgende Opdracht Windows om alle mappen en bestanden onder de bestandsshare, inclusief de hoofdmap, volledige machtigingen te verlenen. Vergeet niet om de tijdelijke aanduidingswaarden in het voorbeeld te vervangen door uw eigen waarden.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Zie [de command-line referentie voor icacls voor](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)meer informatie over het gebruik van icacls om NTFS-machtigingen in te stellen en over de verschillende soorten ondersteunde machtigingen.

### <a name="mount-a-file-share-from-the-command-prompt"></a>Een bestandsshare monteren vanuit de opdrachtprompt

Gebruik de opdracht **Windows-net om** de Azure-bestandsshare te monteeren. Vergeet niet om de tijdelijke aanduidingswaarden in het volgende voorbeeld te vervangen door uw eigen waarden. Zie [Een Azure-bestandsshare gebruiken met Windows](../articles/storage/files/storage-how-to-use-files-windows.md)voor meer informatie over het maken van bestandsshares.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>NTFS-machtigingen configureren met Windows Verkenner
Gebruik Windows Verkenner om alle mappen en bestanden onder de bestandsshare, inclusief de hoofdmap, volledige toestemming te geven.

1. Windows Verkenner openen en met de rechtermuisknop op het bestand/de map klikken en **Eigenschappen** selecteren
2. Klik op het tabblad **Beveiliging**
3. Klik op **Bewerken..**. knop om machtigingen te wijzigen
4. U de toestemming van bestaande gebruikers wijzigen of op **Toevoegen klikken...** om nieuwe gebruikers machtigingen te verlenen
5. Voer in het snelvenster voor het toevoegen van nieuwe gebruikers de doelgebruikersnaam in waaraan u toestemming wilt verlenen in het vak **De objectnamen invoeren om in te schakelen** en klik op Namen **controleren** om de volledige UPN-naam van de doelgebruiker te vinden.
7.  Klik op **OK**
8.  Selecteer op het tabblad Beveiliging alle machtigingen die u wilt verlenen aan de nieuw toe te voegen gebruiker
9.  Klik op **Solliciteren**

## <a name="4-mount-a-file-share-from-a-domain-joined-vm"></a>4. Een bestandsshare van een vm met domein

In het volgende proces wordt gecontroleerd of uw bestandsshare- en toegangsmachtigingen correct zijn ingesteld en dat u toegang hebt tot een Azure-bestandsshare vanuit een vm die is verbonden met een domein. Houd er rekening mee dat de rbac-roltoewijzing op aandelenniveau enige tijd in beslag kan nemen. 

Meld u aan bij de VM met de Azure AD-identiteit waaraan u machtigingen hebt verleend, zoals in de volgende afbeelding wordt weergegeven. Als u AD-verificatie voor Azure-bestanden hebt ingeschakeld, gebruikt u de AD-referenties. Meld u voor Azure AD DS-verificatie aan met Azure AD-referenties.

![Schermafbeelding van het aanmeldingsscherm van Azure AD voor gebruikersverificatie](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Gebruik de volgende opdracht om de Azure-bestandsshare te monteren. Vergeet niet om de tijdelijke aanduidingwaarden te vervangen door uw eigen waarden. Omdat u bent geverifieerd, hoeft u de opslagaccountsleutel, de AD-referenties of de Azure AD-referenties niet op te geven. Eenmalige aanmeldingservaring wordt ondersteund voor verificatie met AD of Azure AD DS.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

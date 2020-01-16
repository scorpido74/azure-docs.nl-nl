---
title: Azure Active Directory Domain Services inschakelen met behulp van een sjabloon | Microsoft Docs
description: Meer informatie over het configureren en inschakelen van Azure Active Directory Domain Services met behulp van een Azure Resource Manager sjabloon
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: e63f330d463be21905467869474527fdf9d6abff
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030196"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Een door Azure Active Directory Domain Services beheerd domein maken met behulp van een Azure Resource Manager sjabloon

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP, Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. U gebruikt deze domein Services zonder zelf domein controllers te implementeren, beheren en repareren. Azure AD DS integreert met uw bestaande Azure AD-Tenant. Met deze integratie kunnen gebruikers zich aanmelden met hun bedrijfs referenties, en kunt u bestaande groepen en gebruikers accounts gebruiken om de toegang tot bronnen te beveiligen.

In dit artikel wordt beschreven hoe u Azure AD DS kunt inschakelen met behulp van een Azure Resource Manager sjabloon. Ondersteunende bronnen worden gemaakt met behulp van Azure PowerShell.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om dit artikel te volt ooien:

* Installeer en configureer Azure PowerShell.
    * Als dat nodig is, volgt u de instructies om [de Azure PowerShell-module te installeren en verbinding te maken met uw Azure-abonnement](/powershell/azure/install-az-ps).
    * Zorg ervoor dat u zich aanmeldt bij uw Azure-abonnement met behulp van de cmdlet [Connect-AzAccount][Connect-AzAccount] .
* Azure AD Power Shell installeren en configureren.
    * Als dat nodig is, volgt u de instructies voor [het installeren van de Azure AD Power shell-module en verbinding maken met Azure AD](/powershell/azure/active-directory/install-adv2).
    * Zorg ervoor dat u zich aanmeldt bij uw Azure AD-Tenant met de cmdlet [Connect-AzureAD][Connect-AzureAD] .
* U hebt *globale beheerders* bevoegdheden nodig in uw Azure AD-Tenant om Azure AD DS in te scha kelen.
* U hebt *Inzender* bevoegdheden nodig in uw Azure-abonnement om de vereiste Azure AD DS-resources te maken.

## <a name="dns-naming-requirements"></a>Vereisten voor DNS-naamgeving

Wanneer u een exemplaar van Azure AD DS maakt, geeft u een DNS-naam op. Er zijn enkele aandachtspunten bij het kiezen van deze DNS-naam:

* **Ingebouwde domein naam:** De ingebouwde domein naam van de Directory wordt standaard gebruikt (een *. onmicrosoft.com* -achtervoegsel). Als u beveiligde LDAP-toegang tot het beheerde domein via internet wilt inschakelen, kunt u geen digitaal certificaat maken om de verbinding met dit standaard domein te beveiligen. Micro soft is eigenaar van het *onmicrosoft.com* -domein, waardoor een certificerings instantie (CA) geen certificaat kan uitgeven.
* **Aangepaste domein namen:** De meest voorkomende benadering is het opgeven van een aangepaste domein naam, meestal een die u al bezit en routeerbaar is. Wanneer u een routeerbaar, aangepast domein gebruikt, kan het verkeer op de juiste wijze worden uitgevoerd om uw toepassingen te ondersteunen.
* **Niet-routeerbaar domein achtervoegsels:** U wordt geadviseerd om een niet-routeerbaar domein naam achtervoegsel, zoals *contoso. local*, te voor komen. Het achtervoegsel *. local* is niet routeerbaar en kan problemen met de DNS-omzetting veroorzaken.

> [!TIP]
> Als u een aangepaste domein naam maakt, moet u rekening houden met bestaande DNS-naam ruimten. U kunt het beste een uniek voor voegsel voor de domein naam toevoegen. Als uw naam van de DNS-basis bijvoorbeeld *contoso.com*is, maakt u een Azure AD DS beheerd domein met de aangepaste domein naam *Corp.contoso.com* of *DS.contoso.com*. In een hybride omgeving met een on-premises AD DS omgeving zijn deze voor voegsels mogelijk al in gebruik. Gebruik een uniek voor voegsel voor Azure AD DS.
>
> U kunt de DNS-basis naam voor uw Azure AD DS beheerde domein gebruiken, maar u moet mogelijk enkele extra DNS-records maken voor andere services in uw omgeving. Als u bijvoorbeeld een webserver uitvoert die als host fungeert voor een-site met behulp van de DNS-naam van de basis, kan er sprake zijn van naam conflicten waarvoor extra DNS-vermeldingen zijn vereist.
>
> In deze zelf studies en artikelen met procedures wordt het aangepaste domein *aadds.contoso.com* als een kort voor beeld gebruikt. Geef in alle opdrachten uw eigen domein naam op. Dit kan ook een uniek voor voegsel bevatten.
>
> Zie [een naam voorvoegsel voor het domein selecteren][naming-prefix]voor meer informatie.

De volgende DNS-naam beperkingen zijn ook van toepassing:

* **Beperkingen voor domein voorvoegsels:** U kunt geen beheerd domein met een voor voegsel maken dat langer is dan 15 tekens. Het voor voegsel van de opgegeven domein naam (bijvoorbeeld *Contoso* in de domein naam *contoso.com* ) mag Maxi maal 15 tekens bevatten.
* **Conflicten met netwerk naam:** De DNS-domein naam voor uw beheerde domein mag niet al bestaan in het virtuele netwerk. Controleer met name op de volgende scenario's die leiden tot een naam conflict:
    * Als u al een Active Directory domein met dezelfde DNS-domein naam hebt in het virtuele Azure-netwerk.
    * Als het virtuele netwerk waar u het beheerde domein wilt inschakelen, een VPN-verbinding heeft met uw on-premises netwerk. In dit scenario zorgt u ervoor dat u geen domein hebt met dezelfde DNS-domein naam in uw on-premises netwerk.
    * Als u een bestaande Azure-Cloud service hebt met die naam in het virtuele Azure-netwerk.

## <a name="create-required-azure-ad-resources"></a>Vereiste Azure AD-resources maken

Voor Azure AD DS is een Service-Principal en een Azure AD-groep vereist. Met deze resources kunnen de Azure AD DS beheerde domein gegevens synchroniseren en definiëren welke gebruikers beheerders machtigingen hebben in het beheerde domein.

Registreer eerst de Azure AD Domain Services resource provider met behulp [van de cmdlet REGI ster-AzResourceProvider][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Maak een Azure AD-Service-Principal met behulp van de [New-azureadserviceprincipal namelijk niet-][New-AzureADServicePrincipal] cmdlet voor Azure AD DS om zichzelf te communiceren en te verifiëren. Een specifieke toepassings-ID wordt gebruikt met de naam *Domain Controller Services* met de id *2565bd9d-DA50-47d4-8b85-4c97f669dc36*. Wijzig deze toepassings-ID niet.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Maak nu een Azure AD-groep met de naam *Aad DC-Administrators* met behulp van de cmdlet [New-AzureADGroup][New-AzureADGroup] . Aan gebruikers die zijn toegevoegd aan deze groep worden machtigingen verleend voor het uitvoeren van beheer taken op het door Azure AD DS beheerde domein.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Wanneer de groep *Aad DC-Administrators* is gemaakt, voegt u een gebruiker aan de groep toe met behulp van de cmdlet [add-AzureADGroupMember][Add-AzureADGroupMember] . U krijgt eerst de groeps object-ID voor *Aad DC-Administrators* met behulp van de cmdlet [Get-AzureADGroup][Get-AzureADGroup] en vervolgens de object-id van de gewenste gebruiker met de cmdlet [Get-AzureADUser][Get-AzureADUser] .

In het volgende voor beeld wordt de gebruikers object-ID voor het account met een UPN van `admin@contoso.onmicrosoft.com`. Vervang dit gebruikers account door de UPN van de gebruiker die u wilt toevoegen aan de groep *Aad DC-Administrators* :

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Maak ten slotte een resource groep met behulp van de cmdlet [New-AzResourceGroup][New-AzResourceGroup] . In het volgende voor beeld heeft de resource groep de naam *myResourceGroup* en wordt deze gemaakt in de regio *westus* . Gebruik uw eigen naam en gewenste regio:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Als u een regio kiest die Beschikbaarheidszones ondersteunt, worden de Azure AD DS-resources verdeeld over zones voor extra redundantie. Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's.

Er is niets waarmee u kunt configureren voor Azure AD DS worden gedistribueerd over meerdere zones. Het Azure-platform verwerkt automatisch de zone distributie van resources. Zie [Wat zijn Beschikbaarheidszones in azure?][availability-zones]voor meer informatie en om de beschik baarheid van regio's te bekijken.

## <a name="resource-definition-for-azure-ad-ds"></a>Resource definitie voor Azure AD DS

Als onderdeel van de Resource Manager-resource definitie zijn de volgende configuratie parameters vereist:

| Parameter               | Waarde |
|-------------------------|---------|
| Naam              | De DNS-domein naam voor uw beheerde domein, waarbij rekening wordt gehouden met de vorige punten voor het benoemen van voor voegsels en conflicten. |
| filteredSync            | Met Azure AD DS kunt u *alle* gebruikers en groepen synchroniseren die beschikbaar zijn in azure AD of een synchronisatie met een *bereik* van alleen specifieke groepen. Als u ervoor kiest om alle gebruikers en groepen te synchroniseren, kunt u er later niet voor kiezen om alleen een synchronisatie met een bereik uit te voeren.<br /> Zie [Azure AD Domain Services scoped Synchronization][scoped-sync](Engelstalig) voor meer informatie over het bereik van synchronisatie.|
| notificationSettings    | Als er waarschuwingen worden gegenereerd in het beheerde domein van Azure AD DS, kunnen e-mail meldingen worden verzonden. <br />*Globale beheerders* van de Azure-Tenant en leden van de groep *Aad DC-Administrators* kunnen voor deze meldingen worden *ingeschakeld* .<br /> Desgewenst kunt u extra ontvangers voor meldingen toevoegen wanneer er waarschuwingen zijn die aandacht vereisen.|
| domainConfigurationType | Standaard wordt een door Azure AD DS beheerd domein gemaakt als een *gebruikers* forest. Dit type forest synchroniseert alle objecten van Azure AD, met inbegrip van gebruikers accounts die zijn gemaakt in een on-premises AD DS omgeving. U hoeft geen *domainConfiguration* -waarde op te geven om een gebruikers forest te maken.<br /> Een *resource* -forest synchroniseert alleen gebruikers en groepen die rechtstreeks in azure AD zijn gemaakt. Bron-forests zijn momenteel beschikbaar als preview-versie. Stel de waarde in op *ResourceTrusting* om een resource-forest te maken.<br />Zie [overzicht van Azure AD DS-resource forests][resource-forests]voor meer informatie over *bron* -forests, waaronder waarom u er één kunt gebruiken en hoe u forest-vertrouwens relaties maakt met on-premises AD DS domeinen.|

De definitie van de volgende verkorte para meter laat zien hoe deze waarden worden gedeclareerd. Een gebruikers forest met de naam *aadds.contoso.com* wordt gemaakt met alle gebruikers van Azure AD gesynchroniseerd met het door Azure AD DS beheerde domein:

```json
"parameters": {
    "domainName": {
        "value": "aadds.contoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

Het volgende gesmalle Resource Manager-sjabloon resource type wordt vervolgens gebruikt voor het definiëren en maken van het door Azure AD DS beheerde domein. Er moet al een virtueel netwerk van Azure en een subnet bestaan of als onderdeel van de Resource Manager-sjabloon zijn gemaakt. Het beheerde domein van Azure AD DS is verbonden met dit subnet.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Deze para meters en dit resource type kunnen worden gebruikt als onderdeel van een bredere Resource Manager-sjabloon voor het implementeren van een beheerd domein, zoals wordt weer gegeven in de volgende sectie.

## <a name="create-a-managed-domain-using-sample-template"></a>Een beheerd domein maken met voorbeeld sjabloon

Met de volgende voorbeeld sjabloon voor Resource Manager maakt u een Azure AD DS beheerd domein en de ondersteunende regels voor het virtuele netwerk, het subnet en de netwerk beveiligings groep. De regels voor de netwerk beveiligings groep zijn vereist om het beheerde domein te beveiligen en ervoor te zorgen dat verkeer correct kan stromen. Er wordt een gebruikers forest met de DNS-naam *aadds.contoso.com* gemaakt, waarbij alle gebruikers zijn gesynchroniseerd vanuit Azure AD:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aadds.contoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Deze sjabloon kan worden geïmplementeerd met uw voorkeurs implementatie methode, zoals de [Azure Portal][portal-deploy], [Azure POWERSHELL][powershell-deploy]of een CI/cd-pijp lijn. In het volgende voor beeld wordt de cmdlet [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment] gebruikt. Geef de naam van uw eigen resource groep en de bestands naam van de sjabloon op:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Het duurt enkele minuten om de resource te maken en het besturings element terug te sturen naar de Power shell-prompt. Het beheerde domein van Azure AD DS blijft op de achtergrond worden ingericht en kan tot een uur duren voordat de implementatie is voltooid. In de Azure Portal wordt op de pagina **overzicht** voor uw door Azure AD DS beheerde domein de huidige status weer gegeven in de gehele implementatie fase.

Wanneer de Azure Portal laat zien dat de inrichting van het beheerde Azure AD DS-domein is voltooid, moeten de volgende taken worden uitgevoerd:

* Werk de DNS-instellingen voor het virtuele netwerk bij, zodat de virtuele machines het beheerde domein kunnen vinden voor het lid worden van het domein of de verificatie.
    * Als u DNS wilt configureren, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **overzichts** venster wordt u gevraagd deze DNS-instellingen automatisch te configureren.
* [Wachtwoord synchronisatie inschakelen voor Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) zodat eind gebruikers zich kunnen aanmelden bij het beheerde domein met hun bedrijfs referenties.

## <a name="next-steps"></a>Volgende stappen

Als u de Azure AD DS beheerde domein in actie wilt zien, kunt u [een domein toevoegen aan een Windows-VM, een][windows-join] [beveiligd LDAP configureren][tutorial-ldaps]en een [wachtwoord-hash-synchronisatie configureren][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment

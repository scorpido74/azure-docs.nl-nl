---
title: Azure DS Domain Services inschakelen met behulp van een sjabloon | Microsoft Documenten
description: Meer informatie over het configureren en inschakelen van Azure Active Directory Domain Services met behulp van een Azure Resource Manager-sjabloon
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: 2daadb539bc08df37f15c187866b735e45309288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612789"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Een beheerd Azure Directory Domain Services-domein maken met een Azure Resource Manager-sjabloon

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domeinservices zoals domeinjoin, groepsbeleid, LDAP, Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. U gebruikt deze domeinservices zonder zelf domeincontrollers te implementeren, te beheren en te patchen. Azure AD DS integreert met uw bestaande Azure AD-tenant. Met deze integratie kunnen gebruikers zich aanmelden met hun bedrijfsreferenties en u bestaande groepen en gebruikersaccounts gebruiken om de toegang tot bronnen te beveiligen.

In dit artikel ziet u hoe u Azure AD DS inschakelt met behulp van een Azure Resource Manager-sjabloon. Ondersteunende resources worden gemaakt met Azure PowerShell.

## <a name="prerequisites"></a>Vereisten

Om dit artikel te voltooien, hebt u de volgende bronnen nodig:

* Installeer en configureer Azure PowerShell.
    * Volg indien nodig de instructies om [de Azure PowerShell-module te installeren en verbinding te maken met uw Azure-abonnement.](/powershell/azure/install-az-ps)
    * Zorg ervoor dat u zich aanmeldt bij uw Azure-abonnement met de [cmdlet Connect-AzAccount.][Connect-AzAccount]
* Azure AD PowerShell installeren en configureren.
    * Volg indien nodig de instructies om [de Azure AD PowerShell-module](/powershell/azure/active-directory/install-adv2)te installeren en verbinding te maken met Azure AD.
    * Zorg ervoor dat u zich aanmeldt bij uw Azure AD-tenant met de cmdlet [Connect-AzureAD.][Connect-AzureAD]
* U hebt *algemene beheerdersrechten* nodig in uw Azure AD-tenant om Azure AD DS in te schakelen.
* U hebt *inzenderbevoegdheden* in uw Azure-abonnement nodig om de vereiste Azure AD DS-resources te maken.

## <a name="dns-naming-requirements"></a>DNS-naamgevingsvereisten

Wanneer u een Azure AD DS-exemplaar maakt, geeft u een DNS-naam op. Er zijn enkele overwegingen wanneer u deze DNS-naam kiest:

* **Ingebouwde domeinnaam:** Standaard wordt de ingebouwde domeinnaam van de map gebruikt (een *.onmicrosoft.com* achtervoegsel). Als u via internet beveiligde LDAP-toegang tot het beheerde domein wilt inschakelen, u geen digitaal certificaat maken om de verbinding met dit standaarddomein te beveiligen. Microsoft is eigenaar van het *domein .onmicrosoft.com,* zodat een Certificaatautoriteit (CA) geen certificaat afgeeft.
* **Aangepaste domeinnamen:** De meest voorkomende aanpak is het opgeven van een aangepaste domeinnaam, meestal een die u al bezit en is routetabel. Wanneer u een routeerbaar, aangepast domein gebruikt, kan het verkeer correct stromen als dat nodig is om uw toepassingen te ondersteunen.
* **Niet-routeerbare domeinachtervoegsels:** We raden u over het algemeen aan een niet-routeerbaar domeinnaamachtervoegsel te vermijden, zoals *contoso.local.* Het *.local* achtervoegsel is niet routeerbaar en kan problemen veroorzaken met dns-resolutie.

> [!TIP]
> Als u een aangepaste domeinnaam maakt, moet u rekening houden met bestaande DNS-naamruimten. Het wordt aanbevolen om een domeinnaam te gebruiken die los staat van bestaande Azure- of on-premises DNS-naamruimte.
>
> Als u bijvoorbeeld een bestaande DNS-naamruimte van *contoso.com*hebt, maakt u een door Azure AD DS beheerd domein met de aangepaste domeinnaam van *aaddscontoso.com*. Als u veilige LDAP moet gebruiken, moet u deze aangepaste domeinnaam registreren en bezitten om de vereiste certificaten te genereren.
>
> Mogelijk moet u een aantal extra DNS-records maken voor andere services in uw omgeving of voorwaardelijke DNS-expediteurs tussen bestaande DNS-naamruimten in uw omgeving. Als u bijvoorbeeld een webserver uitvoert die een site host met de hoofd-DNS-naam, kunnen er naamgevingsconflicten zijn waarvoor extra DNS-vermeldingen nodig zijn.
>
> In deze tutorials en how-to artikelen wordt het aangepaste domein van *aaddscontoso.com* als kort voorbeeld gebruikt. Geef in alle opdrachten uw eigen domeinnaam op.

De volgende DNS-naambeperkingen zijn ook van toepassing:

* **Beperkingen voor domeinvoorvoegsel:** U geen beheerd domein maken met een voorvoegsel dat langer is dan 15 tekens. Het voorvoegsel van uw opgegeven domeinnaam (zoals *aaddscontoso* in de *aaddscontoso.com* domeinnaam) moet 15 of minder tekens bevatten.
* **Conflicten met netwerknamen:** De DNS-domeinnaam voor uw beheerde domein zou nog niet in het virtuele netwerk moeten bestaan. Controleer specifiek op de volgende scenario's die tot een naamconflict zouden leiden:
    * Als u al een Active Directory-domein hebt met dezelfde DNS-domeinnaam in het virtuele Azure-netwerk.
    * Als het virtuele netwerk waar u het beheerde domein wilt inschakelen, een VPN-verbinding heeft met uw on-premises netwerk. Zorg er in dit scenario voor dat u geen domein hebt met dezelfde DNS-domeinnaam op uw on-premises netwerk.
    * Als u een bestaande Azure-cloudservice met die naam in het virtuele Azure-netwerk hebt.

## <a name="create-required-azure-ad-resources"></a>Vereiste Azure AD-resources maken

Azure AD DS vereist een serviceprincipal en een Azure AD-groep. Met deze bronnen kunnen het door Azure AD DS beheerde domein gegevens synchroniseren en bepalen welke gebruikers beheerdersmachtigingen hebben in het beheerde domein.

Registreer eerst de Azure AD Domain Services-bronprovider met de cmdlet [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Maak een Azure AD-serviceprincipal met de cmdlet [Nieuw-AzureADServicePrincipal][New-AzureADServicePrincipal] voor Azure AD DS om zichzelf te communiceren en te verifiëren. Een specifieke toepassings-ID wordt gebruikt met de naam *Domain Controller Services* met een ID van *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Wijzig deze toepassings-id niet.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Maak nu een Azure AD-groep met de naam *AAD DC-beheerders* met de cmdlet [Nieuw-AzureADGroup.][New-AzureADGroup] Gebruikers die aan deze groep zijn toegevoegd, krijgen vervolgens machtigingen om beheertaken uit te voeren op het beheerde Azure AD DS-domein.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Als de groep *AAD DC-beheerders* is gemaakt, voegt u een gebruiker toe aan de groep met de cmdlet [Add-AzureADGroupMember.][Add-AzureADGroupMember] U krijgt eerst de groepsobject-id *van AAD DC Administrators* met de cmdlet [Get-AzureADGroup][Get-AzureADGroup] en vervolgens de object-id van de gewenste gebruiker met de cmdlet [Get-AzureADUser.][Get-AzureADUser]

In het volgende voorbeeld wordt de gebruikersnaam van het `admin@aaddscontoso.onmicrosoft.com`gebruikersobject voor het account met een UPN van . Vervang dit gebruikersaccount door de UPN van de gebruiker die u wilt toevoegen aan de groep *AAD DC-beheerders:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Maak ten slotte een resourcegroep met de cmdlet [Nieuw-AzResourceGroep.][New-AzResourceGroup] In het volgende voorbeeld wordt de resourcegroep *myResourceGroup* genoemd en wordt deze gemaakt in de *regio Westus.* Gebruik uw eigen naam en gewenste regio:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Als u een regio kiest die beschikbaarheidszones ondersteunt, worden de Azure AD DS-resources verdeeld over zones voor extra redundantie. Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's.

U er niets voor configureren dat Azure AD DS over zones wordt verdeeld. Het Azure-platform verwerkt automatisch de zonedistributie van resources. Zie [Beschikbaarheidszones in Azure voor][availability-zones]meer informatie en voor de beschikbaarheid van de regio.

## <a name="resource-definition-for-azure-ad-ds"></a>Resourcedefinitie voor Azure AD DS

Als onderdeel van de resourcedefinitie resourcemanager zijn de volgende configuratieparameters vereist:

| Parameter               | Waarde |
|-------------------------|---------|
| Domeinnaam              | De DNS-domeinnaam voor uw beheerde domein, rekening houdend met de vorige punten voor het benoemen van voorvoegsels en conflicten. |
| filteredSync            | Met Azure AD DS u *alle* gebruikers en groepen synchroniseren die beschikbaar zijn in Azure AD of een *scoped* synchronisatie van alleen specifieke groepen. Als u ervoor kiest om alle gebruikers en groepen te synchroniseren, u er niet later voor kiezen om alleen een scoped synchronisatie uit te voeren.<br /> Zie [Azure AD Domain Services scoped synchronization][scoped-sync]voor meer informatie over scoped synchronization.|
| meldingInstellingen    | Als er waarschuwingen worden gegenereerd in het beheerde Azure AD DS-domein, kunnen e-mailmeldingen worden verzonden. <br />*Globale beheerders* van de Azure-tenant en leden van de aad *dc-beheerdersgroep* kunnen voor deze meldingen worden *ingeschakeld.*<br /> Indien gewenst u extra ontvangers toevoegen voor meldingen wanneer er waarschuwingen zijn die aandacht vereisen.|
| domainConfigurationType | Standaard wordt een door Azure AD DS beheerd domein gemaakt als *gebruikersforest.* Dit type forest synchroniseert alle objecten uit Azure AD, inclusief gebruikersaccounts die zijn gemaakt in een on-premises AD DS-omgeving. U hoeft geen *domeinconfiguratiewaarde* op te geven om een gebruikersforest te maken.<br /> Een *resourceforest* synchroniseert alleen gebruikers en groepen die rechtstreeks in Azure AD zijn gemaakt. Resourceforests zijn momenteel in preview. Stel de waarde in *op ResourceTrusting* om een resourceforest te maken.<br />Zie overzicht van [Azure AD DS-bronforests][resource-forests]voor meer informatie over *resourceforests,* waaronder waarom u er een gebruiken en hoe u forestvertrouwensrelaties maken met on-premises AD DS-domeinen.|

De volgende definitie van gecondenseerde parameters laat zien hoe deze waarden worden gedeclareerd. Er wordt een gebruikersforest met de naam *aaddscontoso.com* gemaakt met alle gebruikers van Azure AD gesynchroniseerd met het beheerde Azure AD DS-domein:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
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

Het volgende gecondenseerde resourcebrontype voor Resource Manager wordt vervolgens gebruikt om het beheerde Azure AD DS-beheerde domein te definiëren en te maken. Er moet al een virtueel Azure-netwerk en subnet bestaan of worden gemaakt als onderdeel van de sjabloon Resourcebeheer. Het beheerde Azure AD DS-domein is verbonden met dit subnet.

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

Deze parameters en resourcetypen kunnen worden gebruikt als onderdeel van een bredere Resource Manager-sjabloon om een beheerd domein te implementeren, zoals in de volgende sectie wordt weergegeven.

## <a name="create-a-managed-domain-using-sample-template"></a>Een beheerd domein maken met voorbeeldsjabloon

Met de volgende volledige voorbeeldsjabloon Resourcebeheer wordt een door Azure AD DS beheerd domein en de regels voor de ondersteuning van virtuele netwerk-, subnet- en netwerkbeveiligingsgroepen gemaakt. De regels voor netwerkbeveiliging zijn vereist om het beheerde domein te beveiligen en ervoor te zorgen dat het verkeer correct kan stromen. Er wordt een gebruikersforest gemaakt met de DNS-naam van *aaddscontoso.com,* waarbij alle gebruikers zijn gesynchroniseerd vanuit Azure AD:

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
            "value": "aaddscontoso.com"
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

Deze sjabloon kan worden geïmplementeerd met behulp van de gewenste implementatiemethode, zoals de [Azure-portal,][portal-deploy] [Azure PowerShell][powershell-deploy]of een CI/CD-pijplijn. In het volgende voorbeeld wordt de cmdlet [Nieuw-AzResourceGroupDeployment][New-AzResourceGroupDeployment] gebruikt. Geef de naam en de bestandsnaam van de brongroep op:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Het duurt een paar minuten om de resource te maken en het besturingselement terug te geven aan de PowerShell-prompt. Het beheerde Azure AD DS-domein blijft op de achtergrond worden ingericht en kan tot een uur duren voordat de implementatie is voltooid. In de Azure-portal wordt op de **pagina Overzicht** voor uw door Azure AD DS beheerde domein de huidige status in deze implementatiefase weergegeven.

Wanneer de Azure-portal aangeeft dat het door Azure AD DS beheerde domein is voltooid, moeten de volgende taken worden voltooid:

* Dns-instellingen voor het virtuele netwerk bijwerken, zodat virtuele machines het beheerde domein kunnen vinden voor domeinjoin of verificatie.
    * Als u DNS wilt configureren, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **venster Overzicht** wordt u gevraagd deze DNS-instellingen automatisch te configureren.
* [Wachtwoordsynchronisatie inschakelen voor Azure AD Domain Services,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) zodat eindgebruikers zich kunnen aanmelden bij het beheerde domein met hun bedrijfsreferenties.

## <a name="next-steps"></a>Volgende stappen

Als u het door Azure AD DS beheerde domein in actie wilt zien, u [een Windows-vm in het domein invoeren,][windows-join] [beveiligde LDAP configureren][tutorial-ldaps]en [wachtwoordhashsynchronisatie configureren.][tutorial-phs]

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

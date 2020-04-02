---
title: Een sjabloon gebruiken om lid te worden van een Windows-vm naar Azure AD DS | Microsoft Documenten
description: Meer informatie over het gebruik van Azure Resource Manager-sjablonen om een nieuwe of bestaande Windows Server-vm samen te voegen aan een beheerd Azure Directory Domain Services-domein.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 19e4033b462cda9511ee45420bd4c1a76cc36b92
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518961"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Een virtuele machine van Windows Server aansluiten bij een beheerd Azure Directory Domain Services-domein met behulp van een resourcebeheersjabloon

Als u de implementatie en configuratie van Virtuele Azure-machines (VM's) wilt automatiseren, u een resourcemanagersjabloon gebruiken. Met deze sjablonen u telkens consistente implementaties maken. Extensies kunnen ook worden opgenomen in sjablonen om een VM automatisch te configureren als onderdeel van de implementatie. Eén handige extensie voegt VM's toe aan een domein, dat kan worden gebruikt met azure active directory domain services (Azure AD DS) beheerde domeinen.

In dit artikel ziet u hoe u een Windows Server-vm maakt en lid maakt van een door Azure AD DS beheerd domein met behulp van Resource Manager-sjablonen. U leert ook hoe u een bestaande Windows Server VM aansluiten bij een Azure AD DS-domein.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Indien nodig maakt [en configureert][create-azure-ad-ds-instance]de eerste zelfstudie een Azure Active Directory Domain Services-exemplaar .
* Een gebruikersaccount dat deel uitmaakt van het door Azure AD DS beheerde domein.

## <a name="azure-resource-manager-template-overview"></a>Overzicht van Azure Resource Manager-sjabloon

Met resourcebeheersjablonen u azure-infrastructuur definiëren in code. De vereiste resources, netwerkverbindingen of configuratie van VM's kunnen allemaal worden gedefinieerd in een sjabloon. Deze sjablonen maken elke keer consistente, reproduceerbare implementaties en kunnen worden geversioneerd terwijl u wijzigingen aanbrengt. Zie [overzicht van Azure Resource Manager-sjablonen][template-overview]voor meer informatie.

Elke bron wordt gedefinieerd in een sjabloon met JavaScript Object Notatie (JSON). In het volgende VOORBEELD van JSON wordt het brontype *Microsoft.Compute/virtualMachines/extensions/extensions* gebruikt om de Active Directory-domeinjoinextensie te installeren. Er worden parameters gebruikt die u opgeeft tijdens de implementatie. Wanneer de extensie wordt geïmplementeerd, wordt de VM samengevoegd met het opgegeven beheerde Azure AD DS-domein.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Deze VM-extensie kan worden geïmplementeerd, zelfs als u geen VM in dezelfde sjabloon maakt. De voorbeelden in dit artikel tonen beide van de volgende benaderingen:

* [Een Windows Server-vm maken en lid worden van een beheerd domein](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Een bestaande Windows Server-VM naar een beheerd domein voegen](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Een Windows Server-vm maken en lid worden van een beheerd domein

Als u een Windows Server-vm nodig hebt, u er een maken en configureren met behulp van een resourcemanagersjabloon. Wanneer de VM wordt geïmplementeerd, wordt vervolgens een extensie geïnstalleerd om lid te worden van de VM naar een door Azure AD DS beheerd domein. Als u al een VM hebt waaraan u wilt deelnemen aan een door Azure AD DS beheerd domein, gaat u naar [Een bestaande Windows Server VM deelnemen aan een beheerd domein.](#join-an-existing-windows-server-vm-to-a-managed-domain)

Voer de volgende stappen uit om een Windows Server VM te maken en deze vervolgens te gebruiken voor een door Azure AD DS beheerd domein:

1. Blader naar de [sjabloon snel aanmaken](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Selecteer de optie voor **implementeren naar Azure**.
1. Voer op de pagina **Aangepaste implementatie** de volgende gegevens in om een Windows Server VM te maken en lid te worden van het beheerde Azure AD DS-domein:

    | Instelling                   | Waarde |
    |---------------------------|-------|
    | Abonnement              | Kies hetzelfde Azure-abonnement waarin u Azure AD Domain Services hebt ingeschakeld. |
    | Resourcegroep            | Kies de resourcegroep voor uw vm. |
    | Locatie                  | Selecteer de locatie van uw vm. |
    | Bestaande VNET-naam        | De naam van het bestaande virtuele netwerk om de VM mee aan te sluiten, zoals *myVnet.* |
    | Bestaande subnetnaam      | De naam van het bestaande virtuele netwerksubnet, zoals *Workloads*. |
    | DNS-labelvoorvoegsel          | Voer een DNS-naam in die u voor de VM wilt gebruiken, zoals *myvm.* |
    | VM-grootte                   | Geef een VM-grootte op, zoals *Standard_DS2_v2*. |
    | Domein om lid te worden            | De door Azure AD DS beheerde domeinnaam domain DNS, zoals *aaddscontoso.com*. |
    | Domeingebruikersnaam           | Het gebruikersaccount in het beheerde Azure AD DS-domein dat moet worden `contosoadmin@aaddscontoso.com`gebruikt om de VM aan het beheerde domein te voegen, zoals . Dit account moet deel uitmaken van het beheerde Azure AD DS-domein. |
    | Domeinwachtwoord           | Het wachtwoord voor het gebruikersaccount dat in de vorige instelling is opgegeven. |
    | Optioneel OU-pad          | De aangepaste organisatie-eenheid waarin de VM kan worden toegevoegd. Als u geen waarde voor deze parameter opgeeft, wordt de VM toegevoegd aan de standaard *AAD DC-computers-eenheid.* |
    | Gebruikersnaam VM-beheerder         | Geef een lokaal beheerdersaccount op dat u op de vm wilt maken. |
    | VM-beheerderswachtwoord         | Geef een lokaal beheerderswachtwoord op voor de vm. Maak een sterk lokaal beheerderswachtwoord om te beschermen tegen brute-force-aanvallen met wachtwoorden. |

1. Bekijk de algemene voorwaarden en schakel het selectievakje in, want **ik ga akkoord met de hierboven vermelde algemene voorwaarden.** Selecteer Inkoop **om** te maken en sluit u aan bij de VM in het beheerde Azure AD DS-domein.

> [!WARNING]
> **Ga voorzichtig om met wachtwoorden.**
> In het sjabloonparameterbestand wordt het wachtwoord aangevraagd voor een gebruikersaccount dat deel uitmaakt van het beheerde Azure AD DS-domein. Voer geen waarden handmatig in dit bestand in en laat het toegankelijk voor bestandsshares of andere gedeelde locaties.

Het duurt enkele minuten voordat de implementatie is voltooid. Wanneer de Windows-VM is voltooid, wordt deze gemaakt en samengevoegd tot het beheerde Azure AD DS-domein. De VM kan worden beheerd of aangemeld met domeinaccounts.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Een bestaande Windows Server-VM naar een beheerd domein voegen

Als u een bestaande VM of groep VM's hebt waaraan u wilt deelnemen aan een door Azure AD DS beheerd domein, u een resourcemanagersjabloon gebruiken om de VM-extensie te implementeren.

Voer de volgende stappen uit om een bestaande Windows Server-vm aan te sluiten bij een door Azure AD DS beheerd domein:

1. Blader naar de [sjabloon snel aanmaken](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Selecteer de optie voor **implementeren naar Azure**.
1. Voer op de pagina **Aangepaste implementatie** de volgende gegevens in om lid te worden van de VM naar het beheerde Azure AD DS-domein:

    | Instelling                   | Waarde |
    |---------------------------|-------|
    | Abonnement              | Kies hetzelfde Azure-abonnement waarin u Azure AD Domain Services hebt ingeschakeld. |
    | Resourcegroep            | Kies de resourcegroep met uw bestaande VM. |
    | Locatie                  | Selecteer de locatie van uw bestaande VM. |
    | VM-lijst                   | Voer de door komma's gescheiden lijst in van de bestaande VM(s) om deel te nemen aan het beheerde Azure AD DS-domein, zoals *myVM1,myVM2*. |
    | Gebruikersnaam voor domeinjoin     | Het gebruikersaccount in het beheerde Azure AD DS-domein dat moet worden `contosoadmin@aaddscontoso.com`gebruikt om de VM aan het beheerde domein te voegen, zoals . Dit account moet deel uitmaken van het beheerde Azure AD DS-domein. |
    | Gebruikerswachtwoord voor domein joinen | Het wachtwoord voor het gebruikersaccount dat in de vorige instelling is opgegeven. |
    | Optioneel OU-pad          | De aangepaste organisatie-eenheid waarin de VM kan worden toegevoegd. Als u geen waarde voor deze parameter opgeeft, wordt de VM toegevoegd aan de standaard *AAD DC-computers-eenheid.* |

1. Bekijk de algemene voorwaarden en schakel het selectievakje in, want **ik ga akkoord met de hierboven vermelde algemene voorwaarden.** Wanneer u klaar bent, selecteert u **Kopen** om lid te worden van de VM in het beheerde Azure AD DS-domein.

> [!WARNING]
> **Ga voorzichtig om met wachtwoorden.**
> In het sjabloonparameterbestand wordt het wachtwoord aangevraagd voor een gebruikersaccount dat deel uitmaakt van het beheerde Azure AD DS-domein. Voer geen waarden handmatig in dit bestand in en laat het toegankelijk voor bestandsshares of andere gedeelde locaties.

Het duurt even voordat de implementatie succesvol is voltooid. Wanneer de opgegeven Windows VM's zijn voltooid, worden ze samengevoegd met het beheerde Azure AD DS-domein en kunnen ze worden beheerd of aangemeld bij het gebruik van domeinaccounts.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de Azure-portal gebruikt om resources te configureren en te implementeren met behulp van sjablonen. U ook resources implementeren met Resource Manager-sjablonen met [Azure PowerShell][deploy-powershell] of azure [CLI.][deploy-cli]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md

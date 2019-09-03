---
title: Meer informatie over het controleren van de inhoud van een machine
description: Meer informatie over hoe Azure Policy gast configuratie gebruikt voor het controleren van instellingen binnen een Azure-machine.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 054f9ed21ee0d7ef725c2b7eee8174c53374b5bc
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232260"
---
# <a name="understand-azure-policys-guest-configuration"></a>Informatie over Azure Policy Gast-configuratie

Naast het controleren en [herstellen](../how-to/remediate-resources.md) van Azure-resources, kunnen Azure Policy instellingen in een machine controleren. De validatie wordt uitgevoerd door de configuratie van de Gast-extensie en de client. De extensie, via de client, valideert instellingen zoals de configuratie van het besturingssysteem, Toepassingsconfiguratie of aanwezigheid en omgevingsinstellingen.

Op dit moment voert Azure Policy-gast configuratie alleen een controle van instellingen in de computer uit.
Het is nog niet mogelijk om configuraties toe te passen.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Extensie en client

Voor het controleren van instellingen binnen een machine is de extensie van de [virtuele machine](../../../virtual-machines/extensions/overview.md) ingeschakeld. De extensie wordt gedownload voor de toewijzing van configuratiebeleid van toepassing en de bijbehorende definitie van de configuratie.

### <a name="limits-set-on-the-exension"></a>Limieten die zijn ingesteld voor de exension

De gast configuratie mag niet meer dan 5% van het CPU-gebruik overschrijden om de uitbrei ding te beperken van impact toepassingen die worden uitgevoerd op de computer.
Dit is waar BOH voor configuraties van micro soft als ' ingebouwd ' en voor aangepaste configuraties die door klanten zijn gemaakt.

## <a name="register-guest-configuration-resource-provider"></a>Configuratie van de Gast-resourceprovider registreren

Voordat u de configuratie van de Gast gebruiken kunt, moet u de resourceprovider registreren. U kunt registreren via de portal of via PowerShell. De resource provider wordt automatisch geregistreerd als de toewijzing van een gast configuratie beleid wordt uitgevoerd via de portal.

### <a name="registration---portal"></a>Registratie - Portal

Volg deze stappen voor het registreren van de resourceprovider voor de configuratie van de Gast via Azure portal:

1. Starten van de Azure portal en klikt u op **alle services**. Zoek en selecteer **abonnementen**.

1. Zoek en klik op het abonnement dat u wilt de configuratie van de Gast voor inschakelen.

1. In het menu links van de **abonnement** pagina, klikt u op **resourceproviders**.

1. Filteren op of blader totdat u **Microsoft.GuestConfiguration**, klikt u vervolgens op **registreren** in dezelfde rij.

### <a name="registration---powershell"></a>Registratie - PowerShell

Voor het registreren van de resourceprovider voor de configuratie van de Gast via PowerShell, voer de volgende opdracht:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Hulpprogramma's voor validatie

In de computer gebruikt de gast configuratie-client lokale hulpprogram ma's om de controle uit te voeren.

De volgende tabel bevat een overzicht van de lokale hulpprogramma's die op elk ondersteund besturingssysteem wordt gebruikt:

|Besturingssysteem|Hulpprogramma voor het valideren|Opmerkingen|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef inspectie mogelijk](https://www.chef.io/inspec/)| Ruby en Python worden geïnstalleerd door de configuratie van de Gast-extensie. |

### <a name="validation-frequency"></a>Validatie frequentie

De gast configuratie client controleert elke vijf minuten op nieuwe inhoud. Zodra een gast toewijzing is ontvangen, worden de instellingen gecontroleerd met een interval van 15 minuten. Er worden resultaten verzonden naar de provider van de gast configuratie resource zodra de controle is voltooid. Wanneer er een beleids [evaluatie](../how-to/get-compliance-data.md#evaluation-triggers) wordt uitgevoerd, wordt de status van de machine naar de provider van de gast configuratie genoteerd. Dit zorgt ervoor Azure Policy de Azure Resource Manager eigenschappen te evalueren. Een Azure Policy evaluatie op aanvraag haalt de meest recente waarde op van de provider van de gast configuratie resource. Er wordt echter geen nieuwe controle van de configuratie op de computer geactiveerd.

## <a name="supported-client-types"></a>Ondersteunde client-typen

De volgende tabel ziet u een lijst met ondersteunde besturingssystemen op Azure-installatiekopieën:

|Uitgever|Name|Versies|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Data Center, 2012 R2 Data Center, 2016 Data Center, 2019 Data Center|
|Microsoft|Windows-client|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> Met gast configuratie kunt u knoop punten controleren waarop een ondersteund besturings systeem wordt uitgevoerd. Als u virtuele machines wilt controleren die gebruikmaken van een aangepaste installatie kopie, moet u de **DeployIfNotExists** -definitie dupliceren en de **if** -sectie wijzigen zodat de eigenschappen van de installatie kopie worden opgenomen.

### <a name="unsupported-client-types"></a>Niet-ondersteunde client-typen

Windows Server nano server wordt niet ondersteund in een versie.

## <a name="guest-configuration-extension-network-requirements"></a>Netwerk vereisten gast configuratie uitbreiding

Voor de communicatie met de provider van de gast configuratie resource in azure, hebben computers uitgaande toegang tot Azure-data centers op poort **443**. Als u een particulier virtueel netwerk in azure gebruikt en uitgaand verkeer niet toestaat, moeten uitzonde ringen worden geconfigureerd met regels voor [netwerk beveiligings groepen](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . Op dit moment bestaat er geen servicetag voor Azure Policy-gast configuratie.

Voor IP-adres lijsten kunt u de [IP-adresbereiken van Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653)downloaden. Dit bestand wordt wekelijks bijgewerkt en heeft de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken. U hoeft alleen uitgaande toegang toe te staan voor de IP-adressen in de regio's waar uw Vm's worden geïmplementeerd.

> [!NOTE]
> Het XML-bestand met IP-adressen van Azure Data Center geeft een lijst van de IP-adresbereiken die worden gebruikt in de Microsoft Azure data centers. Het bestand bevat compute-, SQL-en Storage-bereiken. Er wordt wekelijks een bijgewerkt bestand geplaatst. Het bestand weerspiegelt de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken. Nieuwe bereiken die in het bestand worden weer gegeven, worden gedurende ten minste één week niet gebruikt in de data centers. Het is een goed idee om elke week het nieuwe XML-bestand te downloaden. Werk vervolgens uw site bij om de services die in Azure worden uitgevoerd, correct te identificeren. Gebruikers van Azure ExpressRoute moeten weten dat dit bestand wordt gebruikt om de Border Gateway Protocol (BGP)-advertentie van Azure Space bij te werken in de eerste week van elke maand.

## <a name="guest-configuration-definition-requirements"></a>Configuratie van de Gast de definitie van vereisten

Voor elke controle die wordt uitgevoerd per gast configuratie zijn twee beleids definities, een **DeployIfNotExists** -definitie en een **AuditIfNotExists** -definitie vereist. De definitie van de **DeployIfNotExists** wordt gebruikt om de computer voor te bereiden met de gast configuratie agent en andere onderdelen ter ondersteuning van de [validatie hulpprogramma's](#validation-tools).

De **DeployIfNotExists** beleidsdefinitie valideert en corrigeert de volgende items:

- Controleer of aan de computer een configuratie is toegewezen om te evalueren. Als er momenteel geen toewijzing aanwezig is, haalt u de toewijzing op en bereidt u de machine voor door het volgende te doen:
  - Verifiëren met de computer met behulp van een [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installeer de nieuwste versie van de **Microsoft.GuestConfiguration** extensie
  - Installeren van [-validatiehulpprogramma's](#validation-tools) en afhankelijkheden, indien nodig

Als de **DeployIfNotExists** -toewijzing niet compatibel is, kan een [herstel taak](../how-to/remediate-resources.md#create-a-remediation-task) worden gebruikt.

Zodra de **DeployIfNotExists** -toewijzing compatibel is, gebruikt de **AuditIfNotExists** -beleids toewijzing de lokale validatie hulpprogramma's om te bepalen of de configuratie toewijzing compatibel of niet-compatibel is.
Het hulpprogramma voor het valideren biedt de resultaten naar de configuratie van de Gast-client. De client verzendt de resultaten naar de Gast-extensie, waardoor ze beschikbaar zijn via de configuratie van de Gast-resourceprovider.

Azure Policy maakt gebruik van de configuratie van de Gast-resourceproviders **complianceStatus** eigenschap voor rapport naleving in de **naleving** knooppunt. Zie voor meer informatie, [ophalen van Nalevingsgegevens](../how-to/getting-compliance-data.md).

> [!NOTE]
> Het **DeployIfNotExists** -beleid is vereist voor het **AuditIfNotExists** -beleid om resultaten te retour neren.
> Zonder de **DeployIfNotExists**wordt met het **AuditIfNotExists** -beleid ' 0 van 0 ' resources weer gegeven als status.

Alle ingebouwde beleidsregels voor de configuratie van de Gast zijn opgenomen in een initiatief aan groep de definities voor gebruik in toewijzingen. Het ingebouwde initiatief met de naam *[Preview]: Instellingen voor wachtwoord beveiliging controleren binnen Linux-en* Windows-machines bevat 18 beleids regels. Er zijn zes **DeployIfNotExists** en **AuditIfNotExists** paren voor Windows en drie sets voor Linux. In elk geval wordt de logica in de definitie van de alleen het doel valideert besturingssysteem wordt geëvalueerd op basis van de [beleidsregel](definition-structure.md#policy-rule) definitie.

### <a name="multiple-assignments"></a>Meerdere toewijzingen

Gast configuratie beleid biedt momenteel alleen ondersteuning voor het toewijzen van dezelfde gast toewijzing per computer, zelfs als de beleids toewijzing gebruikmaakt van verschillende para meters.

## <a name="built-in-resource-modules"></a>Ingebouwde resource modules

Wanneer de uitbrei ding gast configuratie is geïnstalleerd, wordt de Power shell-module ' GuestConfiguration ' opgenomen in de meest recente versie van DSC-resource modules. Deze module kan vanuit de PowerShell Gallery worden gedownload met behulp van de koppeling hand matig downloaden van de module pagina [GuestConfiguration/](https://www.powershellgallery.com/packages/GuestConfiguration/).
De bestands indeling '. nupkg ' kan worden gewijzigd in '. zip ' om deze te decomprimeren en te controleren.

## <a name="client-log-files"></a>Client logboek bestanden

De gast configuratie-extensie schrijft logboek bestanden naar de volgende locaties:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Waar `<version>` verwijst naar het huidige versie nummer.

## <a name="guest-configuration-samples"></a>Voor beelden van gast configuraties

Voor beelden voor beleids gast configuratie zijn beschikbaar op de volgende locaties:

- [Voor beelden van index-gast configuratie](../samples/index.md#guest-configuration)
- [Azure Policy-voor beelden github opslag plaats](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](definition-structure.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/getting-compliance-data.md).
- Meer informatie over het [oplossen van niet-compatibele resources](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/index.md).

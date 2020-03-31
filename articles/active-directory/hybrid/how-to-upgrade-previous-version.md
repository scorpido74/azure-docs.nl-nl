---
title: 'Azure AD Connect: upgrade vanaf een vorige versie | Microsoft Documenten'
description: Hiermee worden de verschillende methoden uitgelegd om te upgraden naar de nieuwste versie van Azure Active Directory Connect, inclusief een upgrade op de plaats en een swingmigratie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a3e7373a8b0354a3d08debf944f2f77f1609382
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347688"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: upgraden van een vorige naar de meest recente versie
In dit onderwerp worden de verschillende methoden beschreven die u gebruiken om uw Azure Active Directory -installatie (Azure AD) Connect te upgraden naar de nieuwste versie. We raden u aan om uzelf op de hoogte te houden van de releases van Azure AD Connect. U gebruikt ook de stappen in de sectie [Swing-migratie](#swing-migration) wanneer u een aanzienlijke configuratiewijziging aanbrengt.

>[!NOTE]
> Het wordt momenteel ondersteund om te upgraden van elke versie van Azure AD Connect naar de huidige versie. In-place upgrades van DirSync of ADSync worden niet ondersteund en een swing migratie is vereist.  Zie [Upgraden van Azure AD-synchronisatietool (DirSync)](how-to-dirsync-upgrade-get-started.md) of de sectie [Swing-migratie](#swing-migration) als u wilt upgraden vanuit DirSync.  </br>In de praktijk kunnen klanten met extreem oude versies problemen ondervinden die niet direct verband houden met Azure AD Connect. Servers die al enkele jaren in productie zijn, hebben meestal verschillende patches op hen toegepast en niet al deze kunnen worden verantwoord.  Over het algemeen moeten klanten die niet hebben geüpgraded in 12-18 maanden overwegen om in plaats daarvan een swing-upgrade te overwegen, omdat dit de meest conservatieve en minst riskante optie is.

Zie [Upgraden van Azure AD-synchronisatietool (DirSync)](how-to-dirsync-upgrade-get-started.md) als u wilt upgraden vanuit DirSync.

Er zijn een paar verschillende strategieën die u gebruiken om Azure AD Connect te upgraden.

| Methode | Beschrijving |
| --- | --- |
| [Automatische upgrade](how-to-connect-install-automatic-upgrade.md) |Dit is de eenvoudigste methode voor klanten met een express installatie. |
| [In-place upgrade](#in-place-upgrade) |Als u één server hebt, u de installatie op dezelfde server upgraden. |
| [Swingmigratie](#swing-migration) |Met twee servers u een van de servers voorbereiden met de nieuwe release of configuratie en de actieve server wijzigen wanneer u er klaar voor bent. |

Zie de [machtigingen die nodig zijn voor een upgrade voor](reference-connect-accounts-permissions.md#upgrade)informatie over machtigingen.

> [!NOTE]
> Nadat u uw nieuwe Azure AD Connect-server hebt ingeschakeld om wijzigingen in Azure AD te synchroniseren, mag u niet opnieuw worden teruggedraaid naar DirSync of Azure AD Sync. Downgraden van Azure AD Connect naar oudere clients, waaronder DirSync en Azure AD Sync, worden niet ondersteund en kunnen leiden tot problemen zoals gegevensverlies in Azure AD.

## <a name="in-place-upgrade"></a>In-place upgrade
Een in-place upgrade werkt voor het verplaatsen van Azure AD Sync of Azure AD Connect. Het werkt niet voor het verplaatsen van DirSync of voor een oplossing met Forefront Identity Manager (FIM) + Azure AD Connector.

Deze methode heeft de voorkeur wanneer u één server hebt en minder dan ongeveer 100.000 objecten. Als er wijzigingen zijn in de out-of-box synchronisatieregels, vindt na de upgrade een volledige import en volledige synchronisatie plaats. Deze methode zorgt ervoor dat de nieuwe configuratie wordt toegepast op alle bestaande objecten in het systeem. Deze run kan enkele uren duren, afhankelijk van het aantal objecten dat zich in het bereik van de synchronisatieengine bevindt. De normale deltasynchronisatieplanner (die standaard elke 30 minuten synchroniseert) wordt opgeschort, maar wachtwoordsynchronisatie gaat door. Je zou kunnen overwegen het doen van de in-place upgrade tijdens een weekend. Als er geen wijzigingen zijn in de out-of-box configuratie met de nieuwe Azure AD Connect-release, wordt in plaats daarvan een normale deltaimport/-synchronisatie gestart.  
![In-place upgrade](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Als u wijzigingen hebt aangebracht in de out-of-box synchronisatieregels, worden deze regels teruggezet naar de standaardconfiguratie bij de upgrade. Als u ervoor wilt zorgen dat uw configuratie tussen upgrades wordt bewaard, moet u ervoor zorgen dat u wijzigingen aanbrengt zoals deze worden beschreven in [De aanbevolen procedures voor het wijzigen van de standaardconfiguratie.](how-to-connect-sync-best-practices-changing-default-configuration.md)

Tijdens de upgrade op hun plaats kunnen er wijzigingen worden aangebracht die vereisen dat specifieke synchronisatieactiviteiten (inclusief volledige importstap en volledige synchronisatiestap) moeten worden uitgevoerd nadat de upgrade is voltooid. Als u dergelijke activiteiten wilt uitstellen, raadpleegt u de sectie [Hoe volledige synchronisatie na de upgrade uit te stellen.](#how-to-defer-full-synchronization-after-upgrade)

Als u Azure AD Connect met een niet-standaardconnector gebruikt (bijvoorbeeld Generieke LDAP-connector en Generieke SQL Connector), moet u de bijbehorende connectorconfiguratie in de [synchronisatieservicebeheer](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) vernieuwen na een upgrade op zijn plaats. Zie artikelsectie [Versiereleasegeschiedenis van](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting)de sectie Connector - Probleemoplossing voor meer informatie over het vernieuwen van de connectorconfiguratie. Als u de configuratie niet vernieuwt, werken de uitvoerstappen voor het importeren en exporteren niet goed voor de connector. U ontvangt de volgende fout in het logboek van de toepassingsgebeurtenis met bericht *"Assemblageversie in AAD Connector-configuratie ("X.X.XXX. X") is eerder dan de eigenlijke versie ("X.X.XXX. X") van "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".*

## <a name="swing-migration"></a>Swingmigratie
Als u een complexe implementatie of veel objecten hebt, is het mogelijk onpraktisch om een in-place upgrade op het live-systeem uit te voeren. Voor sommige klanten kan dit proces meerdere dagen duren en gedurende deze periode worden er geen deltawijzigingen verwerkt. U deze methode ook gebruiken wanneer u van plan bent aanzienlijke wijzigingen aan te brengen in uw configuratie en u ze wilt uitproberen voordat ze naar de cloud worden geduwd.

De aanbevolen methode voor deze scenario's is het gebruik van een swing migratie. U hebt (ten minste) twee servers nodig: één actieve server en één staging-server. De actieve server (weergegeven met effen blauwe lijnen in de volgende afbeelding) is verantwoordelijk voor de actieve productiebelasting. De staging server (weergegeven met stippellijnen) wordt voorbereid met de nieuwe release of configuratie. Wanneer deze volledig klaar is, wordt deze server actief gemaakt. De vorige actieve server, die nu de oude versie of configuratie heeft geïnstalleerd, wordt gemaakt in de staging server en wordt bijgewerkt.

De twee servers kunnen verschillende versies gebruiken. De actieve server die u van plan bent te ontmantelen, kan bijvoorbeeld Azure AD Sync gebruiken en de nieuwe faseringsserver kan Azure AD Connect gebruiken. Als u swingmigratie gebruikt om een nieuwe configuratie te ontwikkelen, is het een goed idee om dezelfde versies op de twee servers te hebben.  
![Faseringsserver](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Sommige klanten geven de voorkeur aan drie of vier servers voor dit scenario. Wanneer de staging-server is geüpgraded, hebt u geen back-upserver voor [herstel na noodgevallen.](how-to-connect-sync-staging-server.md#disaster-recovery) Met drie of vier servers u één set primaire/stand-byservers voorbereiden met de nieuwe versie, die ervoor zorgt dat er altijd een staging-server is die klaar is om het over te nemen.

Deze stappen werken ook om te verhuizen van Azure AD Sync of een oplossing met FIM + Azure AD Connector. Deze stappen werken niet voor DirSync, maar dezelfde swingmigratiemethode (ook wel parallelle implementatie genoemd) met stappen voor DirSync bevindt zich in [Upgrade Azure Active Directory sync (DirSync).](how-to-dirsync-upgrade-get-started.md)

### <a name="use-a-swing-migration-to-upgrade"></a>Een swingmigratie gebruiken om te upgraden
1. Als u Azure AD Connect op beide servers gebruikt en van plan bent alleen een configuratiewijziging aan te brengen, controleert u of uw actieve server en de staging-server beide dezelfde versie gebruiken. Dat maakt het makkelijker om later verschillen te vergelijken. Als u een upgrade uitvoert vanuit Azure AD Sync, hebben deze servers verschillende versies. Als u een upgrade uitvoert van een oudere versie van Azure AD Connect, is het een goed idee om te beginnen met de twee servers die dezelfde versie gebruiken, maar dit is niet vereist.
2. Als u een aangepaste configuratie hebt gemaakt en de staging-server deze niet heeft, voert u de stappen uit onder [Een aangepaste configuratie verplaatsen van de actieve server naar de stagingserver.](#move-a-custom-configuration-from-the-active-server-to-the-staging-server)
3. Als u een upgrade uitvoert vanaf een eerdere versie van Azure AD Connect, upgradet u de staging-server naar de nieuwste versie. Als u overstapt van Azure AD Sync, installeert u Azure AD Connect op uw staging-server.
4. Laat de synchronisatie-engine volledig importeren en volledige synchronisatie uitvoeren op uw staging-server.
5. Controleer of de nieuwe configuratie geen onverwachte wijzigingen heeft veroorzaakt door de stappen te gebruiken onder 'Verifiëren' in [De configuratie van een server verifiëren.](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server) Als iets niet is zoals verwacht, corrigeert u het, voert u de import en synchronisatie uit en controleert u de gegevens totdat deze er goed uitzien door de stappen te volgen.
6. Schakel de staging server om de actieve server te zijn. Dit is de laatste stap "Switch active server" in [Verify the configuration of a server](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Als u Azure AD Connect upgradet, upgradet u de server die nu in de faseringsmodus staat naar de nieuwste versie. Volg dezelfde stappen als voorheen om de gegevens en configuratie te upgraden. Als u een upgrade hebt uitgevoerd vanuit Azure AD Sync, u uw oude server nu uitschakelen en buiten gebruik stellen.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Een aangepaste configuratie verplaatsen van de actieve server naar de faseringsserver
Als u configuratiewijzigingen hebt aangebracht in de actieve server, moet u ervoor zorgen dat dezelfde wijzigingen worden toegepast op de stagingserver. Om u met deze stap te helpen, u de [configuratiedocumenter azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter)gebruiken.

U de aangepaste synchronisatieregels verplaatsen die u hebt gemaakt met PowerShell. U moet andere wijzigingen op dezelfde manier toepassen op beide systemen en u de wijzigingen niet migreren. De [configuratiedocumenter](https://github.com/Microsoft/AADConnectConfigDocumenter) kan u helpen de twee systemen te vergelijken om ervoor te zorgen dat ze identiek zijn. De tool kan ook helpen bij het automatiseren van de stappen in deze sectie.

U moet de volgende dingen op dezelfde manier configureren op beide servers:

* Verbinding met dezelfde bossen
* Alle domein- en organisatie-eenheidfilters
* Dezelfde optionele functies, zoals wachtwoordsynchronisatie en terugschrijven van wachtwoorden

**Aangepaste synchronisatieregels verplaatsen**  
Ga als volgt te werk om aangepaste synchronisatieregels te verplaatsen:

1. Open **De editor voor synchronisatieregels** op uw actieve server.
2. Selecteer een aangepaste regel. Klik op **Exporteren**. Dit brengt een Notepad venster. Sla het tijdelijke bestand op met een PS1-extensie. Dit maakt het een PowerShell script. Kopieer het PS1-bestand naar de staging-server.  
   ![Export van synchronisatieregels](./media/how-to-upgrade-previous-version/exportrule.png)
3. De CONNECTOR-GUID is anders op de staging-server en u moet deze wijzigen. Als u de GUID wilt krijgen, start u **De editor voor synchronisatieregels,** selecteert u een van de kant-en-klare regels die hetzelfde verbonden systeem vertegenwoordigen en klikt u op **Exporteren**. Vervang de GUID in uw PS1-bestand door de GUID van de staging-server.
4. Voer in een PowerShell-prompt het PS1-bestand uit. Hiermee wordt de aangepaste synchronisatieregel op de staging-server gemaakt.
5. Herhaal dit voor al uw aangepaste regels.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Volledige synchronisatie uitstellen na upgrade
Tijdens de upgrade op hun plaats kunnen er wijzigingen worden aangebracht die vereisen dat specifieke synchronisatieactiviteiten (inclusief volledige importstap en volledige synchronisatiestap) moeten worden uitgevoerd. Wijzigingen in het connectorschema vereisen bijvoorbeeld **volledige importstap** en wijzigingen in de out-of-boxsynchronisatieregel vereisen **volledige synchronisatiestap** om op de betrokken connectors uit te voeren. Tijdens de upgrade bepaalt Azure AD Connect welke synchronisatieactiviteiten nodig zijn en registreert deze als *overschrijft.* In de volgende synchronisatiecyclus neemt de synchronisatieplanner deze overschrijvingen op en voert deze uit. Zodra een overschrijving is uitgevoerd, wordt deze verwijderd.

Er kunnen situaties zijn waarin u niet wilt dat deze overschrijvingen onmiddellijk na de upgrade plaatsvinden. U hebt bijvoorbeeld tal van gesynchroniseerde objecten en u wilt dat deze synchronisatiestappen na kantooruren plaatsvinden. Ga als volgende over op het verwijderen van deze overschrijvingen:

1. Schakel tijdens de upgrade de **optie Start** **het synchronisatieproces start wanneer de configuratie is voltooid.** Hiermee wordt de synchronisatieplanner uitgeschakeld en wordt voorkomen dat de synchronisatiecyclus automatisch plaatsvindt voordat de overschrijvingen worden verwijderd.

   ![FullSyncafterUpgrade uitschakelen](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Nadat de upgrade is voltooid, voert u de volgende cmdlet uit om erachter te komen welke overschrijvingen zijn toegevoegd:`Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > De overschrijvingen zijn connector-specifiek. In het volgende voorbeeld zijn de stap Volledige invoer en de stap Volledige synchronisatie toegevoegd aan zowel de on-premises AD-connector als de Azure AD Connector.

   ![FullSyncafterUpgrade uitschakelen](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Noteer de bestaande overschrijvingen die zijn toegevoegd.
   
4. Als u de overschrijvingen voor zowel volledige import als volledige synchronisatie op een willekeurige connector wilt verwijderen, voert u de volgende cmdlet uit:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Als u de overschrijvingen van alle connectors wilt verwijderen, voert u het volgende PowerShell-script uit:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Voer de volgende cmdlet uit om de planninger te hervatten:`Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Vergeet niet om de vereiste synchronisatiestappen zo snel mogelijk uit te voeren. U deze stappen handmatig uitvoeren met de synchronisatieservicemanager of de overschrijvingen toevoegen met de cmdlet Set-ADSyncSchedulerConnectorOverride.

Als u de overschrijvingen voor zowel volledige import als volledige synchronisatie op een willekeurige connector wilt toevoegen, voert u de volgende cmdlet uit:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Problemen oplossen
In de volgende sectie vindt u probleemoplossing en informatie die u gebruiken als u een probleem tegenkomt bij het upgraden van Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Er ontbreekt fout in Azure Active Directory-connector tijdens azure AD Connect-upgrade

Wanneer u Azure AD Connect upgradet vanaf een vorige versie, u aan het begin van de upgrade de volgende fout raken 

![Fout](./media/how-to-upgrade-previous-version/error1.png)

Deze fout treedt op omdat de Azure Active Directory-connector met id, b891884f-051e-4a83-95af-2544101c9083, niet bestaat in de huidige Azure AD Connect-configuratie. Als u wilt controleren of dit het geval is, opent u een PowerShell-venster en voert u Cmdlet uit`Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

De PowerShell-cmdlet rapporteert de fout **dat de opgegeven MA niet kan worden gevonden.**

De reden dat dit gebeurt is omdat de huidige Azure AD Connect-configuratie niet wordt ondersteund voor een upgrade. 

Als u een nieuwere versie van Azure AD Connect wilt installeren: sluit de wizard Azure AD Connect, verwijder de bestaande Azure AD Connect en voer een schone installatie uit van de nieuwere Azure AD Connect.



## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).

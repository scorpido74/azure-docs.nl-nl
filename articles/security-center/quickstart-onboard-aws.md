---
title: Verbind uw AWS-account met Azure Security Center
description: Uw AWS-resources bewaken vanuit Azure Security Center
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 33ff6748eba9d0349df244e70f07ad178f258373
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250281"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Verbind uw AWS-accounts met Azure Security Center

Met Cloud werkbelastingen die doorgaans meerdere Cloud platforms bespannen, moeten Cloud beveiligings Services hetzelfde doen.

Azure Security Center beschermt werk belastingen in azure, Amazon Web Services (AWS) en Google Cloud Platform (GCP).

U kunt uw AWS-account in Security Center opdoor nemen, integreert AWS Security hub en Azure Security Center. Security Center biedt daarom inzicht in en bescherming van beide Cloud omgevingen om het volgende te bieden:

- Automatic agent Provisioning (Security Center gebruikt [Azure Arc](../azure-arc/servers/overview.md) om de log Analytics agent te implementeren voor uw AWS-instanties)
- Beleidsbeheer
- Beheer van beveiligingsproblemen
- Detectie en antwoord van Inge sloten eind punten (EDR)
- Detectie van onjuiste configuratie van beveiliging
- Er is één weer gave met Security Center aanbevelingen en AWS van de Security hub
- Incorporatie van uw AWS-resources in de berekening van de veilige Score van Security Center
- Nalevings beoordeling van de regelgeving van uw AWS-resources

In de onderstaande scherm afbeelding ziet u de AWS-accounts die worden weer gegeven in het dash board overzicht van Security Center.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="3 GCP-projecten die worden vermeld op het overzichts Dashboard van Security Center" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Preview|
|Koers|[Azure Defender voor servers](defender-for-servers-introduction.md) vereist|
|Vereiste rollen en machtigingen:|**Eigenaar** of **bijdrager** op het relevante Azure-abonnement|
|Clouds|![Yes](./media/icons/yes-icon.png) Commerciële Clouds<br>![No](./media/icons/no-icon.png) National/soeverein (US Gov, China gov, andere gov)|
|||



## <a name="connect-your-aws-account"></a>Verbinding maken met uw AWS-account

### <a name="step-1-set-up-aws-security-hub"></a>Stap 1. AWS Security hub instellen:

1. Als u beveiligings aanbevelingen voor meerdere regio's wilt weer geven, herhaalt u de volgende stappen voor elke relevante regio.

    > [!IMPORTANT]
    > Als u een AWS-Master account gebruikt, herhaalt u de volgende drie stappen om het hoofd account en alle verbonden leden accounts voor alle relevante regio's te configureren

    1. Schakel [AWS-configuratie](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)in.
    1. Schakel [AWS Security hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html)in.
    1. Controleer of er gegevens stromen naar de Security hub zijn.

        Wanneer u Security hub voor het eerst inschakelt, kan het enkele uren duren voordat gegevens beschikbaar zijn.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>Stap 2. Verificatie instellen voor Security Center in AWS

Er zijn twee manieren om Security Center te verifiëren bij AWS:

- **Een iam-rol maken voor Security Center** : dit is de veiligste methode en wordt aanbevolen
- **AWS-gebruiker voor Security Center** : een minder veilige optie als u iam niet hebt ingeschakeld

#### <a name="create-an-iam-role-for-security-center"></a>Een IAM-rol maken voor Security Center
1. Selecteer in uw Amazon Web Services-console onder **beveiliging, identiteit & naleving**de optie **iam**.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS Services":::

1. Selecteer **rollen** en **Maak een rol**.
1. Selecteer **een ander AWS-account**.
1. Voer de volgende details in:

    - **Account-id** : Voer de id van het micro soft-account (**158177204117**) in, zoals wordt weer gegeven op de pagina AWS-connector in Security Center.
    - **Externe ID vereisen** : moet worden geselecteerd
    - **Externe ID** : Voer de abonnements-id in, zoals wordt weer gegeven op de pagina AWS-connector in Security Center 

1. Selecteer **Next**.
1. Selecteer in de sectie **machtigings beleid koppelen** de volgende beleids regels:

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. Voeg eventueel tags toe. Het toevoegen van labels aan de gebruiker heeft geen invloed op de verbinding.
1. Selecteer **Next**.

1. Kies in de lijst rollen de rol die u hebt gemaakt

1. Sla de naam van de Amazon-resource (ARN) op voor later. 

#### <a name="create-an-aws-user-for-security-center"></a>Een AWS-gebruiker maken voor Security Center 
1. Open het tabblad **gebruikers** en selecteer **gebruiker toevoegen**.
1. Voer in de stap **Details** een gebruikers naam in voor Security Center en zorg ervoor dat u **programmatische toegang** selecteert voor het toegangs type AWS. 
1. Selecteer **Next Permissions**.
1. Selecteer **bestaande beleids regels rechtstreeks koppelen** en pas het volgende beleid toe:
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Selecteer **Volgende: Tags**. Voeg eventueel tags toe. Het toevoegen van labels aan de gebruiker heeft geen invloed op de verbinding.
1. Selecteer **beoordeling**.
1. Sla de automatisch gegenereerde **toegangs sleutel-id** en het CSV-bestand met **geheime toegang** op voor later.
1. Bekijk de samen vatting en klik op **gebruiker maken**.


### <a name="step-3-configure-the-ssm-agent"></a>Stap 3. De SSM-agent configureren

AWS Systems Manager is vereist voor het automatiseren van taken in uw AWS-resources. Als uw EC2-instanties niet beschikken over de SSM-agent, volgt u de relevante instructies van Amazon:

- [SSM-agent installeren en configureren op Windows-instanties](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [SSM-agent installeren en configureren op Amazon EC2 Linux-instanties](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-create-a-service-principal-for-onboarding-at-scale"></a>Stap 4. Een service-principal maken voor onboarding op schaal

Als **eigenaar** van het abonnement dat u voor de onboarding wilt gebruiken, maakt u een service-principal voor Azure Arc-onboarding zoals beschreven in [een service-principal maken voor onboarding op schaal](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)


### <a name="step-5-connect-aws-to-security-center"></a>Stap 5. AWS verbinden met Security Center

1. Selecteer in het menu van Security Center de optie **multi Cloud-connectors**.
1. Selecteer **AWS-account toevoegen**.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Knop AWS-account toevoegen op de pagina multi Cloud connectors van Security Center":::
1. Configureer de opties op het tabblad **AWS-verificatie** :
    1. Voer een **weergave naam** in voor de connector.
    1. Controleer of het abonnement juist is. Het is het abonnement waarin de aanbevelingen voor de connector en de AWS-beveiligings hub worden meegenomen.
    1. Afhankelijk van de verificatie optie die u in [stap 2 hebt gekozen. Verificatie instellen voor Security Center in AWS](#step-2-set-up-authentication-for-security-center-in-aws):
        - Selecteer  **rol aannemen** en plak de ARN [voor het maken van een iam-rol voor het Security Center](#create-an-iam-role-for-security-center) :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="plakken van het Arn-bestand in het relevante veld van de wizard AWS-verbinding in azure Portal":::

            OF

        - Selecteer **referenties** en plak de **toegangs sleutel** en **geheime sleutel** uit het CSV-bestand dat u hebt opgeslagen in [een AWS-gebruiker maken voor Security Center](#create-an-aws-user-for-security-center).
1. Selecteer **Next**.
1. Configureer de opties op het tabblad **Azure-Arc-configuratie** :

    Security Center detecteert de EC2-instanties in het verbonden AWS-account en gebruikt SSM om ze op de Azure-Arc toe te staan. 

    > [!TIP]
    > De lijst met ondersteunde besturings systemen vindt u in de veelgestelde vragen hieronder.

    1. Selecteer de **resource groep** en de **Azure-regio** waarin de gedetecteerde AWS-EC2s worden uitgevoerd in het geselecteerde abonnement.
    1. Voer de **Service-Principal-id** en het **Service-Principal-client geheim** in voor Azure Arc, zoals hier wordt beschreven, [een service-principal maken voor onboarding op schaal](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. Als de computer verbinding maakt met Internet via een proxy server, geeft u het IP-adres van de proxy server of de naam en het poort nummer op dat door de computer wordt gebruikt om te communiceren met de proxy server. Voer de waarde in de notatie in ```http://<proxyURL>:<proxyport>```
    1. Selecteer **Controleren + maken**.

        Bekijk de samenvattings informatie

        In de secties Tags worden alle Azure-Tags vermeld die automatisch worden gemaakt voor elke onboarded-EC2 met de eigen relevante gegevens, zodat deze eenvoudig kunnen worden herkend in Azure. 

        Meer informatie over Azure Tags in [Tags gebruiken om uw Azure-resources en-beheer hiërarchie te organiseren](../azure-resource-manager/management/tag-resources.md).

### <a name="step-7-confirmation"></a>Stap 7. Bevestiging

Wanneer de connector is gemaakt en de AWS Security hub correct is geconfigureerd:

- Security Center scant de omgeving voor AWS EC2-instanties, waardoor ze worden geboardd naar Azure Arc, zodat de Log Analytics agent kan worden geïnstalleerd en aanbevelingen voor beveiliging tegen bedreigingen en beveiliging kan worden geboden. 
- De ASC-service scant elke 6 uur op nieuwe AWS EC2-instanties en onboarding op basis van de configuratie.
- De AWS CIS-standaard wordt weer gegeven in het nalevings Dashboard van de Security Center.
- Als Security hub-beleid is ingeschakeld, worden aanbevelingen weer gegeven in de Security Center Portal en het regelgevings dashboard 5-10 minuten nadat de onboarding is voltooid.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="AWS-resources en-aanbevelingen op de pagina aanbevelingen van Security Center":::



## <a name="monitoring-your-aws-resources"></a>Uw AWS-resources bewaken

Zoals hierboven wordt weer gegeven, worden in de pagina beveiligings aanbevelingen van Azure Security Center uw AWS-resources samen met uw Azure-en GCP-resources weer gegeven voor een echte weer gave met meerdere clouds.

Als u alle actieve aanbevelingen voor uw resources op resource type wilt weer geven, gebruikt u de pagina Asset Inventory van Security Center en filtert u op het resource type AWS waarin u bent geïnteresseerd:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Het resource type filter van de activa-inventaris pagina met de opties voor AWS"::: 


## <a name="aws-in-security-center-faq"></a>AWS in Security Center Veelgestelde vragen

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Welke besturings systemen voor mijn EC2-instanties worden ondersteund?

Ondersteund besturings systeem voor automatische onboarding naar Azure Arc voor AWS-machines

- Ubuntu 16,04-SSM-agent is standaard vooraf geïnstalleerd
- Ubuntu 18,04-SSM-agent is standaard vooraf geïnstalleerd
- Windows Server-SSM agent is standaard vooraf geïnstalleerd
- CentOS Linux 7 – SSM moet hand matig of onboardend worden geïnstalleerd
- SUSE Linux Enterprise Server (SLES) 15 (x64)-SSM moet hand matig of onboardd worden geïnstalleerd
- Red Hat Enterprise Linux (RHEL) 7 (x64)-SSM moet hand matig of onboardd worden geïnstalleerd


## <a name="next-steps"></a>Volgende stappen

Verbinding maken met uw AWS-account maakt deel uit van de ervaring voor meerdere clouds die beschikbaar is in Azure Security Center. Zie de volgende pagina voor verwante informatie:

- [Verbind uw GCP-accounts met Azure Security Center](quickstart-onboard-gcp.md)

---
title: Verbind uw GCP-account met Azure Security Center
description: Uw GCP-resources bewaken vanuit Azure Security Center
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d5f478c279eb4c07332d047935f7c5dab89fd4b7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936524"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Verbind uw GCP-accounts met Azure Security Center

Met Cloud werkbelastingen die doorgaans meerdere Cloud platforms bespannen, moeten Cloud beveiligings Services hetzelfde doen.

Azure Security Center beschermt werk belastingen in azure, Amazon Web Services (AWS) en Google Cloud Platform (GCP).

Als u uw GCP-account inbereidt in Security Center, integreert u GCP Security-opdracht en Azure Security Center. Security Center biedt daarom inzicht in en bescherming van beide Cloud omgevingen om het volgende te bieden:

- Detectie van onjuiste configuratie van beveiliging
- Een enkele weer gave met Security Center aanbevelingen en GCP Security Center-bevindingen
- Incorporatie van uw GCP-resources in de berekening van de veilige Score van Security Center
- Integratie van GCP Security Command Center-aanbevelingen op basis van de CIS-standaard in het nalevings Dashboard van de Security Center

In de onderstaande scherm afbeelding ziet u GCP-projecten die worden weer gegeven in het dash board overzicht van Security Center.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 GCP-projecten die worden vermeld op het overzichts Dashboard van Security Center" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Preview|
|Koers|[Azure Defender voor servers](defender-for-servers-introduction.md) vereist|
|Vereiste rollen en machtigingen:|**Eigenaar** of **bijdrager** op het relevante Azure-abonnement|
|Clouds|![Yes](./media/icons/yes-icon.png) Commerciële Clouds<br>![No](./media/icons/no-icon.png) National/soeverein (US Gov, China gov, andere gov)|
|||

## <a name="connect-your-gcp-account"></a>Verbinding maken met uw GCP-account

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Stap 1. GCP Security-opdracht centrum instellen met Security Health Analytics

Voor alle GCP-projecten in uw organisatie moet u ook het volgende doen:

1. **GCP Security-opdracht centrum** instellen met behulp [van deze instructies in de GCP-documentatie](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup).
1. Gebruik [deze instructies in de GCP-documentatie om de analyse van](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics) **beveiligings status** in te scha kelen.
1. Controleer of er gegevens stromen naar het Security-opdracht centrum.

De instructies voor het verbinden van uw GCP-omgeving voor de beveiligings configuratie volgen de aanbevelingen van Google voor het gebruiken van aanbevelingen voor beveiligings configuraties. De integratie maakt gebruik van het Google Security-opdracht centrum en gebruikt extra resources die van invloed kunnen zijn op uw facturering.

Wanneer u de beveiligings status analyse voor het eerst inschakelt, kan het enkele uren duren voordat gegevens beschikbaar zijn.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Stap 2. GCP Security Command Center-API inschakelen

1. Selecteer in de **Cloud console-API-bibliotheek**van Google het project waarmee u verbinding wilt maken Azure Security Center.
1. Zoek en selecteer in de API-bibliotheek **Security Command Center-API**.
1. Selecteer op de pagina van de API de optie **inschakelen**.

Meer informatie over de [Security Command Center-API](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Stap 3. Een toegewezen service account maken voor de integratie van de beveiligings configuratie

1. Selecteer in de **GCP-console**het project waarmee u verbinding wilt maken Security Center.
1. Selecteer **service accounts**onder **iam &-beheer** opties in het **navigatie menu**.
1. Selecteer **Service account maken**.
1. Voer een account naam in en selecteer **maken**.
1. Geef de **rol** op als **Security Center beheer Viewer**en selecteer **door gaan**.
1. De sectie **gebruikers toegang verlenen tot dit service account** is optioneel. Selecteer **Gereed**.
1. Kopieer de **e-mail waarde** van het gemaakte service account en sla deze op voor later gebruik.
1. Selecteer in het **navigatie menu**onder **iam &-beheer** opties de optie **iam**
    1. Overschakelen naar organisatie niveau.
    1. Selecteer **toevoegen**.
    1. Plak in het veld **nieuwe leden** de **e-mail waarde** die u eerder hebt gekopieerd.
    1. Geef de rol op als **Security Center beheer Viewer** en selecteer vervolgens opslaan.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="De relevante GCP-machtigingen instellen":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Stap 4. Een persoonlijke sleutel voor het toegewezen service account maken
1. Overschakelen naar project niveau.
1. Selecteer **service accounts**onder **iam &-beheer** opties in het **navigatie menu**.
1. Open het toegewezen service account en selecteer Bewerken.
1. Selecteer in de sectie **sleutels** de optie **sleutel toevoegen** en vervolgens **nieuwe sleutel maken**.
1. Selecteer **JSON**in het scherm persoonlijke sleutel maken en selecteer vervolgens **maken**.
1. Sla dit JSON-bestand op voor later gebruik.


### <a name="step-5-connect-gcp-to-security-center"></a>Stap 5. GCP verbinden met Security Center 
1. Selecteer in het menu van de Security Center **Cloud connectors**.
1. Selecteer GCP-account toevoegen.
1. Ga op de pagina onboarding als volgt te werk en selecteer **volgende**.
    1. Valideer het gekozen abonnement.
    1. Voer in het veld **weergave naam** een weergave naam in voor de connector.
    1. Voer in het veld **organisatie-id** de id van uw organisatie in. Zie [organisaties maken en beheren](https://cloud.google.com/resource-manager/docs/creating-managing-organization)als u deze niet kent.
    1. Blader in het vak **persoonlijk sleutel** bestand naar het JSON-bestand dat u in [stap 4 hebt gedownload. Maak een persoonlijke sleutel voor het toegewezen service account](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>Stap 6. Bevestiging

Wanneer de connector is gemaakt en het GCP Security-opdracht centrum correct is geconfigureerd:

- De GCP CIS-standaard wordt weer gegeven in het nalevings Dashboard van de Security Center.
- Beveiligings aanbevelingen voor uw GCP-resources worden weer gegeven in de Security Center Portal en het regelgevings dashboard 5-10 minuten nadat het onboard is voltooid:   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="GCP bronnen en aanbevelingen op de pagina met aanbevelingen voor Security Center":::


## <a name="monitoring-your-gcp-resources"></a>Uw GCP-resources bewaken

Zoals hierboven wordt weer gegeven, worden in de pagina beveiligings aanbevelingen van Azure Security Center uw GCP-resources samen met uw Azure-en AWS-resources weer gegeven voor een echte weer gave met meerdere clouds.

Als u alle actieve aanbevelingen voor uw resources op resource type wilt weer geven, gebruikt u de pagina Asset Inventory van Security Center en filtert u op het resource type GCP waarin u bent geïnteresseerd:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="Het resource type filter van de activa-inventaris pagina met de opties voor GCP"::: 


## <a name="next-steps"></a>Volgende stappen

Verbinding maken met uw GCP-account maakt deel uit van de ervaring voor meerdere clouds die beschikbaar is in Azure Security Center. Zie de volgende pagina voor verwante informatie:

- [Verbind uw AWS-accounts met Azure Security Center](quickstart-onboard-aws.md)

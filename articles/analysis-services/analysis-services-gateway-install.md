---
title: On-premises gegevensgateway installeren voor Azure Analysis Services | Microsoft Documenten
description: Meer informatie over het installeren en configureren van een on-premises gegevensgateway om verbinding te maken met on-premises gegevensbronnen van een Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f6218b32fb9574adf62384d2a6ee5a62f3788de8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062146"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Een on-premises gegevensgateway installeren en configureren

Er is een on-premises gegevensgateway vereist wanneer een of meer Azure Analysis Services-servers in dezelfde regio verbinding maken met on-premises gegevensbronnen.  Hoewel de gateway die u installeert hetzelfde is als die van andere services zoals Power BI, Power Apps en Logic Apps, zijn er enkele aanvullende stappen die u moet uitvoeren. Dit installatieartikel is specifiek voor **Azure Analysis Services**. 

Zie [Verbinding maken met on-premises gegevensbronnen](analysis-services-gateway.md)voor meer informatie over hoe Azure Analysis Services met de gateway werkt. Zie [On-premises documentatie over gegevensgateways](/data-integration/gateway/service-gateway-onprem)voor meer informatie over geavanceerde installatiescenario's en de gateway in het algemeen.

## <a name="prerequisites"></a>Vereisten

**Minimale vereisten**

* .NET 4.5 Framework
* 64-bits versie van Windows 8 / Windows Server 2012 R2 (of hoger)

**Aanbevolen:**

* 8-core CPU
* 8 GB geheugen
* 64-bits versie van Windows 8 / Windows Server 2012 R2 (of hoger)

**Belangrijke overwegingen:**

* Tijdens het instellen wordt bij het registreren van uw gateway met Azure het standaardgebied voor uw abonnement geselecteerd. U een ander abonnement en een andere regio kiezen. Als u servers in meer dan één regio hebt, moet u voor elke regio een gateway installeren. 
* De gateway kan niet worden geïnstalleerd op een domeincontroller.
* Er kan slechts één gateway op één computer worden geïnstalleerd.
* Installeer de gateway op een computer die blijft ingeschakeld en gaat niet slapen.
* Installeer de gateway niet op een computer met een draadloze verbinding met alleen uw netwerk. De prestaties kunnen worden verminderd.
* Bij het installeren van de gateway moet het gebruikersaccount waarmee u bent aangemeld bij uw computer zich moeten aanmelden als servicebevoegdheden hebben. Wanneer de installatie is voltooid, gebruikt de on-premises datagatewayservice het NT SERVICE\PBIEgwService-account om zich als service aan te melden. Een ander account kan worden opgegeven tijdens de installatie of in Services nadat de installatie is voltooid. Als u de instellingen voor groepsbeleid hebt, u ervoor zorgen dat zowel het account waarmee u bent aangemeld bij de installatie als het serviceaccount dat u kiest, aanmelden als servicerechten hebben.
* Meld u aan bij Azure met een account in Azure AD voor dezelfde [tenant](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) als het abonnement waarin u de gateway registreert. Azure B2B-accounts (gast)accounts worden niet ondersteund bij het installeren en registreren van een gateway.
* Als gegevensbronnen zich op een Extern Azure Network (VNet) bevinden, moet u de eigenschap [AlwaysUseGateway-server](analysis-services-vnet-gateway.md) configureren.

## <a name="download"></a><a name="download"></a>Download

 [De gateway downloaden](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a><a name="install"></a>Installeren

1. Voer de installatie uit.

2. Selecteer **On-premises gegevensgateway**.

   ![Selecteer](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Selecteer een locatie, accepteer de voorwaarden en klik op **Installeren**.

   ![Locatie- en licentievoorwaarden installeren](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Meld u aan bij Azure. Het account moet zich in de Azure Active Directory van uw tenant bevinden. Dit account wordt gebruikt voor de gatewaybeheerder. Azure B2B-accounts (gast)accounts worden niet ondersteund bij het installeren en registreren van de gateway.

   ![Aanmelden bij Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Als u zich aanmeldt met een domeinaccount, wordt het toegewezen aan uw organisatieaccount in Azure AD. Uw organisatieaccount wordt gebruikt als gatewaybeheerder.

## <a name="register"></a><a name="register"></a>Registreren

Als u een gatewaybron in Azure wilt maken, moet u de lokale instantie registreren die u hebt geïnstalleerd met de Gateway Cloud Service. 

1.  Selecteer **Een nieuwe gateway registreren op deze computer**.

    ![Registreren](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Typ een naam en herstelsleutel voor uw gateway. Standaard maakt de gateway gebruik van het standaardgebied van uw abonnement. Als u een ander gebied wilt selecteren, selecteert u **Regio wijzigen**.

    > [!IMPORTANT]
    > Bewaar uw herstelsleutel op een veilige plaats. De herstelsleutel is vereist om een gateway te kunnen overnemen, migreren of herstellen. 

   ![Registreren](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a><a name="create-resource"></a>Een Azure-gatewaybron maken

Nadat u uw gateway hebt geïnstalleerd en geregistreerd, moet u een gatewaybron maken in Azure. Meld u aan bij Azure met hetzelfde account dat u hebt gebruikt bij het registreren van de gateway.

1. Klik in Azure-portal op **Een resource maken,** zoek vervolgens naar **on-premises gegevensgateway**en klik vervolgens op **Maken**.

   ![Een gatewaybron maken](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Voer in **Verbindingsgateway maken**de volgende instellingen in:

   * **Naam:** Voer een naam in voor uw gatewaybron. 

   * **Abonnement:** selecteer het Azure-abonnement dat u wilt koppelen aan uw gatewaybron. 
   
     Het standaardabonnement is gebaseerd op het Azure-account dat u hebt gebruikt om u aan te melden.

   * **Resourcegroep**: maak een resourcegroep of selecteer een bestaande resourcegroep.

   * **Locatie:** selecteer het gebied waarin u uw gateway hebt geregistreerd.

   * **Installatienaam:** Als uw gateway-installatie nog niet is geselecteerd, selecteert u de gateway die u op uw computer hebt geïnstalleerd en geregistreerd. 

     Als u klaar bent, klikt u op **Maken**.

## <a name="connect-servers-to-the-gateway-resource"></a><a name="connect-servers"></a>Servers verbinden met de gatewaybron

1. Klik in het overzicht van uw Azure Analysis **Services-server op On-Premises Data Gateway**.

   ![Server verbinden met gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Selecteer **in Pick an On-Premises Data Gateway to connect,** selecteer uw gatewaybron en klik op Geselecteerde gateway **verbinden**.

   ![Server verbinden met gatewaybron](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Als uw gateway niet in de lijst wordt weergegeven, bevindt uw server zich waarschijnlijk niet in hetzelfde gebied als het gebied dat u hebt opgegeven bij het registreren van de gateway.

    Wanneer de verbinding tussen uw server en gatewaybron is geslaagd, wordt de status **Verbonden weergegeven.**


    ![Verbinding maken met server met succes gatewaybron](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

Dat is alles. Als u poorten moet openen of problemen moet oplossen, moet u [on-premises gegevensgateway](analysis-services-gateway.md)controleren.

## <a name="next-steps"></a>Volgende stappen

* [Analysis Services beheren](analysis-services-manage.md)   
* [Gegevens ophalen uit Azure Analysis Services](analysis-services-connect.md)   
* [Gateway gebruiken voor gegevensbronnen in een virtueel Azure-netwerk](analysis-services-vnet-gateway.md)

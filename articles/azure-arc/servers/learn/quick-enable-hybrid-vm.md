---
title: Een hybride machine verbinden met servers met Azure Arc
description: Meer informatie over het verbinden en registreren van uw hybride machine met Azure Arc-servers.
ms.topic: quickstart
ms.date: 09/23/2020
ms.openlocfilehash: b57f30821a105a99041d8187716b75096116ea8e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327881"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers"></a>Quickstart: Een hybride machine verbinden met servers met Azure Arc

Met [servers met Azure Arc](../overview.md) kunt u uw Windows- en Linux-computers beheren en regelen die worden gehost in on-premises, edge en multicloud-omgevingen. In deze quickstart implementeert en configureert u de Connected Machine-agent op uw Windows- of Linux-computer die buiten Azure wordt gehost voor beheer door Arc-servers.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* Als u de Hybrid Connected Machine-agent van de Arc-servers implementeert, is vereist dat u over beheerdersmachtigingen voor de computer beschikt om de agent te installeren en configureren. Op Linux, met behulp van het hoofdaccount en op Windows, met een account dat lid is van de lokale beheerdersgroep.

* Controleer voordat u aan de slag gaat de [vereisten](../agent-overview.md#prerequisites) voor de agent en controleer het volgende:

    * Op uw doelcomputer wordt een ondersteund [besturingssysteem](../agent-overview.md#supported-operating-systems) uitgevoerd.

    * Uw account is toegewezen aan de [vereiste Azure-rollen](../agent-overview.md#required-permissions).

    * Als de computer verbinding maakt via een firewall of proxyserver om via internet te communiceren, moet u ervoor zorgen dat de [vermelde](../agent-overview.md#networking-configuration) URL's niet worden geblokkeerd.

    * Servers met Azure Arc ondersteunen alleen de regio's die [hier](../overview.md#supported-regions) worden opgegeven.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Azure-resourceproviders registreren

Servers voor Azure Arc zijn afhankelijk van de volgende Azure-resourceproviders in uw abonnement om deze service te kunnen gebruiken:

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

Registreer deze met behulp van de volgende opdrachten:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Het installatiescript genereren

Het script voor het automatiseren van downloaden, installeren en tot stand brengen van de verbinding met Azure Arc, is beschikbaar via Azure Portal. Ga als volgt te werk om het proces te voltooien:

1. Start de Azure Arc-service in Azure Portal door op **Alle services** te klikken en dan **Machines - Azure Arc** te zoeken en te selecteren.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Arc-servers zoeken in Alle services" border="false":::

1. Selecteer op de pagina **Machines - Azure Arc** de optie **Toevoegen**, linksboven of de optie **Machine maken - Azure Arc** onder in het middelste deelvenster.

1. Selecteer op de pagina **Een methode selecteren** de tegel **Machines toevoegen met interactief script** en selecteer vervolgens **Script genereren**.

1. Selecteer op de pagina **Script genereren** het abonnement en de resourcegroep waarin u de machine wilt beheren binnen Azure. Selecteer een Azure-locatie waar de metagegevens van de machine worden opgeslagen.

1. Selecteer op de pagina **Script genereren** in de vervolgkeuzelijst **Besturingssysteem** het besturingssysteem waarop het script zal worden uitgevoerd.

1. Als de machine communiceert via een proxyserver om verbinding te maken met internet, selecteert u **Volgende: Proxy Server**.

1. Geef op het tabblad **Proxyserver** het IP-adres van de proxyserver of de naam en het poortnummer op die de machine zal gebruiken om met de proxyserver te communiceren. Voer de waarde in de indeling `http://<proxyURL>:<proxyport>` in.

1. Selecteer **Controleren en genereren**.

1. Bekijk op het tabblad **Controleren en genereren** de overzichtsgegevens en selecteer vervolgens **Downloaden**. Als u nog wijzigingen wilt aanbrengen, selecteert u **Vorige**.

## <a name="install-the-agent-using-the-script"></a>De agent installeren met behulp van het script

### <a name="windows-agent"></a>Windows-agent

1. Meld u aan bij de server.

1. Open een 64-bits PowerShell-opdrachtprompt met verhoogde bevoegdheden.

1. Ga naar de map of share waarnaar u het script hebt gekopieerd en voer dit uit op de server door het script `./OnboardingScript.ps1` uit te voeren.

### <a name="linux-agent"></a>Linux-agent

1. Als u de Linux-agent wilt installeren op de doelmachine die rechtstreeks kan communiceren met Azure, voert u de volgende opdracht uit:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Als de doelmachine communiceert via een proxyserver, voert u de volgende opdracht uit:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>De verbinding met Azure Arc controleren

Nadat u de agent hebt geïnstalleerd en geconfigureerd om verbinding te maken met Azure Arc-servers, gaat u naar Azure Portal om te controleren of de server verbinding heeft gemaakt. Bekijk uw machine in [Azure Portal](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Arc-servers zoeken in Alle services" border="false":::

## <a name="next-steps"></a>Volgende stappen

Nu u uw hybride Linux- of Windows-machine hebt ingeschakeld en met succes bent verbonden met de service, bent u klaar om Azure Policy in te schakelen om de naleving in Azure te begrijpen.

Ga verder met de zelfstudie voor meer informatie over het identificeren van een machine waarop Azure Arc is ingeschakeld waarvoor de Log Analytics-agent niet is geïnstalleerd:

> [!div class="nextstepaction"]
> [Een beleidstoewijzing maken om niet-conforme resources te identificeren](tutorial-assign-policy-portal.md)

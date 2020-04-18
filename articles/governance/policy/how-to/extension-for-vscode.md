---
title: Azure-beleidsextensie voor Visual Studio-code
description: Meer informatie over het gebruik van de Azure Policy-extensie voor Visual Studio Code om aliassen van Resource Manager op te zoeken.
ms.date: 03/07/2020
ms.topic: how-to
ms.openlocfilehash: 5e31af652f2746adbf0eda386bdb178c752f5f84
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641027"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Azure-beleidsextensie gebruiken voor Visual Studio Code

> Van toepassing op Azure Policy extension versie **0.0.21** en nieuwer

Meer informatie over het gebruik van de Azure Policy-extensie voor Visual Studio Code om [aliassen](../concepts/definition-structure.md#aliases) op te zoeken en resources en beleidsregels te bekijken. Eerst beschrijven we hoe u de Azure Policy-extensie installeert in Visual Studio Code. Dan gaan we kijken hoe we aliassen kunnen opzoeken.

Azure Policy extension for Visual Studio Code kan worden geïnstalleerd op alle platforms die worden ondersteund door Visual Studio Code. Deze ondersteuning omvat Windows, Linux en macOS.

> [!NOTE]
> Wijzigingen die lokaal zijn aangebracht in het beleid dat wordt weergegeven in de Azure Policy-extensie voor Visual Studio Code, worden niet gesynchroniseerd met Azure.

## <a name="prerequisites"></a>Vereisten

De volgende items zijn vereist voor het voltooien van de stappen in dit artikel:

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Azure-beleidsextensie installeren

Nadat u aan de vereisten hebt voldoen, u azure-beleidsextensie voor Visual Studio Code installeren door de volgende stappen te volgen:

1. Open Visual Studio Code.

1. Ga in de menubalk naar **Extensies weergeven.** > **Extensions**

1. Voer in het zoekvak **Azure Policy**in .

1. Selecteer **Azure-beleid** in de zoekresultaten en selecteer **Installeren**.

1. Selecteer **Opnieuw laden** indien nodig.

## <a name="set-the-azure-environment"></a>De Azure-omgeving instellen

Voer de volgende stappen uit om de Azure-omgeving eerst in te stellen voor een nationale cloudgebruiker:

1. Selecteer **Bestand\Voorkeuren\Instellingen**.

1. Zoeken op de volgende tekenreeks: _Azure: Cloud_

1. Selecteer de nationale cloud in de lijst:

   ![Standaard aanmelden voor Azure-cloud instellen voor Visual Studio Code](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Verbinding maken met een Azure-account

Als u resources en opzoekaliassen wilt evalueren, moet u verbinding maken met uw Azure-account. Volg de volgende stappen om verbinding te maken met Azure vanuit Visual Studio Code:

1. Meld u aan bij Azure via de Azure-beleidsextensie of het opdrachtpalet.

   - Azure-beleidsextensie

     Selecteer **Aanmelden bij Azure in**de azure-beleidsextensie.

     ![Azure-cloud-aanmelding voor Visual Studio Code vanuit Azure-beleidsextensie](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Opdrachtpalet

     Ga in de menubalk naar**Opdrachtpalet** **weergeven** > en voer **Azure in: Aanmelden**.

     ![Azure-cloudaanmelding voor Visual Studio-code vanuit opdrachtpalet](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Volg de aanmeldingsinstructies om u aan te melden bij Azure. Nadat u bent verbonden, wordt uw Azure-accountnaam weergegeven op de statusbalk onder aan het venster Visual Studio Code.

## <a name="select-subscriptions"></a>Abonnementen selecteren

Wanneer u zich voor het eerst aanmeldt, worden alleen de standaardabonnementsbronnen en -beleidsregels geladen door de Azure Policy-extensie. Voer de volgende stappen uit om abonnementen toe te voegen of te verwijderen uit het weergeven van bronnen en beleidsregels:

1. Start de opdracht Abonnement vanuit het opdrachtpalet of de venstervoettekst.

   - Opdrachtpalet: 

     Ga in de menubalk naar**Opdrachtpalet** **weergeven** > en voer **Azure in: Abonnementen selecteren**.

   - Venstervoettekst

     Selecteer in de venstervoettekst onder aan het scherm het segment dat overeenkomt met **Azure: \<uw account\>**.

1. Gebruik het filtervak om snel abonnementen op naam te vinden. Controleer of verwijder vervolgens de controle van elk abonnement om de abonnementen in te stellen die worden weergegeven door de Azure Policy-extensie. Wanneer u abonnementen toevoegt of verwijdert om weer te geven, selecteert u **OK**.

## <a name="search-for-and-view-resources"></a>Bronnen zoeken en weergeven

De Azure Policy-extensie bevat bronnen in de geselecteerde abonnementen per Resourceprovider en op resourcegroep in het deelvenster **Resources.** De treeview bevat de volgende groeperingen van resources binnen het geselecteerde abonnement of op abonnementsniveau:

- **Resourceproviders**
  - Elke geregistreerde resourceprovider met resources en gerelateerde onderliggende bronnen met beleidsaliassen
- **Brongroepen**
  - Alle resources van de resourcegroep waarin ze zich bevindt

Standaard filtert de extensie het gedeelte 'Resourceprovider' op bestaande resources en resources met beleidsaliassen. Wijzig dit gedrag in Azure**Policy**  > **Instellingen-extensies** > om alle resourceproviders te bekijken zonder te filteren. **Settings**

Klanten met honderden of duizenden resources in één abonnement geven mogelijk de voorkeur aan een doorzoekbare manier om hun bronnen te vinden. De Azure Policy-extensie maakt het mogelijk om met de volgende stappen naar een specifieke bron te zoeken:

1. Start de zoekinterface vanuit de Azure Policy extension of het Command Palette.

   - Azure-beleidsextensie

     Plaats in de azure-beleidsextensie de plaats boven het deelvenster **Resources** en selecteer de ellips en selecteer **Vervolgens Zoekbronnen**.

   - Opdrachtpalet:

     Ga in de menubalk naar **Opdrachtpalet** **weergeven** > en typ **Resources: Zoekbronnen**.

1. Als er meer dan één abonnement is geselecteerd voor weergave, gebruikt u het filter om te selecteren welk abonnement u wilt zoeken.

1. Gebruik het filter om te selecteren welke resourcegroep u wilt doorzoeken die deel uitmaakt van het eerder gekozen abonnement.

1. Gebruik het filter om te selecteren welke resource u wilt weergeven. Het filter werkt voor zowel de resourcenaam als het resourcetype.

## <a name="discover-aliases-for-resource-properties"></a>Aliassen voor resourceeigenschappen ontdekken

Wanneer een resource is geselecteerd, hetzij via de zoekinterface of door deze te selecteren in de structuurweergave, opent de Azure Policy-extensie het JSON-bestand dat die resource en alle eigenschapswaarden van Resource Manager vertegenwoordigt.

Zodra een resource is geopend, wordt de azure-beleidsalias als er een bestaat, weergegeven als de naam of waarde van de resource beheer de naam of waarde van het Azure-beleid weergeeft. In dit voorbeeld is `Microsoft.Compute/virtualMachines` de resource een resourcetype en wordt de eigenschap **properties.storageProfile.imageReference.offer** overzweven. Als u met de boventoon de overeenkomende aliassen ziet.

![Azure Policy extension hover toont Eigenschapalias Resource Manager](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Beleid en toewijzingen zoeken en weergeven

De azure-beleidsextensie bevat beleidstypen en beleidstoewijzingen als een structuurweergave voor de geselecteerde abonnementen die zijn geselecteerd om te worden weergegeven in het deelvenster **Beleid.** Klanten met honderden of duizenden beleidsregels of opdrachten in één abonnement kunnen de voorkeur geven aan een doorzoekbare manier om hun beleid of opdrachten te vinden. De Azure Policy-extensie maakt het mogelijk om met de volgende stappen naar een specifiek beleid of toewijzing te zoeken:

1. Start de zoekinterface vanuit de Azure Policy extension of het Command Palette.

   - Azure-beleidsextensie

     Plaats in de azure-beleidsextensie de plaats boven het deelvenster **Beleid** en selecteer de ellips en selecteer **vervolgens Zoekbeleid**.

   - Opdrachtpalet:

     Ga in de menubalk naar **Opdrachtpalet** **weergeven** > en voer **Beleid in: Zoekbeleid**.

1. Als er meer dan één abonnement is geselecteerd voor weergave, gebruikt u het filter om te selecteren welk abonnement u wilt zoeken.

1. Gebruik het filter om te selecteren welk beleidstype of -toewijzing moet worden gezocht dat deel uitmaakt van het eerder gekozen abonnement.

1. Gebruik het filter om te selecteren welk beleid of welk beleid u wilt weergeven. Het filter werkt voor _displayName_ voor de beleidsdefinitie of beleidstoewijzing.

Wanneer u een beleid of toewijzing selecteert, hetzij via de zoekinterface of door deze te selecteren in de structuurweergave, opent de Azure Policy-extensie de JSON die het beleid of de toewijzing en alle eigenschapswaarden van Resource Manager vertegenwoordigt. De extensie kan het geopende JSON-schema voor Azure Policy valideren.

## <a name="sign-out"></a>Afmelden

Ga in de menubalk naar**Opdrachtpalet** **weergeven** > en voer **Azure in: Afmelden**.

## <a name="next-steps"></a>Volgende stappen

- Voorbeelden bekijken bij [Azure Policy-voorbeelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Begrijpen hoe [u programmatisch beleid maken.](programmatically-create.md)
- Meer informatie over het [herstellen van niet-conforme resources.](remediate-resources.md)
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)
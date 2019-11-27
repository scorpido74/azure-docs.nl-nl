---
title: Gebruikersprofiel en -ID voor gebruik met Azure-laptops
description: Uw gebruikers profiel en gebruikers-ID maken en beheren met Azure Notebooks, dat onderdeel wordt van de URL van gedeelde notitie blokken.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 0874fe8223ae9c49ccfe4e8efedf2620117a0f8c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277611"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Uw profiel en gebruikers-ID voor Azure-laptops

In de ruimte die krachtige, samenwerking van Azure-notitieblokken wordt uw gebruikersprofiel anderen uw openbare installatiekopie:

[![een Azure Notebooks profiel pagina](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Uw gebruikers-ID is onderdeel van de URL's die u gebruiken voor het delen van projecten en notitieblokken. De volgende lijst beschrijft de verschillende URL-patronen:

- `https://notebooks.azure.com/<user_id>`: de profiel pagina.
- `https://notebooks.azure.com/<user_id>/projects`: uw projecten. U ziet alle projecten. andere gebruikers zien alleen uw openbare projecten.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: project bestanden.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: klonen van een bepaalde projecten.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: de HTML-preview van een specifiek notitie blok of bestand.

## <a name="your-user-id"></a>Uw gebruikers-ID

Wanneer voor de eerste keer aanmeldt bij Azure-laptops, wordt een tijdelijke gebruikers-ID, zoals "anon-idr3ca" automatisch toegewezen door uw account. Als u een gebruikers-ID die begint hebt met "anon-', Azure-notitieblokken vraagt u om dit te wijzigen wanneer u zich aanmeldt:

![Prompt voor het maken van een gebruikers-ID bij het aanmelden bij Azure-laptops](media/accounts/create-user-id.png)

De opdracht **gebruikers-id configureren** wordt ook weer gegeven naast de tijdelijke gebruikers naam:

![Gebruikers-ID-opdracht die wordt weergegeven wanneer u een tijdelijke-ID configureren](media/accounts/configure-user-id-command.png)

U kunt ook uw gebruikers-ID op elk gewenst moment wijzigen op uw profielpagina.

Een gebruikers-ID moet bestaan uit vier tot zestien letters, cijfers en afbreek streepjes. Er zijn geen andere tekens zijn toegestaan en de gebruikers-ID mag niet beginnen of eindigen met een afbreekstreepje of gebruikmaken van meerdere opeenvolgende afbreekstreepjes in een rij. Omdat gebruikers-Id's uniek zijn voor alle Azure Notebooks accounts, ziet u mogelijk het bericht ' de gebruikers-ID is al in gebruik '. (Het bericht wordt ook weer gegeven als u een micro soft-handels merk wilt gebruiken als een gebruikers-ID). In deze gevallen kiest u een andere gebruikers-ID.

> [!Important]
> Bijgewerkt met de ID wordt ongeldig alle URL's die u mogelijk hebt gedeeld met uw vorige ID. Terug naar de vorige-ID voor het valideren van de koppelingen kunt u uw ID. Het is echter mogelijk dat een andere gebruiker voor een niet-gebruikte claim in de tussentijd de ID.

## <a name="your-profile"></a>Uw profiel

Uw profiel bestaat uit openbaar zicht bare informatie op de URL, `https://notebooks.azure.com/<user_id>`. Uw profielpagina ziet ook uw recent gebruikte projecten en projecten met een ster.

Als u uw profiel wilt bewerken, gebruikt u de opdracht **Profiel gegevens bewerken** op de profiel pagina. De secties van het profiel zijn als volgt:

| Sectie | Inhoud |
| --- | --- |
| Profielfoto | Een afbeelding die wordt weergegeven op uw profielpagina. |
| Accountgegevens | Uw weergavenaam, gebruikers-ID en openbare e-mailaccount. Het e-mail account biedt andere gebruikers een gemiddelde om contact met u op te nemen en kan afwijken van het [account](azure-notebooks-user-account.md) dat u gebruikt om u aan te melden Azure notebooks zichzelf. |
| Profielgegevens | Uw locatie, bedrijf, functietitel, website en een korte beschrijving van uzelf. |
| Sociale-profielen | Uw GItHub, Twitter en Facebook-ID's, als u wilt delen. |
| Privacyinstellingen | Biedt twee opdrachten uit:<ul><li>**Mijn profiel exporteren**: maakt en downloadt een *zip* -bestand met alle informatie die Azure notebooks opgeslagen in uw profiel, inclusief uw foto, profiel gegevens en beveiligings Logboeken.</li><li>**Mijn account verwijderen**: alle persoonlijke gegevens die zijn opgeslagen in azure notebooks, worden permanent verwijderd.</li></ul> |
| Sitefuncties inschakelen | Kunt u aspecten van het gedrag van Azure-notitieblokken te bepalen:<ul><li>**Unified frontend voor notebooks**: Hiermee kunt u sneller opstarten van notebooks en betere persistentie.</li><li>Wordt **standaard uitgevoerd in jjupyterlab**: Azure notebooks biedt standaard een eenvoudige gebruikers interface die geschikt is voor de meeste gebruikers. Jjupyterlab biedt een uitgebreidere maar meer gecompliceerde interface voor ervaren gebruikers.</li><li>**VNext-website**: Hiermee wordt de moderne webindeling ingeschakeld die in deze documentatie wordt weer gegeven.</li></ul> |

## <a name="next-steps"></a>Volgende stappen  

> [!div class="nextstepaction"]
> [Zelf studie: een Jupyter-notitie blok maken om een lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)

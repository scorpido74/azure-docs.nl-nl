---
title: 'Zelf studie: de insluitende lezer starten met behulp van python'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie maakt u een python-toepassing waarmee de insluitende lezer wordt gestart.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.openlocfilehash: a252afae0a007ee0b791b56d19ffb0685848d30a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844357"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Zelf studie: de insluitende lezer starten met behulp van het python-voorbeeld project

In het [overzicht](./overview.md)hebt u geleerd wat de insluitende lezer is en hoe deze de bewezen technieken implementeert om de Lees vaardigheid te verbeteren voor taal kennis, opkomende lezers en studenten met meer informatie. In deze zelf studie wordt beschreven hoe u een python-webtoepassing maakt waarmee de insluitende lezer wordt gestart. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Python-web-app maken met PIP, fles, jinja en virtualenv met behulp van een voorbeeld project
> * Een toegangstoken verkrijgen
> * De insluitende lezer starten met voorbeeld inhoud

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een resource voor insluitende lezer die is geconfigureerd voor Azure Active Directory authenticatie. Volg [deze instructies om de](./how-to-create-immersive-reader.md) instellingen op te halen. U hebt enkele van de waarden nodig die u hier hebt gemaakt bij het configureren van de eigenschappen van de omgeving. Sla de uitvoer van uw sessie op in een tekst bestand voor toekomstig naslag doeleinden.
* [Git](https://git-scm.com/)
* [Insluitende lezer-SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) en [PIP](https://docs.python.org/3/installing/index.html). Vanaf python 3,4 is PIP standaard opgenomen in de binaire python-installatie Programma's.
* [Erlenmeyer](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) en [virtualenvwrapper-win voor Windows](https://pypi.org/project/virtualenvwrapper-win/) of [virtualenvwrapper voor OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [module aanvragen](https://pypi.org/project/requests/2.7.0/)
* Een IDE zoals [Visual Studio code](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Verificatie referenties configureren

Maak een nieuw bestand met de naam _. env_en plak de volgende code hierin en geef de waarden op die zijn opgegeven bij het maken van uw insluitende lezer-resource.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Zorg ervoor dat dit bestand niet wordt door gegeven aan broncode beheer, omdat het geheimen bevat dat niet openbaar mag worden gemaakt.

Het **getimmersivereadertoken** -API-eind punt moet worden beveiligd achter een vorm van verificatie (bijvoorbeeld [OAuth](https://oauth.net/2/)) om te voor komen dat onbevoegde gebruikers tokens verkrijgen om te gebruiken voor uw insluitende lezer-service en facturering; Dit werk valt buiten het bereik van deze zelf studie.

## <a name="create-a-python-web-app-on-windows"></a>Een Python-web-app maken in Windows

Een Python-web-app maken met behulp van `flask` in Windows.

Installeer [Git](https://git-scm.com/).

Nadat Git is geïnstalleerd, opent u een opdracht prompt en kloont u de insluitende lezer SDK Git-opslag plaats naar een map op uw computer

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installeer [Python](https://www.python.org/downloads/).

Schakel het selectie vakje python toevoegen aan pad in.

![Installatie dialoogvenster van python Windows stap 1](./media/pythoninstallone.jpg)

Voeg optionele functies toe door de selectie vakjes in te scha kelen en op de knop volgende te klikken.

![Installatie dialoogvenster van python Windows-stap 2](./media/pythoninstalltwo.jpg)

Kies aangepaste installatie en stel het installatiepad in als uw hoofdmap, bijvoorbeeld `C:\Python37-32\` klik vervolgens op de knop installeren.

![Installatie dialoogvenster van python Windows-stap 3](./media/pythoninstallthree.jpg)

Nadat de installatie van python is voltooid, opent u een opdracht prompt en `cd` u naar de map python-scripts.

```cmd
cd C:\Python37-32\Scripts
```

Installatie kolf.

```cmd
pip install flask
```

Installeer Jinja2. Een volledig functionele sjabloon engine voor python.

```cmd
pip install jinja2
```

Installeer virtualenv. Een hulp programma voor het maken van geïsoleerde python-omgevingen.

```cmd
pip install virtualenv
```

Installeer virtualenvwrapper-Win. Het idee achter virtualenvwrapper is om het gebruik van virtualenv te vereenvoudigen.

```cmd
pip install virtualenvwrapper-win
```

Installeer de module aanvragen. Aanvragen is een apache2-gelicentieerde HTTP-bibliotheek, geschreven in python.

```cmd
pip install requests
```

Installeer de python-dotenv-module. Deze module leest het sleutel-waardepaar van het. env-bestand en voegt deze toe aan de omgevings variabele.

```cmd
pip install python-dotenv
```

Een virtuele omgeving maken

```cmd
mkvirtualenv advanced-python
```

`cd` naar de hoofdmap van het voorbeeld project.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Verbind het voorbeeld project met de omgeving. Hiermee wordt de zojuist gemaakte virtuele omgeving toegewezen aan de basismap van het voorbeeld project.

```cmd
setprojectdir .
```

Activeer de virtuele omgeving.

```cmd
activate
```

Het project moet nu actief zijn en u ziet iets als `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` in de opdracht prompt.

Deactiveer de omgeving.

```cmd
deactivate
```

Het voor voegsel van `(advanced-python)` moet nu worden verwijderd omdat de omgeving nu is gedeactiveerd.

Voer `workon advanced-python` uit vanuit de hoofdmap van het voorbeeld project om de omgeving opnieuw uit te voeren.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>De insluitende lezer starten met voorbeeld inhoud

Wanneer de omgeving actief is, voert u het voorbeeld project uit door `flask run` vanuit de hoofdmap van het voorbeeld project in te voeren.

```cmd
flask run
```

Open uw browser en ga naar _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Een Python-web-app maken op OSX

Maak een Python-web-app met behulp van `flask` op OSX.

Installeer [Git](https://git-scm.com/).

Nadat Git is geïnstalleerd, opent u Terminal en ' Clone ' de insluitende lezer SDK Git-opslag plaats naar een map op uw computer

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installeer [Python](https://www.python.org/downloads/).

De python-basismap, bijvoorbeeld `Python37-32`, moet zich nu in de map toepassingen bevindt.

Nadat de python-installatie is voltooid, opent u Terminal en `cd` naar de map python-scripts.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Installeer pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Voer vervolgens de volgende handelingen uit om pip te installeren voor de gebruiker die momenteel is aangemeld om machtigingen te voor komen.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Voer uw wacht woord in als u hierom wordt gevraagd.
- Voeg het pad van de PIP-installatie toe aan de padvariabele.
- Ga naar de onderkant van het bestand en geef het pad op dat u wilt toevoegen als laatste item in de lijst, bijvoorbeeld `PATH=$PATH:/usr/local/bin`.
- Druk op Control-x om af te sluiten.
- Voer `Y` in om de gewijzigde buffer op te slaan.
- Dat is alles. U kunt dit testen door in nieuw terminal venster het volgende te typen: `echo $PATH`.

Installatie kolf.

```bash
pip install flask --user
```

Installeer Jinja2. Een volledig functionele sjabloon engine voor python.

```bash
pip install Jinja2 --user
```

Installeer virtualenv. Een hulp programma voor het maken van geïsoleerde python-omgevingen.

```bash
pip install virtualenv --user
```

Installeer virtualenvwrapper. Het idee achter virtualenvwrapper is om het gebruik van virtualenv te vereenvoudigen.

```bash
pip install virtualenvwrapper --user
```

Installeer de module aanvragen. Aanvragen is een apache2-gelicentieerde HTTP-bibliotheek, geschreven in python.

```bash
pip install requests --user
```

Installeer de python-dotenv-module. Deze module leest het sleutel-waardepaar van het. env-bestand en voegt deze toe aan de omgevings variabele.

```bash
pip install python-dotenv --user
```

Kies een map waar u uw virtuele omgevingen wilt opslaan en voer deze opdracht uit

```bash
mkdir ~/.virtualenvs
```

`cd` naar de map insluitende Reader SDK python voor beeld van toepassing.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Een virtuele omgeving maken

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Verbind het voorbeeld project met de omgeving. Hiermee wordt de zojuist gemaakte virtuele omgeving toegewezen aan de basismap van het voorbeeld project.

```bash
setprojectdir .
```

Activeer de virtuele omgeving.

```bash
activate
```

Het project moet nu actief zijn en u ziet iets als `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` in de opdracht prompt.

Deactiveer de omgeving.

```bash
deactivate
```

Het voor voegsel van `(advanced-python)` moet nu worden verwijderd omdat de omgeving nu is gedeactiveerd.

Voer `workon advanced-python` uit vanuit de hoofdmap van het voorbeeld project om de omgeving opnieuw uit te voeren.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>De insluitende lezer starten met voorbeeld inhoud

Wanneer de omgeving actief is, voert u het voorbeeld project uit door `flask run` vanuit de hoofdmap van het voorbeeld project in te voeren.

```bash
flask run
```

Open uw browser en ga naar _http://localhost:5000_ .

## <a name="next-steps"></a>Volgende stappen

* Verken de [insluitende lezer SDK](https://github.com/microsoft/immersive-reader-sdk) en de referentie voor de [insluitende lezer SDK](./reference.md)
* Code voorbeelden weer geven op [github](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)

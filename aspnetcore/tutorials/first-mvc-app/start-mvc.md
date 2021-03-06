---
title: Erste Schritte mit ASP.NET Core MVC
author: rick-anderson
description: Hier finden Sie Informationen zum Einstieg in ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "78648667"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Erste Schritte mit ASP.NET Core MVC

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.

Die App verwaltet eine Datenbank mit Filmtiteln. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen einer Web-App
> * Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell
> * Arbeiten mit einer Datenbank
> * Hinzufügen von Such- und Überprüfungsfunktionen

Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Erstellen einer Web-App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.

* Klicken Sie auf **ASP.NET Core-Webanwendung** und dann auf **Weiter**.

![neue ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* Geben Sie dem Projekt den Namen **MvcMovie**, und klicken Sie dann auf **Erstellen**. Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.

  ![neue ASP.NET Core-Webanwendung](start-mvc/_static/config.png)

* Klicken Sie auf **Webanwendung (Model View Controller)** und anschließend auf **Erstellen**.

![Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App ](start-mvc/_static/new_project30.png)

Visual Studio verwendet die Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben. Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits. Hierbei handelt es sich um ein grundlegendes Startprojekt.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind. Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.
* Führen Sie den folgenden Befehl aus:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**  Wählen Sie **Ja** aus.

  * `dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.
  * `code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie auf **Datei** > **Neue Projektmappe**.

  ![Neue Projektmappe in macOS](./start-mvc/_static/new_project_vsmac.png)

* Wählen Sie **.NET Core** > **App** > **Webanwendung (Model-View-Controller)** > **Weiter** aus.

  ![Dialogfeld „Neue Projektmappe“ in macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* Wählen Sie im Dialogfeld **Configure your new ASP.NET Core Web API** (Neue ASP.NET Core-Web-API konfigurieren) für das **Zielframework** den Eintrag **.NET Core 3.1** aus.

  ![Auswahl für .NET Core 3.1 in macOS](./start-mvc/_static/new_project_31_vsmac.png)

* Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.

---

### <a name="run-the-app"></a>Ausführen der App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus. Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt. Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.
* Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen. Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.
* Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.

  ![IIS Express](start-mvc/_static/iis_express.png)

  Auf der folgenden Abbildung sehen Sie die App:

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`. Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer.

  Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen. Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten. Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet. Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.
* Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.

  Auf der folgenden Abbildung sehen Sie die App:

  ![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.

> [!div class="step-by-step"]
> [Nächste](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.

Die App verwaltet eine Datenbank mit Filmtiteln. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen einer Web-App
> * Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell
> * Arbeiten mit einer Datenbank
> * Hinzufügen von Such- und Überprüfungsfunktionen

Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a>Erstellen einer Web-App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.

* Klicken Sie auf **ASP.NET Core-Webanwendung** und dann auf **Weiter**.

![neue ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* Geben Sie dem Projekt den Namen **MvcMovie**, und klicken Sie dann auf **Erstellen**. Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.

  ![neue ASP.NET Core-Webanwendung](start-mvc/_static/config.png)


* Klicken Sie auf **Webanwendung (Model View Controller)** und anschließend auf **Erstellen**.

![Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App ](start-mvc/_static/new_project22-21.png)

Visual Studio verwendet die Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben. Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits. Dies ist ein grundlegendes Startprojekt und ein guter Einstieg.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind. Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.
* Führen Sie den folgenden Befehl aus:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**  Wählen Sie **Ja** aus.

  * `dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.
  * `code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie auf **Datei** > **Neue Projektmappe**.

  ![Neue Projektmappe in macOS](./start-mvc/_static/new_project_vsmac.png)

* Wählen Sie **.NET Core** > **App** > **Webanwendung (Model-View-Controller)** > **Weiter** aus.

  ![Dialogfeld „Neue Projektmappe“ in macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung für **Zielframework** von **.NET Core 2.2**.

  ![Auswahl für .NET Core 2.2 in macOS](./start-mvc/_static/new_project_22_vsmac.png)

* Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.

---

### <a name="run-the-app"></a>Ausführen der App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus. Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt. Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.
* Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen. Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.
* Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.

  ![IIS Express](start-mvc/_static/iis_express.png)

* Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen. Diese App verfolgt keine personenbezogenen Informationen nach. Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`. Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer.

  Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen. Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.

* Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen. Diese App verfolgt keine personenbezogenen Informationen nach. Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten. Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet. Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.
* Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.

* Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen. Diese App verfolgt keine personenbezogenen Informationen nach. Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.

  ![Start- oder Indexseite](./start-mvc/_static/output_privacy_macos.png)

  Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:

  ![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.

> [!div class="step-by-step"]
> [Nächste](adding-controller.md)

::: moniker-end

---
title: Hosten und Bereitstellen von ASP.NET Core Blazor WebAssembly
author: guardrex
description: Erfahren Sie, wie Sie eine Blazor-App mithilfe von ASP.NET Core, Content Delivery Network (CDN), Dateiservern und GitHub-Seiten hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f364d94085d175fde5596c222ef21852c0106ec1
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751132"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a>Hosten und Bereitstellen von ASP.NET Core Blazor WebAssembly

Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Mit dem [Blazor WebAssembly-Hostingmodell](xref:blazor/hosting-models#blazor-webassembly):

* Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser heruntergeladen.
* Die App wird direkt im UI-Thread des Browsers ausgeführt.

Die folgenden Bereitstellungsstrategien werden unterstützt:

* Die Blazor-App wird von einer ASP.NET Core-App unterstützt. Diese Strategie wird im Abschnitt [Gehostete Bereitstellung mit ASP.NET Core](#hosted-deployment-with-aspnet-core) behandelt.
* Die Blazor-App wird auf einem Webserver oder Webservice für statisches Hosting abgelegt, auf dem .NET nicht zur Unterstützung der Blazor-App verwendet wird. Diese Strategie wird im Abschnitt [Eigenständige Bereitstellung](#standalone-deployment) behandelt, der Informationen zum Hosten einer Blazor WebAssembly-App als untergeordnete IIS-App enthält.

## <a name="rewrite-urls-for-correct-routing"></a>Erneutes Generieren von URLs für ein ordnungsgemäßes Routing

Routinganforderungen für Seitenkomponenten in einer Blazor WebAssembly-App sind nicht so unkompliziert wie Routinganforderungen in einer gehosteten Blazor Server-App. Gehen Sie von einer Blazor WebAssembly-App mit zwei Komponenten aus:

* *Main.razor:* wird im Stammverzeichnis der App geladen und enthält einen Link zur `About`-Komponente (`href="About"`)
* *About.razor:* `About`-Komponente.

Wenn das Standarddokument der App über die Adressleiste des Browsers (z. B. `https://www.contoso.com/`) angefordert wird, geschieht Folgendes:

1. Der Browser sendet eine Anforderung.
1. Die Standardseite wird zurückgegeben, in der Regel *index.html*.
1. *index.html* startet die App.
1. Der Blazor-Router wird geladen, und die Razor-Komponente `Main` wird gerendert.

Auf der Seite „Main“ kann der Link zur `About`-Komponente auf dem Client ausgewählt werden, da der Blazor-Router dafür sorgt, dass der Browser im Internet keine Anforderung für `About` an `www.contoso.com` sendet, und stattdessen die gerenderte `About`-Komponente selbst bereitstellt. Alle Anforderungen von internen Endpunkten *innerhalb der Blazor WebAssembly-App* funktionieren auf dieselbe Weise: Durch Anforderungen werden keine browserbasierten Anforderungen an serverseitig gehostete Ressourcen im Internet ausgelöst. Der Router verarbeitet Anforderungen intern.

Wenn eine Anforderung für `www.contoso.com/About` über die Adressleiste des Browsers gesendet wird, tritt bei der Anforderung ein Fehler auf. Diese Ressource ist im Internethost der App nicht vorhanden. Daher wird die Antwort *404 Nicht gefunden* zurückgegeben.

Da Browser Anforderungen für clientseitige Seiten an internetbasierte Hosts senden, müssen Webserver und Hostingdienste alle Anforderungen für Ressourcen, die sich nicht physisch auf dem Server befinden, auf der Seite *index.html* erneut generieren. Wenn *index.html* zurückgegeben wird, übernimmt der Blazor-Router der App und antwortet mit der richtigen Ressource.

Wenn Sie die Bereitstellung auf einem IIS-Server durchführen, können Sie das URL-Rewrite-Modul mit der veröffentlichten *web.config*-Datei der App verwenden. Weitere Informationen finden Sie im Abschnitt [IIS](#iis).

## <a name="hosted-deployment-with-aspnet-core"></a>Gehostete Bereitstellung mit ASP.NET Core

Mit einer *gehosteten Bereitstellung* wird die Blazor WebAssembly-App über eine auf einem Webserver ausgeführte [ASP.NET Core-App für Browser](xref:index) bereitgestellt.

Der Client Blazor der WebAssembly-App wird im Ordner */bin/Release/{ZIELFRAMEWORK}/publish/wwwroot* der Server-App zusammen mit allen anderen statischen Webressourcen der Server-App veröffentlicht. Die beiden Apps werden zusammen bereitgestellt. Hierfür wird ein Webserver benötigt, auf dem eine ASP.NET Core-App gehostet werden kann. Bei einer gehosteten Bereitstellung enthält Visual Studio die **Blazor WebAssembly-App**-Projektvorlage (`blazorwasm`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)) mit der ausgewählten Option **Hosted** (`-ho|--hosted`, wenn Sie den `dotnet new`-Befehl verwenden).

Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.

Informationen zum Bereitstellen für Azure App Service finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Eigenständige Bereitstellung

Bei einer *eigenständigen Bereitstellung* wird die Blazor WebAssembly-App in Form von statischen Dateien bereitgestellt, die von Clients direkt angefordert werden. Jeder Server für statische Dateien kann die Blazor-App bedienen.

Eigenständige Bereitstellungsobjekte werden im Ordner */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* veröffentlicht.

### <a name="iis"></a>IIS

IIS ist ein leistungsfähiger Server für statische Dateien für Blazor-Apps. Informationen zum Konfigurieren von IIS zum Hosten von Blazor finden Sie unter [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Erstellen einer statischen Website unter IIS).

Veröffentlichte Objekte werden im Ordner */bin/Release/{Zielframework}/publish* erstellt. Die Inhalte des Ordners *publish* werden auf dem Webserver oder über den Hostingdienst gehostet.

#### <a name="webconfig"></a>web.config

Beim Veröffentlichen eines Blazor-Projekts wird eine Datei *web.config* mit der folgenden IIS-Konfiguration erstellt:

* Für die folgenden Dateierweiterungen werden MIME-Typen festgelegt:
  * *.dll* &ndash; `application/octet-stream`
  * *.json* &ndash; `application/json`
  * *.wasm* &ndash; `application/wasm`
  * *.woff* &ndash; `application/font-woff`
  * *.woff2* &ndash; `application/font-woff`
* Für die folgenden MIME-Typen wird die HTTP-Komprimierung aktiviert:
  * `application/octet-stream`
  * `application/wasm`
* URL-Rewrite-Modul-Regeln werden eingerichtet:
  * Stellen Sie das Unterverzeichnis bereit, in dem sich die statischen Objekte der App befinden (*wwwroot/{PATH REQUESTED}* ).
  * Richten Sie ein SPA-Fallbackrouting ein, sodass Anforderungen für nicht dateibasierte Objekte an das Standarddokument der App im entsprechenden Ordner für statische Objekte (*wwwroot/index.html*) umgeleitet werden.
  
#### <a name="use-a-custom-webconfig"></a>Verwenden einer benutzerdefinierten Datei web.config

Gehen Sie wie folgt vor, um eine benutzerdefinierte Datei *web.config*  zu verwenden:

1. Legen Sie die benutzerdefinierte Datei *web.config* im Stamm des Projektordners ab.
1. Fügen Sie das folgende Ziel zur Projektdatei ( *.csproj*) hinzu:

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> Die Verwendung der MSBuild-Eigenschaft `<IsWebConfigTransformDisabled>` mit dem Wert `true` wird in Blazor-WebAssembly-Apps nicht unterstützt, [da diese für in den IIS bereitgestellte ASP.NET Core-Apps gedacht ist](xref:host-and-deploy/iis/index#webconfig-file). Weitere Informationen finden Sie unter [Kopieren von Ziel für Bereitstellung einer benutzerdefinierten Datei web.config für Blazor-WASM erforderlich (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).

#### <a name="install-the-url-rewrite-module"></a>Installieren des URL-Rewrite-Moduls

Das [URL-Rewrite-Modul](https://www.iis.net/downloads/microsoft/url-rewrite) wird zum Umschreiben von URLs benötigt. Das Modul wird nicht standardmäßig installiert und ist für die Installation als Webserver (IIS)-Rollendienstfunktion nicht verfügbar. Das Modul muss von der IIS-Website heruntergeladen werden. Verwenden Sie den Webplattform-Installer zur Installation des Moduls:

1. Navigieren Sie lokal zur [URL-Rewrite-Module-Downloadseite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Wählen Sie zum Herunterladen der englischen Version des WebPI-Installers **WebPI** aus. Wählen Sie zum Herunterladen des Installers in einer anderen Sprache die entsprechende Architektur für den Server (x86/x64) aus.
1. Kopieren Sie den Installer auf den Server. Führen Sie den Installer aus. Klicken Sie auf die Schaltfläche **Installieren**, und stimmen Sie den Lizenzbedingungen zu. Der Server muss nach Abschluss der Installation nicht neu gestartet werden.

#### <a name="configure-the-website"></a>Konfigurieren der Website

Legen Sie für den **physischen Pfad** der Website den Ordner der App fest. Der Ordner enthält Folgendes:

* Die Datei *web.config*, die von IIS zum Konfigurieren der Website verwendet wird, mit den erforderlichen Umleitungsregeln und Dateiinhaltstypen
* Den Ordner der App für statische Objekte

#### <a name="host-as-an-iis-sub-app"></a>Hosten als IIS-untergeordnete App

Wenn eine eigenständige App als IIS-untergeordnete App gehostet wird, führen Sie einen der folgenden Schritte aus:

* Deaktivieren Sie den vererbten ASP.NET Core Module-Handler.

  Entfernen Sie den Handler in der veröffentlichen Datei *web.config* der Blazor-App, indem Sie der Datei einen `<handlers>`-Abschnitt hinzufügen:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Deaktivieren Sie die Vererbung des `<system.webServer>`-Abschnitts der Stammanwendung (d. h. der übergeordneten Anwendung), indem Sie ein `<location>`-Element verwenden, bei dem `inheritInChildApplications` auf `false` festgelegt ist:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

Das Entfernen des Handlers bzw. das Deaktivieren der Vererbung wird zusätzlich zur [Konfiguration des Basispfads der App](xref:host-and-deploy/blazor/index#app-base-path) durchgeführt. Legen Sie den Basispfad der App in der *index.html*-Datei der App auf den IIS-Alias fest, der beim Konfigurieren der untergeordneten App in IIS verwendet wird.

#### <a name="troubleshooting"></a>Problembehandlung

Wenn die Fehlermeldung *500: Interner Serverfehler* angezeigt wird und IIS-Manager beim Zugriff auf die Konfiguration der Website eine Fehlermeldung anzeigt, überprüfen Sie, ob das URL-Rewrite-Modul installiert ist. Wenn das Modul nicht installiert ist, kann die Datei *web.config* von IIS nicht analysiert werden. Dadurch wird verhindert, dass die Konfiguration der Website vom IIS-Manager geladen wird, und dass die statischen Blazor-Dateien auf der Website bereitgestellt werden.

Weitere Informationen zur Problembehandlung von Bereitstellungen für IIS finden Sie unter <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Azure Storage

Das Hosten statischer Dateien von [Azure Storage](/azure/storage/) ermöglicht das serverlose Blazor-App-Hosting. Benutzerdefinierte Domänennamen, das Azure Content Delivery Network (CDN) und HTTPS werden unterstützt.

Wenn der Blobdienst für das Hosten von statischen Websites in einem Speicherkonto aktiviert ist, gehen Sie folgendermaßen vor:

* Legen Sie den **Namen des Indexdokuments** auf `index.html` fest.
* Legen Sie den **Pfad des Fehlerdokuments** auf `index.html` fest. Razor-Komponenten und andere Endpunkte, bei denen es sich nicht um Dateien handelt, befinden sich nicht in physischen Pfaden in dem statischen Inhalt, der vom Blobdienst gespeichert wird. Wenn eine Anforderung für eine dieser Ressourcen empfangen wird, die vom Blazor-Router verarbeitet werden soll, leitet der vom Blob-Dienst generierte *Fehler 404 – Nicht gefunden* die Anforderung an den **Pfad des Fehlerdokuments** weiter. Das Blob *index.html* wird zurückgegeben, und der Blazor-Router lädt und verarbeitet den Pfad.

Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Die folgende *nginx.conf*-Datei wurde vereinfacht, um zu zeigen, wie Nginx so konfiguriert wird, dass die Datei *Index.html* gesendet wird, wenn auf der Festplatte keine entsprechende Datei gefunden wird.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Weitere Informationen zur Nginx-Webserverkonfiguration für die Produktion finden Sie unter [Creating NGINX Plus and NGINX Configuration Files (Erstellen von Konfigurationsdateien für NGINX Plus und NGINX)](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="nginx-in-docker"></a>Nginx in Docker

Richten Sie zum Hosten von Blazor in Docker mithilfe von NGINX das Dockerfile für die Verwendung des auf Alpine basierenden NGINX-Images ein. Aktualisieren Sie die Dockerfile zum Kopieren der Datei *nginx.config* in den Container.

Fügen Sie der Dockerfile eine Zeile hinzu, wie im folgenden Beispiel dargestellt:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

So stellen Sie eine Blazor WebAssembly-App für CentOS 7 oder höher bereit

1. Erstellen Sie die Apache-Konfigurationsdatei. Im folgenden Beispiel wird eine vereinfachte Konfigurationsdatei veranschaulicht (*blazorapp.config*):

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Platzieren Sie die Apache-Konfigurationsdatei in das `/etc/httpd/conf.d/`-Verzeichnis, das das standardmäßige Apache-Konfigurationsverzeichnis in CentOS 7 ist.

1. Platzieren Sie die Dateien der App in das `/var/www/blazorapp`-Verzeichnis (den Speicherort, der für `DocumentRoot` in der Konfigurationsdatei angegeben ist).

1. Starten Sie den Apache-Dienst neu.

Weitere Informationen finden Sie unter [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) und [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>GitHub-Seiten

Fügen Sie zum Verarbeiten von URL-Umschreibungen eine *404.html*-Datei mit einem Skript hinzu, mit dem die Umleitung der Anforderung an die Seite *index.html* verarbeitet wird. Ein Beispiel für eine Implementierung, das von der Community bereitgestellt wurde, finden Sie unter [Single Page Apps for GitHub Pages (Single-Page-Apps für GitHub Pages)](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages auf GitHub](https://github.com/rafrex/spa-github-pages#readme)). Ein Beispiel für die Verwendung des Community-Ansatzes finden Sie unter [blazor-demo/blazor-demo.github.io auf GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([Livewebsite](https://blazor-demo.github.io/)).

Wenn Sie anstelle einer Organisationswebsite eine Projektwebsite verwenden, fügen Sie der Datei *index.html* das `<base>`-Tag hinzu, oder aktualisieren Sie das Tag in der Datei. Legen Sie den Wert des `href`-Attributs auf den Namen des GitHub-Repositorys mit einem nachfolgenden Slash fest (z.B. `my-repository/`).

## <a name="host-configuration-values"></a>Hostkonfigurationswerte

[Blazor WebAssembly-Apps](xref:blazor/hosting-models#blazor-webassembly) können die folgenden Hostkonfigurationswerte als Befehlszeilenargumente zur Laufzeit in der Entwicklungsumgebung akzeptieren.

### <a name="content-root"></a>Inhaltsstammverzeichnis

Mit dem Argument `--contentroot` wird der absolute Pfad zum Verzeichnis festgelegt, das die Inhaltsdateien (das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root)) der App enthält. In den folgenden Beispielen ist `/content-root-path` der Stammpfad für Inhalte der App.

* Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben. Führen Sie den folgenden Befehl über das Verzeichnis der App aus:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu. Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein. Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Basispfad

Mit dem Argument `--pathbase` wird der Basispfad für eine App festgelegt, die lokal mit einem relativen URL-Pfad ausgeführt wird, der sich nicht im Stammverzeichnis befindet (d. h. das `<base>`-Tag `href` wird für das Staging und die Produktion auf einen anderen Pfad als `/` festgelegt). In den folgenden Beispielen ist `/relative-URL-path` die Pfadbasis der App. Weitere Informationen finden Sie unter [Basispfad einer App](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> Im Gegensatz zu dem Pfad, der für `href` des `<base>`-Tags bereitgestellt wird, wird beim Übergeben des `--pathbase`-Argumentwerts kein Schrägstrich (`/`) nachgestellt. Wenn der Basispfad einer App im `<base>`-Tag als `<base href="/CoolApp/">` (mit nachgestelltem Schrägstrich) bereitgestellt wird, wird der Argumentwert in der Befehlszeile als `--pathbase=/CoolApp` (ohne nachgestellten Schrägstrich) übergeben.

* Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben. Führen Sie den folgenden Befehl über das Verzeichnis der App aus:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu. Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein. Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URLs

Mit dem Argument `--urls` werden die IP-oder Hostadressen mit Ports und Protokollen festgelegt, die auf Anforderungen lauschen sollen.

* Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben. Führen Sie den folgenden Befehl über das Verzeichnis der App aus:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu. Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein. Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Konfigurieren des Linkers

Blazor führt auf jedem Releasebuild eine IL-Verknüpfung (Intermediate Language, Zwischensprache) durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys zu entfernen. Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/configure-linker>.

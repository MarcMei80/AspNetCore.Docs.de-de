---
title: Sichern einer Blazor eigenständigen Core WebAssembly-App mit ASP.NET Core WebAssembly mit Microsoft-Konten
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977079"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Sichern einer Blazor eigenständigen Core WebAssembly-App mit ASP.NET Core WebAssembly mit Microsoft-Konten

Von [Javier Calvarro Nelson](https://github.com/javiercn) und Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

So erstellen Blazor Sie eine eigenständige WebAssembly-App, die [Microsoft-Konten mit Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) für die Authentifizierung verwendet:

1. [Erstellen eines AAD-Mandanten und einer Webanwendung](/azure/active-directory/develop/v2-overview)

   Registrieren einer AAD-App im Registrierungsbereich der Azure Active > **Directory-App** im Azure-Portal: **Azure Active Directory**

   1\. Geben Sie einen **Namen** für die App an (z. B. ** Blazor Client AAD**).<br>
   2\. Wählen Sie unter **Unterstützte Kontotypen** **Konten in einem beliebigen Organisationsverzeichnis**aus.<br>
   3\. Lassen Sie die Droptauchliste **"Umleitungs-URI"** auf **Web**festgelegt, und stellen Sie einen Umleitungs-URI von `https://localhost:5001/authentication/login-callback`bereit.<br>
   4\. Deaktivieren Sie das Kontrollkästchen **Permissions** > **Grant admin concent zum öffnen und offline_access Berechtigungen.**<br>
   5\. Wählen Sie **Registrieren**.

   In > **Authentifizierungsplattform-Konfigurationen** > **Web**: **Authentication**

   1\. Bestätigen Sie, dass `https://localhost:5001/authentication/login-callback` der **Umleitungs-URI** von vorhanden ist.<br>
   2\. Aktivieren Sie für **Implizite Erteilung**die Kontrollkästchen für **Access-Token** und **ID-Token**.<br>
   3\. Die verbleibenden Standardeinstellungen für die App sind für diese Erfahrung akzeptabel.<br>
   4\. Wählen Sie die Schaltfläche **Speichern** aus.

   Zeichnen Sie die Anwendungs-ID (Client-ID) auf (z. B. `11111111-1111-1111-1111-111111111111`).

1. Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Um den Ausgabespeicherort anzugeben, der einen Projektordner erstellt, falls dieser nicht vorhanden ist, fügen `-o BlazorSample`Sie die Ausgabeoption in den Befehl mit einem Pfad ein (z. B. ). Der Ordnername wird auch Teil des Projektnamens.

Nach dem Erstellen der App sollten Sie in der Lage sein:

* Melden Sie sich mit einem Microsoft-Konto bei der App an.
* Anfordern von Zugriffstoken für Microsoft-APIs mit Blazor dem gleichen Ansatz wie für eigenständige Apps, sofern Sie die App ordnungsgemäß konfiguriert haben. Weitere Informationen finden Sie unter [Schnellstart: Konfigurieren einer Anwendung zum Verfügbarmachen von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Authentifizierungspaket

Wenn eine App für die Verwendung`SingleOrg`von Arbeits- oder Schulkonten ( erstellt`Microsoft.Authentication.WebAssembly.Msal`wird, erhält die App automatisch eine Paketreferenz für die [Microsoft-Authentifizierungsbibliothek](/azure/active-directory/develop/msal-overview) ( ). Das Paket stellt eine Reihe von Primitiven bereit, die der App helfen, Benutzer zu authentifizieren und Token zum Aufrufen geschützter APIs zu erhalten.

Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell zur Projektdatei der App hinzu:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Ersetzen `{VERSION}` Sie im vorherigen Paketverweis `Microsoft.AspNetCore.Blazor.Templates` durch die <xref:blazor/get-started> im Artikel gezeigte Version des Pakets.

Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv zur App hinzu.

## <a name="authentication-service-support"></a>Unterstützung des Authentifizierungsdienstes

Unterstützung für die Authentifizierung von Benutzern wird `AddMsalAuthentication` im Dienstcontainer `Microsoft.Authentication.WebAssembly.Msal` mit der vom Paket bereitgestellten Erweiterungsmethode registriert. Diese Methode richtet alle Dienste ein, die für die Interaktion der App mit dem Identitätsanbieter (IDENTITY Provider, IP) erforderlich sind.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

Die `AddMsalAuthentication` Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer App erforderlich sind. Die zum Konfigurieren der App erforderlichen Werte können bei der Registrierung der App über die Microsoft-Kontenkonfiguration abgerufen werden.

## <a name="access-token-scopes"></a>Zugriffstokenbereiche

Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch, um ein Zugriffstoken für eine sichere API anzufordern. Um ein Token als Teil des Anmeldeflusses zu bereitstellen, fügen Sie den `MsalProviderOptions`Bereich den Standardzugriffstokenbereichen der folgenden Werte hinzu:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Wenn das Azure-Portal einen Bereichs-URI bereitstellt und **die App eine nicht behandelte Ausnahme auslöst,** wenn sie eine *401 nicht autorisierte* Antwort von der API empfängt, versuchen Sie, einen Bereichs-URI zu verwenden, der das Schema und den Host nicht enthält. Das Azure-Portal kann z. B. eines der folgenden Bereichs-URI-Formate bereitstellen:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Geben Sie den Bereichs-URI ohne Schema und Host:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>Imports-Datei

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>App-Komponente

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin-Komponente

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay-Komponente

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Authentifizierungskomponente

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Zusätzliche Zugriffstoken anfordern](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Schnellstart: Registrieren einer Anwendung mit der Microsoft-Identitätsplattform](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Schnellstart: Konfigurieren einer Anwendung zum Verfügbarmachen von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)

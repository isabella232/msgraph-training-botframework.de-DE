---
ms.openlocfilehash: c0fd9a9f5529f202c125d7f1e7c6b50fa9dc630d
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347819"
---
# <a name="graphcalendarbot"></a>GraphCalendarBot

Bot-Framework-V4-Echo-bot-Beispiel.

Dieser bot wurde mit dem [bot-Framework](https://dev.botframework.com)erstellt und zeigt, wie ein einfacher bot erstellt wird, der Eingaben vom Benutzer annimmt und zurück hallt.

## <a name="prerequisites"></a>Voraussetzungen

- [.Net Core SDK](https://dotnet.microsoft.com/download) Version 3,1

  ```bash
  # determine dotnet version
  dotnet --version
  ```

## <a name="to-try-this-sample"></a>So testen Sie dieses Beispiel

- Navigieren Sie in einem Terminal zu `GraphCalendarBot`

    ```bash
    # change into project folder
    cd GraphCalendarBot
    ```

- Führen Sie den bot über ein Terminal oder Visual Studio aus, und wählen Sie Option a oder B aus.

  A) von einem Terminal

  ```bash
  # run the bot
  dotnet run
  ```

  B) oder von Visual Studio

  - Visual Studio starten
  - Datei > Open-> Projekt/Lösung
  - Navigieren Sie zu `GraphCalendarBot` Ordner
  - `GraphCalendarBot.csproj`Datei auswählen
  - Drücken Sie `F5` , um das Projekt auszuführen.

## <a name="testing-the-bot-using-bot-framework-emulator"></a>Testen des bot mithilfe des bot Framework-Emulators

[Bot-Framework-Emulator](https://github.com/microsoft/botframework-emulator) ist eine Desktopanwendung, die es bot-Entwicklern ermöglicht, ihre Bots auf localhost zu testen und zu debuggen oder Remote über einen Tunnel auszuführen.

- Installieren Sie die bot Framework-Emulatorversion 4.9.0 oder höher von [hier](https://github.com/Microsoft/BotFramework-Emulator/releases) aus.

### <a name="connect-to-the-bot-using-bot-framework-emulator"></a>Herstellen einer Verbindung mit dem Bot mithilfe des bot Framework-Emulators

- Starten des bot Framework-Emulators
- Datei > offener bot
- Geben Sie eine bot-URL für `http://localhost:3978/api/messages`

## <a name="deploy-the-bot-to-azure"></a>Bereitstellen des bot in Azure

Weitere Informationen zum Bereitstellen eines bot für Azure finden Sie unter [deploy your bot to Azure](https://aka.ms/azuredeployment) für eine vollständige Liste der Bereitstellungsanweisungen.

## <a name="further-reading"></a>Weiterführende Literatur

- [Bot-Framework-Dokumentation](https://docs.botframework.com)
- [Bot-Grundlagen](https://docs.microsoft.com/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0)
- [Aktivitäts Verarbeitung](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-activity-processing?view=azure-bot-service-4.0)
- [Einführung in den Azure bot-Dienst](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
- [Dokumentation zum Azure bot-Dienst](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)
- [.Net-Kern-CLI-Tools](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x)
- [Azure-CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
- [Azure-Portal](https://portal.azure.com)
- [Sprachverständnis mit Luis](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/)
- [Kanäle und bot Connector-Dienst](https://docs.microsoft.com/en-us/azure/bot-service/bot-concepts?view=azure-bot-service-4.0)

Generiert mit `dotnet new echobot` v 4.10.2

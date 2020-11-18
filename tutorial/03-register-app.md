---
ms.openlocfilehash: 9a320225c7e7e76506d73909a311fa019b1f74f3
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347792"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="23318-101">In dieser Übung erstellen Sie eine neue bot-Kanal Registrierung und eine Azure AD-Webanwendungs Registrierung mithilfe des Azure-Portals.</span><span class="sxs-lookup"><span data-stu-id="23318-101">In this exercise, you will create a new Bot Channels registration and an Azure AD web application registration using the Azure Portal.</span></span>

## <a name="create-a-bot-channels-registration"></a><span data-ttu-id="23318-102">Erstellen einer bot-Kanal Registrierung</span><span class="sxs-lookup"><span data-stu-id="23318-102">Create a Bot Channels registration</span></span>

1. <span data-ttu-id="23318-103">Öffnen Sie einen Browser, und navigieren Sie zum [Azure-Portal](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="23318-103">Open a browser and navigate to the [Azure Portal](https://portal.azure.com).</span></span> <span data-ttu-id="23318-104">Melden Sie sich mit dem Konto an, das Ihrem Azure-Abonnement zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="23318-104">Login using the account associated with your Azure subscription.</span></span>

1. <span data-ttu-id="23318-105">Wählen Sie das obere linke Menü aus, und wählen Sie dann **Ressource erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-105">Select the upper-left menu, then select **Create a resource**.</span></span>

    ![Screenshot des Azure-Portal Menüs](images/create-resource.png)

1. <span data-ttu-id="23318-107">Suchen Sie auf der **neuen** Seite nach der `Bot Channel` Registrierung von **bot Kanälen**, und wählen Sie Sie aus.</span><span class="sxs-lookup"><span data-stu-id="23318-107">On the **New** page, search for `Bot Channel` and select **Bot Channels Registration**.</span></span>

1. <span data-ttu-id="23318-108">Wählen Sie auf der Seite **bot-Kanal Registrierung** die Option **Create** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-108">On the **Bot Channels Registration** page, select **Create**.</span></span>

1. <span data-ttu-id="23318-109">Füllen Sie die erforderlichen Felder aus, und lassen Sie den **Messaging-Endpunkt** leer.</span><span class="sxs-lookup"><span data-stu-id="23318-109">Fill in the required fields, and leave **Messaging endpoint** blank.</span></span> <span data-ttu-id="23318-110">Das Feld **bot-handle** muss eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="23318-110">The **Bot handle** field must be unique.</span></span> <span data-ttu-id="23318-111">Überprüfen Sie unbedingt die verschiedenen Preiskategorien, und wählen Sie aus, was für Ihr Szenario sinnvoll ist.</span><span class="sxs-lookup"><span data-stu-id="23318-111">Be sure to review the different pricing tiers and select what makes sense for your scenario.</span></span> <span data-ttu-id="23318-112">Wenn es sich nur um eine Lern Übung handelt, können Sie die Option kostenlos auswählen.</span><span class="sxs-lookup"><span data-stu-id="23318-112">If this is just a learning exercise, you may want to select the free option.</span></span>

1. <span data-ttu-id="23318-113">Wählen Sie die **Microsoft App-ID und das Kennwort** aus, und wählen Sie dann **neu erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-113">Select the **Microsoft App ID and password**, then select **Create New**.</span></span>

1. <span data-ttu-id="23318-114">Wählen Sie **im App-Registrierungs Portal die Option APP-ID erstellen aus**.</span><span class="sxs-lookup"><span data-stu-id="23318-114">Select **Create App ID in the App Registration Portal**.</span></span> <span data-ttu-id="23318-115">Dadurch wird ein neues Fenster oder eine neue Registerkarte für das Blade " **App-Registrierungen** " im Azure-Portal geöffnet.</span><span class="sxs-lookup"><span data-stu-id="23318-115">This will open a new window or tab to the **App registrations** blade in the Azure Portal.</span></span>

1. <span data-ttu-id="23318-116">Wählen Sie im Blatt **App-Registrierungen** die Option **neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-116">In the **App registrations** blade, select **New registration**.</span></span>

1. <span data-ttu-id="23318-117">Legen Sie die Werte wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="23318-117">Set the values as follows.</span></span>

    - <span data-ttu-id="23318-118">Legen Sie **Name** auf `Graph Calendar Bot` fest.</span><span class="sxs-lookup"><span data-stu-id="23318-118">Set **Name** to `Graph Calendar Bot`.</span></span>
    - <span data-ttu-id="23318-119">Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.</span><span class="sxs-lookup"><span data-stu-id="23318-119">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="23318-120">Lassen Sie **URI umleiten** leer.</span><span class="sxs-lookup"><span data-stu-id="23318-120">Leave **Redirect URI** empty.</span></span>

    ![Screenshot der Seite "Anwendung registrieren"](./images/aad-register-an-app.png)

1. <span data-ttu-id="23318-122">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-122">Select **Register**.</span></span> <span data-ttu-id="23318-123">Kopieren Sie auf der Seite **Diagramm Kalender-bot** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, benötigen Sie ihn in den folgenden Schritten.</span><span class="sxs-lookup"><span data-stu-id="23318-123">On the **Graph Calendar Bot** page, copy the value of the **Application (client) ID** and save it, you will need it in the following steps.</span></span>

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](./images/aad-application-id.png)

1. <span data-ttu-id="23318-125">Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheime Clientschlüssel** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-125">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="23318-126">Wählen Sie die Schaltfläche **Neuen geheimen Clientschlüssel** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-126">Select the **New client secret** button.</span></span> <span data-ttu-id="23318-127">Geben Sie einen Wert in **Beschreibung** ein, wählen Sie eine der Optionen für **Gilt bis** aus, und wählen Sie dann **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-127">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

1. <span data-ttu-id="23318-128">Kopieren Sie den Wert des geheimen Clientschlüssels, bevor Sie diese Seite verlassen.</span><span class="sxs-lookup"><span data-stu-id="23318-128">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="23318-129">Sie benötigen Sie in den folgenden Schritten.</span><span class="sxs-lookup"><span data-stu-id="23318-129">You will need it in the following steps.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="23318-130">Dieser geheime Clientschlüssel wird nicht noch einmal angezeigt, stellen Sie daher sicher, dass Sie ihn jetzt kopieren.</span><span class="sxs-lookup"><span data-stu-id="23318-130">This client secret is never shown again, so make sure you copy it now.</span></span> <span data-ttu-id="23318-131">Sie müssen diesen Wert an mehreren Stellen eingeben, damit Sie ihn sicher halten können.</span><span class="sxs-lookup"><span data-stu-id="23318-131">You will need to enter this value in multiple places so keep it safe.</span></span>

1. <span data-ttu-id="23318-132">Kehren Sie zum Fenster bot-Kanal Registrierung in Ihrem Browser zurück, und fügen Sie die Anwendungs-ID in das Feld **Microsoft App-ID** ein.</span><span class="sxs-lookup"><span data-stu-id="23318-132">Return to the Bot Channel Registration window in your browser, and paste the application ID into the **Microsoft App ID** field.</span></span> <span data-ttu-id="23318-133">Fügen Sie den geheimen Client Schlüssel in das Feld **Password ein** .</span><span class="sxs-lookup"><span data-stu-id="23318-133">Paste your client secret into the **Password** field.</span></span> <span data-ttu-id="23318-134">Wählen Sie **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-134">Select **OK**.</span></span>

1. <span data-ttu-id="23318-135">Wählen Sie auf der Seite **Bots-Kanal Registrierung** die Option **Create** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-135">On the **Bots Channels Registration** page, select **Create**.</span></span>

1. <span data-ttu-id="23318-136">Warten Sie, bis die bot-Kanal Registrierung erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="23318-136">Wait for the Bot Channels registration to be created.</span></span> <span data-ttu-id="23318-137">Kehren Sie nach der Erstellung zur Startseite im Azure-Portal zurück, und wählen Sie dann **bot-Dienste** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-137">Once created, return to the Home page in the Azure Portal, then select **Bot Services**.</span></span> <span data-ttu-id="23318-138">Wählen Sie Ihre neue Bots-Kanal Registrierung aus, um die Eigenschaften anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="23318-138">Select your new Bots Channel registration to view its properties.</span></span>

## <a name="create-a-web-app-registration"></a><span data-ttu-id="23318-139">Erstellen einer webapp-Registrierung</span><span class="sxs-lookup"><span data-stu-id="23318-139">Create a web app registration</span></span>

1. <span data-ttu-id="23318-140">Kehren Sie zum Abschnitt **App-Registrierungen** des Azure-Portals zurück.</span><span class="sxs-lookup"><span data-stu-id="23318-140">Return to the **App registrations** section of the Azure Portal.</span></span>

1. <span data-ttu-id="23318-141">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-141">Select **New registration**.</span></span> <span data-ttu-id="23318-142">Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="23318-142">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="23318-143">Legen Sie **Name** auf `Graph Calendar Bot Auth` fest.</span><span class="sxs-lookup"><span data-stu-id="23318-143">Set **Name** to `Graph Calendar Bot Auth`.</span></span>
    - <span data-ttu-id="23318-144">Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.</span><span class="sxs-lookup"><span data-stu-id="23318-144">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="23318-145">Legen Sie unter **Umleitungs-URI** die erste Dropdownoption auf `Web` fest, und legen Sie den Wert auf `https://token.botframework.com/.auth/web/redirect` fest.</span><span class="sxs-lookup"><span data-stu-id="23318-145">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `https://token.botframework.com/.auth/web/redirect`.</span></span>

1. <span data-ttu-id="23318-146">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-146">Select **Register**.</span></span> <span data-ttu-id="23318-147">Kopieren Sie auf der Seite **Diagramm Kalender-bot-Authentifizierung** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, benötigen Sie ihn in den folgenden Schritten.</span><span class="sxs-lookup"><span data-stu-id="23318-147">On the **Graph Calendar Bot Auth** page, copy the value of the **Application (client) ID** and save it, you will need it in the following steps.</span></span>

1. <span data-ttu-id="23318-148">Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheime Clientschlüssel** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-148">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="23318-149">Wählen Sie die Schaltfläche **Neuen geheimen Clientschlüssel** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-149">Select the **New client secret** button.</span></span> <span data-ttu-id="23318-150">Geben Sie einen Wert in **Beschreibung** ein, wählen Sie eine der Optionen für **Gilt bis** aus, und wählen Sie dann **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-150">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

1. <span data-ttu-id="23318-151">Kopieren Sie den Wert des geheimen Clientschlüssels, bevor Sie diese Seite verlassen.</span><span class="sxs-lookup"><span data-stu-id="23318-151">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="23318-152">Sie benötigen Sie in den folgenden Schritten.</span><span class="sxs-lookup"><span data-stu-id="23318-152">You will need it in the following steps.</span></span>

1. <span data-ttu-id="23318-153">Wählen Sie **API-Berechtigungen** aus, und wählen Sie dann **Berechtigung hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-153">Select **API permissions**, then select **Add a permission**.</span></span>

1. <span data-ttu-id="23318-154">Wählen Sie **Microsoft Graph** aus, und wählen Sie dann **Delegierte Berechtigungen** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-154">Select **Microsoft Graph**, then select **Delegated permissions**.</span></span>

1. <span data-ttu-id="23318-155">Wählen Sie die folgenden Berechtigungen aus, und wählen Sie dann **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-155">Select the following permissions, then select **Add permissions**.</span></span>

    - <span data-ttu-id="23318-156">**openid**</span><span class="sxs-lookup"><span data-stu-id="23318-156">**openid**</span></span>
    - <span data-ttu-id="23318-157">**Profil**</span><span class="sxs-lookup"><span data-stu-id="23318-157">**profile**</span></span>
    - <span data-ttu-id="23318-158">**Calendars.ReadWrite**</span><span class="sxs-lookup"><span data-stu-id="23318-158">**Calendars.ReadWrite**</span></span>
    - <span data-ttu-id="23318-159">**MailboxSettings.Read**</span><span class="sxs-lookup"><span data-stu-id="23318-159">**MailboxSettings.Read**</span></span>

    ![Screenshot der konfigurierten Berechtigungen](images/configured-permissions.png)

### <a name="about-permissions"></a><span data-ttu-id="23318-161">Informationen zu Berechtigungen</span><span class="sxs-lookup"><span data-stu-id="23318-161">About permissions</span></span>

<span data-ttu-id="23318-162">Stellen Sie sich vor, was jeder dieser Berechtigungs Bereiche dem bot erlaubt und wofür der bot ihn verwendet.</span><span class="sxs-lookup"><span data-stu-id="23318-162">Consider what each of those permission scopes allows the bot to do, and what the bot will use them for.</span></span>

- <span data-ttu-id="23318-163">**OpenID** und **Profil**: ermöglicht dem bot, Benutzer zu signieren und grundlegende Informationen aus Azure AD im Identitätstoken abzurufen.</span><span class="sxs-lookup"><span data-stu-id="23318-163">**openid** and **profile**: allows the bot to sign users in and get basic information from Azure AD in the identity token.</span></span>
- <span data-ttu-id="23318-164">Calendars **. ReadWrite**: ermöglicht dem bot, den Kalender des Benutzers zu lesen und dem Kalender des Benutzers neue Ereignisse hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="23318-164">**Calendars.ReadWrite**: allows the bot to read the user's calendar and to add new events to the user's calendar.</span></span>
- <span data-ttu-id="23318-165">**Mailbox Settings. Read**: ermöglicht dem bot, die Postfacheinstellungen des Benutzers zu lesen.</span><span class="sxs-lookup"><span data-stu-id="23318-165">**MailboxSettings.Read**: allows the bot to read the user's mailbox settings.</span></span> <span data-ttu-id="23318-166">Der Bot wird dies verwenden, um die ausgewählte Zeitzone des Benutzers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="23318-166">The bot will use this to get the user's selected time zone.</span></span>
- <span data-ttu-id="23318-167">**User. Read**: ermöglicht dem bot, das Profil des Benutzers aus Microsoft Graph abzurufen.</span><span class="sxs-lookup"><span data-stu-id="23318-167">**User.Read**: allows the bot to get the user's profile from Microsoft Graph.</span></span> <span data-ttu-id="23318-168">Der Bot wird dies verwenden, um den Namen des Benutzers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="23318-168">The bot will use this to get the user's name.</span></span>

## <a name="add-oauth-connection-to-the-bot"></a><span data-ttu-id="23318-169">OAuth-Verbindung zum bot hinzufügen</span><span class="sxs-lookup"><span data-stu-id="23318-169">Add OAuth connection to the bot</span></span>

1. <span data-ttu-id="23318-170">Navigieren Sie zur Registrierungsseite für bot-Kanäle Ihres bot im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="23318-170">Navigate to your bot's Bot Channels Registration page on the Azure Portal.</span></span> <span data-ttu-id="23318-171">Wählen Sie unter **bot-Verwaltung** **Einstellungen** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-171">Select **Settings** under **Bot Management**.</span></span>

1. <span data-ttu-id="23318-172">Wählen Sie unter **OAuth-Verbindungseinstellungen** am unteren Rand der Seite die Option **Einstellung hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-172">Under **OAuth Connection Settings** near the bottom of the page, select **Add Setting**.</span></span>

1. <span data-ttu-id="23318-173">Füllen Sie das Formular wie folgt aus, und wählen Sie dann **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-173">Fill in the form as follows, then select **Save**.</span></span>

    - <span data-ttu-id="23318-174">**Name**: `GraphBotAuth`</span><span class="sxs-lookup"><span data-stu-id="23318-174">**Name**: `GraphBotAuth`</span></span>
    - <span data-ttu-id="23318-175">**Anbieter**: **Azure Active Directory v2**</span><span class="sxs-lookup"><span data-stu-id="23318-175">**Provider**: **Azure Active Directory v2**</span></span>
    - <span data-ttu-id="23318-176">**Client-ID**: die Anwendungs-ID Ihrer **Graph-Kalender-bot-Authentifizierungs** Registrierung.</span><span class="sxs-lookup"><span data-stu-id="23318-176">**Client id**: The application ID of your **Graph Calendar Bot Auth** registration.</span></span>
    - <span data-ttu-id="23318-177">**Geheimer Client Schlüssel**: der geheime Client Schlüssel Ihrer **Graph Calendar bot auth** -Registrierung.</span><span class="sxs-lookup"><span data-stu-id="23318-177">**Client secret**: The client secret of your **Graph Calendar Bot Auth** registration.</span></span>
    - <span data-ttu-id="23318-178">**Token Exchange-URL**: leer lassen</span><span class="sxs-lookup"><span data-stu-id="23318-178">**Token Exchange URL**: Leave blank</span></span>
    - <span data-ttu-id="23318-179">**Mandanten-ID**: `common`</span><span class="sxs-lookup"><span data-stu-id="23318-179">**Tenant ID**: `common`</span></span>
    - <span data-ttu-id="23318-180">**Bereiche**: `openid profile Calendars.ReadWrite MailboxSettings.Read User.Read`</span><span class="sxs-lookup"><span data-stu-id="23318-180">**Scopes**: `openid profile Calendars.ReadWrite MailboxSettings.Read User.Read`</span></span>

1. <span data-ttu-id="23318-181">Wählen Sie den **GraphBotAuth** -Eintrag unter **OAuth Connection Settings** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-181">Select the **GraphBotAuth** entry under **OAuth Connection Settings**.</span></span>

1. <span data-ttu-id="23318-182">Wählen Sie **Verbindung testen** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-182">Select **Test Connection**.</span></span> <span data-ttu-id="23318-183">Dadurch wird ein neues Browserfenster oder eine neue Registerkarte geöffnet, um den OAuth-Fluss zu starten.</span><span class="sxs-lookup"><span data-stu-id="23318-183">This opens a new browser window or tab to start the OAuth flow.</span></span>

1. <span data-ttu-id="23318-184">Melden Sie sich bei Bedarf an.</span><span class="sxs-lookup"><span data-stu-id="23318-184">If necessary, sign in.</span></span> <span data-ttu-id="23318-185">Überprüfen Sie die Liste der angeforderten Berechtigungen, und wählen Sie dann **akzeptieren** aus.</span><span class="sxs-lookup"><span data-stu-id="23318-185">Review the list of requested permissions, then select **Accept**.</span></span>

1. <span data-ttu-id="23318-186">Es sollte eine **Test Verbindung mit der erfolgreichen Nachricht "GraphBotAuth" angezeigt werden** .</span><span class="sxs-lookup"><span data-stu-id="23318-186">You should see a **Test Connection to 'GraphBotAuth' Succeeded** message.</span></span>

> [!TIP]
> <span data-ttu-id="23318-187">Sie können auf dieser Seite die Schaltfläche " **Token kopieren** " auswählen und das Token in einfügen, [https://jwt.ms](https://jwt.ms) um die Ansprüche innerhalb des Tokens anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="23318-187">You can select the **Copy Token** button on this page, and paste the token into [https://jwt.ms](https://jwt.ms) to see the claims inside the token.</span></span> <span data-ttu-id="23318-188">Dies ist hilfreich bei der Behandlung von Authentifizierungsfehlern.</span><span class="sxs-lookup"><span data-stu-id="23318-188">This is useful when troubleshooting authentication errors.</span></span>

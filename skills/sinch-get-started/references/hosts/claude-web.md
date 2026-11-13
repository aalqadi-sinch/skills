# Sinch get started: Claude chat

> Host steps for Claude in the browser (claude.ai), the desktop app's chat, and the mobile apps. For Claude Code, use `claude-code.md`. Facts come from Anthropic's help page on custom connectors (https://support.claude.com/en/articles/11175166). Follow the rules in [chat-apps.md](chat-apps.md); this file adds the clicks.

## Step 3: the Sinch docs (optional)

Custom connectors work on every plan; the Free plan allows one. Add it in the browser or desktop app.

**Free, Pro and Max:**

1. Open **Customize → Connectors**.
2. Click **+ Add**, then **Add custom connector**.
3. Name: **Sinch Docs**. URL: `https://developers.sinch.com/mcp`. Click **Continue**.
4. Under **Authentication**, choose **No sign in**, then click **Add**.

**Team and Enterprise:** only an Owner can add a custom connector, under **Organization settings → Connectors**. If the user is not an Owner, tell them to ask one, or skip. Once an Owner has added it, the user finds it under **Customize → Connectors** and clicks **Connect**.

**Use it in a chat:** click **+** at the bottom left of the message box, choose **Connectors**, and switch **Sinch Docs** on.

On a Free plan that already has a custom connector, skip this step rather than asking the user to remove one.

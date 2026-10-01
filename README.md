# RedstoneLink

RedstoneLink connects a Minecraft Java Edition world to a local website. The Forge mod connects outward to the backend on the same computer. The website can then control registered RedstoneLink blocks.

## What you need

- Minecraft Java Edition 1.20.1.
- Minecraft Forge for 1.20.1 (Forge 47.x).
- Node.js installed on the computer that will run the backend.
- A Microsoft app registration and its Application (client) ID for Microsoft sign-in.
- Java 17 if you want to build the mod JAR yourself.

The computer running Minecraft must also run the RedstoneLink backend because the mod connects to `127.0.0.1:3000`.

## 1. Set up Microsoft sign-in

The backend needs the client ID from your Microsoft app registration. It does not use a client secret or redirect URI for device-code sign-in.

If `server/.env` does not exist, create it and put these lines in it:

```text
MICROSOFT_CLIENT_ID=YOUR_APPLICATION_CLIENT_ID
PORT=3000
```

Replace the example client ID with your app's **Application (client) ID**. In the Microsoft app registration, enable **Allow public client flows** and add Microsoft Graph **User.Read** as a delegated permission. Keep `.env` private; do not upload it to GitHub or put it in website JavaScript.

## 2. Start the website and backend

If you downloaded a source copy without `server/node_modules`, open PowerShell in the project folder and run:

```powershell
cd server
npm.cmd install
cd ..
```

Then double-click `Start-RedstoneLink.bat` and keep the backend window open. It opens the site at `http://localhost:3000/`. When Minecraft starts with the mod and connects to this backend, the mod also tries to open the site automatically.

### Open the site manually

If the launcher or mod does not open your browser, you can start the backend and open the site yourself:

1. Open **PowerShell** in the RedstoneLink project folder. In File Explorer, open that folder, click the address bar, type `powershell`, and press **Enter**.
2. In PowerShell, enter this command and press **Enter**:

   ```powershell
   cd .\server; npm.cmd start
   ```

   Leave this terminal window open. It should say `RedstoneLink server started` and `Website and WebSocket backend listening on port 3000`.
3. Open your browser and go to **http://localhost:3000/**.

If PowerShell says `Cannot find module`, install the backend packages once by running `cd .\server; npm.cmd install`, then run `npm.cmd start` again. If it says `EADDRINUSE` for port 3000, the backend is already running in another terminal; leave that one open and just open the browser address above.

To use the site from another phone or computer on the same Wi-Fi, open `http://HOST-COMPUTER-IP:3000/`. The launcher prints possible IPv4 addresses. Replace `HOST-COMPUTER-IP` with the Minecraft computer's Wi-Fi IPv4 address. `localhost` on a phone means the phone, so use the computer's IP instead.

RedstoneLink is set up for your local network. Do not forward port 3000 from your router to the public internet.

## 3. Install the mod

1. Close Minecraft.
2. Copy `minecraftmod/build/libs/redstonelink-1.0.0.jar` into `%appdata%\.minecraft\mods`.
3. Launch Minecraft with the Forge 1.20.1 profile.

If the JAR does not exist yet, build it from the project:

1. Open PowerShell in the `minecraftmod` folder.
2. Run `.\gradlew.bat build`.
3. Find the JAR in `minecraftmod/build/libs/`.

The first Gradle build may need internet access to download Forge and Minecraft build files.

## 4. Link Minecraft and use a block

1. On the website, click **Sign in with Microsoft** and follow the displayed verification link and code.
2. Start Minecraft with the mod and enter a world. In chat, find the RedstoneLink one-time code.
3. Enter that code in **Link your Minecraft world** on the website.
4. Get the block with `/give @p redstonelink:redstone_link_block`, then place it.
5. Right-click the block to register it. Its website control should appear.
6. To name the control, enter a name and click **Save name**. Click the control to pulse the block's redstone output. Breaking the block removes its control from the site.

Sign in with the same Microsoft account on each device. Restarting the backend clears active sessions and linked Minecraft worlds; sign in and link the world again after a restart.

## Building and development

- Website page content: `index.html`
- Website appearance: `style.css`
- Website behavior and block controls: `script.js`
- Backend and WebSocket routing: `server/server.js`
- Forge mod code: `minecraftmod/src/main/java/redstonelink/`
- Mod resources such as block models and names: `minecraftmod/src/main/resources/`

After editing website files, save and press **Ctrl+F5** in the browser. Restart the backend after editing `server/server.js`. After editing Java mod code, rebuild with `.\gradlew.bat build` from `minecraftmod` and install the new JAR.

## Troubleshooting

- **The site says it cannot be reached:** make sure the backend window is still open. Try `http://localhost:3000/` on the Minecraft computer.
- **Microsoft sign-in will not start:** verify `MICROSOFT_CLIENT_ID`, public client flows, and Graph `User.Read`; restart the backend after editing `.env`.
- **No Minecraft world appears:** make sure Minecraft is open with Forge and the mod, then link the current one-time code on the site.
- **A block has no website control:** right-click the block in Minecraft after linking the world.
- **Another device cannot open the site:** connect it to the same Wi-Fi and use the Minecraft computer's IPv4 address with `:3000`.

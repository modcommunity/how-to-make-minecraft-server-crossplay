A guide on how to install and configure [Geyser](https://geysermc.org/) and [Floodgate](https://geysermc.org/wiki/floodgate/) so that [Minecraft: Bedrock Edition](https://www.minecraft.net/) players can join your Java Edition server. This guide covers [Spigot/Paper](https://www.spigotmc.org/), [Fabric](https://fabricmc.net/), [NeoForge](https://neoforged.net/), [BungeeCord](https://www.spigotmc.org/wiki/bungeecord/), [Velocity](https://velocitypowered.com/), [ViaProxy](https://github.com/ViaVersion/ViaProxy), and the standalone build.

[**View Guide On TMC (Recommended Due To Better Formatting)**](https://moddingcommunity.com/blog/how-to-make-minecraft-server-crossplay)

Geyser is a bridge that translates between the two editions of Minecraft. It makes your Java Edition server appear to a Bedrock client as though it were a Bedrock server, which means players on phones, tablets, consoles, and the Windows 10/11 edition can join a server they otherwise could not see. Floodgate is the companion to it, and it is what lets those Bedrock players in without owning a Java Edition account.

## Table of Contents
- [Requirements](#requirements)
- [How Geyser and Floodgate Work Together](#how-geyser-and-floodgate-work-together)
- [Downloading](#downloading)
    - [Which File Do I Need?](#which-file-do-i-need)
    - [Direct Download Links](#direct-download-links)
- [Installing Geyser](#installing-geyser)
    - [Spigot & Paper](#spigot--paper)
    - [Fabric](#fabric)
    - [NeoForge](#neoforge)
    - [BungeeCord](#bungeecord)
    - [Velocity](#velocity)
    - [ViaProxy](#viaproxy)
    - [Standalone](#standalone)
- [Configuring Geyser](#configuring-geyser)
    - [The Bedrock Section](#the-bedrock-section)
    - [The Remote Section (Standalone and ViaProxy Only)](#the-remote-section-standalone-and-viaproxy-only)
- [Installing Floodgate](#installing-floodgate)
    - [Spigot & Paper](#spigot--paper-1)
    - [Fabric & NeoForge](#fabric--neoforge)
    - [BungeeCord & Velocity](#bungeecord--velocity)
- [Setting `auth-type` To floodgate](#setting-auth-type-to-floodgate)
- [Running Floodgate Behind a Proxy](#running-floodgate-behind-a-proxy)
- [Opening the Port](#opening-the-port)
- [Connecting From Bedrock](#connecting-from-bedrock)
- [Notes](#notes)
    - [Older Minecraft Versions Need ViaVersion](#older-minecraft-versions-need-viaversion)
    - [Linking Java and Bedrock Accounts](#linking-java-and-bedrock-accounts)
    - [Bedrock Players & Java Mods](#bedrock-players--java-mods)
    - [Things That Cannot Share the Port](#things-that-cannot-share-the-port)
- [See Also](#see-also)
- [Conclusion](#conclusion)

## Requirements
- A Minecraft: Java Edition server you control the files of. A host that does not let you upload jars cannot run Geyser.
- Java 21 or later, which is what current Minecraft server software requires anyway.
    - Download From: [Adoptium](https://adoptium.net/) or [Oracle](https://www.oracle.com/java/technologies/downloads)
- A **UDP** port that you can open and [port forward](https://portforward.com/). Bedrock Edition uses UDP, not TCP, and this is the single most common thing people get wrong.
- If you are on a shared host, check that they allow a second port. Some only give you one.

## How Geyser and Floodgate Work Together
I'd like to briefly explain how the two plugins work together, because installing one and not the other can result in Bedrock players being unable to connect properly (e.g. they may need to connect their Java account still).

**Geyser** does the `Java <-> Bedrock` translation. It listens on a UDP port, accepts Bedrock connections, and speaks the Java Edition's protocol to your server on the other side. On its own, Geyser will let a Bedrock player connect, but that player still has to authenticate as a Java Edition account, which most Bedrock players do not have.

**Floodgate** removes that requirement. It tells the Java server to accept a player who has been approved by Geyser, without needing a Java account. This is why the two are almost always installed together, and it is why Floodgate ships a `key.pem` file that you are told in fairly strong terms not to share. That key is the thing that lets an account skip Java authentication.

Geyser is installed once, in the place your Bedrock players will point at. Floodgate is installed on the server that owns the player list. On a single server, that is the same machine. On a proxy network, Geyser and Floodgate both go on the proxy.

## Downloading
Everything comes from the [official download page](https://geysermc.org/download). We do not recommend downloading this elsewhere.

At the time of this writing, the current builds are **Geyser build #1234** (2 September 2026) and **Floodgate build #140** (9 August 2026). Both projects publish continuously, so the build numbers you see will be higher. That is normal and expected.

### Which File Do I Need?
Geyser publishes a build for every platform. Floodgate does not, and this catches people out:

| Platform | Geyser | Floodgate |
| --- | --- | --- |
| Spigot / Paper | `Geyser-Spigot.jar` | `floodgate-spigot.jar` |
| Fabric | `Geyser-Fabric.jar` | `floodgate-fabric.jar` |
| NeoForge | `Geyser-NeoForge.jar` | `floodgate-neoforge.jar` |
| BungeeCord | `Geyser-BungeeCord.jar` | `floodgate-bungee.jar` |
| Velocity | `Geyser-Velocity.jar` | `floodgate-velocity.jar` |
| ViaProxy | `Geyser-ViaProxy.jar` | Not available |
| Standalone | `Geyser-Standalone.jar` | Not available |

There is **no Floodgate build for ViaProxy or for standalone Geyser**. Floodgate has to run inside the Java server that owns the player list, and in those two setups Geyser is running externally. You install the platform-specific Floodgate jar on the Java server you are pointing Geyser at, and then copy that server's `key.pem` across to Geyser. There is a section on this further down.

### Direct Download Links
These URLs always resolve to the newest build, so they are safe to bookmark or to script!

- [Spigot](https://download.geysermc.org/v2/projects/geyser/versions/latest/builds/latest/downloads/spigot)
- [Fabric](https://download.geysermc.org/v2/projects/geyser/versions/latest/builds/latest/downloads/fabric)
- [NeoForge](https://download.geysermc.org/v2/projects/geyser/versions/latest/builds/latest/downloads/neoforge)
- [BungeeCord](https://download.geysermc.org/v2/projects/geyser/versions/latest/builds/latest/downloads/bungeecord)
- [Velocity](https://download.geysermc.org/v2/projects/geyser/versions/latest/builds/latest/downloads/velocity)
- [ViaProxy](https://download.geysermc.org/v2/projects/geyser/versions/latest/builds/latest/downloads/viaproxy)
- [Standalone](https://download.geysermc.org/v2/projects/geyser/versions/latest/builds/latest/downloads/standalone)
- [Floodgate Spigot](https://download.geysermc.org/v2/projects/floodgate/versions/latest/builds/latest/downloads/spigot)
- [Floodgate Bungee](https://download.geysermc.org/v2/projects/floodgate/versions/latest/builds/latest/downloads/bungee)
- [Floodgate Velocity](https://download.geysermc.org/v2/projects/floodgate/versions/latest/builds/latest/downloads/velocity)

On a Linux server you can pull one down directly:

```bash
wget -O plugins/Geyser-Spigot.jar \
  https://download.geysermc.org/v2/projects/geyser/versions/latest/builds/latest/downloads/spigot
```

## Installing Geyser
Pick the one section that matches your server setup. You only need to follow the instructions for that setup.

### Spigot & Paper
1. Download `Geyser-Spigot.jar`.
2. Put it in your server's `plugins/` folder.
3. Restart the server. Do not use `/reload`, as plugin reloads cause more problems than they solve.
4. Geyser generates its config at `plugins/Geyser-Spigot/config.yml`.

### Fabric
1. Download `Geyser-Fabric.jar`.
2. Put it in your server's `mods/` folder.
3. Fabric servers also need the [Fabric API](https://modrinth.com/mod/fabric-api) mod in the same folder.
4. Restart the server. The config appears at `config/Geyser-Fabric/config.yml`.

### NeoForge
1. Download `Geyser-NeoForge.jar`.
2. Put it in your server's `mods/` folder.
3. Restart the server. The config appears at `config/Geyser-NeoForge/config.yml`.

### BungeeCord
Install Geyser on the **proxy**, not on the backend servers. The proxy is what Bedrock players will connect to.

1. Download `Geyser-BungeeCord.jar`.
2. Put it in the proxy's `plugins/` folder.
3. Restart the proxy. The config appears at `plugins/Geyser-BungeeCord/config.yml`.

### Velocity
Same idea as BungeeCord. Geyser goes on the proxy.

1. Download `Geyser-Velocity.jar`.
2. Put it in the proxy's `plugins/` folder.
3. Restart the proxy. The config appears at `plugins/Geyser-Velocity/config.yml`.

### ViaProxy
[ViaProxy](https://github.com/ViaVersion/ViaProxy) is a standalone proxy that lets clients on one Minecraft version join a server on another. Geyser plugs into it as an addon.

1. Download `Geyser-ViaProxy.jar`.
2. Put it in ViaProxy's `plugins/` folder.
3. Start ViaProxy once so Geyser generates its config, then stop it.
4. Because Geyser is running outside your Java server here, you have to tell it where that server is. See [The Remote Section](#the-remote-section-standalone-and-viaproxy-only).

### Standalone
Standalone is Geyser running as its own program, on its own, pointed at a Java server over the network. It is the right choice when you cannot install plugins or mods on the server itself, and it is the wrong choice in almost every other case, because it adds a network hop and it cannot use Floodgate without extra setup.

1. Download `Geyser-Standalone.jar`.
2. Put it in a folder of its own.
3. Run it:

```bash
java -Xmx1G -jar Geyser-Standalone.jar
```

4. It generates `config.yml` beside itself and then exits or waits. Stop it, edit the config, and start it again.

## Configuring Geyser
Open the `config.yml` for your platform. The two sections that matter are `bedrock` and, on standalone and ViaProxy, `remote`.

### The Bedrock Section
This is what Bedrock players connect to:

```yaml
bedrock:
  # The IP address that will listen for connections.
  # Generally, you should only change this if you want to limit what IPs can connect to your server.
  address: 0.0.0.0
  # The port that will Geyser will listen on for incoming Bedrock connections.
  # Since Minecraft: Bedrock Edition uses UDP, this port must allow UDP traffic.
  port: 19132
  # Some hosting services change your Java port everytime you start the server and require the same port to be used for Bedrock.
  # This option makes the Bedrock port the same as the Java port every time you start the server.
  clone-remote-port: false
```

The port is the part that matters. `19132` is the Bedrock Edition default, and leaving it there means players can add your server by address alone without typing a port. Change it only if 19132 is already taken or your host assigns you something else.

`clone-remote-port` is for hosts that hand you a new port on every restart and expect Bedrock traffic on the same one. If you turn it on, it **overwrites** the `port` value above, so setting both and wondering why your port is ignored is a trap worth knowing about.

`address: 0.0.0.0` means "listen on every network interface", which is what you want unless you are deliberately restricting access.

### The Remote Section (Standalone and ViaProxy Only)
When Geyser runs as a plugin or mod, it already knows where the server is, because it is inside it. When it runs standalone or under ViaProxy, you have to configure the `remote` section to point to your Java server.

```yaml
remote:
  address: 127.0.0.1
  port: 25565
  auth-type: floodgate
```

Use `127.0.0.1` (localhost) if the Java server is on the same machine. Use its real address if it is not, and be aware that you have just put an unencrypted Minecraft connection on the network between the two.

## Installing Floodgate
Floodgate should be put on the Minecraft Java Server that owns the player list.

### Spigot & Paper
1. Download `floodgate-spigot.jar` from the download page.
2. Put it in the `plugins/` folder.
3. Restart the server.

### Fabric & NeoForge
1. Download the mod from the [Modrinth page](https://modrinth.com/mod/floodgate) or the download page.
2. Place the file into the `mods/` folder. Fabric servers also requires the [Fabric API](https://modrinth.com/mod/fabric-api) installed onto the server.
3. Restart the server.

If the Fabric server sits behind a Velocity proxy, you will also need to configure `FabricProxyLite` so the Fabric server accepts forwarded player data from Velocity.

### BungeeCord & Velocity
Install Floodgate on the **proxy only**. You do not need it on the backend servers unless you specifically want to use the Floodgate API there, or you want Bedrock skins to display correctly on the backend.

1. Download `floodgate-bungee.jar` or `floodgate-velocity.jar`.
2. Place the file into the proxy's `plugins/` folder.
3. Restart the proxy.

## Setting `auth-type` To floodgate
This is the step that actually lets Bedrock players in, and many people accidently skip this step (including myself when I first installed Geyser and Floodgate years ago, lol).

Open **Geyser's** config file (not Floodgate's), find the `auth-type` key, and set it to `floodgate`:

```yaml
auth-type: floodgate
```

The three values it accepts are:

- **`floodgate`** - Bedrock players join without a Java Edition account. This is what you want, and it requires Floodgate to be installed.
- **`online`** - every player must own and authenticate a Java Edition account. This is the default.
- **`offline`** - no authentication at all. Not recommended on public servers.

Restart the server after applying any changes. Geyser reads this setting on server startup.

## Running Floodgate Behind a Proxy
If you have a proxy with backend servers behind it, and you want the Floodgate API available on those backends, there is a bit more to do. Skip this section entirely if you have one server.

1. Install Floodgate on the proxy first, as above.
2. Install Floodgate on each backend server as well, using `floodgate-spigot.jar` or `floodgate-fabric.jar`.
3. Turn on player information forwarding:
    - **BungeeCord**: set `ip_forward` to `true` in the proxy's `config.yml`, and set `bungeecord` to `true` in each backend's `spigot.yml`.
    - **Velocity**: set up [player information forwarding](https://docs.papermc.io/velocity/player-information-forwarding/). Velocity has several modes and each needs a different setup on the backend, so follow their guide rather than guessing.
4. Start the proxy so Floodgate generates its files.
5. In the proxy's Floodgate config, set `send-floodgate-data` to `true`.
6. Copy the `key.pem` file from the proxy's Floodgate config folder into every backend server's Floodgate config folder.
7. Restart the backends and the proxy.

**WARNING**: As stated before, do **not share** `key.pem` with anybody, and do not paste it into a support channel when asking for help. That file is what allows Bedrock accounts to bypass Java Edition authentication. Anyone who holds a copy can log into your server as basically anyone if they are capable of doing so.

The same `key.pem` copy is how you use Floodgate with standalone Geyser or with ViaProxy. Install the platform Floodgate jar on the Java server, then copy that server's `key.pem` file into Geyser's own config folder.

## Opening the Port
Bedrock Edition uses **UDP**. If you try to open just TCP, the server will not display in the server list for Bedrock clients.

On a Linux server, you can typically achieve this using the installed firewall.

```bash
# IPTables (underlying firewall used by ufw on many Linux distributions)
iptables -A INPUT -p udp --dport 19132 -j ACCEPT

# UFW (Uncomplicated Firewall)
sudo ufw allow 19132/udp

# Firewalld (used by many Linux distributions)
sudo firewall-cmd --permanent --add-port=19132/udp
sudo firewall-cmd --reload
```

If you are *hosting from home*, you will most likely need to forward **UDP** port `19132` on your router to the machine running Geyser. If you are on a hosting provider, this is usually a port allocation in their panel rather than a firewall rule, and you may need to open a ticket to be given a second port.

## Connecting From Bedrock
On the Bedrock client, go to **Play**, then the **Servers** tab, then scroll to the bottom and choose **Add Server**.

- **Server Name**: anything you like.
- **Server Address**: your server's IP address or hostname.
- **Port**: `19132`, or whatever you set in the config.

**NOTE**: This works weird with **console players**. The PlayStation, Xbox, and Nintendo Switch editions do not let you add a server address directly, and the usual workaround is a DNS trick that redirects one of the featured servers to yours. That is outside what this guide covers, but worth looking into if needed.

## Notes
Here are some useful notes.

### Older Minecraft Versions Need ViaVersion
Geyser targets current Minecraft. If your Java server is running a version older than **26.2** (or the latest version of Minecraft), you will also need [ViaVersion](https://github.com/ViaVersion/ViaVersion) so that the old network protocols can be understood. Geyser's own [supported versions page](https://geysermc.org/wiki/geyser/supported-versions/) is what we'd recommend checking out for more info.

### Linking Java and Bedrock Accounts
By default a Bedrock player joining through Floodgate gets their own separate account on your server, with a prefix on their name (a dot, by default). If you want a player to be able to use the same inventory, permissions, and playtime whether they join from Java or from Bedrock, that is **account linking**.

There are two ways to do it. Global linking, which uses [link.geysermc.org](https://link.geysermc.org/) and needs no setup from you. Local linking keeps the links in your own database and needs one of the Floodgate database extensions, published on the same download page:

- `floodgate-sqlite-database.jar`: The simplest (local), and fine for a single server.
- `floodgate-mysql-database.jar`: For MySQL databases.
- `floodgate-mongo-database.jar`: For MongoDB databases.

Most server owners will opt into using global linking as it requires no additional setup. Make sure your players know to link their accounts at [link.geysermc.org](https://link.geysermc.org/)!

### Bedrock Players & Java Mods
Geyser translates the **vanilla** network protocol that Minecraft uses. It does not translate custom blocks and items added by mods, which may look wrong or invisible to a Bedrock player. [Hydraulic](https://geysermc.org/wiki/hydraulic/) is GeyserMC's own project for this, and it converts mod assets into a Bedrock resource pack. We definitely recommend checking it out if you plan to run mods on your server.

### Things That Cannot Share the Port
Anything else on your server that wants UDP on the same port will conflict with Geyser. The common ones are voice chat plugins (Simple Voice Chat in particular) and the Minecraft Query protocol. They must run on their **own port**.

## See Also
- [How To Install A Minecraft Server (Java Edition)](https://moddingcommunity.com/blog/)
- [How Much RAM For A Minecraft Server](https://moddingcommunity.com/blog/)
- [Geyser Wiki](https://geysermc.org/wiki/geyser/)
- [Floodgate Wiki](https://geysermc.org/wiki/floodgate/)

## Conclusion
Crossplay is a two part job. Geyser opens a UDP door that Bedrock clients can walk through, and Floodgate is what stops them being asked for a Java Edition account on the way in. Install the pair, set `auth-type` to `floodgate`, open the port as **UDP**, and the rest is checking that you put each jar in the folder its platform actually reads.

If it does not work, the order to check things in is almost always the same: is the port open as UDP, is `auth-type` set to `floodgate` in Geyser's config rather than Floodgate's, and is Floodgate installed on the server that owns the player list rather than on a backend behind a proxy.

If you have any questions or are looking for a modding community to be a part of, consider joining our [Discord server](https://discord.moddingcommunity.com)!

Guides we create are always open to edits and improvements, so if you have any suggestions or notice any issues, feel free to contribute by creating a [pull request](https://github.com/modcommunity/how-to-make-minecraft-server-crossplay/pulls) on our GitHub repository!

Happy modding!
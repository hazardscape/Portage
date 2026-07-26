# Connecting a Meshtastic Node to Portage - Community Guide

A step-by-step guide for getting your Meshtastic nodes onto your community's Portage map,
and claiming them so they belong to your community. Written to be shared with community
members. No coding required.

---

## What this lets you do

Once a node is connected, it appears as a live marker on your community's **Portage** map,
with its name, last-seen time, battery, position, and any sensor readings (temperature,
air quality, and so on). Your community admin then **claims** the node so it belongs to your
community's map. This is part of the free Portage + Community Assets features.

---

## What you need

- **At least one "gateway" node** - a Meshtastic device with Wi-Fi (an ESP32 board such as a
  Heltec or T-Beam). The gateway is the node that talks to the Portage broker over Wi-Fi.
- **Sensor / field nodes (optional)** - other Meshtastic nodes relay their data to the gateway
  over LoRa, so only the gateway needs Wi-Fi. A single gateway is enough to start.
- **A 2.4 GHz Wi-Fi network** the gateway can join that can reach the Portage broker. The ESP32
  radios do **not** support 5 GHz Wi-Fi.
- **The Meshtastic app** on your phone (to configure the node over Bluetooth).
- **A Portage login** at your community's Portage address, and to claim nodes, a **community
  admin** account.

---

## Connection settings (from your community profile)

Your gateway node connects to the Portage cloud broker over the internet (TLS-encrypted). Your
community's **username, password, and root topic** are shown in the app: sign in as the community
admin, open your **community profile** (or the Portage map), and find the **"Portage nodes"** card.
Copy the values from there into the Meshtastic app.

| Setting | Value |
|---|---|
| MQTT broker address | `app.hazardscape.com` |
| Port | `8883` |
| TLS / encryption to the server | **ON** |
| Username | from the Portage nodes card |
| Password | from the Portage nodes card |
| Root topic | `hs/<your code>` (from the Portage nodes card) |

> Each community has its own login and its own root topic (`hs/<your code>`), so your nodes are
> kept separate from every other community. One login covers all of your nodes. Portage runs its
> own broker - never point your nodes at the public `mqtt.meshtastic.org` broker. The connection
> to `app.hazardscape.com:8883` is protected by TLS. Your gateway node only needs normal internet
> access (any 2.4 GHz Wi-Fi that can reach the internet).

---

## Step 1 - Get into Portage (free)

1. Go to your Portage sign-in page and **join / create your account** (it is free).
2. One person becomes the **community admin** (Hazardscape approves this). Other members then
   join that same community by invite.
3. Sign in. From the menu, open **Portage** to see your community's map, and **Community Assets**
   to map shelters, water, and other resilience assets.

---

## Step 2 - Put the node on Wi-Fi

In the Meshtastic app, connect to your node over Bluetooth, then open **Settings > Network**:

- **Wi-Fi:** enable it and choose your **2.4 GHz** network; enter the password.
- **IPv4 Mode:** leave on **DHCP** if your router assigns addresses automatically (recommended).
  Use **STATIC** only if you were given a fixed IP, gateway, and subnet to enter.
- Save. The node reboots and joins Wi-Fi.

---

## Step 3 - Point the node at the Portage broker (MQTT)

In the Meshtastic app, open **Settings > Module Settings > MQTT** and enter the values from your
settings table above:

- **MQTT Server / Address:** `app.hazardscape.com` (use port `8883` if the app asks)
- **Username / Password:** as provided by Hazardscape
- **TLS / Encryption to server (root CA):** **ON**  (Portage uses port 8883 with TLS)
- **Encryption Enabled (message encryption):** **OFF**  (leave the channel unencrypted so the
  decoded JSON can be read; the TLS above still protects the connection)
- **JSON Output Enabled:** **ON**  (required - Portage reads the JSON feed)
- **Root topic:** `hs/<your code>` (copy the exact value from the Portage nodes card)
- **Proxy to Client Enabled:** **OFF**
- **Map Reporting / position broadcast:** **ON**, so the node reports where it is

Save. The node reboots and starts publishing to the broker. Your gateway node needs current
Meshtastic firmware for TLS MQTT to work reliably.

---

## Step 4 - Make sure the node has a position

A node only appears on the map once it reports a **position**.

- If the node **has GPS**, make sure GPS / position is enabled and it has a fix (outdoors helps).
- If the node has **no GPS** (many indoor gateway boards do not), set a **Fixed Position** in the
  Meshtastic app (**Settings > Position > Fixed Position**) and enter its latitude and longitude.
  Without a fixed position, a GPS-less node will connect but will **not** show on the map.

---

## Step 5 - See it on the map

1. In Portage, open the **Portage** map.
2. Within a few minutes your node should appear as a marker (name, battery, last-seen).
3. New nodes may start **unclaimed** or in a default bucket until an admin claims them (Step 6).

> Nodes that go silent for more than **24 hours** drop off the map automatically, and reappear
> the next time they report in.

---

## Step 6 - Claim the node to your community (community admin)

Claiming makes the node belong to your community's map. **Only a community admin can claim.**

1. Sign in as the **community admin** and open the **Portage** map (`/mesh`).
2. Find the **Node ownership** panel (in the side rail / drawer on the Portage page).
   - Members who are not community admins will not see this panel - ask your admin.
3. If you administer more than one community, pick the right community from the selector.
4. Next to your node (identified by its name and its `!` id), click **Claim**.
   - The node moves onto your community's map.
5. To hand a node back, click **Release** - it becomes unclaimed again.

Only unclaimed nodes and your own community's nodes are listed, so you can never claim another
community's node.

---

## Who can see your node

Once your node is on the map, **you choose who can see it**. On the **Portage** page, find the **My nodes** panel:

1. **Take ownership** of your node - a button next to any unclaimed node in your community. A node you take starts **Private**.
2. Set its visibility:
   - **Private (only me)** - only you see it on the map, and it is not sent to ATAK.
   - **Community** - everyone in your community sees it.
   - **Public (everyone)** - everyone on the platform (all communities) sees it, and it is shared to ATAK. You are asked to confirm first.

A **community admin** can require approval before a member makes a node **Public** (a toggle in the Node ownership panel); the request then waits for the admin to approve.

**Markers** you drop on the map have the same three choices - Private / Community / Public - picked when you place the marker. A private marker is visible only to you and is not sent to ATAK.

---

## Troubleshooting

**Node does not appear on the map**
- Confirm the gateway is on **2.4 GHz** Wi-Fi and actually connected (the Meshtastic app shows
  its IP).
- Confirm **JSON Output = ON** and the **Root topic** exactly matches the `hs/<your code>` value
  shown on the Portage nodes card.
- Confirm the node has a **position** (GPS fix, or a Fixed Position set - see Step 4).
- Confirm the broker **address, port, username, and password** match the settings table.
- Give it a few minutes after saving; the node reboots and then reports in.

**Node shows up but in the wrong / no community**
- Have your community admin **Claim** it (Step 6).

**Node disappeared**
- If it has been offline more than 24 hours it drops off automatically; it returns when it
  reports again.

---

## Good to know

- Your gateway connects to Portage over **TLS on port 8883**, so the link is encrypted. Never
  point nodes at the public `mqtt.meshtastic.org` broker - use `app.hazardscape.com` so your
  data stays with Portage.
- Sensor and gateway nodes are optional to the map - you can also add community assets and
  markers by hand in Portage and Community Assets without any hardware.
- Your node and map data belong to your community. Claiming and releasing nodes, and who can see
  them, stay under your community's control.

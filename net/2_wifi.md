# infrastructure vs ad-hoc

* https://www.linux-magazin.de/ausgaben/2008/12/vermascht/

HotSpot and Ad-Hoc can both be used to share your internet connection, and in some respect they are two of the same. They differ in what they are intended for.

# HotSpot

Generally speaking, a HotSpot is what you are using when accessing Internet when you’re at a Café or a Hotel. Usually you need a username  and/or password to log on through a web-Portal to be able to connect to  the Internet.  HotSpot requires a Wi-Fi connection and works like your  Wi-Fi Network at home. The HotSpot will normally only allow you to use  the Internet. Not interact with other computers or gadgets using the  same HotSpot.

# Ad-Hoc

An ad hoc wireless network is when wireless devices communicate in a **peer-to-peer** (P2P) manner without a central controller (AP or router). A client workstation can be configured to operate in ad hoc mode enabling another device to connect to it. **Bluetooth** and **Wi-Fi Direct** are examples of ad hoc mode. 

* keine zentrale Verwaltung

* alle Teilnehmer sind gleichberechtigt und gleichzeitig sowohl Client als auch Master


**The IEEE 802.11 standard refers to an ad hoc network as an IBSS (Independent Basic Service Set)**

So lassen sich auch ohne feste Infrastruktur schnell  kleine Netze aufbauen.

A variation of the ad hoc topology is when a smart phone or tablet with cellular data access is enabled to create a personal **hotspot**. This feature is sometimes referred to as **Tethering**. A hotspot is usually a temporary quick solution that enables a smart phone to provide the wireless services of a Wi-Fi router. Other devices can  associate and authenticate with the smart phone to use the Internet  connection. The Apple iPhone refers to this as the Personal Hotspot  feature, while Android devices refer to as either Tethering or Portable  Hotspot. 

If you have multiple computers in your household, and all are running Windows 7, you might be using an Ad-Hoc Network already, otherwise  known as **HomeGroup**.  The Ad-Hoc gives you much of the same functionality you would come to expect from a wired network, like Printer- and  Filesharing.

# Mesh

- mehrere verteilte Geräte erzeugen mehrere WLAN Netze und fassen sie zu einem einzigen Netz zusammen – wie Maschen (mesh).
- Das gesamte WLAN hat praktischerweise nur noch einen Namen und ein Passwort.  
- Zentrale des Mesh ist der Router. Ergänzt werden: WLAN Repeater und Powerline / D-LAN-Geräte (Stromleitung als zusätzliche Datenautobahn)

# Security

### wps

**Wi-Fi Protected Setup**

- Zwei WLAN-fähige Geräte werden per Knopfdruck verbunden. Die Eingabe eines Passworts entfällt.
- Funktion wird von Hackern gerne ausgenutzt
- Andererseits: Kennt jemand das Kennwort für Ihren WLAN-Zugang, kann er Ihr Internet nutzen, auch ohne sich in Ihren vier Wänden zu befinden. Nutzt Ihr Router WPS, ist das nicht mehr möglich.
- hilfreich bei der Einrichtung von Geräten ohne Display. Ein WLAN-Repeater, der das WLAN-Signal vom Router auffängt und verstärkt, muss bei der Einrichtung mit dem Router verbunden werden. Die Eingabe eines Passworts ist ohne ein Display nicht möglich, hier wird WPS benötigt.

# WIFI GENERATIONS

| Generation            | IEEE standard | Adopted | Maximum link rate (Mbit/s) | Radio frequency (GHz)  |
| --------------------- | ------------- | ------- | -------------------------- | ---------------------- |
| **Wi-Fi 8**           | 802.11bn      | 2028    | 100,000                    | 2.4, 5, 6, 7, 42.5, 71 |
| **Wi-Fi 7** (**EHT**) | 802.11be      | 2024    | 1376–46,120                | 2.4, 5, 6              |
| **Wi-Fi 6** (**HE**)  | 802.11ax      | 2019    | 574–96082.4, 5             | 2.4, 5                 |
| **Wi-Fi 5** (**VHT**) | 802.11ac      | 2014    | 433–6933                   | 5                      |
| **Wi-Fi 4** (**HT**)  | 802.11n       | 2008    | 72–600                     | 2.4, 5                 |
| **(Wi-Fi 3)**         | 802.11g       | 2003    | 6–54                       | 2.4                    |
| **(Wi-Fi 2)**         | 802.11a       | 1999    | 5                          |                        |
| **(Wi-Fi 1)**         | 802.11b       | 1999    | 1–11                       | 2.4                    |
| **(Wi-Fi 0)**         | 802.11        | 1997    | 1–2                        | 2.4                    |

## check supported wifi standards

```sh
iw list | less # check my wifi card capabilities
# look for VHT (wifi 5)
sudo lshw -class network # find manufacturer and model
```

```sh
sudo iw wlp8s0 scan ssid <ssi> | less # check router capabilities
```

|                                     | Generation | Frequency   |
| ----------------------------------- | ---------- | ----------- |
| **HT** (High Throughput)            | Wifi 4     | 2.4 Ghz     |
| **VHT** (Very High Throughput)      | Wifi 5     | 5 Ghz       |
| **HE** (High Efficiency)            | Wifi 6/6E  | 2.4/5 Ghz   |
| **EHT** (Extremely High Throughput) | Wifi 7     | 2.4/5/6 Ghz |

Now if we read the output carefully we can note that for Band 1  (which is the 2.4 Ghz band) the supported technologies are HT (wifi 4)  and HE (wifi 6). Whereas for band 2 (5 Ghz) the supported technologies  are VHT (wifi 5) and HE (wifi 6).

This tells us that this particular wifi chipset supports wifi 4 on 2.4ghz, wifi 5 on 5ghz and wifi 6 on 2.4/5 ghz bands.
# PCI 

*Peripheral Component Interconnect*

* Schnittstelle für Erweiterungskarten
* ein Bus-Standard zur Verbindung von Peripheriegeräten mit dem Chipsatz eines Prozessors.

* Konventioneller PCI: PCI 3.0, (2004)

# PCI-Extended

* *PCI-X*
* Version: PCI-X 2.0, (2002)

# PCI Express

* *PCIe* oder *PCI-E*
* Version: PCI Express 5.0, (2019)

* PCIe 3.0: 8 GT/s

* PCIe 4.0: 16 GT/s

* Die mechanische Länge des Slots: Entsprechend der Länge des Slots oder Steckplatzes spricht man von *PCIe ×1*, *PCIe ×4*, *PCIe ×8* 
  * *PCIe ×18*: Anschluss von Grafikkarten 
  * *PCIe ×16*: Anschluss von Grafikkarten

## compatibility

Oberflächlich gesehen sehen neuere PCIe-Steckplätze genauso aus wie  3.0. Darüber hinaus verfügen sie über eine Rückwärts- und  Vortwärtskompatibilität: 

- [x] PCIe 3.0 SSD + PCIe 4.0 Slot
- [x] PCIe 4.0 SSD + PCIe 3.0 Slot

## Lanes

Die Bezeichnung `x1`, `x4`, `x8` und `x16` sagt aus, wie viele PCIe-Lanes in dem Slot kaskadiert sind.

Auf dem Mainboard erscheinen PCIe-Lanes in x1, x2, x4, x8 und x16. Mehr Lanes bedeuten, mehr Bandbreite und ein längerer Steckplatz. 

* **GPUs** werden in der Regel im oberen x16-Steckplatz installiert, da er die größte Bandbreite und traditionell die direkteste Verbindung zur CPU hat.
* Moderne PCIe **m.2 *SSDs*** verwenden x4-Lanes.

Grundsätzlich funktionieren kurze Karten auch in langen Slots. Aber **Achtung**, die mechanische Länge sagt nichts darüber aus, wie viele Lanes  ein Slot hat. Ein x16-Slot kann auch nur 4 (x4) oder 8 (x8) Lanes haben. Bei manchen Boards teilen sich mehrere Slots die Lanes.

## Geschwindigkeit

| PCIe    | Symbolrate       pro Lane | PCIe x1      | PCIe x4      | PCIe x8      | PCIe x16     | Kodierung       (Balast in %) |
| ------- | ------------------------- | ------------ | ------------ | ------------ | ------------ | ----------------------------- |
| 1.0/1.1 | 2,5 GT/s                  | 0,25 GByte/s | 1,0 GByte/s  | 2,0 GByte/s  | 4,0 GByte/s  | 8b10b / 20%                   |
| 2.0/2.1 | 5 GT/s                    | 0,50 GByte/s | 2,0 GByte/s  | 4,0 GByte/s  | 8,0 GByte/s  | 8b10b / 20%                   |
| 3.0/3.1 | 8 GT/s                    | 0,97 GByte/s | 3,9 GByte/s  | 7,8 GByte/s  | 15,5 GByte/s | 128b/130b / <2%               |
| 4.0     | 16 GT/s                   | 1,90 GByte/s | 7,8 GByte/s  | 15,5 GByte/s | 31,5 GByte/s | 128b/130b / <2%               |
| 5.0     | 32 GT/s                   | 3,90 GByte/s | 15,5 GByte/s | 31,5 GByte/s | 63,0 GByte/s | 128b/130b / <2%               |
| 6.0     | 32 GT/s                   | 8 GByte/s    | 32 GByte/s   | 64 GByte/s   | 128 GByte/s  | PAM-4                         |
| 7.0     | 128 GT/s                  | 16 GByte/s   | 64 GByte/s   | 128 GByte/s  | 256 GByte/s  | PAM-4                         |
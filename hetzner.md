### **Betreff:** Probleme mit dem Routing und Erreichbarkeit der zusätzlichen IP-Adresse (Neukunde)  

---

**Sehr geehrtes Hetzner-Team,**

ich bin Neukunde bei Ihnen und habe momentan erhebliche Probleme, meine Netzwerkkonfiguration zum Laufen zu bringen. Diese Probleme hatte ich in der Form bei meinem vorherigen Hoster nicht. Daher benötige ich dringend Ihre Unterstützung, da ich meine Systeme ohne eine funktionierende Netzwerkkonfiguration nicht in Betrieb nehmen kann.

---

### **Serverdaten**
- **Haupt-IP (Master IP):** `144.76.87.250`
- **Gateway:** `144.76.87.225`
- **Zusätzliche IP:** `144.76.87.249`
- **Virtuelle MAC:** `00:50:56:00:76:3E`

---

### **Problem**
Die IP-Adresse `144.76.87.249` kann weder das Gateway `144.76.87.225` noch externe IP-Adressen (z. B. `8.8.8.8`) erreichen. Trotz korrekter Konfiguration ist die Kommunikation blockiert. Ein `ping` und `traceroute` zum Gateway bleiben erfolglos. Hier sind die detaillierten Schritte und Konfigurationen, die ich bereits durchgeführt habe:

---

### **Versuche und Tests**

1. **Virtuelle MAC-Adresse:**
   - Ich habe sichergestellt, dass die MAC-Adresse `00:50:56:00:76:3E` im Hetzner Robot für die zusätzliche IP `144.76.87.249` eingetragen ist.
   - Nach Eintrag und Wartezeit bleibt das Problem weiterhin bestehen.

2. **Routing auf dem Host geprüft:**
   - Die Netzwerkkonfiguration für den Host wurde korrekt gemäß Ihrer Dokumentation eingerichtet.
   - Die Ausgabe von `ip route` auf dem Host:
     ```
     default via 144.76.87.225 dev enp41s0 proto kernel onlink
     144.76.87.224/27 dev enp41s0 proto kernel scope link src 144.76.87.250
     144.76.87.249 dev vmbr0 scope link
     192.168.1.0/24 dev vmbr1 proto kernel scope link src 192.168.1.1
     192.168.2.0/24 dev vmbr2 proto kernel scope link src 192.168.2.1
     ```

3. **Aktivierung der IP-Weiterleitung:**
   - IPv4- und IPv6-Weiterleitung sind auf dem Host aktiviert:
     ```
     sysctl net.ipv4.ip_forward = 1
     sysctl net.ipv6.conf.all.forwarding = 1
     ```

4. **iptables NAT-Konfiguration:**
   - Die Konfiguration des NATs wurde gemäß Ihrer Anleitung eingerichtet:
     ```
     iptables -t nat -L -n -v
     Chain POSTROUTING (policy ACCEPT 1350 packets, 81120 bytes)
     pkts bytes target     prot opt in     out     source               destination
       0     0 MASQUERADE  all  --  *      enp41s0 192.168.1.0/24       0.0.0.0/0
       0     0 MASQUERADE  all  --  *      enp41s0 192.168.2.0/24       0.0.0.0/0
     ```

5. **Test mit Traceroute und Ping:**
   - Ein `traceroute` vom Host zur Gateway-IP zeigt ausschließlich `*`:
     ```
     traceroute to 144.76.87.225 (144.76.87.225), 30 hops max, 60 byte packets
     1  *  *  *
     2  *  *  *
     ...
     ```
   - Ping vom Host (z. B. über `ping -I 144.76.87.249 144.76.87.225`) bleibt erfolglos:
     ```
     PING 144.76.87.225 (144.76.87.225) from 144.76.87.249: 56(84) bytes of data.
     --- 144.76.87.225 ping statistics ---
     10 packets transmitted, 0 received, 100% packet loss
     ```

---

### **Aktuelle Konfiguration**
Hier ist meine aktuelle Netzwerkkonfiguration auf dem Host (gekürzt auf die relevanten Bereiche):

```
auto lo
iface lo inet loopback

auto enp41s0
iface enp41s0 inet static
    address 144.76.87.250/27
    gateway 144.76.87.225

auto vmbr0
iface vmbr0 inet static
    address 144.76.87.249/32
    bridge-ports none
    bridge-stp off
    bridge-fd 0
    hwaddress ether 00:50:56:00:76:3E
    post-up ip route add 144.76.87.249/32 dev vmbr0
```

---

### **Dringlichkeit**
Ich bin darauf angewiesen, dass die zusätzliche IP-Adresse `144.76.87.249` und das Netzwerk ordnungsgemäß funktionieren. Ohne funktionierendes Routing kann ich meine Systeme nicht starten. 

Bitte prüfen Sie die Routing-Konfiguration und stellen Sie sicher, dass das Gateway korrekt für mein Netzwerksegment konfiguriert ist. Falls eine zusätzliche Anpassung auf Ihrer Seite notwendig ist, lassen Sie mich dies wissen.

Vielen Dank für Ihre Unterstützung!

Mit freundlichen Grüßen,  
Dennis Floer
Kundennummer K1111919024

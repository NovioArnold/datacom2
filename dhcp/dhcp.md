# Stapsgewijze Handleiding: DHCPv4 Configureren

## Inleiding

Deze handleiding beschrijft de stapsgewijze configuratie van DHCPv4 op een Cisco-netwerk, gebaseerd op de 'Implement DHCPv4' labinstructies.

## Benodigdheden

- 2 Routers (Cisco 4221 met Cisco IOS XE Release 16.9.4 of vergelijkbaar)
- 2 Switches (Cisco Catalyst 2960 met Cisco IOS Release 15.2(2) lanbasek9 of vergelijkbaar)
- 2 PC's met terminalemulatieprogramma (bijv. Tera Term)
- Consolekabels
- Ethernetkabels

## Deel 1: Netwerk Bouwen en Basisinstellingen Configureren

### Stap 1: Adresseringsschema Opstellen

1. **Subnet A:** 58 hosts (Client VLAN op R1)

   - **Subnet:** 192.168.1.0/26
   - **Beschikbare IP-adressen:** 192.168.1.1 - 192.168.1.62
   - **Netwerkadres:** 192.168.1.0
   - **Broadcastadres:** 192.168.1.63

2. **Subnet B:** 28 hosts (Management VLAN op R1)

   - **Subnet:** 192.168.1.64/27
   - **Beschikbare IP-adressen:** 192.168.1.65 - 192.168.1.94
   - **Netwerkadres:** 192.168.1.64
   - **Broadcastadres:** 192.168.1.95

3. **Subnet C:** 12 hosts (Client netwerk op R2)
   - **Subnet:** 192.168.1.96/28
   - **Beschikbare IP-adressen:** 192.168.1.97 - 192.168.1.110
   - **Netwerkadres:** 192.168.1.96
   - **Broadcastadres:** 192.168.1.111

### Stap 2: Bekabel het Netwerk

Verbind de apparaten zoals aangegeven in de topologie.

### Stap 3: Basisinstellingen Routers Configureren

1. Geef elke router een naam.
2. Schakel DNS lookup uit: `no ip domain-lookup`
3. Stel de wachtwoorden in:
   - Privileged EXEC: `enable secret class`
   - Console: `line console 0`, `password cisco`, `login`
   - VTY: `line vty 0 4`, `password cisco`, `login`
4. Versleutel de wachtwoorden: `service password-encryption`
5. Maak een waarschuwingsbanner: `banner motd #Unauthorized access prohibited#`
6. Sla de configuratie op: `copy running-config startup-config`

### Stap 4: Inter-VLAN Routing Configureren op R1

1. Activeer interface G0/0/1: `no shutdown`
2. Configureer sub-interfaces met 802.1Q encapsulatie.
3. Controleer of de sub-interfaces operationeel zijn.

### Stap 5: Configureer G0/0/1 op R2 en statische routing

1. Configureer G0/0/1 met het eerste IP-adres van Subnet C (192.168.1.97).
2. Configureer interface G0/0/0 op beide routers.
3. Stel een standaardroute in: `ip route 0.0.0.0 0.0.0.0 [IP-adres G0/0/0 andere router]`
4. Test de statische routing met `ping`.
5. Sla de configuratie op.

### Stap 6: Basisinstellingen voor Switches Configureren

- Voer dezelfde basisinstellingen uit als bij de routers.

### Stap 7: VLAN's Aanmaken op S1

1. Maak en benoem de VLAN's volgens de VLAN-tabel.
2. Activeer de managementinterface op S1 (VLAN 200) en stel de default gateway in.
3. Configureer de Parking_Lot VLAN en deactiveer ongebruikte poorten.

### Stap 8: VLAN's Toewijzen aan Interfaces

1. Wijs de juiste VLAN's toe aan de juiste poorten.
2. Controleer of de toewijzing correct is.

### Stap 9: Configureer Interface F0/5 van S1 als 802.1Q Trunk

1. Forceer trunking modus: `switchport mode trunk`
2. Stel native VLAN in op 1000: `switchport trunk native vlan 1000`
3. Sta VLAN's 100, 200 en 1000 toe op de trunk.
4. Verifieer trunking status.

## Deel 2: Configureer en Verifieer DHCPv4 Servers op R1

### Stap 1: DHCPv4 Pools Configureren

1. **Adresuitsluiting:** `ip dhcp excluded-address [eerste vijf IP-adressen]`
2. **Maak DHCP-pool aan:**

   1. voor `Subnet A (Client VLAN op R1)`

   ```lua
   ip dhcp pool SubnetA
   dhcp excluded-address 192.168.1.1 192.168.1.8
   dhcp excluded-address 192.168.1.63
   network 192.168.1.0 255.255.255.192
   default-router 192.168.1.1
   domain-name ccna-lab.com
   lease 2 12 30
   ```

   2. Herhaal voor de tweede pool met naam `SubnetC (Client netwerk op R2)`:

   ```lua
   ip dhcp pool SubnetC
   dchp excluded-address 192.168.1.97 192.168.1.101
    dhcp excluded-address 192.168.1.110
   network 192.168.1.96 255.255.255.240
   default-router 192.168.1.97
   domain-name ccna-lab.com
   lease 2 12 30
   ```

### Stap 2: Configuratie Opslaan

- `copy running-config startup-config`

### Stap 3: DHCPv4 Server Configuratie Verifiëren

1. Bekijk pooldetails: `show ip dhcp pool`
2. Controleer DHCP bindingen: `show ip dhcp bindings`
3. Controleer DHCP-statistieken: `show ip dhcp server statistics`

### Stap 4: Verkrijg een IP-adres via DHCP op PC-A

1. `ipconfig /renew`
2. Test connectiviteit met `ping`.

## Deel 3: DHCP Relay Configureren op R2

### Stap 1: R2 Configureren als DHCP Relay Agent

1. `interface G0/0/1`
2. `ip helper-address 10.0.0.1`

### Stap 2: Verkrijg een IP-adres via DHCP op PC-B

1. `ipconfig /renew`
2. Controleer IP-informatie met `ipconfig`
3. Test connectiviteit met `ping`
4. Controleer DHCP bindingen op R1: `show ip dhcp binding`
5. Controleer DHCP-statistieken op R1 en R2.

## Conclusie

Na het volgen van deze stappen zou het netwerk correct geconfigureerd moeten zijn voor DHCPv4 met twee DHCP-servers en een DHCP-relay. Test alle verbindingen en controleer of alle apparaten dynamisch IP-adressen ontvangen.

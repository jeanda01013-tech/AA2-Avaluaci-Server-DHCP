# AA2-Avaluaci-Server-DHCP

![](/img/01_Instalacion_Zorin_OS_18.png)

Es l’instal·lador de Zorin. Aquesta serà la màquina virtual que actuarà com a client de la xarxa.mostra

![](/img/02_Configuracion_red_VirtualBox_SMx-Lab.png)

Es configura l’Adaptador 2 com a “Xarxa interna” amb el nom SMX-Lab i una targeta Intel PRO/1000 MT Desktop. Així es crea una xarxa aïllada entre servidor i client, amb el cable virtual connectat.

![](/img/03_Configuracion_IPv4_manual.png)

A la connexió cablejada se selecciona el mètode IPv4 “Manual” i s’assigna l’adreça 192.169.6.1 amb màscara 255.255.255.0. La porta d’enllaç queda buida perquè és una xarxa interna sense sortida a l’exterior.

![](/img/04_Actualizacion_repositorios_Ubuntu.png)

Al servidor (struchserv, Ubuntu Noble) s’executa sudo apt update && sudo apt install kea -y. Es veuen els repositoris que es descarreguen, i a dalt un avís de 3 intents de contrasenya incorrectes.

![](/img/05_Configuracion_Servidor_DHCP.png)

Es veuen els temporitzadors renew i rebind i la base de dades de concessions a /var/lib/kea/kea-leases4.csv. Es defineix la subxarxa 192.168.200.0/24 amb router 192.168.200.1, DNS 8.8.8.8, pool del .100 al .200 i una reserva per a la MAC 08:00:27:57:16:fc amb la IP 192.168.200.50.

![](/img/06_Configuracion_DHCP_isc-dhcp-server.png)

S’obre amb nano l’arxiu /etc/kea/kea-ctrl-agent.conf, que configura el Control Agent de Kea. Inclou el host 127.0.0.1, el port 8000 i l’autenticació bàsica amb l’usuari kea-api.

![](/img/07_Configuracion_red_DHCP.png)

Es mostra l’arxiu /etc/kea/kea-dhcp4.conf per defecte (468 línies), amb la llista d’interfícies buida ("interfaces": [ ]). Caldrà indicar-hi la interfície on el servidor ha d’escoltar.

![](/img/08_Netplan_configuracion_DHCP.png)

Es veu la configuració netplan amb les interfícies enp0s3 i enp0s9 en DHCP. El sagnat d’enp0s9 és incorrecte (està dins d’enp0s3) i donaria error en aplicar-la.

![](/img/09_Comprobacion_configuracion_DHCP.png)

Es fan un ping al client (4 paquets enviats i 4 rebuts), una comprovació del port 22 amb ss i un ip a. A la dreta apareix la informació del sistema, amb la IP 10.0.2.15 i l’avís que cal reiniciar.

![](/img/10_Reinicio_servicio_DHCP.png)

Dins de /etc/kea es reanomena kea-dhcp4.conf a old-kea-dhcp4.conf amb sudo mv. Després s’obre amb nano un kea-dhcp4.conf nou per escriure-hi la configuració pròpia.

![](/img/11_Instalacion_Wireshark.png)

A la VM Zorin (jean-VirtualBox) s’instal·la Wireshark amb les seves dependències: 41 paquets nous, 47,6 MB de descàrrega i 214 MB d’espai addicional. El sistema demana confirmació [S/n].

![](/img/12_Wireshark_captura_interfaces.png)

S’obre Wireshark 4.2.2 i es mostra la llista d’interfícies disponibles (enp0s3, enp0s8, any, etc.). Es prepara la captura sobre una de les interfícies.

![](/img/13_Wireshark_captura_trafico.png)

Wireshark comença a capturar des d’enp0s8, la interfície de la xarxa interna. De moment no hi ha paquets i la barra d’estat indica “live capture in progress”.

![](/img/14_Wireshark_paquetes_DHCP.png)

Es capturen tres paquets: un Router Solicitation (ICMPv6) i dues consultes MDNS des de 192.169.6.1. Es mostra el detall del paquet 1 (Ethernet, IPv6 i ICMPv6) i no hi ha trànsit DHCP, perquè el client té IP estàtica.

![](/img/15_Comando_ip_a_configuracion_red.png)

Al client, enp0s3 té 10.0.2.15/24 (xarxa NAT) i enp0s8 té 192.169.6.1/24 (xarxa interna). Confirma que la IP manual s’ha aplicat correctament.

![](/img/16_Informacion_interfaz_enp0s3.png)

La sortida mostra les dades d’enp0s3: IP 10.0.2.15/24, gateway 10.0.2.2, dos servidors DNS i el domini thematrix.local. També s’hi veuen les rutes i adreces IPv6.

![](/img/17_Comprobacion_fichero_dhcp_leases.png)

Al servidor, sudo cat /var/lib/dhcp4.leases dona l’error “No such file or directory”. Aquest arxiu no existeix perquè Kea desa les concessions en una altra ruta (/var/lib/kea/kea-leases4.csv)

![](/img/18_Comando_ip_a_estado_red.png)

Es mostren les interfícies del servidor: enp0s3 amb 10.0.2.15 (NAT), enp0s8 amb 192.168.50.10/24 i enp0s9 amb 192.168.56.103 assignada dinàmicament. Serveix per saber quina interfície pertany a cada xarxa abans de configurar Kea.

## Enllaç Github
https://github.com/jeanda01013-tech/AA2-Avaluaci-Server-DHCPs
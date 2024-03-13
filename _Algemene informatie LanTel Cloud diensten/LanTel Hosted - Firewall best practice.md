In dit document staan een aantal best practice voorbeelden voor het gebruik van LanTel Hosted oplossingen in combinatie met een firewall/router welke niet door LanTel zelf is geleverd en beheerd wordt.

<p class="callout info">Let erop dat termologie per merk kan verschillen. Dit document is niet bedoeld om aan te geven hoe een firewall exact moet worden ingericht, maar dient als richtlijn om te kijken naar instellingen welke mogelijk dienen te worden gewijzigd. Wat voor het ene merk firewall wel kan gelden is voor een ander merk weer niet aan de orde.   
Door de jaren heen hebben we van bepaalde merken routers kennis opgedaan, en specifieke commando’s om instellingen te wijzigen vermeld in dit document. Het kan zijn dat deze commando’s door firmware updates niet volledig accuraat zijn.  
</p>

LanTel heeft geen specifieke kennis van alle merken firewall, en lever daarom zelf bij eigen diensten de volgende routers:  
\- Cisco 800 series IOS Router  
\- Cisco 1100 series IOS Routers  
\- Cisco 1900 series IOS Routers  
\- Draytek Vigor 2800/2900 series routers

### NAT

NAT staat voor Network Address Translating en is een techniek die in alle routers terug te vinden is. NAT zorgt ervoor dat een router altijd een uniek IP-adres kan toekennen aan een apparaat dat verbinding wil maken met het internet. De werking van NAT en de configuratie hiervan heeft echter grote invloed op de werking van VoIP diensten.

Wat kan er voorkomen als er problemen met nat configuraties zijn:

- Een inkomend gesprek gaat niet rinkelen op toestel
- Timer loopt door na gesprek
- Geen spraak bij gesprek in/uit wacht
- Niet alle gesprekken komen door: Ondanks dat je toestellen geregistreerd zijn en normaal gesproken de gesprekken moeten worden aangeboden bij een toestel. Uitgaand bellen kan dan wel.
- Eenzijdig geluid: Je hoort je klant wel praten maar ze horen jou niet.
- Vertraagd geluid: Het geluid heeft een vertraging.
- Gesprekken vallen weg: Je merkt dat gesprekken spontaan worden verbroken. Vaak gebeurt dit elke keer na een vaste gesprekstijd van bijvoorbeeld 30 seconden.
- Toestellen registeren niet: Je telefoontoestellen willen maar niet registreren. Je ziet vaak de melding register failed.

#### Timeout

Als een sessie geïnitieerd wordt wordt er door de router/firewall een NAT entry aangemaakt voor het ip adres en de port mapping.  
De NAT session timeout bepaald de tijdsduur dat de nat entry actief is. Als deze timer verlopen is (zonder ge-update te worden) zal de sessie worden beëindigd en zal de firewall geen inkomend verkeer meer accepteren op deze NAT entry.

<p class="callout warning"><span style="text-decoration: underline;">**Aanbeveling**</span>  
Vaak is het voor voip toepassingen noodzakelijk dat deze NAT timeout instellingen niet te laat staan.   
Aanbevolen instelling voor NAT timeout zijn:  
  
TCP: 3600  
UDP: 300</p>

#### Consistent/Persistent NAT

Daarnaast kan het voorkomen dat firewalls dynamische nat entries maken, dat wil zeggen dat de entry gegevens gewijzigd worden in de firewall zonder de partijen welke het verbindt te informeren. Door de toepassing van consistent/persistent NAT voorkom je dat een NAT sessie tussentijds gewijzigd wordt.

<p class="callout warning"><span style="text-decoration: underline;">**Aanbeveling**</span>  
Zorg ervoor dat consistent/persistent NAT aanstaat op de firewall.  
</p>

#### Hardware NAT / Hardware Acceleration

Bij gebruik van SIP / VoIP dient er geen gebruik gemaakt worden van Hardware NAT of Hardware Acceleration. Bepaalde routers en firewall maken gebruik van deze optie om middels het manipuleren van pakketten de maximale doorvoersnelheid te verhogen.

<p class="callout warning"><span style="text-decoration: underline;">**Aanbeveling**</span>  
Wanneer er gebruik gemaakt wordt van een router/firewall welke beschikt over de functie hardware NAT of Hardware Acceleration, dan dient deze uitgeschakeld te worden.  
</p>

### SIP ALG

Hoewel SIP ALG bedoeld is om gebruikers te helpen die telefoons hebben achter NAT, wordt het in veel gevallen slecht geïmplementeerd en veroorzaakt het eigenlijk meer problemen dan het oplost. SIP ALG wijzigt SIP-pakketten op onverwachte manieren, waardoor ze beschadigd raken en onleesbaar worden voor een toestel of de voip dienst. Dit kan onverwacht gedrag veroorzaken, zoals telefoons die niet worden geregistreerd en inkomende oproepen die mislukken.

<p class="callout warning"><span style="text-decoration: underline;">**Aanbeveling**</span>  
Schakel SIP ALG UIT!  
</p>

<details id="bkmrk-hoe-schakel-je-sip-a"><summary>Hoe schakel je SIP ALG uit op je router?</summary>

Voor veel bekende router merken hebben we hieronder beschreven hoe je SIP ALG uitgschakelt.

**Asus Routers**  
Schakel de optie SIP Passthrough uit onder Advanced Settings / WAN -&gt; NAT Passthrough.  
Als uw router deze optie niet heeft, kan SIP ALG mogelijk worden uitgeschakeld via Telnet.

nvram get nf\_sip   
(It should return a "1")

nvram set nf\_sip=0   
nvram commit  
Reboot

**Barracuda Firewalls**   
Ga naar Firewall &gt; Firewall Rules &gt; Custom FirewallAccess Rules  
Klik op het selectievakje "Disabled" naast de regels met de naam LAN-2-INTERNET-SIP en INTERNET-2-LAN-SIP  
Dit schakelt SIP ALG uit.

**Billion**  
Navigeer naar de webinterface  
-&gt; Selecteer Configuration  
-&gt; Selecteer NAT  
-&gt; Selecteer ALG  
-&gt; Disable SIP ALG

**BT (Homehubs)**  
SIP ALG kan niet worden uitgeschakeld in de instellingen van BT HomeHubs.

**Cisco IOS Router**  
no ip nat service sip udp port 5060  
no ip nat service sip tcp port 5060

**D-Link**  
In 'Advanced' settings --&gt; 'Application Level Gateway (ALG) Configuration' schakel de 'SIP' optie uit.

**DrayTek**  
DrayTek Vigor apparaten, de optie is te vinden in de interface onder Netwerk -&gt; NAT -&gt; ALG.

**Fortinet**  
Voor Fortinet firewalls zie hoofdstuk 3.

**Huawei**  
Navigeer naar de webinterface  
Klik op Settings.  
Voer de vereiste gebruikersnaam en wachtwoord in en klik vervolgens op Log In.  
Klik op de Security dropdown.  
Klik op SIP ALG Settings. Schakel de checkbox Enable SIP ALG box uit.  
Klik op Apply.

**Juniper**  
Typ het volgende in de CLI  
Om te controleren SIP ALG is in of uitgeschakeld run show security alg status | match sip  
Om uit te schakelen:

configure  
set security alg sip disable  
commit

**Linksys**  
Controleer voor SIP ALG opties in de Administration tab onder 'Advanced'.  
Het is ook aan te raden om de SPI Firewall optie uit te schakelen.

**Mikrotik**  
Mikrotik SIP ALG wordt een SIP-helper genoemd en bevindt zich onder / IP&gt; Firewall&gt; Service Ports. De SIP Helper-functie moet worden uitgeschakeld.

**Netgear**  
Zoek naar een 'SIP ALG' checkbox in 'WAN' settings.

**SonicWALL Firewall**  
Onder de VoIP tab, kan je controleren of de optie 'Enable Consistent NAT' aan staat. ’SIP Transformations' moet niet aangevinkt staan.

**Speedtouch**  
Om SIP ALG uit te schakelen moet je via telnet je Speedtouch-router benaderen en het volgende typen:

-&gt; connection unbind application=SIP port=5060  
-&gt; saveall

**TP-Link**  
Navigeer naar de webinterface van uw router.  
De standaard gebruikersnaam is admin en het standaard wachtwoord is admin.  
Klik aan de linkerkant op Advanced Setup en vervolgens op NAT en daarna op ALG.  
Verwijder het vinkje uit de checkbox bij SIP Enabled. (Sommige TP-link routers geven dit weer als SIP Transformations).  
Klik op Save/Apply.

**UBEE**  
Ga naar Advanced &gt; Options.  
Schakel SIP uit.  
Schakel RTSP uit.  
druk op Apply.

**Ubiquiti**  
system -&gt; conntrack -&gt; modules -&gt; sip -&gt; disable

**ZyXEL**  
Schakel onder Network of Advanced -&gt; ALG de opties Enable SIP ALG en Enable SIP Transformations uit.   
In sommige gevallen moet SIP ALG worden uitgeschakeld door middel van telnet.  
Telnet in de router. Selecteer menu-items 24 en 8. Voer de volgende opdracht uit om de huidige SIP ALG-status weer te geven:

ip nat service sip active

Om SIP ALG uit te schakelen:

ip nat service sip active 0

**ZyXEL (ZyWALL USG Routers)**  
Ga naar Settings -&gt; Configuration &gt; Network&gt; ALG.  
Schakel SIP ALG uit.  
Schakel Enable SIP Transformations uit.  
Schakel Enable Configure SIP Inactivity Timeout uit.

</details>### Firewall specifieke informatie

Voor enkele bekende firewalls hebben we extra specifieke informatie over de configuratie.

<details id="bkmrk-fortigate-on-fortiga"><summary>Fortigate</summary>

<span lang="EN-US" style="font-family: 'Century Gothic',sans-serif; mso-ansi-language: EN-US;">On Fortigate firewalls<span style="mso-spacerun: yes;"> </span>SIP<span style="mso-spacerun: yes;"> </span>Application Layer Gateway (SIP ALG) is enabled by default. This will cause problems with SIP VoIP phones registration and call processing.</span>

<span lang="EN-US" style="font-family: 'Century Gothic',sans-serif; mso-ansi-language: EN-US;">How to Disable SIP ALG</span>

<u><span lang="EN-US" style="font-family: 'Century Gothic',sans-serif; mso-ansi-language: EN-US;">Backup configuration of your firewall before making any changes!</span></u>

<span lang="EN-US" style="font-family: 'Century Gothic',sans-serif; mso-ansi-language: EN-US;">FortiOS starting at 6.2.2 : Run following commands from Fortigate firewall CLI</span>

```
config system settings
set sip-expectation disable
set sip-nat-trace disable
set default-voip-alg-mode kernel-helper-based
end
```

<span lang="EN-US" style="font-family: 'Century Gothic',sans-serif; mso-ansi-language: EN-US;">FortiOS below 6.2.2 : Run following commands from Fortigate firewall CLI</span>

```
config system settings
set sip-helper disable
set sip-nat-trace disable
set default-voip-alg-mode kernel-helper-based
end
```

<span lang="EN-US" style="font-family: 'Century Gothic',sans-serif; mso-ansi-language: EN-US;">If you see an error while entering “set default-voip-alg-mode kernel-helper-based” , just ignore it.</span>

<span lang="EN-US" style="font-family: 'Century Gothic',sans-serif; mso-ansi-language: EN-US;">Next we need to locate SIP entry in session helper list and delete it</span>

```
config system session-helper
show
```

<span lang="EN-US" style="font-family: 'Century Gothic',sans-serif; mso-ansi-language: EN-US;">Scroll down until you see an entry for SIP, in our example it was number 13 but this may be different depending on model and software release. Now execute<span style="mso-spacerun: yes;"> </span>following commands:</span>

```
delete 13
end
```

<span lang="EN-US" style="font-family: 'Century Gothic',sans-serif; mso-ansi-language: EN-US;">The last set of commands disables processing of RTP protocol on the firewall</span>

```
config voip profile
edit default
config sip
set rtp disable
end
```

<span lang="EN-US" style="font-family: 'Century Gothic',sans-serif; mso-ansi-language: EN-US;">Normally Fortigate firewalls do not require a reboot when you change configuration, but , it seems, in this case we need reboot it to activate session helper changes.</span>

<p class="callout warning"><span lang="EN-US" style="font-family: 'Century Gothic',sans-serif; mso-ansi-language: EN-US;">Last step – restart or power cycle all your SIP phones and devices.</span></p>

<span lang="EN-US" style="font-family: 'Century Gothic',sans-serif; mso-ansi-language: EN-US;"> </span>

</details><details id="bkmrk-pfsense-%C2%A0bron%3A-https"><summary>pfSense</summary>

<p class="callout info"> Bron: [https://docs.netgate.com/pfsense/en/latest/recipes/nat-voip-phones.html](https://docs.netgate.com/pfsense/en/latest/recipes/nat-voip-phones.html) </p>

**Disable source port rewriting**  
By default pfSense® software rewrites the source port on all outbound traffic. This is necessary for proper NAT in some circumstances such as having multiple SIP phones behind a single public IP registering to a single external PBX. With a minority of providers, rewriting the source port of RTP can cause one way audio. In that case, setup manual outbound NAT and Static Port on all traffic.

**Set Conservative state table optimization**  
The default UDP timeouts in pf are too low for some VoIP services. If phones mostly work, but randomly disconnect, set Firewall Optimization Options to Conservative under System &gt; Advanced, Firewall/NAT tab.

**Disable scrub**  
In very rare circumstances, scrubbing needs to be disabled under System &gt; Advanced, Firewall/NAT tab. In most cases this should be left at the default setting (unchecked). Only change this setting if it has been determined it is necessary to do so. Some phones send malformed packets that will be silently dropped without scrub active (e.g. unfragmented packets that claim to be fragmented).

</details>

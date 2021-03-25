Betriebskonzept bwForCluster NEMO
=================================

eScience, Rechenzentrum, Albert-Ludwigs-Universität Freiburg

Michael Janczyk (MJ), Jan Leendertse (JL), Dirk von Suchodoletz (DvS), Bernd Wiebelt (BW)

============= =====
Vorlage an:
Vorgelegt am:
Gültig ab:
Version:      |release|
Datum:        25.03.2021
============= =====

.. warning::
  TLP:AMBER
    Limited disclosure, restricted to participants' organizations. Distribution outside this audience requires written permission from the originator.

========= ==========  =============== ==========================================
Version   Datum       Autor*innen     Änderungen
========= ==========  =============== ==========================================
|release| 25.03.2021  MJ              Korrekturen, Ergänzungen.
0.3.1     19.03.2021  DvS             Korrekturen, Ergänzungen.
0.3.0     13.03.2021  DvS             Umstrukturierung des Textes, stärkere Verknüpfung von Sicherheit und Betriebsmodell, Ergänzungen.
0.2.1     09.03.2021  BW, JL, DvS, MJ Anmerkungen und Korrekturen.
0.2.0     09.03.2021  DvS             Ergänzungen u.a. aus dem DFN-Paper zu Security-Domains und Absicherungskonzept.
0.1.0     06.03.2021  MJ              Erster Entwurf basierend auf Informationen des Betriebskonzepts de.NBI-FR und einer Vorlage von TOMs aus Tübingen.
========= ==========  =============== ==========================================


Einleitung
==========

Das Konzept, auf dessen Grundlage das bwForCluster NEMO betrieben wird,
geht auf Überlegungen in der Abteilung eScience des Rechenzentrums der
Universität Freiburg (RZ) zurück und wurde in verschiedenen Projekten
iterativ entwickelt. [1]_ Die für den bwForCluster NEMO beschaffte
Hardware – Rechenknoten, Speichersystem für wissenschaftliche Daten und
Infrastrukturserver – wird in Datenschränken untergebracht, die vom RZ
in hierfür dedizierten Räumlichkeiten betrieben werden. Die
Datenschränke werden vom RZ betreut und auf grundlegende
Betriebsbereitschaft (Strom, Kälte) überwacht. Es werden primär zwei
große Speichersysteme genutzt, einerseits eine professionelle
Storage-Appliance für administrative und Nutzer*innendaten, andererseits
ein schnelles paralleles Dateisystem für die Nutzung durch Compute-Jobs.
Auf den Infrastrukturservern laufen diverse Dienste, die skriptgesteuert
mit Rezepten installiert und konfiguriert werden. Die Rechenknoten
werden via netzwerkbasiertem Remote-Boot von dedizierten
Infrastrukturservern zustandslos betrieben. Auf diesen Knoten werden die
Jobs der Wissenschaftler*innen ausgeführt, teilweise auch in VFUs und
CFUs. In diesen VFUs und CFUs laufen die Anwendungen oder Umgebungen,
die von den Wissenschafter*innen kontrolliert werden.

Es wird auf allgemeine Prinzipien des High Performance Computings (HPC)
zurückgegriffen, die um weitere Elemente erweitert werden. Besonders der
Anspruch, Daten über längere Zeiträume hinweg in ihren Entstehungs- und
Verarbeitungskontexten reproduzierbar zu halten, führte zu einer
Komplementierung des traditionellen Betriebsmodells von HPC-Systemen um
Virtualisierung. Das bwForCluster NEMO offeriert über den traditionellen
Baremetal-Betrieb hinaus Dienste für "Virtualisierte
Forschungsumgebungen" (VFU), die auf HPC-Ressourcen lauffähig sind gemäß
einem Betriebsmodell, das von Mitarbeitern des RZ seit mehreren Jahren
in verschiedenen Projekten erprobt und in den produktiven Betrieb
übernommen wurde
dfn-forum-2017,bwhpc2018:vicevre,ViCE2019,ViCE2019a.
Innerhalb von VFUs haben Wissenschaftler*innen die Freiheit,
Forschungsdaten und deren Kontext so zu abstrahieren, dass sie auf
anderen Systemen weiter ausgeführt und reproduziert werden können.
Dieses Methodik wurde in den letzten Jahren zu den "Containerisierten
Forschungsumgebungen" (CFU) weiterentwickelt. Diese Umgebungen sind
weniger komplex und mit einfachen Rezepten – beispielsweise den
Singularity Definition Files, die eine Container-Umgebung beschreiben
und verwendet werden um diese zu generieren – herzustellen.

Mit den VFU und CFU werden Hardware, Software und Dienste so entkoppelt,
dass für den bwForCluster NEMO ein verbessertes Management von
Ressourcen erreicht wird. Ein Grundgedanke dieser konzeptionellen
Vorüberlegungen ist die Aufteilung in Schichten. Der hier vorgestellte
Dienst des bwForClusters NEMO deckt im Modell, das in
Meier:2017,konrad-werk vorgestellt wurde, die
Ebene der IT-Ressourcen ab. Für die Betriebsorganisation wird sie
innerhalb des bwForClusters NEMO weiter differenziert in Schrank,
Hardware und Dienste. Dieses Konzeptpapier beschreibt den Betrieb des
bwForClusters NEMO.

Dienstbeschreibung bwForCluster NEMO
====================================

Es liegt eine Dienstbeschreibung für das HPC-Computing-Angebot des
Rechenzentrums im Rahmen des allgemeinen Servicekatalogs vor. Diese kann
online von den Seiten des Rechenzentrums abgerufen werden. [2]_ Diese
Dienstbeschreibung wird einem regelmäßigen Review-Prozess in der Runde
der Abteilungsleiter*innen unterzogen.

Generelles Sicherheitskonzept
=============================

Das Sicherheitskonzept der Abteilung eScience strebt an, die Fähigkeiten
zur Business-Continuity von großen, verteilten IT-Infrastrukturen durch
technische Vorbereitungen schrittweise zu verbessern und damit das
Schutzziel der Verfügbarkeit durch einen geplanten Prozess zu verfolgen
dfn-sec-2021. Die Schutzziele Integrität und
Vertraulichkeit werden in die Überlegungen einbezogen. Das umfasst eine
Reihe von Grundgedanken und Konzepte, wie Angriffen vorgebeugt und eine
flächendeckende und skalierende Wiederherstellung arbeitsfähiger
Compute-Infrastrukturen vorbereitet werden kann. Die angestrebte Lösung
beruht im wesentlichen auf drei Säulen: einer klaren funktionalen und
Netzwerkseparierung, einem reproduzierbaren Maschinen-Deployment mit
abgesichertem Speichersystem und einer Sicherstellung der
Datenintegrität auf einem geschützten Persistenzlayer. Ein zentraler
Baustein des zuverlässigen und abgesicherten Deployments basiert auf
einem netzbasiertem Bootsystem mit "Stateless System Remote-Boot"
hpc-remote-boot,bwhpc2018:dnbd3.

Security-Domains
----------------

Als System, auf das aus dem Internet zugegriffen wird, ist das
HPC-Cluster laufenden Attacken ausgesetzt, die automatisiert nach
Schwachstellen scannen. Es kann nie mit Sicherheit ausgeschlossen
werden, dass einer der zahlreichen Angriffe erfolgreich ist, entweder da
er gezielt stattfindet, oder weil aus rein statistischen Gründen eine
noch nicht einschlägig bekannte Lücke automatisiert gefunden wird. In
einem solchen Fall ist es bedeutsam, dass der Angriff nicht sofort in
einem Domino-Effekt auf den gesamten Cluster durchschlägt.

Es existieren verschieden Methoden, das Risiko durch Angriffe auf
Systeme und Infrastrukturen zu verringern. Für den HPC-Cluster geschieht
die Reduktion des Risikos auf mehreren Ebenen: klare Rechte- und
Netzwerkseparierung, reproduzierbares Maschinen-Deployment mit
abgesicherten Speichersystem, Sicherstellung der Datenintegrität auf
einem geschützten Persistenzlayer.

In dem Augenblick, in dem die überwiegende Anzahl von Maschinen im
Cluster als Angriffsvektor aus dem Spiel genommen werden, lassen sich
kritische Systeme leichter identifizieren und schützen. Maßnahmen im
Arbeitsfeld der Business-Continuity fokussieren sich auf auf eine
überschaubare Anzahl von Systemvarianten und Ressourcen wie
beispielsweise die Boot-Kette aus TFTP, HTTP(S) und DNBD3-Services. [3]_

Es wird in Hinblick auf die Planungen für den Nachfolge-Cluster eine
gestufte, hierarchische Sicherheitsarchitektur angestrebt. Dabei werden
verschiedene Sicherheitsbereiche oder Sicherheitszonen definiert, wobei
die Übergänge zwischen den Zonen klar und scharf definiert sind. Die
Hindernisse, die ein Angreifer überwinden muss, um sich zwischen den
Zonen zu bewegen, steigen mit zunehmendem Schutzbedarf. Für das
HPC-System wurden die folgenden Sicherheitszonen identifiziert:

* Sicherheitszone 0: Physische Absicherung
* Sicherheitszone 1: Zugangsverwaltung für Adminstration
* Sicherheitszone 2: Persistenter und abgesicherter Speicher
* Sicherheitszone 3: Dienste
* Sicherheitszone 4: HPC-User

Ein Hauptaspekt bei der Ausgestaltung der Sicherheitszonen ist die
Separierung der Netzwerke, sodass Kommunikation zwischen den Netzwerken
auf das Notwendige beschränkt wird und es möglichst wenige
Kommunikationspfade gibt, die Zonen überspringen. Wo es solche Übergänge
gibt, müssen sie aus wohldefinierten mit klar vorgegebenen Daten- und
Informationsflussrichtungen bestehen. Technisch lässt sich das durch den
Einsatz von privaten Netzwerken mit eingeschränktem Routing,
VLAN-Definitionen für die Netzwerkports auf den Switchen und dem Einsatz
von Firewalls und Proxys erreichen.

Sicherheitszone 0: Physische Absicherung
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Die Rechenknoten, Server und die zugehörigen Speichersysteme sind in
wassergekühlten Schränken im Maschinensaal II (MSII) untergebracht. [4]_
Speicher und Service-Knoten sind zusätzlich redundant gegen
Stromausfälle gesichert und können bis zu einem sicheren Herunterfahren
betrieben werden. Die Bedingungen führt die Dienstbeschreibung zum
"Machine-Hosting" näher aus MachHost:2020.

Das Speichersystem für Home-Verzeichnisse und administrative Daten
Dell-EMC Isilon ist gegen Ausfälle neben dem Hauptstandort
MSII (Betriebsstandort A) noch in einem weiteren externen Serverraum
(Betriebsstandort B, KG II) redundant gesichert.

Sicherheitszone 1: Zugangsverwaltung für Adminstration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Letztlich wird eine Infrastruktur durch Menschen verwaltet, die sich in
irgendeiner Form während ihrer Tätigkeit authentifizieren und
autorisieren müssen. Bei großen Infrastrukturen wird zudem ein Rollen-
und Rechtemanagement notwendig sein, weil nicht jeder Administrator die
gleichen Rechte benötigt oder bekommen soll. Insofern ist diese
Sicherheitszone mit maximalen Einschränkungen zu versehen und
bestmöglich abzusichern.

Eine wesentliche Aufgabe dieser Zone liegt in der Verwaltung von
Passwörtern und SSH-Schlüsseln. Passwörter und Schlüssel können dann auf
untergeordnete Sicherheitszonen per Push-Verfahren verteilt werden.
Alternativ könnte hier eine Zertifizierungsstelle angesiedelt sein, die
Zugangs-Schlüssel zertifizieren kann oder beschränkt gültige
Zugangstoken (beispielsweise signierte SSH-Schlüssel) generiert und
ausgibt.

Die Server in dieser Zone sollten eine physische Präsenz besitzen oder
mit einfacher Virtualisierung oder Containerisierung (libvirt, docker)
realisiert werden. Der Einsatz einer komplexen
Virtualisierungsinfrastruktur (beispielsweise OpenStack) würde
weitergehende rekursive Maßnahmen erfordern.

Der Zugang zu dieser Sicherheitszone muss sowohl physisch als auch
logisch stark eingeschränkt sein. Das heißt, dass die Server mit einer
mechanischen Zugangssperre versehen werden müssen, sodass nur
berechtigte Personen physisch Zugriff erhalten können. Für den logischen
Zugriff via Netzwerk müssen die zulässigen Netzwerkbereiche und
Protokolle auf das notwendige Minimum reduziert werden. "Push-Requests",
von dieser Sicherheitszone initiierte Verbindungen an untergeordnerte
Zonen, sind unproblematischer als "Pull-Requests", also von
untergeordneten Zonen initiierte Verbindungen. Für letztere sollten nach
Möglichkeit Proxy-Lösungen zum Einsatz kommen. Idealerweise beschränkt
sich die Kommunikation auf jeweils benachbarte Zonen.

Sicherheitszone 2: Persistenter und abgesicherter Speicher
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Im persistenten und abgesicherten Speicher werden unter anderem
Konfigurationen, Build-Rezepte und Datenbanken abgelegt, die für das
Aufsetzen der Dienste in nachgeordneten Zonen benötigt werden.
"Abgesicherter Speicher" versteht sich in diesem Fall auf zweifache Art:
Zum einen als Speichersystem, das gegen unbeabsichtigten Verlust oder
Verfälschung der Daten Vorkehrungen trifft, zum anderen als Speicher,
der gegen absichtliche Angriffe von Dritten besonders geschützt ist.

Zum Einsatz kommen an dieser Stelle professionelle Storage-Systeme mit
einer hohen geo-redundanten Absicherung mit eventuellem Tape-Backup und
gestaffelter Versionierung von Daten, beispielsweise durch Snapshots.
Dabei werden die integralen Mechanismen des Speichersystems genutzt, um
Verfügbarkeit, Integrität und bei Bedarf Vertraulichkeit
sicherzustellen. Die Systeme laufen in abgetrennten, eigenen
Netzwerkbereichen mit wenigen wohldefinierten und kontrollierten
Übergängen zu den nachgeordneten Sicherheitszonen, wobei idealerweise
nur die direkt darunterliegende Zone direkt versorgt wird.

Prinzipiell sind mehrere Arten von Aufgaben und damit von Datenzugriff
und Datenflussrichtung zu unterscheiden:

* Lesender und schreibender Zugriff auf Datenbanken und
  Konfigurationen, die in der Sicherheitszone 3 (Dienste) benötigt
  werden. Diese Methode wird für Laufzeitdaten der Dienste genutzt.
* Ein Nur-Lese-Zugriff (Readonly) kann für System-Images und andere
  nicht durch nachgeordnete Sicherheitszonen zu verändernde Daten
  angeboten werden.
* Ein Nur-Hinzufügen-Zugriff (Append-Only) ist beispielsweise für Log-
  und Monitoringdaten relevant, um nachträgliche unerkennbare
  Modifikationen zu unterbinden.

Über die systemisch verfügbaren Sicherheitsmerkmale hinaus können zudem
zonenübergreifende Methoden eingesetzt werden. Im Falle von
System-Images können beispielsweise alle erzeugten System-Images sowie
relevanten Konfigurationen auf einem gesicherten Fileserver abgelegt
werden, der mit Versionierung beispielsweise durch Snapshots arbeitet.
Die Unveränderlichkeit der System-Images könnte in Zukunft zusätzlich
durch kryptografische Prüfsummen abgesichert werden. Die Server wäre für
nachgeordnete Zonen ausschließlich für die den Client-Betrieb
notwendigen Ports freigegeben. Sie können nur aus speziellen Netzen für
die Konfiguration erreicht werden.

Sicherheitszone 3: Dienste
~~~~~~~~~~~~~~~~~~~~~~~~~~

Die Sicherheitszone Dienste definiert die Erreichbarkeit der
Infrastruktur und die Art, wie Dienste ausgerollt und betrieben werden.
Bei der Compute-Infrastruktur des HPC-Clusters wird zwischen Servern,
die öffentliche Dienste anbieten, und internen Servern unterschieden.
Die internen Server bestehen aus Infrastruktur-relevanten Komponenten,
wie Storage-, Boot- und Build-Servern, sowie Servern, die API-Dienste,
die keine öffentliche Erreichbarkeit benötigen, vorhalten. Sie befinden
sich in nach außen hin abgekapselten privaten Netzen mit nur wenigen
manuell freigeschalteten Zutrittspunkten.

Auf allen Servern sind stark restriktive Firewall-Regeln aktiv, die
zusätzlich zur Separierung auf Netzwerkebene die Zugriffsmöglichkeiten
(und damit auch die potentiellen Angriffsvektoren) reduzieren.
Prinzipiell sind lediglich die notwendigen Ports der erforderlichen
Protokolle in den notwendigen Netzen freigeschaltet. SSH nimmt als
erster Angriffspunkt eine Sonderrolle ein. Allen Servern ist gemein,
dass sie lediglich SSH-Verbindungen von innerhalb des Projektes (und
damit aus klar definierten Netzen) kommend annehmen. Lediglich die
Einstiegspunkte für die Administration, beispielsweise über
Proxy-Jump-Knoten, in die Compute-Infrastrukturen stellen eine Ausnahme
dar.

Diese Netzwerkseparierung und die restriktiven Firewall-Regeln
gewährleisten, dass die Anzahl der Angriffsvektoren so gering wie
möglich ist und Übergriffe von anderen möglicherweise befallenen
Compute-Infrastrukturen unterbindet. Um die Auswirkungen von Angriffen
auch bei einer großen Serveranzahl gering zu halten, wird viel Wert auf
eine schnelle Wiederherstellbarkeit der Dienste gelegt.

Sicherheitszone 4: HPC-User
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Die äußerste Zone zeichnet sich dadurch aus, dass Nutzer*innen aus den
Gruppen Forschende oder Mitarbeitende direkt oder indirekt Zugriff
haben. Sie nutzen primär SSH, um mit dem HPC-Cluster zu interagieren.
Der Zugriff beschränkt sich auf die zwei im öffentlichen Netz
exponierten Login-Knoten zum Abschicken von Jobs und zwei
Visualisierungs-Knoten. Zur Absicherung des Zugangs durch HPC-Nutzer
wird auf eine Zwei-Faktor-Authentifizierung gesetzt.

Betriebsmodell
==============

Das Betriebsmodell beschreibt konkrete Schritte des Deployments und der
täglichen Produktion des HPC-Clusters. Hierzu wird eine Kombination aus
administrativen und für Compute genutzte Server eingesetzt.

Aussagen zu Installation und Softwareupdates

Administrative Infrastruktur
----------------------------

Dem Security-Konzept folgend was zur Persistenzschicht sagen.

Eventuell Server und Dienste vom nächsten Abschnitt hier reinziehen!?

Knoten, Server und Dienste
--------------------------

Die installierte Hardware des bwForCluster NEMO besteht aus über 900
Rechenknoten und einigen dedizierten Servern für NEMO-Dienste.
Zusätzlich können virtuelle Maschinen als "Virtualisierte
Forschungsumgebungen" (VFU) in einer Openstack-Cloud-Umgebung gestartet
werden. Diese werden auf den gleichen über 900 Rechenknoten ausgeführt,
wie reguläre Cluster-Jobs. Zugang zum Cluster erfolgt über sogenannte
Login-Knoten,

.. code-block:: bash

  login1.nemo.uni-freiburg.de (alias login.nemo.uni-freiburg.de)
  login2.nemo.uni-freiburg.de

den Visualisierungsknoten (Vis),

.. code-block:: bash

  vis1.nemo.uni-freiburg.de
  vis2.nemo.uni-freiburg.de

und über das Openstack-Dashboard. Die Zugangsknoten sind im öffentlichen
Internet exponiert, welches jedoch auf das Belwü-Netz eingeschränkt
wurde. [5]_ Der Zugriff erfolgt primär mittels SSH-Dienst, das
Openstack-Dashboard über verschlüsseltes HTTPS.

Dienste
~~~~~~~

SSH
^^^

Dieser Dienst läuft auf allen Knoten und Servern und erlaubt den Login
von Wisschenschaftler*innen und Administrator*innen auf diesen.

Scheduler
^^^^^^^^^

Dieser Dienst ist auf dem Management-Server von NEMO aktiv und dient zum
"Scheduling" (Verteilen nach vorgegebenem Algorithmus) von Jobs auf dem
Cluster. Dazu sind auf den Rechenknoten Clients installiert, die Jobs
und Ressourcenverbrauch protokollieren und diese Information an den
Scheduler zurückmelden.

HTTP(S)
^^^^^^^

Das OpenStack-Dashboard ist als Webschnittstelle umgesetzt und setzt für
den Zugriff auf HTTPS, um eine Absicherung bei der Nutzung über das
öffentliche Belwü-Netz zu erreichen. Auf dem Deployment-Server wird HTTP
verwendet um Konfigurationen zu den Rechenknoten zu verteilen (Teil des
iPXE-basierten Boot-Ablaufs und der individuellen Knotenkonfiguration).
Die Deployment-Server sind nur im internen NEMO-Netz erreichbar.

DNBD3
^^^^^

Auf den Deployment-Servern laufen zwei DNBD3-Instanzen. Dies stellt
sicher, dass wenn einer dieser Server ausfällt, das Cluster weiterhin
mit dem Betriebssystem-Image versorgt wird.

Ansible
^^^^^^^

Auf dem Management-Server übernimmt Ansible das Ausrollen der Dienste
und deren Konfiguration.

OpenStack
^^^^^^^^^

Mehrere Openstack-Server und -Dienste sind Cluster-intern für die
Nutzung von VFUs zuständig.

DHCP
^^^^

Die IP-Adressen werden bei Rechen-, Login, sowie Visualisierungs-Knoten
über DHCP verteilt. Dieser Dienst wird von der Netzwerkabteilung
mithilfe der Appliance Infoblox betrieben.

Monitoring
^^^^^^^^^^

Der Monitoring-Server empfängt und speichert alle Log- und
Protokoll-Dateien. Hierbei werden Login-Versuche, kritische Fehler und
Hardware-Parameter protokolliert und teilweise visualisiert. Für
einfache Parameter wie Temperatur eines Knotens sind Grenzwerte
definiert. Bei Überschreitung dieser werden per Mail die
Administrator*innen des Clusters verständigt.

Deployment
----------

Die Erzeugung und das Deployment der Server geschieht in einem
mehrstufigen Prozess, der konzeptionell in den Sicherheitszonen 3 und 4
ähnlich abläuft. Das Gerüst für die Prozesse in den Zonen 3 und 4 bilden
vorbereitete Skripte und Konfigurationen aus der Zone 2. Sie ermöglichen
eine rasche Wiederherstellung von Servern und Diensten in den Zonen 3
und 4. Von den Maschinen in Zone 3 (Dienste) gibt es wenige Ausformungen
von Instanzen mit individuellen Konfigurationen. In der
Sicherheitszone 4 (HPC-User) liegen deutlich mehr Instanzen, die sich
jedoch auf wenige Vorlagen für HPC-Knoten beziehen. Es existieren
weniger individuelle Konfigurationen, und für den Neustart von Instanzen
ist ein Rückgriff auf gespeicherte Zustände nicht notwendig.

Die Dienste beim bwForCluster NEMO werden über Ansible-Rollen auf den
Serverknoten aufgesetzt. Das ermöglicht ein schnelles und einfaches
ausrollen auf neuen Servern. Es müssen nur wenige Anpassungen
durchgeführt werden.

Die Rechenknoten werden ebenfalls mittels Ansible erzeugt. Hierzu wird
das CentOS7-Vorlagen-Image mit Ansible konfiguriert und in in ein
lesbares QCOW2-Image konvertiert. Mit dem in der Abteilung "eScience"
entwickelten Boot-Framework wird dann das Image über das Netzwerk
gestartet. Das Image wird dabei über das nur lesbare Blockdevice DNBD3
eingebunden. Für Schreiboperationen wird eine copy-on-write Schicht
darüber gelegt, die bei jedem Boot eines Knotens frisch initialisiert
wird. Alle neu generierten Images bekommen eine inkrementierte
Revisionsnummer, so dass die Umgebung zum einen reproduzierbar ist, zum
anderen bei Problemen mit einer Revision einfach auf eine ältere zurück
gegriffen werden kann.

Die Entscheidung, welche Systemversion, Revision und Konfiguation
geladen wird, trifft der sogenannte Bootauswahlserver anhand der
Zugehörigkeit der MAC-Adresse der Netzwerkkarte, über die der initiale
Start lief, zu einer Boot-Gruppe
bwhpc2018:sortinghat. Diese Information wird
jedesmal beim Boot ausgewertet. Die Boot-Gruppe entscheidet über die
Konfiguration des Knotens. Sie wird verwendet, um spezielle Knoten zu
konfigurieren, beispielsweise bei GPU-Knoten. Bei neuer Hardware durch
Neubeschaffungen oder Ersatz bei Reparaturen muss lediglich die
MAC-Adresse einer Gruppe zugeordnet werden. Neue Konfigurationen können
ebenfalls schnell eingerichtet werden, da nur die zur Basisgruppe
unterschiedliche Konfiguration vorgenommen werden muss.

Changemanagement
----------------

Der Deploymentprozess erleichtert das Changemanagement. Die
Bereitstellung des Basissystems erlaubt schnelle Funktionstests, da beim
Netzwerk-Boot lediglich die neuere Version angefahren werden muss. Die
Hardwaregrundlage der Rechenknoten verändert sich im Laufe der
Beschaffungszyklen, jedoch wird im Beschaffungsprozess und beim Design
des Basissystems darauf geachtet, dass neue Knoten ohne Brüche in das
Grundsystem übernommen werden können. Die Heterogenität wird durch den
kontinuierlichen Austausch von Hardware verursacht, für die jeweils die
zum Moment der Beschaffung günstigsten oder passendsten Komponenten
verwendet werden.

Für jede Geräteklasse wird ein Knoten reserviert, mit dem ausschließlich
Tests durchgeführt werden. Erst wenn bei Änderungen am Grundsystem oder
Patches auf den reservierten Knoten durchgetestet wurden, werden diese
Änderungen auf den produktiven Knoten ausgerollt.

Wissenschafts- und HOME-Speicher
--------------------------------

Die HOME-Verzeichnisse der Nutzer*innen liegen auf dem Isilon-Speicher
der Universität. [6]_ Für die aktuell verarbeiteten wissenschaftlichen
Daten dient ein zentraler Parallelspeicher, der auf BeeGFS
aufsetzt. [7]_ Dieser Speicher ist neben dem bwForCluster NEMO ebenfalls
in der ATLAS-Umgebung eingebunden. Diese beinhaltet das
ATLAS-Cluster [8]_ und die ATLAS-VFU. [9]_ Dadurch können zusätzlich
Nutzer*innen und Administratoren der Freiburger ATLAS-Gruppen auf diesen
Speicher zugreifen.

Nutzer*innen können in der Standardeinstellung nur ihre Daten einsehen
und bearbeiten. Administratoren können alle Daten einsehen, sofern sie
nicht Nutzer- oder Client-seitig verschlüsselt wurden, und bearbeiten.
Beide Speicher werden nicht standardmäßig verschlüsselt.

Netze
-----

Die Netzwerkanbindung der Serverchränke im Maschinensaal und der
zentralen Switche wird von der Abteilung "eScience" in Zusammenarbeit
mit der Abteilung "Netze und Kommunikationsdienste" (Netzabteilung) im
RZ durchgeführt. Diese Anbindung erlaubt eine Administration der Knoten
in den Schränken von festgelegten IP-Adressen aus, die nur in Räumen der
Universität Freiburg sowie über VPN-Verbindungen zugewiesen werden.

Die internen Uni-Netzwerke für das bwForCluster NEMO, die VFUs, das
ATLAS-Cluster und die Isilon sind voneinander getrennt und lassen nur
Zugriff von zum Betrieb notwendigen Netzen zu. Welche dies im einzelnen
sind, müssen vom jeweiligen Dienst erfragt werden.

Das bwForCluster NEMO verwendet folgende Netze:

.. code-block:: bash

     10.16.0.0/16          NEMO: Rechenknoten, Server und Parallelspeicher
                                 Login- und Vis-Knoten über interne Netzwerkschnittstelle
     132.230.222.0/24      NEMO: Login- und Visualisierungsknoten
     10.17.0.0/16          NEMO: CMS-VFU
     10.18.0.0/16          NEMO: ATLAS-VFU
     10.20.0.0/21          NEMO: NEMO-VFU (unused)
     10.20.8.0/21          NEMO: NEMO-VFU (unused)
     10.20.16.0/21         NEMO: NEMO-VFU (unused)
     10.20.24.0/21         NEMO: NEMO-VFU (unused)
     10.20.32.0/21         NEMO: NEMO-VFU (unused)
     10.20.40.0/21         NEMO: ATLAS-TEST-VFU

Obige Netze sind jeweils voneinander getrennt. Lediglich die ATLAS-VFU
und ATLAS-TEST-VFU können zusätzlich auf das NEMO-Netz ``10.16.0.0/16``
zugreifen. Das Cluster kann ansonsten nur über die öffentliche
IP-Adressen der Login- und Vis-Knoten erreicht werden. Die Rechenknoten
sind mit mindestens versorgt, Server, die Dienste anbieten, sind mit
mindestens zwei Anschlüssen mit über das Link Aggregation Control
Protocol (LACP) an zwei Top-Level-Switche angebunden. [10]_ Zusätzlich
sind alle Rechenknoten mit dem Hochgeschwindigkeitsnetzwerk "Omni-Path"
mit miteinander und dem wissenschaftlichen Parallelspeicher
verbunden. [11]_

Zugang zur Ressource
--------------------

Zugang zum bwForCluster NEMO haben lediglich registrierte Nutzer*innen
des bwForClusters NEMO. Antragsberechtigt sind nur Wissenschaftler*innen
aus Baden-Württemberg. Die genauen Zugangskriterien und die einzelnen
Schritte der Registrierungsprozedur sind im bwHPC-Wiki
beschreiben. [12]_

Das Auslaufen und die Invalidierung von Accounts regelt jede Universität
selbst. [13]_ Der Nutzer hat danach keinen Zugriff mehr auf die
Ressourcen. Die Daten der Nutzer*innen verbleiben jedoch so lange auf
dem Cluster, bis die Ressource abgeschaltet wird oder die eine Anfrage
einer berechtigten Person erfolgt. Es gibt derzeit keine festen Regeln
diesbezüglich, so dass diese Frage einer genaueren Ausarbeitung Bedarf.

Kontingentierung
----------------

Die Wissenschaftler*innen sind im Sinne der gemeinschaftlichen
DFG-Beantragung Stakeholder des bwForClusters NEMO. Zusätzlich gibt es
Shareholder die mit eigenen Mitteln Teile des Clusters mitfininaziert
haben ZKI-Aufwuchs. Diesen stehen
zusätzliche Anteile am Cluster zur Verfügung. Die Regelung, wer wie
viele Ressourcen des Clusters nutzen kann, wird über einen
"Fairshare-Mechanismus" geregelt. [14]_ Dieser bestimmt wann ein Job
eines/r Wissenschaftler*in starten kann. Hierzu wird von einer Gruppe
jeweils der Verbrauch der letzten drei Monate mit ihrem "Share"
verglichen. Ist der Verbrauch höher als der Share, der der Arbeitsgruppe
zur Verfügung steht, werden die Jobs niedriger priorisiert, ist er
niedriger als der verfügbare Share, werden die Jobs höher priorisiert.
Wissenschaftler*innen können aber mehr Ressourcen verwenden, als ihnen
aufgrund ihres Shares zustehen würden. Sie werden dadurch in Zukunft nur
schlechter in der Warteschlange priorisiert. Es gibt lediglich eine
maximale Anzahl an Ressourcen, die ein/e Wissenschaftler*in in die
Warteschlange stellen können.

Administration
--------------

Administrator*innen verfügen über erweiterte Rechte. Sie haben Zugriff
auf alle Daten der Nutzer*innen, sofern diese nicht zusätzlich
verschlüsselt werden. Der Zugang wird bei Bedarf manuell gewährt und
wird bei Ausscheiden beziehungsweise wenn die Rechte nicht mehr benötigt
werden manuell entzogen. Derzeit wird ein Protokoll für die
Administration entwickelt, das diesen Aspekt regelt.

.. _monitoring-1:

Monitoring
----------

Monitoring überwacht den dauerhaften Betrieb mit Verfolgung der Ziele
Verfügbarkeit, Vertraulichkeit und Integrität der Daten. Beim Monitoring
werden Schränke, Infrastrukturkomponenten wie Netzwerk, Speichersysteme,
Server und Rechenknoten überwacht. Neben der Überwachung der Hardware
wird die Temperatur, Stromaufnahme und zusätzlich bei Schränken die
Luftfeuchtigkeit kontrolliert. Die Nachverfolgung des Netzwerks findet
in der Netzwerkabteilung und bei Schränken in der Abteilung Allgemeiner
Betrieb statt. Strom und Kühlung werden zudem vom "Technischen
Gebäugemanagement" (TGM) überwacht. Zusätzlich misst der
Monitoring-Server des Clusters mit Hilfe von Zabbix Hardwaredaten wie
Temperatur und Defekte auf Knotenebene und schlägt per Mail Alarm beim
Überschreiben von Grenzwerten. Zabbix überprüft zudem, ob die Dienste
noch auf den Servern noch zur Verfügung stehen. Hier wird nur geprüft,
ob der Dienst noch existiert.

Zusätzlich werden Hardware- sowie Softwareprobleme, Login- und
Zugriffsversuche lokal auf den Knoten und für die von den
Wissenschaftler*innen erreichbaren Knoten wie Login-, Vis- und
Rechenknoten zusätzlich auf dem Monitoringserver in Dateien gespeichert
und in konsolidierter Form auf einen gesicherten Bereich der
Storage-Appliance geschrieben.

Der Speicherverbrauch im parallelen Dateisystem und den
Home-Verzeichnissen wird mittels Quotas durchgesetzt. Die Auslastung
wird jeweils von den zuständigen Betreibern ermittelt. Bei Isilon ist
das die Abteilung "Virtualisierung und Speichersysteme", beim BeeGFS
machen das die Administrator*innen des bwForClusters NEMO. "Workspaces"
auf dem parallelen Wissenschaftsspeicher BeeGFS haben einen Laufzeit von
und müssen von den Wissenschaftler*innen mit einem Kommando manuell
verlängert werden. Erfolgt das nicht, werden die Daten endgültig nach
einer Wartezeit von sieben Tagen gelöscht.

Verantwortlichkeiten
====================

Die Verantwortung für den Betrieb des bwForClusters NEMO liegt bei
dem/der Leiter*in der Abteilung eScience. Diese/r berichtet der/dem
Leiter*in des Rechenzentrums der Universität Freiburg.

Maschinensaal II (MSII)
--------------------

Der MSII sowie die darüber bereitgestellten Schränke werden innerhalb von
der Abteilung "Allgemeiner Betrieb" verantwortet. Das operative Geschäft
sowie die organisatorischen Schnittstellen innerhalb des RZ sowie zu
Nutzer*innen, die Ressourcen im Maschinensaal betreiben, werden in der
"Maschinensaalbenutzungsordnung" MSBO:2020 für
den Maschinensaal beschrieben. Die Nutzung der Server-Schränke wird im
Dienstkatalog "Machine-Hosting" MachHost:2020
spezifiziert. Die Maschinensaalbenutzungsordnung bestimmt ebenfalls den
physikalischen Zugriff der Administrator*innen des Clusters auf die
Schränke und die darin eingebauten Maschinen.

Erwartete Ergebnisse
====================

Mit dem Aufbau der Teildienste in technischen Schichten und der
flexiblen Boot-Prozedur können die strategischen Ziele des bwForClusters
NEMO mit den gegebenen Ressourcen erreicht und die
Informationssicherheit gewahrt werden. Die Gliederung der Schichten
erlaubt es, die Arbeitsbereiche zu trennen und die Risiken im Betrieb
einzelner Schichten besser zu isolieren. Besonders die im
Wissenschaftsbereich hohe Erwartung an Verfügbarkeit lässt sich besser
erreichen. Die Zahl der "Single Points of Failures" ist besser
kontrollierbar. Die Standardisierung in der Steuerung der Hardware
reduziert die Komplexität im Betrieb, die den Wissenschaftler*innen
gebotene Freiheit ist praktisch vollständig von der Betriebsschicht
getrennt. Die Abstraktion reduziert Angriffsvektoren auf die
Betriebsschicht, die durch Ereignisse auf der Ebene der
Wissenschaftler*innen eröffnet werden.

Ziele im Sinne der Informationssicherheit
=========================================

Kernanliegen von bwForcluster NEMO ist, Forscher*innen den Zugang zu
Rechen- und Speicherkapazitäten zu geben. Als Schutzziel betrachtet ist
dies die Sicherstellung der *Verfügbarkeit* von Rechenressourcen, auf
denen Forscher*innen ihre Rechenjobs ausführen. Als Dienstanbieter muss
das bwForCluster NEMO eine Verfügbarkeit bieten, die Forscher*innen
Big-Data-Analysen über das Maß selbst gemanagter oder dezentral in
Forschungseinrichtung administrierten IT-Cluster hinaus ermöglicht.

Für die Forscher*innen ist, um ihre Datenverarbeitung und Ergebnisse
reproduzierbar zu halten, eine *Integrität* von Daten auf Speicherebene
notwendig. Für Integrität von Daten auf der von ihnen betriebenen
Verarbeitungsschicht sind sie selbst verantwortlich.

Das Schutzziel *Vertraulichkeit* bezieht sich auf die Kontrolle des
Zutritts, Zugangs und Zugriffs und die Trennung von Umgebungen. Mit der
Abstraktion der Dienstschichten – Konzentration auf der Ebene der
physischen und netztechnischen Infrastrukturen, der
Clusteradministration in HPC, der Entkoppelung von logischen
Nutzer-bezogenen Einheiten – sind Arbeitsteilungen möglich, die ein
größeres Spektrum an Diensten für die Forschung in den Bereich des
wirtschaftlich möglichen schieben. Sicherheitsrelevante Arbeitsbereiche
können zentral von qualifiziertem Personal abgedeckt werden.

Regulatorische Anforderungen an Forschung im Bereich der
Neurowissenschaft, die in einigen Bereichen personenbezogene
medizinische Daten mit hohem Schutzbedarf erzeugen, erfordern die
Sicherstellung, diese Daten innerhalb eines kontrollierbaren Rechtsraums
zu speichern. Die Souveränität wird auf die Kontrolle der physischen,
organisatorischen und operativen Aspekte bezogen.

Das bwForCluster NEMO ist mit diesen Überlegungen in der Lage, die aus
den strategischen Zielen abgeleiteten Schutzziele mit eigenen Maßnahmen
wirtschaftlich tragen und erreichen zu können.

Vertraulichkeit
---------------

Die Zutrittskontrolle
^^^^^^^^^^^^^^^^^^^^^

ist über die Maschinensaalbenutzungsordnung (MsBO) festgelegt. [15]_
Diese regelt folgende Aspekte:

-  Zugang ist nur für berechtigte Personen mittels Transponder möglich.

-  Die Vergabe der Schlüssel und Transponder ist dokumentiert.

-  MSII ist Alarmgesichert gegen Zutritt unbefugter Personen.

-  MSII verfügt über Rauchmelder.

-  Während der Öffnungszeiten ist Zutritt in MSII nur über eine
   Magnetkarte oder einen Schlüssel möglich.

-  Außerhalb der Öffnungszeiten wird das Rechenzentrum regelmäßig von
   einem Sicherheitsdienst kontrolliert.

-  Zutritt für Wartungen durch externe Parteien wird dokumentiert.

Die Zugangskontrolle
^^^^^^^^^^^^^^^^^^^^

zum bwForCluster NEMO erfolgt über mehrere Stufen. Das genauen Verfahren
ist im bwHPC-Wiki beschrieben. [16]_ Die genauen Maßnahmen beinhalten:

-  Wissenschaftler*innen müssen von der Universität. berechtigt sein die
   Ressource zu nutzen. Hierzu wird von den Universitäten das
   Entitlement "bwForCluster" vergeben. [17]_

-  Wissenschaftler*innen müssen einen Projektantrag stellen
   (Rechenvorhaben).

-  Wissenschaftler*innen müssen sich beim Cluster registrieren, dazu
   werden Daten beim jeweiligen Identity-Provider (IdP) abgefragt. [18]_
   Diese Daten können auch jederzeit von den Nutzer*innen abgefragt
   werden. [19]_ Hierzu gehören:

   -  Name

   -  E-Mail-Adresse

   -  EPPN [20]_

   -  Berechtigungen über "Entitlements".

   -  Eine Unix-Gruppe, falls diese vom IdP ausgeliefert wird.

-  Der Zugang ist über eine Firewall auf Belwü-Netze beschränkt. [21]_

   -  Außerhalb dieser Netze ist der Zugriff nur über Jump-Hosts
      (beispielsweise Proxy-Jump) oder VPN möglich.

-  Wissenschaftler*innen haben Zugriff von außen über SSH mit Nutzername
   und Passwort beziehungsweise SSH-Schlüssel auf Login- und
   Visualisierungsknoten.

-  Zugang über das Openstack-Dashboard über HTTPS mit Nutzername und
   Passwort.

-  Administrator*innen haben nur mit Nutzername und SSH-Schlüssel
   Zugriff.

-  Eine Zwei-Faktor-Authentifizierung wird derzeit ausgerollt.

-  Der Zugang wird über Log-Dateien lokal und im Monitoring-Server
   protokolliert.

Die Zugriffskontrolle
^^^^^^^^^^^^^^^^^^^^^

erfolgt auf Ebene von Unix-Rechten und "Access Control Lists" (ACL).
Folgende Regeln gelten:

-  Wissenschaftler*innen haben nur auf ihre eigenen Daten Zugriff. Diese
   Berechtigungen können selbst für weitere Nutzer*innen, beispielsweise
   über ACLs, erweitert werden.

-  Administratoren haben auf alle Daten und Bereiche Zugriff, wenn sie
   nicht von Client- oder Nutzerseite verschlüsselt werden.

-  Externe Supportmitarbeiter haben nur Zugriff auf die von ihnen zu
   wartenden Komponenten.

-  Testsysteme und weitere Untersuchungen werden nur ausgewählten
   Personengruppen zur Verfügung gestellt und beeinträchtigen nicht den
   Produktivbetrieb.

-  Zentrales Benutzer-/Berechtigungs-Management folgt dem
   Need-to-have-Prinzip für Wissenschaftler*innen, externen Support,
   Projektmitarbeiter*innen im bwHPC, Testnutzer*innen und
   Administrator*innen.

-  Die Wissenschafler*innen verlieren ihren Zugriff, sobald dieser von
   der Universität entzogen wird beziehungsweise der Account nicht mehr
   gültig ist. Die Regeln, wann dies erfolgt, werden von den
   Universitäten festgelegt.

-  Weitere zusätzliche Rechte beispielsweise Administratorrechte werden
   manuell entzogen. Ein Protokoll hierzu wird derzeit ausarbeitet.

Die Trennungskontrolle
^^^^^^^^^^^^^^^^^^^^^^

gewährleistet, dass zu unterschiedlichen Zwecken erhobene Daten getrennt
verarbeitet werden können. Hierzu zählen folgende Maßnahmen:

-  Physikalische und logische Trennung von Diensten, die nicht
   unmittelbar miteinander in Bezug stehen.

-  Physikalische und logische Trennung von Diensten und Netzen, die
   nicht aufeinander zugreifen müssen.

Integrität
----------

Als Weitergabekontrolle
^^^^^^^^^^^^^^^^^^^^^^^

werden Maßnahmen bezeichnet, die ein unbefugtes Lesen, Kopieren,
Verändern oder Entfernen bei elektronischer Übertragung oder Transport
verhindern. Das bwForCluster NEMO ist nur über folgende Protokolle und
Wege erreichbar:

-  Zugriff auf das Cluster ist auf das Belwü-Netz beschränkt.

-  Außerhalb des Belwü-Netzes muss VPN oder ein Jump-Host im Belwü
   verwendet werden.

-  Zugriffe werden auf Serverseite protokolliert.

-  Zugriff kann nur über verschlüsselte Dienste wie SSH und HTTPS
   erfolgen.

-  Die lokale Festplatte der Rechenknoten wird beim Booten verschlüsselt
   und kann nach Entfernen nicht ausgelesen werden.

-  Die Platten im Parallelspeicher enthalten nur teilweise Blöcke und
   eine Rekonstruktion ist nur möglich, wenn größere Teile entwendet
   werden. Jedoch ist für das Nachfolgesystem evtl. eine Verschlüsselung
   geplant.

-  Alle lokalen Datenträger auf per Remote-Boote gesteuerten Maschinen
   werden mit einem zufällig erzeugten individuellen Schlüssel lokal
   verschlüsselt aufgesetzt. Damit liegen keine auslesbaren
   Informationen auf einer ausgeschalteten Maschine vor.

-  Updates des Basissystems und Softwarekomponenten

-  Die sichere Entsorgung von Datenträgern ist in der MsBO
   geregelt. [22]_

Verfügbarkeit, Belastbarkeit
----------------------------

Zur Verfügbarkeitskontrolle
^^^^^^^^^^^^^^^^^^^^^^^^^^^

zählen Maßnahmen, die eine zufällige Zerstörung oder Verlust von Daten
und die Nutzbarkeit der Rechenressourcen beschreiben. Das bwForCluster
NEMO implementiert folgende Schutzmaßnahmen:

-  Feuer- und Rauchmeldeanlagen in MSII und Infrastrukturräumen wie
   Kühlung und Strom.

-  Redundante Kühlung bis zur Abschaltung für Cluster-kritische Dienste
   wie Parallelspeicher, HOME-Speicher und Server für Dienste.
   Rechenknoten sind nicht geschützt, können aber nach Behebung der
   Störung sofort wieder hochgefahren werden. Da im Desasterfall der
   Parallelspeicher und die Dienste vermutlich nicht benötigt werden,
   können diese unter Umständen vorsichtshalber sicher herunter gefahren
   werden.

-  Für Server und Speicher des bwForClusters NEMO besteht
   unterbrechungsfreie Stromversorgung sowie Notstromversorgung.

-  Die Temperatur, Feuchtigkeit und Stromverbrauch der Maschinen und der
   Datenschränke werden überwacht.

-  Das HOME-Verzeichnis der Wissenschaftler*innen ist georedundant
   gespeichert und bietet automatische Snapshots. [23]_

-  Der Parallelspeicher ist mit RAID-6 abgesichert.

-  Der Zugriff auf die Login- und Vis-Knoten ist durch eine Firewall und
   Fail2ban gesichert. Der Zugriff auf das Cloud-Dashboard über eine
   Firewall abgesichert. Die Restlichen Komponenten sind nur
   Cluster-intern erreichbar. Ausfälle durch Angriffe externer Parteien
   können so minimiert werden.

-  Der Zugriff auf das bwForCluster NEMO ist nur Wissenschaftler*innen
   aus baden-württembergischen Universitäten, wenigen
   Administrator*innen und Support-Mitarbeitern erlaubt, was den
   Angriffsvektor zusätzlich verkleinert.

-  Die Verfügbarkeit des bwForClusters NEMO wird überwacht.

-  Das Wiederanfahren des Systems kann nach "Ausfällen ohne
   Datenverlust" innerhalb weniger Stunden erfolgen.

Regelmäßige Überprüfung, Bewertung, Evaluation
----------------------------------------------

Datenschutz- und Informationssicherheits-Management
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Die Universität Freiburg nimmt den Schutz der ihr anvertrauten
personenbezogenen Daten sehr ernst und behandelt diese vertraulich und
entsprechend der gesetzlichen Vorschriften. Neben den Regelungen der
Europäischen Datenschutz-Grundverordnung (DSGVO) richtet sich die
Verarbeitung personenbezogener Daten an der Universität nach dem
Landesdatenschutzgesetz (LDSG) sowie den einschlägigen Regelungen des
Landeshochschulgesetzes (LHG).

Die Datenschutzbeauftragte Person der Universität Freiburg kann unter
der E-Mail-Adresse

.. code-block:: bash

   datenschutzbeauftragter@uni-freiburg.de

sowie unter der Postadresse der Universität mit dem Zusatz "Der
Datenschutzbeauftragte" erreicht werden. Allgemeinen Fragen zum Thema
Datenschutz können an die E-Mail-Adresse

.. code-block:: bash

   datenschutz@uni-freiburg.de

gerichtet werden.

Dazu arbeitet die Universität weiterhin mit der Zentralen
Datenschutzstelle der baden-württembergischen Universitäten (ZENDAS)
zusammen.

Das Incident-Response-Management
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

unterstützt bei der Reaktion auf Sicherheitsverletzungen. Hierzu zählen
beim bwForCluster NEMO:

-  Meldung von Sicherheitsvorfällen beim Sicherheitsbeauftragten und
   Datenschutzbeauftragten der Universität, bei den Projektpatrnern im
   bwHPC und dem DFNCert.

-  Das DFNCert untersucht Angriffe durch externe Parteien.

Datenschutzfreundliche Voreinstellungen
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Für die Registrierung beim bwForCluster NEMO werden nur so viele
personenbezogene Daten erhoben, wie für den Dienst notwendig sind, siehe
Abschnitt Zugangskontrolle. [24]_

Bezüge zu Konzepten, Richtlinien und Verordnungen
=================================================

.. [1]
   Vgl. "Compute-Forschungsinfrastrukturen: HPC",
   https://bwsyncandshare.kit.edu/s/t3gWxTnnjYKD9fS

.. [2]
   Siehe HPC-Dienstbeschreibung, Version vom 07.07.2016,
   https://www.rz.uni-freiburg.de/inhalt/dokumente/pdfs/dienstbeschreibung-hpc
   (Überarbeitungsversion:
   https://bwsyncandshare.kit.edu/s/RMnZSCbJaJXgQGw)

.. [3]
   Beim Distributed Network Block Device 3 (DNBD3) handelt es sich um
   ein spezielles, auf das Lesen optimiertes, verteiltes, blockbasiertes
   Speichersystem bwhpc2018:dnbd3.

.. [4]
   Rechenzentrum Universität Freiburg, Hermann-Herder-Str. 10,
   79104 Freiburg

.. [5]
   TODO

.. [6]
   Vgl. Dienstbeschreibung vom 28.01.2019,
   https://www.rz.uni-freiburg.de/inhalt/dokumente/pdfs/speichersysteme

.. [7]
   `beegfs.io <beegfs.io>`__

.. [8]

.. [9]

.. [10]
   Wiki-Eintrag zu LACP: https://de.wikipedia.org/wiki/Link_Aggregation,
   besucht am 19.02.2021.

.. [11]
   Eintrag zu Omni-Path: https://de.wikipedia.org/wiki/Intel_Omni-Path,
   besucht am 19.02.2021.

.. [12]
   WIKI

.. [13]
   TODO: Links zu NO

.. [14]
   FAIRSHARE

.. [15]
   Vgl. https://www.rz.uni-freiburg.de/inhalt/dokumente/pdfs/msbo
   Version vom 24.09.2020

.. [16]
   TODO Wiki

.. [17]
   bwidm seite ent

.. [18]
   link zu bwservices bwz bwidm

.. [19]
   https://bwservices.uni-freiburg.de/user/index.xhtml

.. [20]
   bwidm

.. [21]
   die genaue nummer und www verlinken

.. [22]
   TODO Nicht in der MsBO, wo?

.. [23]
   INFO zu SNAPSHOTS

.. [24]
   ZAS, bwIDM

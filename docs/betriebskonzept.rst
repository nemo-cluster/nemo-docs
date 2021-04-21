Betriebskonzept bwForCluster NEMO
=================================

eScience, Rechenzentrum, Albert-Ludwigs-Universität Freiburg

Michael Janczyk (MJ), Jan Leendertse (JL), Dirk von Suchodoletz (DvS), Bernd Wiebelt (BW)

============= =====
Vorlage an:
Vorgelegt am:
Gültig ab:
Version:      0.4.0
Datum:        20.04.2021
============= =====

.. warning::
  TLP:AMBER
    Limited disclosure, restricted to participants' organizations. Distribution outside this audience requires written permission from the originator.

========= ==========  =============== ==========================================
Version   Datum       Autor*innen     Änderungen
========= ==========  =============== ==========================================
0.4.0     20.04.2021  MJ              Einleitung gestrafft, theoretische Sicherheitszonen entfernt, zusätzliche Dokumente und Fußnoten, TOMs ausgelagert, Ziele und Ergebnisse nach vorne gezogen, neue Abschnitte zu Updates und Workspaces.
0.3.2     25.03.2021  MJ              Korrekturen, Ergänzungen.
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
Betriebsbereitschaft (Strom, Kühlung) überwacht. Es werden primär zwei
große Speichersysteme genutzt, einerseits eine professionelle
Storage-Appliance für administrative und Nutzer*innendaten, andererseits
ein schnelles paralleles Dateisystem für die Nutzung durch Compute-Jobs.
Auf den Infrastrukturservern laufen diverse Dienste, die skriptgesteuert
mit Ansible-Rezepten installiert und konfiguriert werden. Die
Rechenknoten werden via netzwerkbasiertem Remote-Boot von dedizierten
Infrastrukturservern zustandslos betrieben. Auf diesen Knoten werden die
Jobs der Wissenschaftler*innen ausgeführt. Besonders der Anspruch, Daten
über längere Zeiträume hinweg in ihren Entstehungs- und
Verarbeitungskontexten reproduzierbar zu halten, führte zu einer
Komplementierung des traditionellen Betriebsmodells von HPC-Systemen um
virtuelle Maschinen und Container. In diesen Virtualisierten (VFU) und
Containerisieten (CFU) Forschungsumgebungen laufen die Anwendungen oder
Umgebungen, die von den Wissenschafter*innen kontrolliert werden. [2]_

Innerhalb dieser Forschungsumgebungen haben Wissenschaftler*innen die
Freiheit, Forschungsdaten und deren Kontext so zu abstrahieren, dass sie
auf anderen Systemen weiter ausgeführt und reproduziert werden können.
Mit VFUs und CFUs werden Hardware, Software und Dienste so entkoppelt,
dass für den bwForCluster NEMO ein verbessertes Management von
Ressourcen erreicht wird. Ein Grundgedanke dieser konzeptionellen
Vorüberlegungen ist die Aufteilung in Schichten. Der hier vorgestellte
Dienst des bwForClusters NEMO deckt im Modell, das in [3]_ vorgestellt
wurde, die Ebene der IT-Ressourcen ab. Für die Betriebsorganisation wird
sie innerhalb des bwForClusters NEMO weiter differenziert in Schrank,
Hardware und Dienste. Dieses Konzeptpapier beschreibt den Betrieb des
bwForClusters NEMO.

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
wirtschaftlich tragen und erreichen zu können. Das Dokument “Technisch
Organisatorische Maßnahmen – bwForCluster NEMO” geht auf die einzelnen
Schutzziele detaillierter ein.

Erwartete Ergebnisse
====================

Mit dem Aufbau der Teildienste in technischen Schichten und der
flexiblen Boot-Prozedur können die strategischen Ziele des bwForClusters
NEMO mit den gegebenen Ressourcen erreicht und die
Informationssicherheit gewahrt werden. Die Gliederung der Schichten
erlaubt es, die Arbeitsbereiche zu trennen und die Risiken im Betrieb
einzelner Schichten besser zu isolieren. Besonders die im
Wissenschaftsbereich hohe Erwartung an Verfügbarkeit lässt sich besser
erreichen. Die Zahl der “Single Points of Failures” ist besser
kontrollierbar. Die Standardisierung in der Steuerung der Hardware
reduziert die Komplexität im Betrieb, die den Wissenschaftler*innen
gebotene Freiheit ist praktisch vollständig von der Betriebsschicht
getrennt. Die Abstraktion reduziert Angriffsvektoren auf die
Betriebsschicht, die durch Ereignisse auf der Ebene der
Wissenschaftler*innen eröffnet werden.

.. _sec:hpc:

Dienstbeschreibung HPC
======================

Es liegt eine Dienstbeschreibung für das HPC-Computing-Angebot des
Rechenzentrums im Rahmen des allgemeinen Servicekatalogs vor. Diese kann
online von den Seiten des Rechenzentrums abgerufen werden. [4]_ Diese
Dienstbeschreibung wird einem regelmäßigen Review-Prozess in der Runde
der Abteilungsleiter*innen unterzogen.

Betriebsmodell bwForCluster NEMO
================================

Das Betriebsmodell beschreibt konkrete Schritte des Deployments und der
täglichen Produktion des HPC-Clusters. Hierzu wird eine Kombination aus
administrativen Infrastruktur (Server) und von den
Wisschenschaftler*inenn zu Berechnungen verwendeten Rechenknoten
eingesetzt.

Hardware und Dienste
--------------------

Die installierte Hardware des bwForClusters NEMO besteht aus über 900
Rechenknoten und einigen dedizierten Servern für NEMO-Dienste. [5]_
Virtuelle Maschinen als VFUs und Container (CFUs) werden ebenfalls auf
diesen Rechenknoten ausgeführt, wie reguläre Cluster-Jobs. Auf den
regulären Rechenknoten (ausgenommen Knoten für interaktive Nutzung)
werden immer nur Jobs eines/einer Nutzers/Nutzerin ausgeführt. Zugang
zum Cluster erfolgt über sogenannte Login-Knoten,

::

   [
     language=bash,
     ]
   login1.nemo.uni-freiburg.de (alias login.nemo.uni-freiburg.de)
   login2.nemo.uni-freiburg.de

den Visualisierungsknoten (Vis),

::

   [
     language=bash,
     ]
   vis1.nemo.uni-freiburg.de
   vis2.nemo.uni-freiburg.de

und über das Openstack-Dashboard. Die Zugangsknoten sind im öffentlichen
Internet exponiert, welches jedoch auf das Belwü-Netz eingeschränkt
wurde. [6]_ Der Zugriff erfolgt primär über den SSH-Dienst. Beim
Openstack-Dashboard wird der Transport mit HTTPS abgesichert.

Ausgewählte Dienste
~~~~~~~~~~~~~~~~~~~

SSH
^^^

Dieser Dienst läuft auf allen Knoten und Servern und erlaubt den Login
von Wisschenschaftler*innen und Administrator*innen auf diesen über
Nutzername und Dienst-Passwort oder SSH-Key.

Scheduler
^^^^^^^^^

Dieser Dienst ist auf dem Management-Server von NEMO aktiv und dient zum
“Scheduling” (Verteilen nach vorgegebenem Algorithmus) von Jobs auf dem
Cluster. Dazu sind auf den Rechenknoten Clients installiert, die Jobs
und Ressourcenverbrauch protokollieren und diese Information an den
Scheduler zurückmelden.

HTTP(S)
^^^^^^^

Das OpenStack-Dashboard ist als Webschnittstelle umgesetzt und setzt für
den Zugriff auf HTTPS, um eine Absicherung bei der Nutzung über das
öffentliche Belwü-Netz zu erreichen. Der Zugang erfolgt über Nutzername
und Dienst-Passwort. Auf dem Deployment-Server wird HTTP verwendet um
Konfigurationen zu den Rechenknoten zu verteilen (Teil des
iPXE-basierten Boot-Ablaufs und der individuellen Knotenkonfiguration).
Die Deployment-Server sind nur im internen NEMO-Netz erreichbar.

DNBD3
^^^^^

Auf den Deployment-Servern laufen zwei
Distributed-Network-Block-Device-3-Instanzen. Dieser Dienst stellt das
Betriebssystem für Login-, Vis- und Rechenknoten zur Verfügung. Eine
redundante Auslegung stellt sicher, dass wenn einer dieser Server
ausfällt, das Cluster weiterhin mit dem Betriebssystem-Image versorgt
wird.

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

Die IP-Adressen werden bei Rechen-, Login-, sowie Visualisierungsknoten
über DHCP verteilt. Dieser Dienst wird von der Abteilung “Netze und
Kommunikationsdienste” mithilfe der Appliance Infoblox betrieben. [7]_

Monitoring
^^^^^^^^^^

Der Monitoring-Server empfängt und speichert alle Log- und
Protokoll-Dateien. Hierbei werden Login-Versuche, kritische Fehler und
Hardware-Parameter protokolliert und teilweise visualisiert. Für
einfache Parameter wie die Temperatur eines Knotens sind Grenzwerte
definiert. Bei Überschreitung dieser werden die Administrator*innen des
Clusters per Mail verständigt.

.. _sub:deploy:

Deployment
----------

Die Dienste beim bwForCluster NEMO werden über Ansible-Rollen auf den
Serverknoten aufgesetzt. Das ermöglicht ein schnelles und einfaches
ausrollen auf neuen Servern. Es müssen nur wenige Anpassungen
durchgeführt werden.

Die Rechenknoten werden ebenfalls mittels Ansible erzeugt. Hierzu wird
das CentOS-Vorlagen-Image mit Ansible konfiguriert und in in ein
lesbares QCOW2-Image konvertiert. [8]_ Mit dem in der Abteilung
“eScience” entwickelten Boot-Framework wird dann das Image über das
Netzwerk gestartet. Das Image wird dabei über das nur lesbare
Blockdevice DNBD3 eingebunden. Für Schreiboperationen wird eine
copy-on-write Schicht darüber gelegt, die bei jedem Boot eines Knotens
frisch initialisiert wird. Alle neu generierten Images bekommen eine
inkrementierte Revisionsnummer, so dass die Umgebung zum einen
reproduzierbar ist, zum anderen bei Problemen mit einer Revision einfach
auf eine ältere zurück gegriffen werden kann.

Die Entscheidung, welche Systemversion, Revision und Konfiguation
geladen wird, trifft der sogenannte Bootauswahlserver anhand der
Zugehörigkeit der MAC-Adresse der Netzwerkkarte, über die der initiale
Start lief, zu einer Boot-Gruppe. [9]_ Diese Information wird jedesmal
beim Boot ausgewertet. Die Boot-Gruppe entscheidet über die
Konfiguration des Knotens. Sie wird verwendet, um spezielle Knoten zu
konfigurieren, beispielsweise bei GPU-Knoten. Bei neuer Hardware durch
Neubeschaffungen oder Ersatz bei Reparaturen muss lediglich die
MAC-Adresse einer Gruppe zugeordnet werden. Neue Konfigurationen können
ebenfalls schnell eingerichtet werden, da nur die zur Basisgruppe
unterschiedliche Konfiguration vorgenommen werden muss.

.. _sub:change:

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

Updates und Sicherheit
----------------------

Bei allen Servern, die keinen direkten Zugriff durch die Nutzer*innen
erlauben, werden Updates bei den größeren Wartungen eingespielt, die
üblicherweise ein bis zwei Mal im Jahr statt finden. Sollte eine
außerordentliche Sicherheitslücke bestimmte Dienste betreffen, wird das
Update sobald es verfügbar ist, eingespielt. Sollte hierzu ein
Herunterfahren des Clusters notwendig werden, kann sich das Update um
bis zu vier Tage verzögern. Das Vorgehen wird dann im eScience-Team
unter Zuhilfenahme zusätzlicher IT-Experten diskutiert. Diese Wartungen
werden an die Wissenschaftler*innen vorab kommuniziert.

Bei den Login-, Vis- und Rechenknoten werden monatliche Updates
eingespielt. Dabei findet ein Rolling-Update statt. Das Cluster wird
offline genommen und neue Jobs können erst wieder starten, wenn die
Rechenknoten mit der neuen Systemversion gebootet sind. Damit können
alte Jobs noch zu Ende laufen, neue Jobs jedoch nur noch in der neuen
Umgebung starten. Durch das Deployment () und Changemanagement () kann
bei Problemen auf eine ältere Version gewechselt werden. Bei
außerordentlichen Sicherheitslücken wird das Update, sobald es verfügbar
ist, eingespielt und ausgerollt. Durch dieses Rolling-Update sind die
Updates bei allen Knoten eingespielt, wenn der Job, der zum Zeitpunkt
des Ausrollens noch die längste Restlaufzeit besitzt, endet und die vom
Job verwendeten Knoten neu booten können. Da die derzeitige maximale
Laufzeit der Jobs vier Tage beträgt, ist ein reguläres Update spätestens
nach vier Tagen beendet.

Parallel- und HOME-Speicher
---------------------------

Die HOME-Verzeichnisse der Nutzer*innen liegen auf dem Isilon-Speicher
der Universität. [10]_ Für die aktuell verarbeiteten wissenschaftlichen
Daten dient ein zentraler Parallelspeicher, der auf BeeGFS
aufsetzt. [11]_ Anders als der Isilon-Speicher ist der parallele
Speicher nur durch ein RAID6 abgesichert und bietet keine weiteren
Backups. Auf diesem Speicher sollten nur Daten liegen, die unmittelbar
für Berechnungen benötigt werden. Für eine anschließende Speicherung der
auf dem Cluster nicht mehr benötigten Daten wird bis Ende 2021 eine
Lösung auf dem bwSFS angeboten. [12]_

Der Parallelspeicher ist neben dem bwForCluster NEMO ebenfalls in der
ATLAS-Umgebung eingebunden. Diese beinhaltet das ATLAS-Cluster und die
ATLAS-VFU. [13]_ Dadurch können zusätzlich Nutzer*innen und
Administrator*innen der Freiburger ATLAS-Gruppen auf diesen Speicher
zugreifen.

Nutzer*innen können in der Standardeinstellung nur ihre eigenen Daten
einsehen und bearbeiten. Administrator*innen können alle Daten, sofern
sie nicht Nutzer- oder Client-seitig verschlüsselt wurden, einsehen und
bearbeiten. Beide Speicher werden nicht standardmäßig verschlüsselt.

Workspaces
~~~~~~~~~~

Die Daten auf dem parallelen Speicher werden für die Berechnungen der
Wissenschaftler*innen benötigt. Das Management der Daten wird durch
Wissenschaftler*innen in sogenannten “Workspaces” durchgeführt. [14]_
Die Nutzer*innen müssen Workspaces anlegen, um den parallelen Speicher
nutzen zu können. Dabei kann ein Workspace maximal gültig sein. Es
besteht jedoch die Möglichkeit jeden Workspace 99 Mal zu verlängern. Die
Wissenschaftler*innen werden vor Ablauf eines Workspaces per Mail
informiert.

Es wird empfohlen für unterschiedliche Unterprojekte und separate
Berechnungen eigene Workspaces anzulegen. Jeder Workspace kann damit in
einem späteren Schritt als separate Einheit oder Objekt mit Metadaten
versehen in einem Wissenschaftsspeicher wie bwSFS gesichert werden.
Sinnvolle Einheiten/Workspaces müssen durch die Wissenschaftler*innen
selbst definiert werden.

Netze
-----

Die Netzwerkanbindung der Serverschränke im Maschinensaal und der
zentralen Switche wird von der Abteilung “eScience” in Zusammenarbeit
mit der Abteilung “Netze und Kommunikationsdienste” (Netzwerkabteilung)
im RZ durchgeführt. Diese Anbindung erlaubt eine Administration der
Knoten in den Schränken von festgelegten IP-Adressen aus, die nur in
Räumen der Universität Freiburg sowie über VPN-Verbindungen zugewiesen
werden.

Die internen Uni-Netzwerke für das bwForCluster NEMO, die VFUs, das
ATLAS-Cluster und die Isilon sind voneinander getrennt und lassen nur
Zugriff von zum Betrieb notwendigen Netzen zu. Welche dies im einzelnen
sind, müssen vom jeweiligen Dienst erfragt werden.

Das bwForCluster NEMO verwendet folgende Netze:

::

   [
     language=bash,
     ]
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
Protocol (LACP) an zwei Top-Level-Switche angebunden. [15]_ Zusätzlich
sind alle Rechenknoten mit dem Hochgeschwindigkeitsnetzwerk “Omni-Path”
mit miteinander und dem wissenschaftlichen Parallelspeicher
verbunden. [16]_

Zugang zur Ressource
--------------------

Zugang zum bwForCluster NEMO haben lediglich registrierte Nutzer*innen
des bwForClusters NEMO. Antragsberechtigt sind nur Wissenschaftler*innen
aus Baden-Württemberg. Die genauen Zugangskriterien und die einzelnen
Schritte der Registrierungsprozedur sind im bwHPC-Wiki
beschreiben. [17]_ Für das bwForCluster NEMO muss von dem/der
Wissenschaftler/in ein separates Dienst-Passwort angelegt werden.

Das Auslaufen und die Invalidierung von Accounts regelt jede Universität
selbst. Der Nutzer hat danach keinen Zugriff mehr auf die Ressourcen.
Die Daten der Nutzer*innen verbleiben jedoch so lange auf dem Cluster,
bis die Ressource abgeschaltet wird oder die eine Anfrage einer
berechtigten Person erfolgt. Es gibt derzeit keine festen Regeln
diesbezüglich, so dass diese Frage einer genaueren Ausarbeitung Bedarf.
Für das Nachfolgecluster, das voraussichtlich im Jahr 2022 in Betrieb
gehen wird, wird eine Lösung erarbeitet. Die Universität stellt hierzu
die folgenden Ordnungen zur Verfügung:. [18]_

Kontingentierung
----------------

Die Wissenschaftler*innen sind im Sinne der gemeinschaftlichen
DFG-Beantragung Stakeholder des bwForClusters NEMO. Zusätzlich gibt es
Shareholder die mit eigenen Mitteln Teile des Clusters mitfininaziert
haben. [19]_ Diesen stehen zusätzliche Anteile am Cluster zur Verfügung.
Die Regelung, wer wie viele Ressourcen des Clusters nutzen kann, wird
über einen “Fairshare-Mechanismus” geregelt. [20]_ Dieser bestimmt wann
ein Job eines/r Wissenschaftler*in starten kann. Hierzu wird von einer
Gruppe jeweils der Verbrauch der letzten drei Monate mit ihrem “Share”
verglichen. Ist der Verbrauch höher als der Share, der der Arbeitsgruppe
zur Verfügung steht, werden die Jobs niedriger priorisiert, ist er
niedriger als der verfügbare Share, werden die Jobs höher priorisiert.
Wissenschaftler*innen können aber mehr Ressourcen verwenden, als ihnen
aufgrund ihres Shares zustehen würden. Sie werden dadurch in Zukunft nur
schlechter in der Warteschlange priorisiert. Es gibt lediglich eine
maximale Anzahl an Ressourcen, die ein/e Wissenschaftler*in gleichzeitig
in die Warteschlange stellen kann.

Administration
--------------

Administrator*innen verfügen über erweiterte Rechte. Sie haben Zugriff
auf alle Daten der Nutzer*innen, sofern diese nicht zusätzlich
verschlüsselt werden. Der administrative Zugang wird bei Bedarf manuell
gewährt und wird bei Ausscheiden, beziehungsweise wenn die Rechte nicht
mehr benötigt werden, manuell entzogen. Derzeit wird ein Protokoll für
die Administration entwickelt, das diesen Aspekt regelt. Die Einführung
des Protokolls zum Ein- beziehungsweise Austritt von Administrator*innen
ist für den Start des bwForClusters NEMO2 2022 geplant.

.. _monitoring-1:

Monitoring
----------

Das Monitoring überwacht den dauerhaften Betrieb mit Verfolgung der
Ziele Verfügbarkeit, Vertraulichkeit und Integrität der Daten. Beim
Monitoring werden Schränke, Infrastrukturkomponenten wie Netzwerk,
Speichersysteme, Server und Rechenknoten überwacht. Neben der
Überwachung der Hardware wird die Temperatur, Stromaufnahme und
zusätzlich bei Schränken die Luftfeuchtigkeit kontrolliert. Die
Nachverfolgung des Netzwerks findet in der Netzwerkabteilung und bei
Schränken in der Abteilung “Allgemeiner Betrieb” statt. Strom und
Kühlung werden zudem vom “Technischen Gebäugemanagement” (TGM)
überwacht. Zusätzlich misst der Monitoring-Server des Clusters mit Hilfe
von Zabbix Hardwaredaten wie Temperatur und Defekte auf Knotenebene und
schlägt per Mail Alarm beim Überschreiben von Grenzwerten. [21]_ Zabbix
überprüft laufend, ob die Dienste, die auf den Servern laufen müssen,
noch aktiv sind. Es wird allerdings nicht geprüft, ob die Dienste noch
korrekt funktionieren.

Außerdem werden Hardware- sowie Softwareprobleme, Login- und
Zugriffsversuche über ``rsyslog`` lokal auf der SSD und für die von den
Wissenschaftler*innen erreichbaren Knoten wie Login-, Vis- und
Rechenknoten zusätzlich auf dem Monitoringserver in Dateien gespeichert.

Der Speicherverbrauch im parallelen Dateisystem und den
Home-Verzeichnissen wird mittels Quotas durchgesetzt. Die Auslastung
wird jeweils von den zuständigen Betreibern ermittelt. Bei Isilon ist
das die Abteilung “Virtualisierung und Speichersysteme,” beim BeeGFS
machen das die Administrator*innen des bwForClusters NEMO. “Workspaces”
auf dem parallelen Wissenschaftsspeicher BeeGFS haben einen Laufzeit von
und müssen von den Wissenschaftler*innen mit einem Kommando manuell
verlängert werden. Erfolgt das nicht, werden die Daten endgültig nach
einer Wartezeit von sieben Tagen gelöscht.

Verantwortlichkeiten
====================

Die Verantwortung für den Betrieb des bwForClusters NEMO liegt bei
dem/der Leiter*in der Abteilung eScience. Diese/r berichtet der/dem
Leiter*in des Rechenzentrums der Universität Freiburg.

Maschinensaal  (MS )
--------------------

Der MS  sowie die darüber bereitgestellten Schränke werden innerhalb von
der Abteilung “Allgemeiner Betrieb” verantwortet. Das operative Geschäft
sowie die organisatorischen Schnittstellen innerhalb des RZ sowie zu
Nutzer*innen, die Ressourcen im Maschinensaal betreiben, werden in der
“Maschinensaalbenutzungsordnung” [22]_ für den Maschinensaal
beschrieben. Die Nutzung der Server-Schränke wird im Dienstkatalog
“Machine-Hosting” [23]_ spezifiziert. Die Maschinensaalbenutzungsordnung
bestimmt ebenfalls den physikalischen Zugriff der Administrator*innen
des Clusters auf die Schränke und die darin eingebauten Maschinen.

.. container:: references csl-bib-body
   :name: refs

   .. container:: csl-entry
      :name: ref-dfn-forum-2017

      1.MEIER, Konrad, GRÜNING, Björn, BLANK, Clemens, JANCZYK, Michael
      and SUCHODOLETZ, Dirk von. Virtualisierte wissenschaftliche
      forschungsumgebungen und die zukünftige rolle der rechenzentren.
      In : *10. DFN-forum kommunikationstechnologien, 30.-31. Mai 2017,
      berlin, gesellschaft für informatik eV (GI)*. 2017. p. 145–154.

   .. container:: csl-entry
      :name: ref-bwhpc2018:vicevre

      2.BAUER, Jonathan, SUCHODOLETZ, Dirk von, VOLLMER, Jeannette and
      RASCHE, Helena. Game of templates: Deploying and (re-)using
      virtualized research environments in high-performance and
      high-throughput computing. In : JANCZYK, Michael, SUCHODOLETZ,
      Dirk von and WIEBELT, Bernd (eds.), *Proceedings of the 5th bwHPC
      symposium: HPC activities in
      ba:raw-latex:`\-`den-würt:raw-latex:`\-`tem:raw-latex:`\-`berg.
      Freiburg, september 2018*. TLP, Tübingen, 2019. p. 245–262.

   .. container:: csl-entry
      :name: ref-ViCE2019

      3.SUCHODOLETZ, Dirk von, BAUER, Jonathan, ZHARKOV, Oleg, MOCKEN,
      Susanne and GRÜNING, Björn. Lessons learned from virtualized
      research environments in today’s scientific compute
      infrastructures. In : *E-science-tage 2019: Data to knowledge*.
      Heidelberg : heiBOOKS, March 2020. p. 88–81.
      ISBN `978-3-948083-14-4 <https://worldcat.org/isbn/978-3-948083-14-4>`__.

   .. container:: csl-entry
      :name: ref-ViCE2019a

      4.SUCHODOLETZ, Dirk von and BAUER, Jonathan. ViCE – creating
      uniform approach to large-scale research infrastructures. In :
      *E-science-tage 2019: Data to knowledge*. Heidelberg : heiBOOKS,
      March 2020. p. 218–222.
      ISBN `978-3-948083-14-4 <https://worldcat.org/isbn/978-3-948083-14-4>`__.

   .. container:: csl-entry
      :name: ref-konrad-werk

      5.MEIER, Konrad. *Infrastrukturkonzepte für virtualisierte
      wissenschaftliche forschungsumgebungen*. PhD thesis.
      Albert-Ludwigs-Universität Freiburg im Breisgau, 2017.

   .. container:: csl-entry
      :name: ref-DienstHPC2016

      6.ESCIENCE TEAM. *Cluster betrieb: High performance computing*.
      technical report. Rechenzentrum der Universität Freiburg, 2016.

   .. container:: csl-entry
      :name: ref-bwhpc2018:sortinghat

      7.BAUER, Jonathan, MESSNER, Manuel, JANCZYK, Michael, SUCHODOLETZ,
      Dirk von, WIEBELT, Bernd and RASCHE, Helena. A sorting hat for
      clusters: Dynamic provisioning of compute nodes for colocated
      large scale computational research infrastructures. In : JANCZYK,
      Michael, SUCHODOLETZ, Dirk von and WIEBELT, Bernd (eds.),
      *Proceedings of the 5th bwHPC symposium: HPC activities in
      ba:raw-latex:`\-`den-würt:raw-latex:`\-`tem:raw-latex:`\-`berg.
      Freiburg, september 2018*. TLP, Tübingen, 2019. p. 217–229.

   .. container:: csl-entry
      :name: ref-DienstStor2019

      8.STORAGE UND VIRTUALISIERUNGSGRUPPE. *Speichersysteme für die
      universität*. technical report. Rechenzentrum der Universität
      Freiburg, 2019.

   .. container:: csl-entry
      :name: ref-VBO1981

      9.UNIVERSITÄT FREIBURG. *Verwaltungs- und benutzungsordnung:
      (VBO)*. technical report. Universität Freiburg, 1981.

   .. container:: csl-entry
      :name: ref-NBO1996

      10.UNIVERSITÄT FREIBURG. *Benutzungsordnung für die vom
      rechenzentrum der albert-ludwigs-universität angebotenen
      netzdienste: (NBO)*. technical report. Universität Freiburg, 1996.

   .. container:: csl-entry
      :name: ref-NO1996

      11.UNIVERSITÄT FREIBURG. *Netzordnung für das freiburger
      universitäts netz: (NO)*. technical report. Universität Freiburg,
      1996.

   .. container:: csl-entry
      :name: ref-ZKI-Aufwuchs:2016

      12.SUCHODOLETZ, Dirk von, WESNER, Stefan and SCHNEIDER, Gerhard.
      Überlegungen zu laufenden
      clus:raw-latex:`\-`ter-er:raw-latex:`\-`wei:raw-latex:`\-`te:raw-latex:`\-`run:raw-latex:`\-`gen
      in bwHPC. In : SUCHODOLETZ, Dirk von, SCHULZ, Janne Chr.,
      LEENDERTSE, Jan, HOTZEL, Hartmut and WIMMER, Martin (eds.),
      *Kooperation von rechenzentren: Governance und steuerung –
      organisation, rechtsgrundlagen, politik*. De Gruyter, 2016.
      p. 331–342.
      ISBN `978-3-11-045888-6 <https://worldcat.org/isbn/978-3-11-045888-6>`__.

   .. container:: csl-entry
      :name: ref-MSBO2020

      13.SCHULZ, Janne Chr., SUCHODOLETZ, Dirk von, GEHRING, Ulrich,
      MEYER, Willibald and LEENDERTSE, Jan.
      *Maschinensaalbenutzungsordnung des rechenzentrums der universität
      freiburg: Richtlinien für das hosting und housing von hardware in
      den räumen desRechenzentrums der universität freiburg*. technical
      report. Rechenzentrum der Universität Freiburg, 2020.

   .. container:: csl-entry
      :name: ref-MachHost2020

      14.SUCHODOLETZ, Dirk von, GEHRING, Ulrich and LEENDERTSE, Jan.
      *Machine-hosting: Bereitstellung von rackspace in den
      maschinensälen des RZ. Externe version*. technical report.
      Rechenzentrum der Universität Freiburg, 2020.

.. [1]
   Hierzu entsteht derzeit das Dokument
   “Compute-Forschungsinfrastrukturen: HPC.”

.. [2]
   (1–4).

.. [3]
   (1, 5).

.. [4]
   (6).

.. [5]
   Die aktuelle Hardware des bwForClusters NEMO im zentralen Wiki
   dokumentiert:
   https://wiki.bwhpc.de/e/BwForCluster_NEMO_Hardware_and_Architecture#Compute_and_Special_Purpose_Nodes,
   besucht am 19.04.2021.

.. [6]
   Der Zugriff ist auf die IPv4-Prefixe des Belwü-Netzes beschränkt:
   https://bgpview.io/asn/553, besucht am 16.04.2021.

.. [7]
   Webseite Infoblox: https://www.infoblox.com/, besucht am 20.04.2021.

.. [8]
   Derzeit wird CentOS7 als Betriebssystem eingesetzt. Das
   Nachfolgecluster wird RHEL8 oder ein binärkompatibles Derivat
   einsetzen.

.. [9]
   (7).

.. [10]
   (8).

.. [11]
   Webseite zum Parallelspeicher BeeGFS: `beegfs.io <beegfs.io>`__,
   besucht am 20.04.2021.

.. [12]
   Die Dokumente zu bwSFS werden derzeit noch erarbeitet. Diese werden
   nachgereicht.

.. [13]
   Webseite von ATLAS-BFG: https://www.hpc.uni-freiburg.de/atlas-bfg,
   besucht am 20.04.2021.

.. [14]
   Github-Repo zu Workspaces:
   https://github.com/holgerBerger/hpc-workspace, besucht am 19.04.2021

.. [15]
   Wiki-Eintrag zu LACP: https://de.wikipedia.org/wiki/Link_Aggregation,
   besucht am 19.02.2021.

.. [16]
   Eintrag zu Omni-Path: https://de.wikipedia.org/wiki/Intel_Omni-Path,
   besucht am 19.02.2021.

.. [17]
   Registrierungsprozedur im Wiki:
   https://wiki.bwhpc.de/e/BwForCluster_User_Access, besucht am
   20.04.2021.

.. [18]
   (9–11).

.. [19]
   (12).

.. [20]
   Erklärung des Fairshare-Mechanismus Anhand der Anleitung des
   Schedulers Moab:
   http://docs.adaptivecomputing.com/9-1-3/suite/help.htm#topics/moabWorkloadManager/fairness/fairnessoverview.html,
   besucht am 20.04.2021.

.. [21]
   Zabbix Monitoring-Lösung: https://www.zabbix.com, besucht am
   20.04.2021.

.. [22]
   (13).

.. [23]
   (14).
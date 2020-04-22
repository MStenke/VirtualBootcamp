.. _lab5:

------------------------------
Lab 5: Verwalten von Workloads
------------------------------
**Jetzt da wir einige VMs bereits ausgerollt haben, lassen Sie uns anhand dieser einige VM-Verwaltungsmöglichkeiten mit AHV (Power Aktionen, Klonen und Migration) testen.**

Power-Actions und Console-Zugang
++++++++++++++++++++++++++++++++++
#. In **Prism Element > VM > Table** nutzen Sie die Suche, um Ihre Linux-VM, die Sie in dem vorherigen Lab angelegt haben, zu finden (*Initialen*-LinuxVM).

   Beachten Sie, dass in der Spalte "Energiezustand" für diese VM entweder ein roter Punkt angezeigt wird, der angibt, dass die VM ausgeschaltet ist, oder ein grüner Punkt zu sehen ist, wenn die VM eingeschaltet ist.
 
#. Wählen Sie die VM aus und stellen Sie sicher, dass diese noch läuft. (Falls nicht, klicken Sie auf **Power On**).

#. Bei ausgewählter VM klicken Sie unterhalb der VM-Tabelle auf **Launch Console**.

   Das HTML5-Konsolen-Fenster bietet rechts oben 4 Aktionen:

    .. figure:: images/vmactionoptions.png

   - **Mount ISO**: Bietet die Option eine ISO aus dem Image Service zu mounten.

    .. figure:: images/mountiso.png

   - **CTRL-ALT-DEL**: Sendet Ctrl+Alt+Del an die VM.

   - **Take Screen Capture**: Nimmt einen Screenshot als png-Datei auf.

   - **Power off / Reset VM**

    .. figure:: images/poweroffvm.png

   .. note::

     Falls Sie als Hypervisor ESXi einsetzen gilt: Die Schritte in dieser Übung können ebenfalls aus Prism heraus durchgeführt werden, wenn ein Cluster verwendet wird, der ESXi as Hypervisor nutzt und das vCenter in Prism registriert wurde.

     .. figure:: images/manage_workloads_06.png

VM-Cloning
++++++++++

#. In **Prism Element > VM > Table** wählen Sie Ihre *Initialen*-LinuxVM aus.

#. Klicken Sie auf **Clone** aus der **Actions-Leiste** unter der VM-Tabelle.

#. Füllen Sie die folgenden Details aus und klicken Sie auf **Save**:

   - **Number of Clones** : 2
   - **Prefix Name** : *Initialen*-LinuxVM**-Clone**
   - **Starting Index Number** : 1

   .. figure:: images/manage_workloads_02.png

#. Lassen Sie alle Clones ausgeschaltet **(Powered Off)**.

   Sowohl Nutanix Snapshots als auch Clones verwenden den `redirect-on-write <https://nutanixbible.com/#anchor-book-of-acropolis-snapshots-and-clones>`_ Algorithmus um schnell und effizient Kopien von VMs durch Metadata-Operationen zu erstellen.

VM-Migration
++++++++++++
Live-Migration von VMs stellt ein extrem wichtiges Feature für jede virtualisierte Umgebung dar. Denn auf diese Weise können VMs im eingeschalteten Zustand unterbrechungsfrei - also nahtlos - zwischen den Hosts innerhalb eines Clusters verschoben werden.  Dieses Feature ist im Umfeld von Infrastruktur-Wartung oder im Bereich der Lastverteilung unverzichtbar.

#. In **Prism Element > VM > Table** wählen Sie ihre *Initialen*-LinuxVM aus.

   Falls die VM ausgeschaltet ist, sollten Sie sehen, dass die VM keinen Eintrag in der **Host** Spalte besitzt.

   .. figure:: images/lab5-1.png

#. Wählen Sie die VM aus und starten Sie diese gegebenenfalls mit (**Powered On**). Nun sollte in der Spalte **Host** einer der Hosts im Cluster erscheinen.

   .. figure:: images/lab5-2.png

#. Klicken Sie als nächstes auf **Migrate** in der Leiste unter der VM-Tabelle.
   Sie können entweder einen der Hosts in dem Cluster als Migrationsziel bestimmen oder standardmäßig AHV automatisch den passenden Hosts auswählen lassen.

   .. figure:: images/lab5-3.png

#. Klicken Sie auf **Migrate** um den Schritt abzuschließen.

   Wenn der Live-Migration-Schritt abgeschlossen ist, werden Sie feststellen, dass Ihre VM nun auf einem anderen Host läuft.

   .. figure:: images/lab5-4.png

Affinity-Policies
+++++++++++++++++

#. In **Prism Element > VM > Table** selektieren Sie Ihre *Initialen*-LinuxVM aus.

#. Wählen Sie **Powered Off** VM, dann klicken Sie auf **Update** und innerhalb von **VM Host Affinity** auf 
 **+ Set Affinity**.

#. Wählen Sie nun **zwei** der **Hosts** aus, zu welcher die VM eine *Affinity* haben soll und klicken sie auf **Save** und im Anschluss daran dann nochmals auf **Save** um den Vorgang abzuschließen.

   .. figure:: images/lab5-5.png

   .. note:: Wir selektieren hier mehr als einen Host, dass die VM, falls einer der Hosts ausfallen sollte, immer noch ein Migrationsziel hat.

#. Starten Sie nun die VM und verifizieren Sie, dass die VM nun auf einem der **Host** läuft welchen Sie in der *Affinity Policy* angegeben haben.

#. Wählen Sie die VM aus und klicken dann auf **Migrate**.

   Sie sollten nun die folgende Nachricht angezeigt bekommen:

   .. figure:: images/lab5-6.png

   - Diese VM hat eine **Host Affinity Rule** mit 2 von 4 möglichen verfügbaren Hosts, in einem 4-Node-Cluster und kann daher auch nur auf die diesbezüglich definierten Hosts migriert werden.

#. Klicken Sie auf **Migrate**.

   Sie sollten nun auch unter **view task detailes** sehen können, wie die VM auf einen der anderen zugelassenen Hosts verschoben wurde.

*VM-zu-Host Affinity Policies* werden typischerweise genutzt um VMs an bestimmte Host zu binden, z.B. aus Performance- oder Lizenz-Gründen, etc.. AHV unterstützt darüber hinaus auch noch *VM-zu-VM Anti-Affinity*-Regeln. Z.B. für Anwendungen bei denen sichergestellt werden muss, dass mehrere Instanzen einer Anwendungn z.B. nicht auf dem gleichen Host laufen dürfen.

High Availability & Dynamic Scheduling
++++++++++++++++++++++++++++++++++++++

Im Gegensatz zu VMware's ESXi-Hypervisor, ist bei AHV *High Availability* standardmäßig bereits aktiviert und sorgt dafür, dass VMs im Falle eines Host-Ausfalles die VMs bestmöglich (*best effort*) auf einem anderen Host wieder neugestarted werden. Erweiterte Konfigurationen erlauben es Ressourcen zu reservieren, um sicherzustellen, dass auch genügend Ressourcen vorhanden sind um alle VMs auch tatsächlich auf den Zielsystemen neustarten zu können.

.. note::

   Um Memory Reservierungen vorzunehmen, wählen Sie **Enable HA Reservation** unter **Prism Element > Settings > Manage VM High Availability**.

   .. figure:: images/lab5-7.png

   Da diese Umgebung nicht über die entsprechenden Ressourcen verfügt, aktivieren Sie bitte **kein** *HA memory reservations*.

Mit dem **Acropolis Dynamic Scheduler (ADS)** Service nimmt AHV ein intelligentes, initiales Platzieren von VMs vor und kann VMs dynamisch zu anderen Hosts im Cluster verschieben um Performance zu optimieren. Diese Funktion ist mit dem VMware DRS (Distributed Resource Scheduler)zu vergleichen. ADS ist bereits standardmäßig (*out of the box*) ohne zusätzliche Konfiguration aktiviert.

Ein Mehrwert der Nutanix AHV Lösung ist, dass VM Platzierungs-Entscheidungen nicht nur ausschließlich auf CPU- & Memory-Engpass-Vermeidung basiert, sondern ebenso die Storage Performance mitberücksichtigt.

Mehr Informationen bzgl. des **Acropolis Dynamic Scheduler** ist `hier <https://nutanixbible.com/#anchor-book-of-acropolis-dynamic-scheduler>`_ in der Nutanix Bible zu finden.

Zusammenfassung
+++++++++++++++
In diesem Lab konnten Sie sehen, welche vielfältigen Werkzeuge und Optionen AHV bietet um VMs in einem Cluster zu verwalten. AHV bietet wichtige Funktionen wie z.B. Live-Migration, Hochverfügbarkeit (High Availability) und dynamische VM-Platzierung (Dynamic VM Placement) "out-of-the-box" ohne zusätzliche Konfiguration. VMs können unter AHV darüber hinaus nicht nur durch Prism verwaltet werden, sondern ebenfalls via CLI oder REST-API.

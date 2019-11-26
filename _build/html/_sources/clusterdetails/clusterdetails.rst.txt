.. clusterdetails:

--------------------
Bootcamp Cluster Details
--------------------

Cluster Hardware Details
++++++++++++++++++++++++

**Hardware Modell: NX-3060 G6 (All Flash)**

.. figure:: images/cluster3060g5a.png

.. list-table::
   :widths: 15 40
   :header-rows: 1

   * - Eigenschaft
     - Ausstattung
   * - **CPU-Modell**
     - Intel(R) Xeon(R) Silver 4114 CPU @ 2.20GHz
   * - **CPU-Cores**
     - 24 Core je Node | 80 Cores im Cluster
   * - **CPU-Taktrate**
     - 52,8 GHz je Node  | 211,2 GHz im Cluster
   * - **Arbeitsspeicher**
     - 256 GB je Node  | 1.024 GB im Cluster
   * - **SSD-Kapazität**
     - 2,46 TiB je Node | 7.38 TiB im Cluster

.. note::
  Bedenken Sie bitte, dass diese Technology-Bootcamp-Testumgebung zum einen nicht auf der neuesten Hardware basiert und das zum anderen auf Grund der Entfernung zum Lab-Datacenter entsprechende Latenzen auftreten können. Nichtsdestotrotz lassen sich mit dieser Umgebung die typischen Routineaufgaben bzgl. einer Nutanix-Cluster-Plattform mit einer ausgezeichneten User-Experience testen.

Infrastruktur IPs
+++++++++++++

.. list-table::
   :widths: 10 10 10 10
   :header-rows: 1

   * - Nodes
     - CVMs
     - Hypervisors
     - IPMI
   * - **Position A**
     - 10.42.236.29
     - 10.42.236.25
     - 10.42.236.33
   * - **Position B**
     - 10.42.236.30
     - 10.42.236.26
     - 10.42.236.34
   * - **Position C**
     - 10.42.236.31
     - 10.42.236.27
     - 10.42.236.35
   * - **Position D**
     - 10.42.236.32
     - 10.42.236.28
     - 10.42.236.36

.. list-table::
   :widths: 20 10
   :header-rows: 1

   * - Services
     - IP-Adressen
   * - **Virtuelle IP Adresse Cluster**
     - 10.42.236.37
   * - **iSCSI Data Services IP**
     - 10.42.236.38


Virtuelle Maschinen
++++++++++++++++++++

Die folgenden VMs wurden bereits auf dem Cluster ausgerollt:

.. list-table::
   :widths: 25 25 50
   :header-rows: 1

   * - VM-Name
     - IP-Adresse
     - Beschreibung
   * - **Prism Central**
     - 10.42.113.39
     - Nutanix Prism Central
   * - **AutoDC2**
     - 10.42.113.41
     - ntnxlab.local Domain Controller
   * - **X-Ray**
     - 10.42.113.125
     - HCI Benchmarking Tool


Images / VM Vorlagen & ISOs
++++++++++++++++++++++++++++

Die folgenden Disk-Images & ISOs wurden bereits im Vorfeld auf den Cluster hochgeladen, sodass sie via Image-Service verwendet werden können:

.. list-table::
   :widths: 20 7 50
   :header-rows: 1

   * - Image-Name
     - Typ
     - Beschreibung
   * - **CentOS7.iso**
     - ISO
     - Ein CentOS7 ISO
   * - **CentOS7.qcow2**
     - DISK
     - Ein bereits vorgefertigtes CentOS 7 Disk Image
   * - **hycu-3.5.0-6253.qcow2**
     - DISK
     - Ein bereits vorgefertigtes HYCU Disk Image
   * - **Linux_ToolsVM.qcow2**
     - DISK
     - Ein vorkonfiguriertes Linux Image  + Tools Disk Image
   * - **MSSQL-2016-VM.qcow2**
     - DISK
     - Ein Windows Server 2016 Standard Image mit MS SQL 2016 vorinstalliert.
   * - **Nutanix-VirtIO-1.1.3.iso**
     - ISO
     - Ein ISO mit den Nutanix VM Treibern.
   * - **ToolsVM.qcow2**
     - DISK
     - Ein vorkonfiguriertes Windows Server 2012 R2 + Tools (pgAdmin, CyberDuck, text editors, etc.) Disk Image
   * - **Windows10-1709.qcow2**
     - DISK
     - Ein Windows 10 Disk Image.
   * - **Windows10.iso**
     - ISO
     - Eine Windows 10 ISO.
   * - **Windows2012R2.iso**
     - ISO
     - Eine Windows2016 ISO.
   * - **Windows2012R2.qcow2**
     - DISK
     - Ein Windows2016 Disk Image.
   * - **Windows2016.iso**
     - ISO
     - Ein Windows2016 ISO
   * - **Windows2016.qcow2**
     - DISK
     - Ein Windows2016 Disk Image



Zugangsdaten
++++++++++++

Die folgende Tabelle führt die standardmäßig hinterlegten Zugangsdaten für die Umgebung auf (falls andere zum Einsatz kommen sollten wird dies gesondert aufgeführt):

.. list-table::
  :widths: 20 20 10
  :header-rows: 1

  * - Name
    - Benutzername
    - Passwort
  * - **IPMI**
    - ADMIN
    - ADMIN
  * - **Prism Element**
    - admin
    - ntnx4Stgt!
  * - **Prism Element VM's**
    - nutanix
    - ntnx4Stgt!
  * - **Prism Central**
    - admin
    - ntnx4Stgt!
  * - **Prism Central VM**
    - nutanix
    - ntnx4Stgt!
  * - **NTNXLAB Domain**
    - NTNXLAB\\Administrator
    - nutanix/4u
  * - **CentOS VM Image**
    - root
    - nutanix/4u
  * - **X-Ray VM**
    - root
    - nutanix/4u
  * - **Move**
    - nutanix
    - nutanix/4u

Darüber hinaus besitzt der Cluster eine dedizierte Domain-Controller-VM, welche die Active-Directory-Services für die **NTNXLAB.local** Domain bereitstellt. Die Domain wurde mit den folgenden Nutzern und Gruppen vorkonfiguriert:

.. list-table::
  :widths: 20 20 10
  :header-rows: 1

  * - Gruppe
    - Benutzername(n)
    - Passwort
  * - **Administrators**
    - Administrator
    - nutanix/4u
  * - **SSP Admins**
    - adminuser01-adminuser25
    - nutanix/4u
  * - **SSP Developers**
    - devuser01-devuser25
    - nutanix/4u
  * - **SSP Power Users**
    - poweruser01-poweruser25
    - nutanix/4u
  * - **SSP Basic Users**
    - basicuser01-basicuser25
    - nutanix/4u

Netzwerk
++++++++

Die folgenden virtuellen Netzwerke wurden wie folgt vorkonfiguriert:

.. list-table::
   :widths: 33 33 33
   :header-rows: 1

   * -
     - **Primäres** Netzwerk
     - **Sekundäres** Netzwerk
   * - **VLAN**
     - 0
     - 2361
   * - **Netzwerk IP Adresse**
     - 10.42.236.0
     - 10.42.236.128
   * - **Netzmaske**
     - 255.255.255.128 (/25)
     - 255.255.255.128 (/25)
   * - **Default Gateway**
     - 10.42.236.1
     - 10.42.236.129
   * - **IP Address Management (IPAM)**
     - Aktiviert
     - Aktiviert
   * - **DHCP Pool**
     - 10.42.236.50  - 125
     - 10.42.236.132 - 253
   * - **Domain**
     - NTNXLAB.local
     - NTNXLAB.local
   * - **DNS**
     - 10.42.236.40 (DC VM)
     - 10.42.236.40 (DC VM)
   

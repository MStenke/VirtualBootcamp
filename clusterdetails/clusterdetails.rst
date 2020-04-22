.. _clusterdetails:

------------------------
Bootcamp Cluster Details
------------------------

Cluster Hardware Details
++++++++++++++++++++++++

**Typisches Modell für PoC's mit 4 Nodes in 2 Höheneinheiten:**

.. figure:: images/cluster3060g5a.png

.. note::
  Bedenken Sie bitte, dass diese Testumgebung zum einen nicht auf der neuesten Hardware basiert und das zum anderen auf Grund der Entfernung zum Lab-Datacenter entsprechende Latenzen auftreten können. Nichtsdestotrotz lassen sich mit dieser Umgebung die typischen Routineaufgaben bzgl. einer Nutanix-Cluster-Plattform mit einer ausgezeichneten User-Experience testen.

Infrastruktur IPs
+++++++++++++++++

.. list-table::
   :widths: 10 10 10 10
   :header-rows: 1

   * - Nodes
     - CVMs
     - Hypervisors
     - IPMI
   * - **Position A**
     - 10.42.99.29
     - 10.42.99.25
     - 10.42.99.33
   * - **Position B**
     - 10.42.99.30
     - 10.42.99.26
     - 10.42.99.34
   * - **Position C**
     - 10.42.99.31
     - 10.42.99.27
     - 10.42.99.35
   * - **Position D**
     - 10.42.99.32
     - 10.42.99.28
     - 10.42.99.36

.. list-table::
   :widths: 20 10
   :header-rows: 1

   * - Services
     - IP-Adressen
   * - **Virtuelle IP Adresse Cluster**
     - 10.42.99.37
   * - **iSCSI Data Services IP**
     - 10.42.99.38


Virtuelle Maschinen
++++++++++++++++++++

Die folgenden VMs wurden u.a. bereits auf dem Cluster ausgerollt:

.. list-table::
   :widths: 25 25 50
   :header-rows: 1

   * - VM-Name
     - IP-Adresse
     - Beschreibung
   * - **Prism Central**
     - 10.42.99.39
     - Nutanix Prism Central
   * - **AutoAD**
     - 10.42.99.41
     - ntnxlab.local Domain Controller
   * - **NTNX-BootcampFS-1**
     - 10.42.99.53 / 10.42.99.174
     - Nutanix File Server
   * - **NTNX-FA-BootcampFileAnalytics**
     - 10.42.99.132
     - Nutanix File Analytics


Images / VM Vorlagen & ISOs
++++++++++++++++++++++++++++

Die folgenden Disk-Images & ISOs wurden bereits im Vorfeld auf den Cluster hochgeladen, sodass sie via Image-Service verwendet werden können:

.. list-table::
   :widths: 20 7 50
   :header-rows: 1

   * - Image-Name
     - Typ
     - Beschreibung
   * - **CentOS7.qcow2**
     - DISK
     - Ein bereits vorgefertigtes CentOS 7 Disk Image.
   * - **ERA-Server-build-1.2.1.qcow2**
     - DISK
     - Ein Nutanix ERA Disk Image.
   * - **Linux_ToolsVM.qcow2**
     - DISK
     - Ein vorkonfiguriertes Linux Image  + Tools Disk Image.
   * - **MSSQL-2016-VM.qcow2**
     - DISK
     - Ein Windows Server 2016 Standard Image mit MS SQL 2016 vorinstalliert.
   * - **Nutanix-VirtIO-1.1.5.iso**
     - ISO
     - Ein ISO mit den Nutanix VM Treibern.
   * - **Windows2016.qcow2**
     - DISK
     - Ein Windows2016 Disk Image.
   * - **WinToolsVM.qcow2**
     - DISK
     - Ein vorkonfiguriertes Windows Server 2012 R2 + Tools (pgAdmin, CyberDuck, Text Editoren, etc.) Disk Image.


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
    - nutanix/4u
  * - **NTNXLAB Domain**
    - NTNXLAB\\Administrator
    - nutanix/4u
  * - **CentOS VM Image**
    - root
    - nutanix/4u


Darüber hinaus besitzt der Cluster eine dedizierte Domain-Controller-VM, welche die Active-Directory-Services für die **NTNXLAB.local** Domain bereitstellt. Die Domain wurde mit den folgenden Nutzern und Gruppen vorkonfiguriert:

.. list-table::
  :widths: 20 20 10
  :header-rows: 1

  * - Gruppe
    - Benutzername(n)
    - Passwort
  * - **Administrators / Domain Admins**
    - Administrator
    - nutanix/4u
  * - **Bootcamp Users**
    - User01-User25
    - nutanix/4u
  * - **SSP Admins**
    - Adminuser01-Adminuser25
    - nutanix/4u
  * - **SSP Operators**
    - Operator01-Operator25
    - nutanix/4u
  * - **SSP Developers**
    - Devuser01-Devuser25
    - nutanix/4u
  * - **SSP Consumers**
    - Consumer01-Consumer25
    - nutanix/4u
  * - **SSP Custom**
    - Custom01-Custom25
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
     - 991
   * - **Netzwerk IP Adresse**
     - 10.42.99.0
     - 10.42.99.128
   * - **Netzmaske**
     - 255.255.255.128 (/25)
     - 255.255.255.128 (/25)
   * - **Default Gateway**
     - 10.42.99.1
     - 10.42.99.129
   * - **IP Address Management (IPAM)**
     - Aktiviert
     - Aktiviert
   * - **DHCP Pool**
     - 10.42.99.50  - 125
     - 10.42.99.132 - 253
   * - **Domain**
     - NTNXLAB.local
     - NTNXLAB.local
   * - **DNS**
     - 10.42.99.41 (DC VM)
     - 10.42.99.41 (DC VM)
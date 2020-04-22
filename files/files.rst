.. _files:

--------------------
Lab 8: Nutanix Files
--------------------

*Die geschätzte Zeit für die Durchführung dieses Labs beträgt 45 Minuten.*

Traditionell war der Dateispeicher ein weiteres Silo innerhalb der IT, das unnötige Komplexität mit sich brachte und unter den gleichen Skalierungsproblemen und dem Mangel an kontinuierlicher Innovation litt, die beim SAN-Speicher auftreten. Nutanix glaubt, dass in der Enterprise Cloud kein Platz für Silos ist. Durch die Annäherung an den Dateispeicher als App, die in Software auf einer bewährten HCI-Lösung ausgeführt wird, bietet Nutanix Files durch One-Click-Management hohe Leistung, Skalierbarkeit und schnelle Innovation.

**In dieser Übung arbeiten Sie mit Nutanix Files, um SMB-Freigaben und NFS-Exporte zu verwalten und die neuen Funktionen für die Bereitstellung von Dateien mit File Analytics zu erkunden.**

Aus Zeitgründen und zur gemeinsamen Nutzung von Infrastrukturressourcen wurde in Ihrem Cluster bereits ein Dateicluster bereitgestellt. Der **BootcampFS** - File Server ist eine Einzelknoteninstanz. Typische **Nutanix Files** Installationen beginnen mit 3 File-Server-VMs und können je nach benötigter Leistung per Scale-up und Scale-out erweitert werden.

**BootcampFS** wurde so konfiguriert, dass es das **primäre** Netzwerk für die Kommunikation mit dem Back-End-Speicher, iSCSI-Verbindungen von **CVM** zu Volume-Groups und das **sekundäre** Netzwerk für die Kommunikation mit Clients, Active Directory, Antiviren-Diensten usw. verwendet.

.. figure:: images/1.png

.. note::
   In Produktionsumgebungen ist es normalerweise wünschenswert, Dateien mit dedizierten virtuellen Netzwerken für den Client- und Speicherverkehr bereitzustellen. Bei Verwendung von zwei Netzwerken verbietet Files im Storage-Netzwerk standardmäßig den Client Traffic, was bedeutet, dass VMs, die dem primären Netzwerk zugewiesen sind, nicht auf Freigaben zugreifen können.

Da Files Nutanix Volume Groups für die Datenspeicherung nutzt, können dieselben zugrunde liegenden Speichervorteile wie Komprimierung, Erasure Coding, Snapshots und Replikation genutzt werden.

In **Prism Element > File Server > File Server**, wählen Sie **BootcampFS** und Klicken Sie auf **Protect**.

   .. figure:: images/10.png

Beachten Sie die Standardzeitpläne für die Self-Service-Wiederherstellung. Diese Funktion steuert den Snapshot-Zeitplan für die frühere Versionen Funktionalität von Windows. Durch die Unterstützung früherer Windows-Versionen können Endbenutzer Änderungen an Dateien rückgängig machen, ohne Speicher- oder Sicherungsadministratoren zu involvieren. Beachten Sie, dass diese lokalen Snapshots den Dateiservercluster nicht vor lokalen Fehlern schützen und dass die Replikation des gesamten Dateiserverclusters auf entfernte Nutanix-Cluster durchgeführt werden kann.


Verwalten von SMB-Freigaben
+++++++++++++++++++++++++++

In dieser Übung erstellen und testen Sie eine SMB-Freigabe, die zur Unterstützung der unstrukturierten Dateidatenanforderungen eines abteilungsübergreifenden Teams für die Fiesta-Anwendung verwendet wird.


Freigabe erstellen
..................

#. In **Prism Element > File Server**, klicken Sie auf **+ Share/Export**.

#. Füllen Sie die folgenden Felder aus:

   - **Name** - *Initialen*\ **-FiestaShare**
   - **Description (Optional)** - Fiesta App Team-Freigabe, die von Produkt-Management, Entwicklung und Marketing verwendet wird.
   - **File Server** - **BootcampFS**
   - **Share Path (Optional)** - Leer lassen. In diesem Feld können Sie einen vorhandenen Pfad angeben, in dem eine verschachtelte Freigabe (nested share) erstellt werden soll.
   - **Max Size (Optional)** - 200 GiB
   - **Select Protocol** - SMB

   .. figure:: images/2.png

   Da dies ein AOS-Cluster mit einem einzelnen Knoten und daher eine einzelne Dateiserver-VM ist, sind alle Freigaben **Standard** - Freigaben . Eine Standardfreigabe (Standard Share) bedeutet, dass alle Verzeichnisse und Dateien der obersten Ebene innerhalb der Freigabe sowie die Verbindungen zur Freigabe von einer einzelnen Dateiserver-VM bereitgestellt werden.

   Wenn dies ein Dateicluster mit drei Knoten oder größer wäre, hätten Sie die Möglichkeit, eine **verteilte** Freigabe (Distributed Share) zu erstellen. Verteilte Freigaben eignen sich für Basisverzeichnisse, Benutzerprofile und Anwendungsordner. Diese Art der Freigabe speichert Verzeichnisse der obersten Ebene auf allen Datei-VMs und gleicht die Verbindungen auf allen Datei-VMs im Dateicluster aus.

#. Klicken Sie auf **Next**.

#. Wählen Sie **Enable Access Based Enumeration** und **Self Service Restore**. Wählen Sie **Blocked File Types** und geben Sie eine durch Kommas getrennte Liste von Erweiterungen wie .flv, .mov ein.

   .. figure:: images/3.png

   .. note::

      **Access Based Enumeration (ABE)** stellt sicher, dass nur Dateien und Ordner, auf die ein bestimmter Benutzer Lesezugriff hat, für diesen Benutzer sichtbar sind. Dies ist normalerweise für Windows-Dateifreigaben aktiviert.

      Mit der **Self Service Restore** können Benutzer die vorherige Windows-Version nutzen, um einzelne Dateien auf der Grundlage von Nutanix-Snapshots problemlos auf frühere Revisionen zurückzusetzen.

      Mit **Blocked File Types** können File Server Administratoren verhindern, dass bestimmte Dateitypen (z.B. große, persönliche Mediendateien) auf Unternehmensfreigaben geschrieben werden. Dies kann pro Server oder pro Freigabe konfiguriert werden, wobei die Einstellungen pro Freigabe die serverweiten Einstellungen überschreiben.

#. Klicken Sie auf **Next**.

#. Überprüfen Sie die **Summary** und klicken Sie auf **Create**.

   .. figure:: images/4.png

   Es ist üblich, dass Freigaben, die von vielen Menschen verwendet werden, Quoten nutzen, um einen fairen Umgang mit Ressourcen sicherzustellen. Nutanix Files bietet die Möglichkeit, entweder weiche oder harte Quoten pro Freigabe für einzelne Benutzer in Active Directory oder für bestimmte Active Directory-Sicherheitsgruppen festzulegen.
   
#. Wählen Sie in **Prism Element > File Server > Share/Export**, Ihren Share aus und klicken Sie **+ Add Quota Policy**.

#. Füllen Sie die folgenden Felder aus und klicken Sie auf **Save**:

   - Wählen Sie **Group**
   - **User or Group** - SSP Developers
   - **Quota** - 10 GiB
   - **Enforcement Type** - Hard Limit

   .. figure:: images/9.png

#. Klicken Sie auf **Save**.

Testen der Freigabe
...................

.. note::

      Um die Freigabe zu testen zu können, benötigen Sie eine Windows VM ausrollen die Sie im folgenden verwenden. Folgen Sie dafür der Anleitung für die **Windows Tools VM** (optionale Labs).

#. Stellen Sie über die VM-Konsole als **non-Administrator NTNXLAB** - Domänenkonto eine Verbindung zu Ihrer *Initials*\ **-WinTools** VM her:

   .. note::

      Mit diesen Konten können Sie keine Verbindung über RDP herstellen.

   - user01 - user25
   - devuser01 - devuser25
   - operator01 - operator25
   - **Password** nutanix/4u

   .. figure:: images/16.png

   .. note::

     Die Windows Tools-VM wurde bereits der Domäne NTNXLAB.local hinzugefügt. Sie können jede VM mit Domänenbeitritt verwenden, um die folgenden Schritte auszuführen.

#. Öffnen Sie ``\\BootcampFS.ntnxlab.local\`` im **File Explorer**.

#. Öffnen Sie einen Browser auf Ihrem *Initialien*\ **-WinTools** Desktop und laden Sie Beispieldaten herunter, um sie in Ihre Freigabe einzufügen:

   - **Bei Verwendung eines PHX-Clusters** - http://10.42.194.11/workshop_staging/peer/SampleData_Small.zip
   - **Bei Verwendung eines RTP-Clusters** - http://10.55.251.38/workshop_staging/peer/SampleData_Small.zip

#. Extrahieren Sie den Inhalt der Zip-Datei in Ihre Dateifreigabe.

   .. figure:: images/5.png

   - Der Benutzer **NTNXLAB\\Administrator** wurde während der Bereitstellung des Dateiclusters als Files Administrator angegeben, sodass er standardmäßig Lese- / Schreibzugriff auf alle Freigaben hat.
   - Das Verwalten des Zugriffs für andere Benutzer unterscheidet sich nicht von anderen SMB-Freigaben.


#. Klicken Sie von ``\\BootcampFS.ntnxlab.local\`` mit der rechten Maustaste auf *Initialen*\ **-FiestaShare > Properties**.

   #. Wählen Sie die Registerkarte **Security** und klicken Sie auf **Advanced**.

      .. figure:: images/6.png

   #. Wählen Sie **Users (BootcampFS\\Users)** und klicken Sie auf **Remove**.

      .. figure:: images/7.png

   #. Klicken Sie auf **Add**.

   #. Klicken Sie auf **Select a principal** und geben Sie im Feld **Object Name** - **Everyone** an. Klicken Sie auf **OK**.

      .. figure:: images/8.png

   #. Füllen Sie die folgenden Felder aus und klicken Sie auf **OK**:

      - **Type** - Allow
      - **Applies to** - This folder only
      - Wählen Sie  **Read & execute**
      - Wählen Sie  **List folder contents**
      - Wählen Sie  **Read**
      - Wählen Sie  **Write**

      .. figure:: images/8b.png

   #. Wählen Sie auf **OK > OK > OK**, um die Berechtigungsänderungen zu speichern.

   Alle Benutzer können jetzt Ordner und Dateien in der *Initialien*\ **-FiestaShare** Freigabe erstellen.

#. Öffnen Sie **PowerShell** und versuchen Sie, eine Datei mit einem blockierten Dateityp zu erstellen, indem Sie den folgenden Befehl ausführen:

   .. code-block:: PowerShell

      New-Item \\BootcampFS\INITIALS-FiestaShare\MyFile.flv

   Beachten Sie, dass die Erstellung der neuen Datei abgelehnt wird.

#. Kehren Sie zu **Prism Element > File Server > Share/Export** zurück und wählen Sie Ihre Freigabe aus. Überprüfen Sie die **Share Details**, **Usage** und **Performance** Registerkarten die zur Verfügung stehenden Informationen auf Share Ebene, einschließlich der Anzahl der Dateien und Verbindungen, die Speichernutzung im Laufe der Zeit, Latenz, Durchsatz und IOPS.

   .. figure:: images/11.png

   In der nächsten Übung erfahren Sie, wie Dateien weitere Einblicke in die Verwendung der einzelnen Dateiserver und Freigaben geben können.

Nutanix File Analytics
++++++++++++++++++++++
In dieser Übung lernen Sie die neuen, integrierten Funktionen für die Dateianalyse kennen, die mit Nutanix-Files Analytics zur Verfügung stehen. Dazu gehören das Scannen vorhandener Freigaben, das Erstellen von Anomaliewarnungen und das Überprüfen von Überwachungsdetails. File Analytics wird in wenigen Minuten als eigenständige VM über einen automatisierten One-Click-Vorgang in Prism Element bereitgestellt. Diese VM wurde bereits in Ihrer Umgebung bereitgestellt und aktiviert.

#. In **Prism Element > File Server > File Server**, wählen Sie **BootcampFS** und klicken Sie auf **File Analytics**.

   .. figure:: images/12.png

   .. note::

      File Analytics sollte bereits aktiviert sein. Wenn Sie jedoch dazu aufgefordert werden, müssen Sie Ihr Dateiverwaltungskonto angeben, damit Analytics alle Freigaben scannen kann.

      - **Username**: NTNXLAB\\administrator
      - **Password**: nutanix/4u

      .. figure:: images/old13.png

#. Da es sich um eine gemeinsam genutzte Umgebung handelt, wird das Dashboard wahrscheinlich bereits mit Daten aus Freigaben gefüllt, die von anderen Benutzern erstellt wurden. Klicken Sie auf :fa:`gear` **> Scan File System**, um Ihre neu erstellte Freigabe zu scannen. Wählen Sie Ihre Freigabe aus und klicken Sie auf **Scan**.

   .. figure:: images/14.png

   .. note::

      Wenn Ihre Freigabe nicht angezeigt wird, geben Sie ihm bitte etwas Zeit, um dies zu aktualisieren.

#. Schließen Sie das Fenster **Scan File System** und aktualisieren Sie Ihren Browser.

#. Sie sollten folgendes sehen **Data Age**, **File Distribution by Size** und **File Distribution by Type** sobald sich das Dashboard aktualisiert hat.

   .. figure:: images/15.png

#. Erstellen Sie auf Ihrer *Initialien*\ **-WinTools** - VM einige Audit-Trail-Aktivitäten, indem Sie mehrere der Dateien unter **Sample Data** öffnen.

   .. note:: Möglicherweise müssen Sie einen kurzen Assistenten für OpenOffice ausführen, wenn Sie diese Anwendung zum Öffnen einer Datei verwenden.

#. Aktualisieren Sie die **Dashboard** - Seite in Ihrem Browser, um die Aktualisierung der Bedienfelder **Top 5 Active Users**, **Top 5 Accessed Files** und **File Operations** anzuzeigen.

   .. figure:: images/17.png

#. Um auf den **Audit Trail** für Ihr Benutzerkonto zuzugreifen, klicken Sie unter **Top 5 Active Users** auf Ihren Benutzer.

   .. figure:: images/17b.png

#. Alternativ können Sie in der Symbolleiste **Audit Trails** auswählen und nach Ihrem Benutzer oder einer bestimmten Datei suchen.

   .. figure:: images/18.png

   .. note::

      Sie können Platzhalter für Ihre Suche verwenden, z. B. **.doc**
..
   #. Next, we will create rules to detect anomalous behavior on the File Server. From the toolbar, click :fa:`gear` **> Define Anomaly Rules**.

      .. figure:: images/19.png

      .. note::

         Anomaly Rules are defined on a per File Server basis, so the below rules may have already been created by another user.

   #. Click **Define Anomaly Rules** and create a rule with the following settings:

      - **Events:** Delete
      - **Minimum Operation %:** 1
      - **Minimum Operation Count:** 10
      - **User:** All Users
      - **Type:** Hourly
      - **Interval:** 1

   #. Under **Actions**, click **Save**.

   #. Choose **+ Configure new anomaly** and create an additional rule with the following settings:

      - **Events**: Create
      - **Minimum Operation %**: 1
      - **Minimum Operation Count**: 10
      - **User**: All Users
      - **Type**: Hourly
      - **Interval**: 1

   #. Under **Actions**, click **Save**.

      .. figure:: images/20.png

   #. Click **Save** to exit the **Define Anomaly Rules** window.

   #. To test the anomaly alerts, return to your *Initials*\ **-WinTools** VM and make a second copy of the sample data (via Copy/Paste) within your *Initials*\ **-FiestaShare** share.

   #. Delete the original sample data folders.

      .. figure:: images/21.png

      While waiting for the Anomaly Alerts to populate, next we’ll create a permission denial.

      .. note:: The Anomaly engine runs every 30 minutes.  While this setting is configurable from the File Analytics VM, modifying this variable is outside the scope of this lab.

   #. Create a new directory called *Initials*\ **-MyFolder** in the *Initials*\ **-FiestaShare** share.

   #. Create a text file in the *Initials*\ **-MyFolder** directory and take out your deep seeded worldly frustrations on your for a few moments to populate the file. Save the file as *Initials*\ **-file.txt**.

      .. figure:: images/22.png

   #. Right-click *Initials*\ **-MyFolder > Properties**. Select the **Security** tab and click **Advanced**. Observe that **Users (BootcampFS\\Users)** lack the **Full Control** permission, meaning that they would be unable to delete files owned by other users.

      .. figure:: images/23.png

   #. Open a PowerShell window as another non-Administrator user account by holding **Shift** and right-clicking the **PowerShell** icon in the taskbar and selecting **Run as different user**.

      .. figure:: images/24.png

   #. Change Directories to *Initials*\ **-MyFolder** in the *Initials*\ **-FiestaShare** share.

        .. code-block:: bash

           cd \\BootcampFS.ntnxlab.local\XYZ-FiestaShare\XYZ-MyFolder

   #. Execute the following commands:

        .. code-block:: bash

           cat .\XYZ-file.txt
           rm .\XYZ-file.txt

      .. figure:: images/25.png

   #. Return to **Analytics > Dashboard** and note the **Permission Denials** and **Anomaly Alerts** widgets have updated.

      .. figure:: images/26.png

   #. Under **Permission Denials**, select your user account to view the full **Audit Trail** and observe that the specific file you tried to removed is recorded, along with IP address and timestamp.

      .. figure:: images/27.png

   #. Select **Anomalies** from the toolbar for an overview of detected anomalies.

      .. figure:: images/28.png

File Analytics gibt Speicheradministratoren einfache und dennoch leistungsstarke Informationen in die Hand, sodass sie sowohl die Nutzung als auch den Zugriff in einer Nutanix Files-Umgebung verstehen und prüfen können.

Verwenden von NFS-Exporten 
++++++++++++++++++++++++++

In dieser Übung erstellen und testen Sie einen NFSv4-Export, der zur Unterstützung von Clusteranwendungen, zum Speichern von Anwendungsdaten wie der Protokollierung oder zum Speichern anderer unstrukturierter Dateidaten verwendet wird, auf die Linux-Clients häufig zugreifen.

Aktivieren des NFS-Protokolls 
.............................

.. note::

   Das Aktivieren des NFS-Protokolls muss nur einmal pro Dateiserver durchgeführt werden und wurde möglicherweise bereits in Ihrer Umgebung abgeschlossen. Wenn NFS bereits aktiviert ist, fahren Sie mit `Benutzerzuordnungen konfigurieren`_ fort.

#. In **Prism Element > File Server**, wählen Sie Ihren File-Server und klicken Sie auf **Protocol Management > Directory Services**.

   .. figure:: images/29.png

#. Wählen Sie **Use NFS Protocol** mit **Unmanaged** User Management und Authentication und klicken Sie auf **Update**.

   .. figure:: images/30.png

Export erstellen
................

#. In **Prism > File Server**, klicken Sie auf **+ Share/Export**.

#. Füllen Sie die folgenden Felder aus:

   - **Name** - logs
   - **Description (Optional)** - Dateifreigabe für Systemprotokolle
   - **File Server** - *Initials*\ **-Files**
   - **Share Path (Optional)** - Leave blank
   - **Max Size (Optional)** - Leave blank
   - **Select Protocol** - NFS

#. Klicken Sie auf **Next**.

#. Füllen Sie die folgenden Felder aus:

   - Wählen Sie **Enable Self Service Restore**
      - Diese Snapshots werden als .snapshot-Verzeichnis für NFS-Clients angezeigt.
   - **Authentication** - System
   - **Default Access (For All Clients)** - No Access
   - Wählen Sie **+ Add exceptions**
   - **Clients with Read-Write Access** - *Die ersten 3 Oktette Ihres Clusternetzwerks*\ .* (z.B. 10.42.99.\*)

   Standardmäßig ermöglicht ein NFS-Export jedem Host ein Lese- / Schreibzugriff der den Export mounted. Dies kann jedoch auf bestimmte IPs oder IP-Bereiche beschränkt werden.

#. Klicken Sie auf **Next**.

#. Überprüfen Sie die **Summary** und klicken Sie auf **Create**.

Testen des Exports
..................

Sie stellen zunächst eine CentOS-VM bereit, die Sie als Client für Ihren Dateiexport verwenden können.

.. note:: Wenn Sie die :ref:`linux_tools_vm` bereits als Teil eines anderen Labors bereitgestellt haben, können Sie diese VM stattdessen als NFS-Client verwenden.

#. In **Prism > VM > Table**, klicken Sie auf **+ Create VM**.

#. Füllen Sie die folgenden Felder aus:

   - **Name** - *Initialien*\ -NFS-Client
   - **Description** - CentOS VM zum Testen des NFS-Exports von Dateien
   - **vCPU(s)** - 2
   - **Number of Cores per vCPU** - 1
   - **Memory** - 2 GiB
   - Select **+ Add New Disk**
      - **Operation** - Clone from Image Service
      - **Image** - CentOS
      - Wählen Sie **Add**
   - Wählen Sie **Add New NIC**
      - **VLAN Name** - Secondary
      - Wählen Sie **Add**

#. Klicken Sie auf **Save**.

#. Wählen Sie die *Initialien*\ **-NFS-Client** VM und klicken Sie auf **Power on**.

#. Notieren Sie sich die IP-Adresse der VM in Prism und stellen Sie über SSH eine Verbindung mit den folgenden Anmeldeinformationen her:

   - **Username** - root
   - **Password** - nutanix/4u

#. Führen Sie Folgendes aus:

     .. code-block:: bash

       [root@CentOS ~]# yum install -y nfs-utils #This installs the NFSv4 client
       [root@CentOS ~]# mkdir /filesmnt
       [root@CentOS ~]# mount.nfs4 <Intials>-Files.ntnxlab.local:/ /filesmnt/
       [root@CentOS ~]# df -kh
       Filesystem                      Size  Used Avail Use% Mounted on
       /dev/mapper/centos_centos-root  8.5G  1.7G  6.8G  20% /
       devtmpfs                        1.9G     0  1.9G   0% /dev
       tmpfs                           1.9G     0  1.9G   0% /dev/shm
       tmpfs                           1.9G   17M  1.9G   1% /run
       tmpfs                           1.9G     0  1.9G   0% /sys/fs/cgroup
       /dev/sda1                       494M  141M  353M  29% /boot
       tmpfs                           377M     0  377M   0% /run/user/0
       *intials*-Files.ntnxlab.local:/             1.0T  7.0M  1.0T   1% /afsmnt
       [root@CentOS ~]# ls -l /filesmnt/
       total 1
       drwxrwxrwx. 2 root root 2 Mar  9 18:53 logs

#. Beachten Sie, dass das **logs** - Verzeichnis in ``/filesmnt/logs`` gemounted ist.

#. Starten Sie die VM neu und stellen Sie fest, dass der Export nicht mehr bereitgestellt wird. Um den Mount beizubehalten, fügen Sie ihn zu ``/etc/fstab`` hinzu, indem Sie Folgendes ausführen:

     .. code-block:: bash

       echo 'Intials-Files.ntnxlab.local:/ /filesmnt nfs4' >> /etc/fstab

#. Mit dem folgenden Befehl werden 100 2-MB-Dateien mit zufälligen Daten hinzugefügt ``/filesmnt/logs``:

     .. code-block:: bash

       mkdir /filesmnt/logs/host1
       for i in {1..100}; do dd if=/dev/urandom bs=8k count=256 of=/filesmnt/logs/host1/file$i; done

#. Kehren Sie zu **Prism > File Server > Share > logs** zurück, um die Leistung und Verwendung zu überwachen.

   Beachten Sie, dass die Nutzungsdaten alle 10 Minuten aktualisiert werden.

Multiprotokoll-Freigaben
++++++++++++++++++++++++

Nutanix Files bieten die Möglichkeit, sowohl SMB-Freigaben als auch NFS-Exporte separat bereitzustellen. Jetzt wird jedoch auch die Möglichkeit unterstützt, Multiprotokollzugriff auf dieselbe Freigabe bereitzustellen. In der folgenden Übung konfigurieren Sie Ihre vorhandenen *Initialien*\ **-FiestaShare** so , dass der NFS-Zugriff ermöglicht wird, sodass Entwickler Anwendungsprotokolle an diesen Speicherort umleiten können.

Benutzerzuordnungen konfigurieren
.................................

Eine Nutanix Files-Freigabe hat das Konzept eines nativen und eines nicht nativen Protokolls. Alle Berechtigungen werden mit dem nativen Protokoll angewendet. Alle Zugriffsanforderungen, die das nicht native Protokoll verwenden, erfordern eine Benutzer- oder Gruppenzuordnung zu der von der nativen Seite angewendeten Berechtigung. Es gibt verschiedene Möglichkeiten, Benutzer- und Gruppenzuordnungen anzuwenden, einschließlich regelbasierter, expliziter und Standardzuordnungen. Sie konfigurieren zunächst eine Standardzuordnung.


#. In **Prism Element > File Server**, wählen Sie Ihre Datei - Server und klicken Sie auf **Protocol Management > User Mapping**.

#. Klicken Sie zweimal auf **Next**, um zu dem **Default Mapping**zu gelangen.

#. Von der **Default Mapping** Seite wählen Sie **Deny access to NFS export** und **Deny access to SMB share** als die Standardwerte, sofern keine Zuordnung gefunden wird.

   .. figure:: images/31.png

#. Klicken Sie auf **Next > Save**, um die Standardzuordnung abzuschließen.

#. In **Prism Element > File Server**, wählen Sie Ihren *Initialien*\ **-FiestaShare** nd klicken Sie auf **Update**.

#. Wählen Sie unter **Basics** die Option **Enable multiprotocol access for NFS** aus und klicken Sie auf **Next**.

   .. figure:: images/32.png

#. Wählen Sie unter **Settings > Multiprotocol Access** die Option **Simultaneous access to the same files from both protocols**.

   .. figure:: images/33.png

#. Klicken Sie auf **Next > Save**, um die Aktualisierung der Freigabeeinstellungen abzuschließen.

Testen des Exports
..................

#. Um den NFS-Export zu testen, stellen Sie über SSH eine Verbindung zu Ihrer *Initialien*\ **-LinuxToolsVM** VM her:

   - **User Name** - root
   - **Password** - nutanix/4u

#. Führen Sie die folgenden Befehle aus:

     .. code-block:: bash

       [root@CentOS ~]# yum install -y nfs-utils #This installs the NFSv4 client
       [root@CentOS ~]# mkdir /filesmulti
       [root@CentOS ~]# mount.nfs4 bootcampfs.ntnxlab.local:/<Initials>-FiestaShare /filesmulti
       [root@CentOS ~]# dir /filesmulti
       dir: cannot open directory /filesmulti: Permission denied
       [root@CentOS ~]#

   .. note:: Bei der mount Operation wird zwischen Groß- und Kleinschreibung unterschieden.

Da die Standardzuordnung darin besteht, den Zugriff zu verweigern, wird der Fehler "Permission denied" (Berechtigung verweigert) erwartet. Sie fügen jetzt eine explizite Zuordnung hinzu, um den Zugriff auf den nicht nativen NFS-Protokollbenutzer zu ermöglichen. Wir benötigen die Benutzer-ID (UID), um die explizite Zuordnung zu erstellen.

#. Führen Sie den folgenden Befehl aus und notieren Sie sich die UID:

     .. code-block:: bash

       [root@CentOS ~]# id
       uid=0(root) gid=0(root) groups=0(root)
       [root@CentOS ~]#

#. In **Prism Element > File Server**, wählen Sie Ihre Datei - Server und klicken Sie auf **Protocol Management > User Mapping**.

#. Klicken Sie auf **Next** um zur **Explicit Mapping** zu gelangen.

#. Klicken Sie unter **One-to-onemapping list**, auf **Add manually**.

#. Füllen Sie die folgenden Felder aus:

   - **SMB Name** - NTNXLAB\\devuser01
   - **NFS ID** - UID aus dem vorherigen Schritt (0 wenn root)
   - **User/Group** - User

   .. figure:: images/34.png

#. Klicken Sie unter **Actions** auf **Save**.

#. Klicken Sie auf **Next > Next > Save** um die Aktualisierung Ihrer Zuordnungen abzuschließen.

#. Kehren Sie zu Ihrer SSH-Sitzung *Initialien*\ **-LinuxToolsVM** zurück und versuchen Sie erneut, auf die Freigabe zuzugreifen:

     .. code-block:: bash

       [root@CentOS ~]# dir /filesmulti
       Documents\ -\ Copy  Graphics\ -\ Copy  Pictures\ -\ Copy  Presentations\ -\ Copy  Recordings\ -\ Copy  Technical\ PDFs\ -\ Copy  XYZ-MyFolder
       [root@CentOS ~]#

#. Erstellen Sie in Ihrer SSH-Sitzung eine Textdatei und überprüfen Sie anschließend, ob Sie von Ihrem Windows-Client aus auf die Datei zugreifen können.

Zusammenfassung
+++++++++++++++

Was sind die wichtigsten Dinge, die Sie über **Nutanix Files** wissen sollten?

- Files kann schnell auf vorhandenen Nutanix-Clustern bereitgestellt werden und bietet SMB- und NFS-Speicher für Benutzerfreigaben, Basisverzeichnisse, Abteilungsfreigaben, Anwendungen und andere allgemeine Dateispeicheranforderungen.
- Files ist keine punktuelle Lösung. VM-, Datei-, Block- und Objektspeicher können alle von derselben Plattform mit denselben Verwaltungstools bereitgestellt werden, wodurch Komplexität und Verwaltungssilos reduziert werden.
- Mit der One-Click-Leistungsoptimierung kann Nutanix Files per Scale-up udn Scale-out automatisch angepasst werden werden.
- Mithilfe von File Analytics können Sie besser verstehen, wie Daten von Ihren Organisationen verwendet werden, um Ihre Datenprüfungs-, Datenzugriffsminimierungs- und Compliance-Anforderungen zu erfüllen.

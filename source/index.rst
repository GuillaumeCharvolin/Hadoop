======================================
Hadoop Installation Guide (for Giraph)
======================================

This guide explains how to install Hadoop 2.10.2 on Ubuntu 24.04.1 LTS for development and testing purposes.

Prerequisites
=============

- A user with sudo privileges.

Install Java
============

.. code-block:: bash

    sudo apt update
    sudo apt install openjdk-8-jdk

Download and Install Hadoop
===========================

1. **Download Hadoop**:

   https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-2.10.2/hadoop-2.10.2.tar.gz

2. **Extract and move**:

   .. code-block:: bash

      tar -xzf hadoop-2.10.2.tar.gz
      sudo mv hadoop-2.10.2 /opt/hadoop

3. **Set environment variables**:

   Add the following to your `~/.bashrc`:

   .. code-block:: bash

      export JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
      export HADOOP_HOME="/opt/hadoop"
      export HADOOP_CONF_DIR="/opt/hadoop/etc/hadoop"
      export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

   Reload the `~/.bashrc` file:

   .. code-block:: bash

      source ~/.bashrc

Configure Hadoop
================

These file can be find in /opt/hadoop/etc/hadoop/

1. **core-site.xml**:

   .. code-block:: xml

      <configuration>
         <property>
            <name>fs.defaultFS</name>
            <value>hdfs://localhost:9000</value>
         </property>
      </configuration>

2. **hdfs-site.xml** (Replace `YOUR_USER`):

   .. warning:: Replace YOUR_USER with your username.

   .. code-block:: xml

      <configuration>
         <property>
            <name>dfs.replication</name>
            <value>1</value>
         </property>
         <property>
            <name>dfs.namenode.name.dir</name>
            <value>/home/YOUR_USER/hadoop_data/namenode</value>
         </property>
         <property>
            <name>dfs.datanode.data.dir</name>
            <value>/home/YOUR_USER/hadoop_data/datanode</value>
         </property>
      </configuration>

   Create the directories:

   .. code-block:: bash

      mkdir -p /home/YOUR_USER/hadoop_data/namenode
      mkdir -p /home/YOUR_USER/hadoop_data/datanode

3. **mapred-site.xml**:

   .. code-block:: xml

      <configuration>
         <property>
            <name>mapreduce.framework.name</name>
            <value>yarn</value>
         </property>
      </configuration>

4. **yarn-site.xml**:

   .. code-block:: xml

      <configuration>
         <property>
            <name>yarn.nodemanager.aux-services</name>
            <value>mapreduce_shuffle</value>
         </property>
         <property>
            <name>yarn.resourcemanager.hostname</name>
            <value>localhost</value>
         </property>
      </configuration>

5. **hadoop-env.sh**:

   Set `JAVA_HOME`:

   .. code-block:: bash

      JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
      export JAVA_HOME=${JAVA_HOME}

Configure SSH
=============

.. code-block:: bash

   ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_rsa
   cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
   chmod 600 ~/.ssh/authorized_keys
   sudo systemctl restart ssh

Format Namenode and Start Hadoop
================================

1. **Format the Namenode**:

   .. code-block:: bash

      hdfs namenode -format

2. **Start HDFS and YARN services**:

   .. code-block:: bash

      start-dfs.sh
      start-yarn.sh

3. **Verify**:

   - **HDFS**: http://localhost:9870
   - **YARN**: http://localhost:8088

Uninstallation
==============

To uninstall Hadoop, remove the Hadoop directory:

.. code-block:: bash

   sudo rm -rf /opt/hadoop

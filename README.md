# Hadoop installation Steps :
These are the steps used for installing the hadoop 3.2.1 (Single Node Cluster Setup). These setups are for the purpose of running the hadoop cluster on a single node. All steps are tested and verified with virtual machines (**Ubuntu 20.04 LTS**). 

Hadoop excels when deployed in a fully distributed mode on a large cluster of networked servers. However, if you are new to Hadoop and want to explore basic commands or test applications, you can configure Hadoop on a single node.

This setup, also called pseudo-distributed mode, allows each Hadoop daemon to run as a single Java process. A Hadoop environment is configured by editing a set of configuration files:

 - bashrc
 - hadoop-env.sh
 - core-site.xml
 - hdfs-site.xml
 - mapred-site-xml
 - yarn-site.xml

-----------------------------------------------------------------------------------------------------------------------------
## Installation of Java

The following command will install the openjdk version 1.8.0_312 on your machine.
```bash
sudo apt install openjdk-8-jdk -y
```
-----------------------------------------------------------------------------------------------------------------------------
## Check Java
After the installation of java, we need to check the java version.
```bash
java -version; javac -version
```
-----------------------------------------------------------------------------------------------------------------------------
## Hadoop installation
Install the hadoop from the following link.
```bash
wget https://archive.apache.org/dist/hadoop/common/hadoop-3.2.1/hadoop-3.2.1.tar.gz
```
-----------------------------------------------------------------------------------------------------------------------------
## Extract the hadoop tar file
```bash
tar -xzf hadoop-3.2.1.tar.gz
```
-----------------------------------------------------------------------------------------------------------------------------
## Setup the hadoop environment
Add the following line in .bashrc file. Replace **akilankm** with the your **username**
```bash
# Hadoop Related Options
export HADOOP_HOME=/home/akilankm/hadoop-3.2.1
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```
After adding the above line in .bashrc file, we need to source it.
```bash
source ~/.bashrc
```
-----------------------------------------------------------------------------------------------------------------------------
## Check the java installation location
If you need help to locate the correct Java path, run the following command in your terminal window:
```bash
which javac
```
Use the provided path to find the OpenJDK directory with the following command:
```bash
readlink -f /usr/bin/javac
```
-----------------------------------------------------------------------------------------------------------------------------
## Edit the hadoop-env.sh file
The hadoop-env.sh file serves as a master file to configure YARN, HDFS, MapReduce, and Hadoop-related project settings.

When setting up a single node Hadoop cluster, you need to define which Java implementation is to be utilized. Use the previously created $HADOOP_HOME variable to access the hadoop-env.sh file:
```bash
sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
After navigating to the file, we need to uncomment the following line. if JAVA_HOME is not set, then we need to set it.
```bash
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```
-----------------------------------------------------------------------------------------------------------------------------
## Edit the core-site.xml file
The core-site.xml file defines HDFS and Hadoop core properties.

To set up Hadoop in a pseudo-distributed mode, you need to specify the URL for your NameNode, and the temporary directory Hadoop uses for the map and reduce process.

Open the core-site.xml file in a text editor:
```bash
sudo nano $HADOOP_HOME/etc/hadoop/core-site.xml
```
Open the file and add the following lines to the file. Open the file and add the following lines to the file. Replace **akilankm** with the your **username**.
```bash
<configuration>
<property>
  <name>hadoop.tmp.dir</name>
  <value>/home/akilankm/tmpdata</value>
</property>
<property>
  <name>fs.default.name</name>
  <value>hdfs://127.0.0.1:9000</value>
</property>
</configuration>
```
-----------------------------------------------------------------------------------------------------------------------------
## Edit the hdfs-site.xml file
The properties in the hdfs-site.xml file govern the location for storing node metadata, fsimage file, and edit log file. Configure the file by defining the NameNode and DataNode storage directories.

Additionally, the default dfs.replication value of 3 needs to be changed to 1 to match the single node setup.
```bash
sudo nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
Open the file and add the following lines to the file. Replace **akilankm** with the your **username**.
```bash
<configuration>
<property>
  <name>dfs.data.dir</name>
  <value>/home/akilankm/dfsdata/namenode</value>
</property>
<property>
  <name>dfs.data.dir</name>
  <value>/home/akilankm/dfsdata/datanode</value>
</property>
<property>
  <name>dfs.replication</name>
  <value>1</value>
</property>
</configuration>
```
-----------------------------------------------------------------------------------------------------------------------------
## Edit the mapred-site.xml file
Use the following command to access the mapred-site.xml file and define MapReduce values:
```bash
sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```
Open the file and add the following lines to the file.
```bash
<configuration> 
<property> 
  <name>mapreduce.framework.name</name> 
  <value>yarn</value> 
</property> 
</configuration>
```
-----------------------------------------------------------------------------------------------------------------------------
## Edit the yarn-site.xml file
The yarn-site.xml file is used to define settings relevant to YARN. It contains configurations for the Node Manager, Resource Manager, Containers, and Application Master.
```bash
sudo nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```
Open the file and add the following lines to the file.
```bash
<configuration>
<property>
  <name>yarn.nodemanager.aux-services</name>
  <value>mapreduce_shuffle</value>
</property>
<property>
  <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
  <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
<property>
  <name>yarn.resourcemanager.hostname</name>
  <value>127.0.0.1</value>
</property>
<property>
  <name>yarn.acl.enable</name>
  <value>0</value>
</property>
<property>
  <name>yarn.nodemanager.env-whitelist</name>   
  <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PERPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
</property>
</configuration>
```
-----------------------------------------------------------------------------------------------------------------------------
## Final steps for hadoop installation
It is important to format the NameNode before starting Hadoop services for the first time:
```bash
hdfs namenode -format
```
Start the NameNode and DataNode service:
```bash
./start-dfs.sh
```
Once the namenode, datanodes, and secondary namenode are up and running, start the YARN resource and nodemanagers by typing:
Start the ResourceManager service:
```bash
./start-yarn.sh
```
Type this simple command to check if all the daemons are active and running as Java processes:
```bash
jps
```
If everything is working as intended, the resulting list of running Java processes contains all the HDFS and YARN daemons.

Congratulations!😁 You have successfully installed Hadoop and setup the environment. You can now start using Hadoop.

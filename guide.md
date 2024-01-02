### Installation & Setup Guide for hadoop 2.10.2 & hive 2.3.9

Start by updating and installing required utilities and downloading java sdk and openssh server.

```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install openssh-server
sudo apt-get install openjdk-8-jdk
```

Optional: To allow hadoop to login with user password we can use login via ssh key.

```bash
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
$ chmod 0600 ~/.ssh/authorized_keys
```

Run ssh services:

```bash
sudo service ssh start
start-all.sh
```

Download & Installing Hadoop 2.10.2

```
wget https://dlcdn.apache.org/hadoop/common/hadoop-2.10.2/hadoop-2.10.2.tar.gz
```

Assuming the downloaded file is in home directory, we extract and rename it the directory to hadoop

```bash
tar -xzvf hadoop-2.10.2.tar.gz
mv hadoop-2.10.2.tar.gz /home/siam/hadoop
```

Here `siam` is the logged in username.
Check the location for java home

```bash
readlink -f /usr/bin/java | sed "s:bin/java::"
```

In the .bashrc file add the following after running `sudo ~/.bashrc`.

```bash
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HADOOP_HOME=/home/siam/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```

Update `JAVA_HOME` in the `hadoop-env.sh` file.

```
sudo nano ~/hadoop/etc/hadoop/hadoop-env.sh
```

Add the address for java directory that you got earlier by running the `readlink -f /usr/bin/java | sed "s:bin/java::"` command similar to this:

```
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre/
```

Try running the hadoop command

```
hadoop
```

> Note: If you didn't updated the hadoop `$PATH` in .bashrc file your can also enter the complete path like this: `~/hadoop/bin/hadoop`

Update the following files in the `~/hadoop/etc/hadoop` directory.

hdfs-site.xml:

```xml
<configuration>
	<property>
		<name>dfs.namenode.name.dir</name>
		<value>/home/siam/hadoop/data/nameNode</value>
	</property>
	<property>
		<name>dfs.datanode.data.dir</name>
		<value>/home/siam/hadoop/data/dataNode</value>
	</property>
	<property>
		<name>dfs.replication</name>
		<value>1</value>
	</property>
</configuration>
```

mapred-site.xml:

```xml
<configuration>
<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>
<property>
<name>yarn.app.mapreduce.am.resource.mb</name>
<value>512</value>
</property>
<property>
<name>mapreduce.map.memory.mb</name>
<value>256</value>
</property>
<property>
<name>mapreduce.reduce.memory.mb</name>
<value>256</value>
</property>
</configuration>
```

core-site.xml:

```xml
<configuration>
<property>
<name>fs.default.name</name>
<value>hdfs://localhost:9000</value>
</property>
</configuration>
```

yarn-site.xml file:

```xml
<configuration>
<property>
<name>yarn.acl.enable</name>
<value>0</value>
</property>
<property>
<name>yarn.resourcemanager.hostname</name>
<value>localhost</value>
</property>
<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
<property>
<name>yarn.nodemanager.resource.memory-mb</name>
<value>1536</value>
</property>
<property>
<name>yarn.scheduler.maximum-allocation-mb</name>
<value>1536</value>
</property>
<property>
<name>yarn.scheduler.minimum-allocation-mb</name>
<value>128</value>
</property>
<property>
<name>yarn.nodemanager.vmem-check-enabled</name>
<value>false</value>
</property>
</configuration>
```

**Formatting HDFS:** check if the code working:

```bash
hdfs namenode -format
```

If it is working, lets start all the services to start hadoop & yarn:

```
start-all.sh
```

Or we can start separately:

```bash
start-dfs.sh
start-yarn.sh
```

To stop hadoop services we can just use `stop-all.sh` or `stop-dfs.sh` and `stop-yarn.sh`

Now we can go to `localhost:50070` to check if it is running or not. The port 50090 is for secondary namenode.

TO see all the running hadoop services we can just use the following command:

```bash
jps
```

Now we can get access to hadoop file system and we can run different queries like the following:

```bash
hadoop fs -mkdir -p /user/hdfs
hadoop fs -ls /user
hdfs dfs -put sample.txt /user/hdfs/sample.txt
```

### Installing hive:

Download & install hive:

```
wget https://dlcdn.apache.org/hive/hive-2.3.9/apache-hive-2.3.9-bin.tar.gz
tar -xzvf apache-hive-2.3.9-bin.tar.gz
mv apache-hive-2.3.9-bin.tar.gz ~/hive
```

In ~/.bashrc file update the Path for hive:

```
# Hive home
export PATH=$PATH:/home/siam/hive/bin
```

In the end of the `hive-config.sh` file inside ~/hive/bin directory add the HADOOP_HOME directory:

```bash
export HADOOOP_HOME=/home/siam/hadoop
```

In `hive/bin`` directory run the schematoo to start the database:

```bash
schematool -initSchema -dbType derby
```

Run `hive` to start hive:

We can run different queryies in `hive`. Below are some examples:

```sql
create database wqd7007;
show databases;
exit;
```

Importing csv file from hadoop:

```bash
 hadoop fs -mkdir -p /user/hive/warehouse
 hdfs dfs -put Batting.csv /user/hive/warehouse/
```

In hive type:

```sql
CREATE EXTERNAL TABLE IF NOT EXISTS batting(
    > playerID STRING, yearID int, sint int, teamID STRING, lgID STRING, G int, G_batting int)
    > COMMENT 'Batting stats'
    > ROW FORMAT DELIMITED
    > FIELDS TERMINATED BY ','
    > STORED AS TEXTFILE
    > LOCATION '/user/hive/warehouse';

```

Some queryies in hive:

```
SELECT yearid, max(r) FROM batting GROUP BY yearid;
```

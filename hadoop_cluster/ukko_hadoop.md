####**How-to: Setup a multi-node Hadoop cluster on Ukko**
**Prerequisites**
 1. Before you start, please read Ukko instructions carefully: https://www.cs.helsinki.fi/en/compfac/highperformance-cluster-ukko. You need to gain access to the Ukko cluster if you are not in the University network. 
 2. The most convenient way is by using HY-VPN (Use OpenVPN, not Pulse Secure):https://helpdesk.it.helsinki.fi/en/search?keys=HY-VPN
 3. In order to transfer files between your computer and Ukko, you also need to have a SSH software (e.g. Putty for Windows) and a SFTP client (WinSCP for Windows, Cyberduck for OS X, FileZilla for Linux).

**Choose your nodes**
1. You should choose serveal nodes from the list (https://www.cs.helsinki.fi/ukko/hpc-report.txt). Note that the node marked with “Reserved” is not available. Please also note the “load” factor.
2. Choose one of the chosen nodes be the namenode. All other nodes will become datanodes. Here we choose ukko026 be the namenode, ukko027, ukko028 and ukko029 be the datanodes.

####**Setup Hadoop**

**PART A: On your namenode**
1. Use your SSH software to login to your namenode. Navigate to /cs/work/scratch/ folder. Make you own working directory here (NB: in case you don't have folder under /cs/work/home/).
2. Download and extract Hadoop 2.7.3 binary distribution to your working directory. Change your current directory to your_working_dir/hadoop-2.7.3/:
	 
    $ wget http://mirror.netinch.com/pub/apache/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz
  	$ tar xvf hadoop-2.7.3.tar.gz
  	$ cd hadoop-2.7.3
 3. Add $JAVA_HOME environment variable. Add the following three lines to the beginning of etc/hadoop/hadoop-env.sh:
 
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
    export PATH=${JAVA_HOME}/bin:${PATH}
    export HADOOP_CLASSPATH=${JAVA_HOME}/lib/tools.jar
 4. Modify etc/hadoop/core-site.xml, put the following lines between < configuration > and < /configuration> tag:
	
    <property>
    	<name>fs.default.name</name>
    	<value>hdfs://ukko026.hpc.cs.helsinki.fi:9000</value>
    </property>
 You need to change the underlined hostname matches your choice of namenode. The port number 9000 is usually not necessarily being changed, unless it is occupied by someone else.
 5. Modify etc/hadoop/mapred-site.xml, put the following lines between < configuration > and < /configuration >:
	
    <property>
    	<name>mapreduce.framework.name</name>
    	<value>yarn</value>
    </property>
 6.  Modify etc/hadoop/yarn-site.xml, put the following lines between < configuration> and < /configuration>:
	
      <property>
    	      <name>yarn.resourcemanager.hostname</name>
    	      <value>ukko026.hpc.cs.helsinki.fi</value>
    	  </property>
    	  <property>
    	      <name>yarn.nodemanager.aux-services</name>
    	      <value>mapreduce_shuffle</value>
    	  </property>
    	  <property>
    	<name>yarn.nodemanager.aux-services.mapreduce_shuffle.class</name>
    	      <value>org.apache.hadoop.mapred.ShuffleHandler</value>
    </property>
   
   You need to change the underlined hostname matches your choice of namenode.
  7. Format the namenode by using ./bin/hadoop namenode –format command. This is only required once, so you don’t need to format it every time you run Hadoop.
  8. Start Hadoop on the namenode by using 

    ./sbin/hadoop-daemon.sh start namenode
9. Check the log logs/hadoop-your_username-namenode-ukko026.log to see if the namenode is up. The last two lines should be
	
    Starting services required for active state
    Starting CacheReplicationMonitor with interval 30000 milliseconds
   10.  Open your web browser and navigate to http://ukko026.hpc.cs.helsinki.fi:50070/ (replace ukko026 with the name of your namenode, you can not open this unless you using office wire connection. You can check the log instead).
   11. Start YARN Resource Manager on the namenode by using 
	
    ./sbin/yarn-daemon.sh start resourcemanager.
  12. Check the log logs/hadoop-your_username-resourcemanager-ukko026.log to see if the namenode is up. The last line should be
	
    IPC Server listener on 8033: starting
13. Open your web browser and navigate to http://ukko026.hpc.cs.helsinki.fi:8088 (replace ukko026 with the name of your namenode).


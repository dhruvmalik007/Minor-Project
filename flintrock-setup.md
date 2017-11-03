
# Install Flintrock Locally MacOSX =======================================

## on local
```bash
pip3 install flintrock

flintrock --help

# Example commands
# flintrock login test-cluster
# flintrock describe test-cluster
# flintrock add-slaves test-cluster --num-slaves 2
# flintrock remove-slaves test-cluster --num-slaves 1
# flintrock run-command test-cluster 'sudo yum install -y package'
# flintrock copy-file test-cluster /local/path /remote/path
```
# Flintrock Usage =======================================
- A command-line tool for launching Apache Spark clusters.

## Options:
-   --config TEXT     Path to a Flintrock configuration file.
-   --provider [ec2]
-   --version         Show the version and exit.
-   --help            Show this message and exit.

## Commands:
-   add-slaves     Add slaves to an existing cluster.
-   configure      Configure Flintrock's defaults.
-   copy-file      Copy a local file up to a cluster.
-   describe       Describe an existing cluster.
-   destroy        Destroy a cluster.
-   launch         Launch a new cluster.
-   login          Login to the master of an existing cluster.
-   remove-slaves  Remove slaves from an existing cluster.
-   run-command    Run a shell command on a cluster.
-   start          Start an existing, stopped cluster.
-   stop           Stop an existing, running cluster

# To setup and edit the default config file =======================================
```bash
flintrock configure
```
## Make it look like this:
**Current Versions:**
- Spark: 2.1.1
- hdfs: 2.7.3

```
services:
  spark:
    version: 2.1.1
    # git-commit: latest  # if not 'latest', provide a full commit SHA; e.g. d6dc12ef0146ae409834c78737c116050961f350
    # git-repository:  # optional; defaults to https://github.com/apache/spark
    # optional; defaults to download from from the official Spark S3 bucket
    #   - must contain a {v} template corresponding to the version
    #   - Spark must be pre-built
    #   - must be a tar.gz file
    download-source: "http://d3kbcqa49mib13.cloudfront.net/spark-2.1.1-bin-hadoop2.4.tgz"
  hdfs:
    version: 2.7.3
    # optional; defaults to download from a dynamically selected Apache mirror
    #   - must contain a {v} template corresponding to the version
    #   - must be a .tar.gz file
    # download-source: "https://www.example.com/files/hadoop/{v}/hadoop-{v}.tar.gz"

provider: ec2

providers:
  ec2:
    key-name: aws_sf17ds6
    identity-file: /Users/eastblue/.ssh/aws_sf17ds6.pem
    instance-type: m3.xlarge
    region: us-west-2
    # availability-zone: <name>
    ami: ami-12382e6b
        # use HVM (M3 or C3 units): http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/virtualization_types.html
        # This image is the overkill nodes: 15gb of worker memory, 4 cores
        # Amazon Linux, us-west-2b
        # 8GB image size (hard disk)
        # 
    user: ec2-user
    # ami: ami-61bbf104   # CentOS 7, us-east-1
    # user: centos
    # spot-price: <price>
    # vpc-id: <id>
    # subnet-id: <id>
    # placement-group: <name>
    security-groups:
      - Neo4j Graph Database - Community Edition-3.2.0-AutogenByAWSMP-
    #   - group-name2
    # instance-profile-name:
    tenancy: default  # default | dedicated
    ebs-optimized: no  # yes | no
    instance-initiated-shutdown-behavior: terminate  # terminate | stop
    # user-data: /path/to/userdata/script

launch:
  num-slaves: 2
  # install-hdfs: True
  # install-spark: False
```

# STARTED WITH 2 NODES
```bash
flintrock launch test-cluster \
    --num-slaves 1 \
    --spark-version 2.1.1 \
    --ec2-key-name aws_sf17ds6 \
    --ec2-identity-file /Users/eastblue/.ssh/aws_sf17ds6.pem \
    --ec2-ami ami-6df1e514 \
    --ec2-user ec2-user
    
     #    Launching 2 instances...
	# [34.212.176.194] SSH online.
	# [34.212.228.203] SSH online.
	# [34.212.176.194] Configuring ephemeral storage...
	# [34.212.228.203] Configuring ephemeral storage...
	# [34.212.176.194] Installing Java 1.8...
	# [34.212.228.203] Installing Java 1.8...
	# [34.212.228.203] Installing Spark...
	# [34.212.176.194] Installing Spark...
	# [34.212.176.194] Configuring Spark master...
	# Spark Health Report:
	#   * Master: ALIVE
	#   * Workers: 1
	#   * Cores: 1
	#   * Memory: 2.7 GB            
	# launch finished in 0:02:08. #    Launching 2 instances...
	# [34.212.176.194] SSH online.
	# [34.212.228.203] SSH online.
	# [34.212.176.194] Configuring ephemeral storage...
	# [34.212.228.203] Configuring ephemeral storage...
	# [34.212.176.194] Installing Java 1.8...
	# [34.212.228.203] Installing Java 1.8...
	# [34.212.228.203] Installing Spark...
	# [34.212.176.194] Installing Spark...
	# [34.212.176.194] Configuring Spark master...
	# Spark Health Report:
	#   * Master: ALIVE
	#   * Workers: 1
	#   * Cores: 1
	#   * Memory: 2.7 GB            
	# launch finished in 0:02:08.
```

# In AWS EC2 instance =======================================
## remember to tweak the IP address of your master node everytime you restart the instance

# ssh into master
```bash
ssh -i "aws_sf17ds6.pem" ec2-user@ec2-54-213-254-250.us-west-2.compute.amazonaws.com
```
# install tmux =======================================
```bash
sudo yum update

sudo yum install tmux
```
# tmux --> jupyter notebook

# install conda =======================================
```bash
wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh

bash Anaconda3-4.4.0-Linux-x86_64.sh 
```
# PySpark Setup =======================================
```bash
vim ~/.bashrc
```
Insert these snippets:
```bash
# Amazon Keys (Substitute your credentials here)
export AWS_ACCESS_KEY_ID=XXXXXXXXXXXXXX
export AWS_SECRET_ACCESS_KEY=XXXXXXXXXXXXXXXXX

# User specific aliases and functions
#Note that your py4j Version may vary!
export PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/py4j-0.10.1-src.zip:$PYTHONPATH

# added by Anaconda2 4.2.0 installer
export PATH='/home/user/path/to/anaconda3/bin:$PATH'
```
Save and quit vim.
```bash
source ~/.bashrc

vim jupyter_setup.sh
```
changes every time you restart the instance

```bash
export spark_master_hostname=ec2-54-213-254-250.us-west-2.compute.amazonaws.com

# use ec2-34-212-176-194.us-west-2.compute.amazonaws.com:/8080 to view memory usage
# Tweak depending on the nodes used: my overkill nodes have 15gb of worker memory, 4 cores
export memory=14000M 
export cores=4

PYSPARK_DRIVER_PYTHON=jupyter PYSPARK_DRIVER_PYTHON_OPTS="notebook --no-browser --port=7777" pyspark --master spark://$spark_master_hostname:7077 --packages graphframes:graphframes:0.5.0-spark2.1-s_2.11 --executor-memory $memory --driver-memory $memory
```

Open a detached tmux window:

```bash
source jupyter_setup.sh
```
Leave it running

# PySpark Access from Local =======================================

# Then, to access the notebook on your browser, port forward on your local computer:
```bash
cd .ssh/

ssh -i "aws_sf17ds6.pem" -NfL localhost:7776:localhost:7777 ec2-user@ec2-54-187-52-226.us-west-2.compute.amazonaws.com
```
- The default user in Flintrock is 'ec2-user' so that will work most of the time.
- I have found that ubuntu instances will require ubuntu@SparkMasterPublicDNS instead.
- Now, go to your browser and type in [localhost:7776](localhost:7776).
  - You should see the jupyter interface displaying the contents of the directory of your Spark Master in which you ran jupyter_setup.sh.

# SCP anything you need into the instance
```bash
scp -i "aws_sf17ds6.pem" /Users/eastblue/ds/metis/challenges/Proj_Kojak/btc/spark-play.ipynb ec2-user@ec2-54-187-52-226.us-west-2.compute.amazonaws.com:/home/ec2-user
```

# Jupyter 2.7 conda environmentfor spark notebook =======================================
```bash
conda create -n py27 python=2.7

which python

conda install nb_conda

source activate py27

source jupyter_setup.sh
```

# install dependencies on the cluster ==================================
```bash
flintrock run-command test-cluster 'sudo yum install -y gcc'
```

# have faith...flintrock takes awhile (3-5 min)
```bash
flintrock run-command test-cluster 'pip install --user numpy' 
```

# added slaves ==================================
flintrock add-slaves \
	--num-slaves 6 test-cluster

# get file on cluster ==================================
flintrock copy-file test-cluster /Users/eastblue/ds/metis/challenges/Proj_Kojak/btc/tmp_txns.txt ec2-user@ec2-54-187-52-226.us-west-2.compute.amazonaws.com:/home/ec2-user

# upload data files to s3 management console
# access data from s3 after making files public:
```bash
flintrock run-command test-cluster 'wget https://s3-us-west-2.amazonaws.com/YourDir/YourFiles'
```

# pickled out the results_df and notebook, made a local conda env ==================================
conda create -q --name py27 python=2.7 \
jupyter \
ipywidgets \
jupyter_contrib_nbextensions \
pyparsing \
matplotlib \
mkl \
mpld3 \
seaborn \
pip \
pandas \
scikit-learn \
scipy \
numpy \
statsmodels \
tqdm\

# open anaconda and launch jupyter notebook to do model evaluation locally

# ASIDE: always make sure you start/stop the cluster as a unit =================
```bash
flintrock stop test-cluster
test-cluster:
  # state: running
  # node-count: 9
  # master: ec2-54-213-254-250.us-west-2.compute.amazonaws.com
  # slaves:
  #   - ec2-54-187-17-103.us-west-2.compute.amazonaws.com
  #   - ec2-54-202-211-73.us-west-2.compute.amazonaws.com
  #   - ec2-34-210-75-186.us-west-2.compute.amazonaws.com
  #   - ec2-54-186-74-30.us-west-2.compute.amazonaws.com
  #   - ec2-54-244-135-43.us-west-2.compute.amazonaws.com
  #   - ec2-54-187-67-21.us-west-2.compute.amazonaws.com
  #   - ec2-54-187-16-211.us-west-2.compute.amazonaws.com
  #   - ec2-34-210-78-111.us-west-2.compute.amazonaws.com

# can take 2-5 min depending on number of clusters
flintrock start test-cluster 
```

# menu_python
import os
import getpass
import subprocess
os.system("tput setaf 6")
print("\t\t\t Hello Everyone")
os.system("tput setaf 3")
print("\t\t\t ----------------------WELCOME TO MY MENU--------------------")
os.system("tput setaf 5")
print("======================================================")
passwd= getpass.getpass("Enter your password...")
if passwd!="admin":
    print("Your entered password is incorrect. You are not an authorized person")
    exit()
r=input("Where you want to run this menu? (local/remote) : ")
print(r)
if r== 'remote':
    ip= input("Enter your Remote system IP - ")
os.system("clear")
def MainMenu():
    os.system("tput setaf 6")
    print("""
        \n
      |-----------------------------------------------|----------------------------------------------------|
      | Press 1 : to run DATE command.                |           Press 17 : Login into Docker Hub.        |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 2 : to show Calendar                    |           Press 18 : Pull Docker Image             |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 3 : Services to want to start.          |           Press 19 : Launch Docker Conatiner.      |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 4 : Services to want to stop.           |           Press 20 : Remove all stopped conatiners.|
      |-----------------------------------------------|----------------------------------------------------| 
      | Press 5 : Add user.                           |           Press 21 : Delete any Image.             |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 6 : Create a file.                      |           Press 22 : Configure AWS linux and login.|
      |-----------------------------------------------|----------------------------------------------------|
      | Press 7 : Create a directory.                 |           Press 23 : Create a key-pair.            |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 8 : Configure WebServer.                |           Press 24 : Create security-group.        |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 9 : Create LVM Partition.               |           Press 25 :  Add Inbound Rule.            |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 10 : Configure system as Hadoop NameNode|           Press 26 : Launch Instance.              |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 11 : Configure system as Hadoop DataNode|           Press 27 : Describe all Instances.       |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 12 : Static Partition.                  |           Press 28 : Create EBS Volume.            |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 13 : Start Python3.                     |           Press 29 : Attach EBS to instance.       |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 14 : Start Docker.                      |           Press 30 : Create S3 bucket.             |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 15 : Stop Docker.                       |           Press 31 : Upload objects on S3 bucket.  |
      |-----------------------------------------------|----------------------------------------------------|
      | Press 16 : Restart Docker services.           |           Press 32 : Exit this Menu.               |
      |-----------------------------------------------|----------------------------------------------------|



             """)
def lvm(pvname,pvname2,vgname,lvsize,lvname,filesystem,mountlvm):
    os.system("df -h")
    os.system("fdisk -l")
    os.system("pvcreate {}".format(pvname))
    os.system("pvcreate {}".format(pvname2))
    os.system("pvdisplay {}".format(pvname))
    os.system("pvdisplay {}".format(pvname2))
    os.system("vgcreate {} {} {}".format(vgname,pvname,pvname2))
    os.system("vgdisplay {}".format(vgname))
    os.system("lvcreate --size {} --name {} {}".format(lvsize,lvname,vgname))
    #os.system("lvs")
    #os.system("lsblk")
    os.system("mkfs.{} /dev/{}/{}".format(filesystem,vgname,lvname))
    os.system("mount /dev/{}/{} {}".format(vgname,lvname,mountlvm))
    os.system("df -h")
    #os.system("lsblk")
    return
def lvmremote(pvname,pvname2,vgname,lvsize,lvname,filesystem,mountlvm):
    os.system("ssh {} df -h".format(ip))
    os.system("ssh {} fdisk -l".format(ip))
    os.system("ssh {} pvcreate {}".format(ip,pvname))
    os.system("ssh {} pvcreate {}".format(ip,pvname2))
    os.system("ssh {} vgcreate {} {} {}".format(ip,vgname,pvname,pvname2))
    os.system("ssh {} vgs".format(ip))
    os.system("ssh {} lvcreate --size {} --name {} {}".format(ip,lvsize,lvname,vgname))
    os.system("ssh {} lvs".format(ip))
    os.system("ssh {} lsblk".format(ip))
    os.system("ssh {} mkfs.{} /dev/{}/{}".format(ip,filesystem,vgname,lvname))
    os.system("ssh {} mount /dev/{}/{} {}".format(ip,vgname,lvname,mountlvm))
    os.system("ssh {} df -h".format(ip))
    os.system("ssh {} lsblk".format(ip))
    return
def jdk():
    a = os.system("java -version")
    if a!=0:
        print("JDK is not installed.")
        return 0
    else :
        return 1
def hadoop():
    a = os.system("hadoop version")
    if a != 0:
        print("hadoop is not installed.")
        return 0
    else:
        return 1
def hdfsfile():
    filename = "/etc/hadoop/hdfs-site.xml"
    a=open(filename,'r')
    read_line = list(a.readlines())
    offset = len(read_line) - 1
    if read_line[offset-1]=="</property>\n":
        print("hdfs is configured....\n")
        return
    foldername = input("Enter folder for namenode: ")
    s="<property>\n<name>dfs.name.dir</name>\n<value>{}</value>\n</property>\n".format(foldername)
    read_line.insert(offset,s)
    print(read_line)
    a.close()
    a = open(filename,"w+")
    for i in range(len(read_line)):
        a.write(read_line[i])
    a.close()
def corefile():
    filename="/etc/hadoop/core-site.xml"
    a=open(filename,'r')
    read_line=list(a.readlines())
    offset=len(read_line)-1
    if read_line[offset-1]=="</property>\n":
        print("Core-site.xml file is already configured.")
        return
    s = "<property>\n<name>fs.default.name</name>\n<value>hdfs://0.0.0.0:9001</value>\n</property>\n"
    read_line.insert(offset,s)
    print(read_line)
    a.close()
    a = open(filename,'w+')
    for i in range(len(read_line)):
        a.write(read_line[i])
    a.close()
def namenodestatus():
    a=subprocess.Popen("jps",shell=True,stdout=subprocess.PIPE)
    output=a.stdout.read()
    if 'NameNode' in str(output):
        return 1
    else:
        return 0

def datanodestatus():
    a=subprocess.Popen("jps",shell=True,stdout=subprocess.PIPE)
    output=a.stdout.read()
    if 'DataNode' in str(output):
        return 1
    else:
        return 0

while True:
    if r=='local':
        MainMenu()
        os.system("tput setaf 3")
        ch=int(input("Enter your Choice : "))
        if ch ==1:
            os.system("date")
        if ch ==2:
            os.system("cal")
        if ch==3:
            start=input("Name the service you want to Start :")
            os.system("systemctl start {}".format(start))
        if ch==4:
            stop=input("Name the service you want to stop :")
            os.system("systemctl stop {}".format(stop))
        if ch==5:
            username= input("Enter the UserName: ")
            os.system("useradd {}".format(username))
        if ch==6:
            filename = input("Enter the Name of your file :")
            os.system("touch {}".format(filename))
        if ch==7:
            dirname = input("Enter the name of your Directory :")
            os.system("mkdir {}".format(dirname))
        if ch==8:
            os.system("yum install httpd")
            os.system("systemctl start httpd")
            os.system("systemctl enable httpd")
            os.system("systemctl status httpd")
        if ch==9:
            pvname=input("Enter your physical Volume name : (like- /dev/sdc) : ")
            pvname2=input("Enter your physical volume name 2 : (like:- /dev/sdb) : ")
            vgname= input("Enter your Volume Group Name : ")
            lvsize=input("Enter the Logical Volume size (eg- 10M,10G) : ")
            lvname=input("Enter the Logical Volume Name ")
            filesystem=input("Which File System you want to use (eg:- ext4, ext3, xfs) :")
            mountlvm=input("Enter the path of Existing directory to mount your logical volume ")
            lvm(pvname,pvname2,vgname,lvsize,lvname,filesystem,mountlvm)
        if ch==10:
            if jdk:
                if hadoop:
                    hdfsfile()
                    corefile()
                    if not namenodestatus():
                        print("Starting the NameNode...please wait...")
                        os.system("hadoop namenode -format -force")
                        os.system("hadoop-daemon.sh start namenode")
                    else:
                        print("Services are already running...")

        if ch==11:
            if jdk:
                if hadoop:
                    hdfsfile()
                    corefile()
                    if not datanodestatus():
                        print("Starting DataNode....please wait....")
                        os.system("hadoop-daemon.sh start datanode")
                    else:

                        print("Datanode is running already")

        if ch==12:
            a=int(input("Press 1 : for create static partition || Press 2 : for remove any partition: "))
            os.system("fdisk -l")
            if int(a)==1:
                diskname= input("Enter the DiskName:  ")
                os.system("fdisk -s /dev/{}".format(diskname))
                partitionsize=input("Enter the size of Partition: ")
                filename=input("Enter the existing filename where you want to mount the hard disk: ")
                os.system("fdisk /dev/{}".format(diskname))
                os.system("echo n | fdisk /dev/{}".format(diskname))
                os.system("echo p | fdisk /dev/{}".format(diskname))
                os.system("echo \n | fdisk /dev/{}".format(diskname))
                os.system("echo +{}G | fdisk /dev/{}".format(partitionsize,diskname))
                os.system("echo w | fdisk /dev/{}".format(diskname))
                os.system("echo q | fdisk /dev/{}".format(diskname))
                os.system("mkfs.ext4 /dev/{}".format(diskname))
                os.system("mkdir /{}".format(filename))
                os.system("mount /dev/{} /{}".format(diskname,filename))
            elif int(a)==2:
                diskname=input("Enter the Diskname: ")
                os.system("fdisk /dev/{}".format(diskname))
                os.system("echo d | fdisk /dev/{}".format(diskname))
                os.system("echo w | fdisk /dev/{}".format(diskname))
                os.system("echo q | fdisk /dev/{}".format(diskname))

        if ch==13:
            os.system("python3")
        if ch==14:
            os.system("systemctl start docker")

        if ch==15:
            os.system("systemctl stop docker")

        if ch==16:
            os.system("systemctl restart docker")

        if ch==17:
            username=input("Enter your Docker Hub username: ")
            password = getpass.getpass("Enter your Password : ")
            os.system("docker login --username {} --password {}".format(username,password))

        if ch==18:
            imagename=input("Enter Image Name and Version you want to pull:")
            os.system("docker pull {}".format(imagename))

        if ch==19:
            cont_name=input("Enter the name of the Container:  ")
            doc_image=input("Enter the docker image through which you want to launch Container: ")
            os.system("docker run -it --name {} {}".format(cont_name,doc_image))

        if ch==20:
            name=input("Enter the name of the container you want to stop: ")
            os.system("docker rm -v {}".format(name))

        if ch==21:
            img=input("Enter the Image name with version which you want to Remove:  ")
            os.system("docker rmi -f {}".format(img))

       if ch==22:
           # os.system("curl ""https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip"" -o ""awscliv2.zip""")
            #os.system("unzip awscliv2.zip")
            #os.system("sudo ./aws/install")
            print("Enter your Access key and Secret Key :")
            os.system("aws configure")


        if ch==23:
            keyname=input("Enter the Key name: ")
            os.system("aws ec2 create-key-pair --key-name {}".format(keyname))

        if ch==24:
            sgname= input("Enter the Security group name: ")
            vpcid = input("Enter the VPC id: ")
            des=input("Enter description in quotes: ")
            os.system("aws ec2 create-security-group --group-name {} --description {} --vpc-id {}".format(sgname,des,vpcid))

        if ch==25:
            sgid = input("Enter the Security group ID:  ")
            protocol = input("Enter the name of protocol or enter all to entertain all : ")
            cidr=input("Enter the CIDR (eg:- 0.0.0.0/0) : ")
            if protocol=="all":
                os.system("aws ec2 authorize-security-group-ingress --group-id {} --protocol all --cidr {}".format(sgid,cidr))

            else:
                port =input("Enter the Port number : ")
                os.system("aws ec2 authorize-security-group-ingress --group-id {} --protocol {} --port {} --cidr {}".format(sgid,protocol,port,cidr))

        if ch==26:
            ami=input("Enter the AMI ID : ")
            insttype = input("Give the Instance Type :")
            no=input("How many instance you want to launch : ")
            subnet=input("Give the Subnet ID :")
            key=input("Enter Key-pair :")
            sg=input("Enter the Security group id :")
            os.system("aws ec2 run-instances --image-id {} --instance-type {} --count {} --subnet-id {} --key-name {} --security-group-ids {}".format(ami,insttype,no,subnet,key,sg))

        if ch==27:
            os.system("aws ec2 describe-instances")

        if ch==28:
            typevol=input("Enter the Volume type :")
            size=input("Enter the size of your Volume: ")
            zone = input("Enter the Availability Zone :")
            os.system("aws ec2 create-volume --volume-type {} --size {} --availability-zone {}".format(typevol,size,zone))

        if ch==29 :
            volid=input("Enter the Volume ID :")
            instid = input("Enter the Instance ID :")
            devname=input("Enter the Device Name : (for eg- /dev/sdc) : ")
            os.system("aws ec2 attach-volume --volume-id {} --instance-id {} --device {}".format(volid,instid,devname))

        if ch==30 :
            bucket=input("Enter the Bucket Name : ")
            regionid = input("Enter the Region : (for eg:- us-east-1):")
           os.system("aws s3api create-bucket --bucket {} --region {} --create-bucket-configuration LocationConstraint={}".format(bucket,regionid,regionid))

        if ch==31 :
            sname=input("Enter the Object local path :")
            dname=input("Enter the Bucket Path :")
            os.system("aws s3 cp {} {}".format(sname,dname))

        if ch==32 :
            exit()

       # else :
           # print("you have entered wrong option")

        input("Press Enter to continue ")
        os.system("clear")
    elif r=="remote":
        MainMenu()
        os.system("tput setaf 4")
        ch = int(input("Enter your Choice "))
        if ch==1:
            os.system("date")

        if ch==2:
            os.system("cal")

        if ch==3 :
            start=input("Name the Service you want to Start :")
            os.system("ssh {} systemctl start {}".format(ip,start))

        if ch==4:
            stop=input("Name the Service you want to stop :")
            os.system("ssh {} systemctl stop {}".format(ip.stop))

        if ch==5:
            username=input("Give your User a pretty Name : ")
            os.system("ssh {} useradd {}".format(ip,username))

        if ch==6:
            filename=input("Enter your filename :")
            os.system("ssh {} touch {}".format(ip,filename))

        if ch==7:
            dirname=input("Enter a name for your Directory :")
            os.system("ssh {} mkdir {}".format(ip,dirname))

        if ch==8:
            os.system("ssh {} yum install httpd".format(ip))
            os.system("ssh {} systemctl start httpd".format(ip))
            os.system("ssh {} systemctl enable httpd".format(ip))
            os.system("ssh {} systemctl enable httpd".format(ip))

        if ch==9:
            pvname=input("Enter the Name of physical volume device :(for eg:- /dev/sbc) : ")
            pvname2=input("Enter the name of physical volume 2 :(for eg- /dev/sdb) : ")
            vgname=input("Enter the Volume Group Name : ")
            lvsize = input("Enter the size of Logical Volume :")
            lvname = input("Enter the name for Logical Volume: ")
            filesystem=input("Enter the File system :")
            mountlvm=input("Enter the Existing directory to mount the LVM :")
            lvmremote(pvname,pvname2,vgname,lvsize,lvname,filesystem,mountlvm)
        if ch==10:
            print("Setting it up ..please wait ")

        if ch==11 :
            print("Setting it up.. please wait")

        if ch==12:
            c=input("Press 1 : for create a partition || Press 2 : for remove a partition ")
            os.system("fdisk -l")
            if int(c)==1:
                diskname=input("Enter the Device Name :")
                os.system("ssh {} fdisk -s /dev/{}".format(ip,diskname))
                partsize= input("Enter the Size for the partition")
                filename=input("Name the file name where you want to mount the harddisk ")
                os.system("ssh {} fdisk /dev/{}".format(ip,diskname))
                os.system("ssh {} echo n | fdisk /dev/{}".format(ip,diskname))
                os.system("ssh {} echo p | fdisk /dev/{}".format(ip,diskname))
                os.system("ssh {} echo \n | fdisk /dev/{}".format(ip,diskname))
                os.system("ssh {} echo +{}G | fdisk /dev/{}".format(ip,partsize,diskname))
                os.system("ssh {} echo w | fdisk /dev/{}".format(ip,diskname))
                os.system("ssh {} echo q | fdisk /dev/{}".format(ip,diskname))
                os.system("ssh {} mkfs.ext4 /dev/{}".format(ip,diskname))
                os.system("ssh {} mkdir /{}".format(ip,filename))
                os.system("ssh {} mount /dev/{} /{}".format(ip,diskname,filename))
            elif int(c)==2:
                os.system("ssh {} fdisk /dev/{}".format(ip,diskname))
                os.system("ssh {} echo d | fdisk /dev/{}".format(ip,diskname))
                os.system("ssh {} echo w | fdisk /dev/{}".format(ip,diskname))
                os.system("ssh {} echo q | fdisk /dev/{}".format(ip,diskname))
        if ch==13:
            os.system("python3")

        if ch==14:
            os.system("ssh {} systemctl start docker".format(ip))

        if ch==15:
            os.system("ssh {} systemctl stop docker".format(ip))

        if ch ==16:
            os.system("ssh {} systemctl restart docker".format(ip))

        if ch==17:
            username=input("Enter the Username of your docker hub")
            useremail=input("Enter the Email Address ")
            os.system("ssh {} docker login --username={} --email={}".format(ip,username,useremail))

        if ch==18:
            img=input("Enter the Image name with Version to be pulled :")
            os.system("ssh {} docker pull {}".format(ip,img))

        if ch==19:
            name=input("Enter the Container Name:")
            img=input("Enter the docker image and version to launch the container")
            os.system("ssh {} docker run -it --name {} {}".format(ip,name,img))
        if ch==20:
            name=input("Enter the name of the container you want to stop")
            os.system("ssh {} docker rm -v {}".format(ip,name))

        if ch==21:
            img= input("Enter the Image name with version which you want to delete :")
            os.system("ssh {} docker rmi {}".format(ip,img))

        if ch==22:
            os.system("ssh {} curl ""https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip"" -o ""awscliv2.zip""".format(ip))
            os.system("ssh {} unzip awscliv.zip".format(ip))
            os.system("ssh {} sudo ./aws/install".format(ip))

        if ch==23:
            key=input("Enter the Key name")
            os.system("ssh {} aws ec2 create-key-pair --key-name {}".format(ip,key))

        if ch==24:
            sgname=input("Enter the security group name: ")
            vpc = input("Enter the VPC id: ")
            desc= input("Enter the description in quotes:  ")
            os.system("ssh {} aws ec2 create-security-group --group-name {} --description {} --vpc-id {}".format(ip,sgname,desc,vpc))

        if ch==25:
            sgid=input("Enter the security name")
            protocol=input("Enter the protocol name or enter all if you want to allow all")
            cidr=input("Enter the CIDR(for eg :- 0.0.0.0/0): ")
            if protocol=="all":
                os.system("ssh {} aws ec2 authorize-security-group-ingress --group-id {} --protocol all --cidr {}".format(ip,sgid,cidr))

            else:
           port=input("Enter port number :")
                os.system("ssh {} aws ec2 authorize-security-group-ingress --group-id {} --protocol {} --port {} --cidr {}".format(ip,sgid,protocol,port,cidr))

        if ch==26:
             ami=input("Enter the AMI id:  ")
             insttype=input("Give the Type of Instance: ")
             no=input("How many instance you want to launch: ")
             subnet=input("Enter the Subnet ID:  ")
             key=input("Enter the Key name: ")
             sg=input("Enter the security group id:  ")
             os.system("ssh {} aws ec2 run-instances --image-id {} --instance --image-id {} --instance-type {} --count {} --subnet-id {} --key-name {} --security-group-ids {}".format(ip,ami,insttype,no,subnet,key,sg))

        if ch==27:
            os.system("ssh {} aws ec2 describe-instances".format(ip))

        if ch==28:
            vol=input("Enter the type of Volume :")
            size=input("Enter the Volume size :")
            zone=input("Enter the Availability zone :")
            os.system("ssh {} aws ec2 create-volume --volume-type {} --size {} --availability-zone {}".format(ip,vol,size,zone))

        if ch==29:
            vol=input("Enter the Volume id :")
            instance=input("Enter the instance ID :")
            dev=input("Enter the device name :(for eg- /dev/sdc) : ")
            os.system("ssh {} aws ec2 attach-volume --volume-id {} --instance-id {} --device {}".format(ip,vol,instance,dev))
    if ch==30:
            bucket=input("Give the bucket name:")
            region=input("Enter the region ID :")
            os.system("ssh {} aws s3api create-bucket --bucket {} --region {} --create-bucket-configuration LocationConstraint={}".format(ip,bucket,region,region))

        if ch==31:
            sname=input("Enter the Object path :")
            dname= input("Enter the path of the bucket:")
            os.system("ssh {} aws s3 cp {} {}".format(ip,sname,dname))
        if ch==32:
            exit()

    #else:
        #print("You have entered a wrong option..")
        input("Enter to Continue.. ")
        os.system("clear")
else:
    print("Enter the Correct option")


























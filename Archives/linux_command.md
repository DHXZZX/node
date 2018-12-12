```shell
git lfs track "*.pdf"
```

```shell
git rm -r --cached .
git status
```

```shell
sudo docker run -d \
--name aria2-with-webui \
-p 6800:6800 \
-p 6880:80 \
-p 6888:8080 \
-v /data/DOWNLOAD_DIR:/data \
-v /data/CONFIG_DIR:/conf \
-e SECRET=donghan19930926 \
xujinkai/aria2-with-webui
```

* 实例用户名ip

```shell
root@118.25.50.86
root@35.200.51.82
root@172.96.204.16
```

```shell
export JAVA_HOME=/data/java/jdk1.8.0_181
export JRE_HOME=/data/java/jdk1.8.0_181/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$JAVA_HOME/bin:$PATH
```

```shell
nameserver   8.8.8.8
nameserver   8.8.8.4
```

```shell
listen=YES
background=YES
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
anon_upload_enable=NO
anon_mkdir_write_enable=NO
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
chown_uploads=NO
xferlog_file=/var/log/vsftpd.log
xferlog_std_format=YES
async_abor_enable=YES
ascii_upload_enable=YES
ascii_download_enable=YES
ftpd_banner=Welcome to FTP servers
pam_service_name=vsftpd
chroot_local_user=NO
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd.chroot_list

```

```shell
iptables -I INPUT -p tcp --dport 21 -j ACCEPT
iptables-save

```

```shell
export MAVEN_HOME=/data/develop/maven
export PATH=${PATH}:${MAVEN_HOME}/bin
```

* [upstream 上游代码](https://github.com/staticblog/wiki/wiki/%E4%BF%9D%E6%8C%81fork%E4%B9%8B%E5%90%8E%E7%9A%84%E9%A1%B9%E7%9B%AE%E5%92%8C%E4%B8%8A%E6%B8%B8%E5%90%8C%E6%AD%A5)



```shell
git config --global https.proxy http://127.0.0.1:1080

git config --global https.proxy https://127.0.0.1:1080

git config --global --unset http.proxy

git config --global --unset https.proxy
```

```powershell
$TargetFolder = "E:\COMPANY\test"
$Files = get-childitem $targetFolder -force
Foreach($File in $Files) {
  $FilePath = $File.FullName
  Remove-Item -Path $FilePath -Recurse  -Force
}

```

```powershell
$basePath = 'E:\COMPANY\xinyu\'

$staticPath = $basePath
mkdir $basePath 


# get code from remote repository
cd $basePath
git clone git@git.51juban.cn:xinyu-server/poggyio.git
cd $basePath
git clone git@git.51juban.cn:xinyu-server/poggyio-commons.git
cd $basePath
git clone git@git.51juban.cn:xinyu-server/poggyio-dataschemas.git
cd $basePath
git clone git@git.51juban.cn:xinyu-server/poggyio-jaxrs.git
cd $basePath
git clone git@git.51juban.cn:xinyu-server/poggyio-modules.git
cd $basePath
git clone git@git.51juban.cn:xinyu-server/poggyio-restapi.git
cd $basePath
git clone git@git.51juban.cn:xinyu-server/poggyio-vfs.git
cd $basePath
git clone git@git.51juban.cn:xinyu-server/poggyio-webapp.git

# compare out code
$path = $basePath + 'poggyio-commons\'
cd $path
mvn clean install -DskipTests

$path = $basePath + 'poggyio-dataschemas\'
cd $path
mvn clean install -DskipTests

$path = $basePath + 'poggyio-jaxrs\'
cd $path
mvn clean install -DskipTests

$path = $basePath + 'poggyio-modules\'
cd $path
mvn clean install -DskipTests

$path = $basePath + 'poggyio-vfs\'
cd $path
mvn clean install -DskipTests

# compare inner code
$basePath = $basePath + 'poggyio\'

$path = $basePath + 'poggyio-apn'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-appstore'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-clientcenter'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-comment'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-connectors'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-daily'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-domainadmin'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-ldap'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-mbox'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-mdm'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-meeting'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-organization'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-pan'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-safeguard'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-schd'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-serveplatform'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-session'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-social'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-terminal'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-translator'
cd $path
mvn clean install -DskipTests
$path = $basePath + 'poggyio-uc'
cd $path
mvn clean install -DskipTests

# compare out code

$basePath = $staticPath

$path = $basePath + 'poggyio-restapi\'
cd $path
mvn clean install -DskipTests

$path = $basePath + 'poggyio-webapp\'
cd $path
mvn clean install -DskipTests

```

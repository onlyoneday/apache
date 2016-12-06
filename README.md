apache
======
jenkins-shell

#!/bin/sh
id
set +e
echo '>>> Get old container id'

CID=$(sudo docker ps | grep "apache" | awk '{print $1}')

echo $CID

sudo docker build -t apache /var/jenkins_home/workspace/docker-build-apache/ | sudo tee /var/jenkins_home/workspace/docker-build-apache/Docker_build_result.log
RESULT=$(sudo cat /var/jenkins_home/workspace/docker-build-apache/Docker_build_result.log | tail -n 1)

#if [["$RESULT" != *Successfully*]];then
#  exit -1
#fi

echo '>>> Stopping old container'

if [ "$CID" != "" ];then
  sudo docker stop $CID
fi

echo '>>> Restarting docker'
sudo service docker restart
sleep 5
  
echo '>>> Starting new container'
sudo docker run -p 3000:80 -d apache

### Local storage capacity issue

You may find local disk (/dev/xvda1) is full sometime, or facing `No space left on device`.
Actually, docker and conda envs are installed in /dev/xvda1 disk. You can move docker data to EBS volume (under ~/SageMaker/), and delete unused conda envs.

```
# mv docker data to SageMaker/dockerEnv
sudo service docker stop
DOCKER_PATH=~/SageMaker/dockerEnv
mkdir $DOCKER_PATH

echo '{ 
   "data-root": "/home/ec2-user/SageMaker/dockerEnv",
   "runtimes": {
        "nvidia": {
            "path": "nvidia-container-runtime",
            "runtimeArgs": []
        }
    }
}' | sudo tee /etc/docker/daemon.json > /dev/null



sudo rsync -aP /var/lib/docker/ $DOCKER_PATH
sudo mv /var/lib/docker /var/lib/docker.old
sudo service docker start

# rm -rf /var/lib/docker.old # delete old docker data
```

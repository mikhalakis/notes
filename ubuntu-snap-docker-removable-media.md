# Ubuntu Snap/Docker access to mounted file systems

I choose to install Docker during the installation process of Ubuntu 22.04 LTS Server and the [Snap](https://snapcraft.io/about) version of Docker has been installed. 
It worked fine but there was one issue: Docker was unable to read *Dockerfile* or *docker-compose.yml* from mounted file systems.

This command fixed it:
```
sudo snap connect docker:removable-media
```

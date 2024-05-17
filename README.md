# DiveOnDocker

## 1. Instalar Docker
Lo primero que vamos a necesitar es instalar Docker en nuestra distribución de Debian en WSL 2.

##### Configurar el repositorio apt de Docker e instalar
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
```
##### Manejar Docker sin ser root
```
# Sustituir $USER por tu usuario, despues de este paso conviene reiniciar Debian para que se aplique
sudo usermod -aG docker $USER
```
##### Verifica que se ha instalado
```
docker --version
```
##### Verifica que se ha iniciado
```
sudo service docker status
```
##### Hello World from Docker
```
docker run hello-world
```
###### Nos devolverá algo semejante a esto

[![Hello-world.png](https://i.postimg.cc/VvjkXd8T/Hello-world.png)](https://postimg.cc/BPn30Ztc)

Con esto en principio deberiamos tener la instalación de docker hecha.

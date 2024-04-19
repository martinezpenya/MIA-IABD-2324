---
title: Instalación de ROS y Gazebo
language: ES
author: www.martinezpenya.es
subject: Modelos de Inteligencia Artificial
keywords: [MIA, 2023, Modelos de Inteligencia Artificial, Curso Especialización IA-BD]
IES: www.ieseduardoprimo.es
header: ${title} - ${subject} (ver. ${today}) 
footer:${currentFileName}.pdf - ${author} - ${IES} - ${pageNo}/${pageCount}
typora-root-url:${filename}/../
typora-copy-images-to:${filename}/../assets
---
[toc]

# VirtualBox

VirtualBox is a powerful x86 and AMD64/Intel64 [virtualization](https://www.virtualbox.org/wiki/Virtualization) product for enterprise as well as home use. Not only is VirtualBox an  extremely feature rich, high performance product for enterprise  customers, it is also the only professional solution that is freely  available as Open Source Software under the terms of the GNU General  Public License (GPL) version 3. See "[About VirtualBox](https://www.virtualbox.org/wiki/VirtualBox)" for an introduction.

https://www.virtualbox.org/wiki/Downloads

> #### My Virtual machine:
>
>  - 4096 RAM
>  - 4 CPU
>  - 250 GB HD

# Kubuntu/Ubuntu 22.04 LTS

## Kubuntu, making your PC friendly

Kubuntu is an operating system built by a worldwide community of developers, testers, supporters and translators.


Kubuntu is a free, complete, and open-source alternative to Microsoft Windows and Mac OS X which contains everything you need to work, play, or share. Check out the Feature Tour if you would like to learn more!


Kubuntu unites Ubuntu with KDE and the fabulous Plasma desktop, bringing you a full set of applications. The installation includes productivity, office, email, graphics, photography, and music applications ready to use at startup.


Firefox, Kmail, LibreOffice, Gwenview are just a few installed and ready to use, with thousands more, available in just a click, from the Discover software centre.

Built using the Qt toolkit, Kubuntu is fast, slick and beautiful. Kubuntu is mobile-ready, enabling easy integration between your PC desktop and phone or tablet. Simply use the Google Play store to install KDE Connect on your Android device and you can integrate your device with your desktop.

https://kubuntu.org/getkubuntu/

## Ubuntu, The OS trusted by millions

Ubuntu comes with everything you need to run your organisation, school, home or enterprise. All the essential applications, like an office suite, browsers, email and media apps, come pre-installed and thousands more games and applications are available in the Ubuntu Software Centre.

https://ubuntu.com/download/desktop

> #### My OS: **Kubuntu 22.04 LTS**
>
> - Minimal installation
> - Update Kubuntu
> - Install 3rd part software
> - Install guest Additions (VirtualBox)

# ROS - Robot Operating System

The Robot Operating System (ROS) is a set of software libraries and tools that help you build robot applications. From drivers to state-of-the-art algorithms, and with powerful developer tools, ROS has what you need for your next robotics project. And it's all open source. 

## Installation

ROS is released as *distributions*, also called “distros”,  with more than one ROS distribution supported at a time. Some releases  come with long term support (LTS), meaning they are more stable and have undergone extensive testing. Other distributions are newer with shorter lifetimes, but with support for more recent platforms and more recent  versions of their constituent ROS packages. See the [distributions list](http://docs.ros.org/) for more details. Generally a new ROS distro is released every year on [world turtle day](https://www.worldturtleday.org/), with LTS distros being released in even years. We currently recommend one of the versions below:

<img src="/assets/humble.png" alt="ROS Humble Hawksbill logo" style="zoom:25%;" />

**Get Humble Hawksbill on Ubuntu Linux 22.04, Windows 10**

(Recommended for ROS 2 LTS)

[Install](https://docs.ros.org/en/humble/Installation.html)

> #### Instalación ROS 2
>
> - El locale ya es compatible con UTF-8
>
> - shell:
>
>   ```sh
>   sudo apt install software-properties-common
>   sudo add-apt-repository universe
>           
>   sudo apt update && sudo apt install curl -y
>   sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
>           
>   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
>           
>   sudo apt update
>   sudo apt upgrade
>           
>   sudo apt install ros-humble-desktop
>           
>   #TEST INSTALLATION
>   #terminal 1
>   source /opt/ros/humble/setup.bash
>   ros2 run demo_nodes_cpp talker
>           
>   #terminal 2
>   source /opt/ros/humble/setup.bash
>   ros2 run demo_nodes_py listener
>   ```

# Gazebo

  Gazebo brings a fresh approach to simulation with a complete toolbox of  development libraries and cloud services to make simulation easy.  Iterate fast on your new physical designs in realistic environments with high fidelity sensors streams. Test control strategies in safety, and  take advantage of simulation in continuous integration tests. 

![](/assets/gazebo_horz_pos_topbar.svg)

## Fortress LTS

Supported Sep, 2021 to Sep, 2026

https://gazebosim.org/docs/fortress/getstarted

> #### Instalación Gazebo Fortress LTS Ubuntu binary
>
> shell:
>
> ```sh
> sudo apt-get update
> sudo apt-get install lsb-release wget gnupg
> 
> sudo wget https://packages.osrfoundation.org/gazebo.gpg -O /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg
> echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null
> sudo apt-get update
> sudo apt-get install ignition-fortress
> ```

## Gazebo Tutorials

https://gazebosim.org/docs/fortress/tutorials



# Turtlebot3? ***PROVISIONAL NO FUNCIONA!!!!****

Repo: https://github.com/ROBOTIS-GIT/turtlebot3

```sh
mkdir -p ~/test_turtlebot/src
cd ~/test_turtlebot/src

git clone https://github.com/ROBOTIS-GIT/turtlebot3.git
git clone https://github.com/ROBOTIS-GIT/turtlebot3_msgs.git
git clone https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git

cd ~/test_turtlebot


```

Intento 2: https://emanual.robotis.com/docs/en/platform/turtlebot3/quick-start/

```sh
wget https://raw.githubusercontent.com/ROBOTIS-GIT/robotis_tools/master/install_ros_kinetic.sh

chmod 755 ./install_ros_kinetic.sh

bash ./install_ros_kinetic.sh

echo "export TURTLEBOT3_MODEL=burger" >> ~/.bashrc


```

Intento 3: https://emanual.robotis.com/docs/en/platform/turtlebot3/quick-start/

```sh
sudo apt install ros-humble-gazebo-*
sudo apt install ros-humble-cartographer
sudo apt install ros-humble-cartographer-ros
sudo apt install ros-humble-navigation2
sudo apt install ros-humble-nav2-bringup

sudo apt install ros-humble-dynamixel-sdk
sudo apt install ros-humble-turtlebot3-msgs
sudo apt install ros-humble-turtlebot3
echo 'export ROS_DOMAIN_ID=30 #TURTLEBOT3' >> ~/.bashrc


```



# Fuentes de información

- [Wikipedia](https://es.wikipedia.org)
- [Programación (Grado Superior) - Juan Carlos Moreno Pérez (Ed. Ra-ma)](https://www.ra-ma.es/libro/programacion-grado-superior_48302/)
- Apuntes IES Henri Matisse (Javi García Jimenez?)
- Apuntes AulaCampus
- [Apuntes José Luis Comesaña](https://www.sitiolibre.com/)
- [Apuntes IOC Programació bàsica (Joan Arnedo Moreno)](https://ioc.xtec.cat/materials/FP/Recursos/fp_asx_m03_/web/fp_asx_m03_htmlindex/index.html)
- [Apuntes IOC Programació Orientada a Objectes (Joan Arnedo Moreno)](https://ioc.xtec.cat/materials/FP/Recursos/fp_dam_m03_/web/fp_dam_m03_htmlindex/index.html)
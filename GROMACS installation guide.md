
GROMACS installation guide
        1)Downloading GROMASCS 
To check the latest version, visit: https://manual.gromacs.org/current/download.html

Steps:
wget https://ftp.gromacs.org/gromacs/gromacs-2024.3.tar.gz

 ![image](https://github.com/user-attachments/assets/54b96a97-4733-4a14-979f-febb615f90a2)


Check the downloaded file:
ls -lrth

![image](https://github.com/user-attachments/assets/973462d5-8b6a-4e6d-95e4-dd4523489d63)


Unzip tar file:
tar -zxvf gromacs-2024.3.tar.gz

![image](https://github.com/user-attachments/assets/dd64d15f-92a3-4d3f-9457-41090f412360)

ls

![image](https://github.com/user-attachments/assets/dde414a6-4591-42f7-a990-956e1d026cf3)

 
2) Installating GROMACS

https://manual.gromacs.org/documentation/current/install-guide/index.html

cd gromacs-2024.3/
ls -lrth
mkdir build
cd build
cmake .. -DGMX_BUILD_OWN_FFTW=ON -DREGRESSIONTEST_DOWNLOAD=ON

![image](https://github.com/user-attachments/assets/bfbd8afd-6077-47ba-ae15-7a85ed34260a)


 
make
make check
sudo make install
source /usr/local/gromacs/bin/GMXRC

3.Cheing the installation of GROMACS
gmx

![image](https://github.com/user-attachments/assets/ef737a9c-63f3-4291-af8b-12a24fcf5bfb)


Note: Make sure to use the path always>

source /usr/local/gromacs/bin/GMXRC



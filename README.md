# iped
Aqui mostro como compilar o IPED no Linux.

Nesta versão estamos utilizando a distribuição Ubuntu 20.04

* Instalando Dependencias basicas para compilação:

```bash
echo "TZ=Brazil/East" >> /etc/environment 
echo "DEBIAN_FRONTEND=noninteractive" >> /etc/environment
echo "LANG=C.UTF-8 LC_ALL=C.UTF-8" >> /etc/environment
source /etc/environment

apt-get update && apt-get install -y wget \
    && wget -q -O - https://download.bell-sw.com/pki/GPG-KEY-bellsoft | apt-key add - \
    && echo "deb [arch=amd64] https://apt.bell-sw.com/ stable main" | tee /etc/apt/sources.list.d/bellsoft.list \
    && apt-get update && apt-get install -y \
      git \
      build-essential \
      libafflib-dev zlib1g-dev libewf-dev unzip patch libssl-dev \
      autoconf automake autopoint libtool pkg-config yasm gettext wget \
      libaa1-dev libasound2-dev libcaca-dev libcdparanoia-dev libdca-dev \
      libdirectfb-dev libenca-dev libfontconfig1-dev libfreetype6-dev \
      libfribidi-dev libgif-dev libgl1-mesa-dev libjack-jackd2-dev libopenal1 libpulse-dev \
      libsdl1.2-dev libvdpau-dev libxinerama-dev libxv-dev libxvmc-dev libxxf86dga-dev \
      libxxf86vm-dev librtmp-dev libsctp-dev libass-dev libfaac-dev libsmbclient-dev libtheora-dev \
      libogg-dev libxvidcore-dev libspeex-dev libvpx-dev libdv4-dev \
      libopencore-amrnb-dev libopencore-amrwb-dev libmp3lame-dev liblivemedia-dev libtwolame-dev \
      libmad0-dev libgsm1-dev libbs2b-dev liblzo2-dev ladspa-sdk libfaad-dev \
      libmpg123-dev libopus-dev libbluray-dev libaacs-dev libtsk-dev libesedb-utils \
      libjpeg-dev \
      libtiff-dev \
      libpng-dev \
      libwmf-dev \
      libgif-dev \
      libheif-dev \
      libwebp-dev \
      librsvg2-dev \
      libopenexr-dev \
      vim \
      less \
	  unzip \
      libparse-win32registry-perl \
      tesseract-ocr tesseract-ocr-por tesseract-ocr-osd \
	  graphviz \
	  bellsoft-java8-full \
      && apt-get download ant && ls *.deb | awk '{system("dpkg-deb -x "$1" /")}' \
      && apt-get clean && rm -rf /var/lib/apt/lists/*
```


* Compilando o sleuthkit (dependencias base para o IPED) [https://www.sleuthkit.org/]:

```bash
cd /opt/ 
git clone https://github.com/lfcnassif/sleuthkit-APFS \
    && cd /opt/sleuthkit-APFS/ \
    && ./bootstrap \
	&& ./configure --prefix=/usr/ \
    && make && make install \
    && rm -rf /opt/sleuthkit-APFS/
```

* Compilando o Mplayer e diferentes dependencias para o IPED, conforme guia [https://github.com/sepinf-inc/IPED/wiki/Linux]:
```bash
cd /opt
wget --progress=bar:force http://www.mplayerhq.hu/MPlayer/releases/MPlayer-1.4.tar.gz \
    && tar xvzf MPlayer-1.4.tar.gz \
    && cd /opt/MPlayer-1.4 \
    && ./configure --prefix=/usr \
    && make all install \
    && cd /usr/lib \
    && wget --progress=bar:force http://www.mplayerhq.hu/MPlayer/releases/codecs/all-20110131.tar.bz2 \
    && tar xvjf all-20110131.tar.bz2 && mv all-20110131 codecs && rm all-20110131.tar.bz2
    
    cd /opt
git clone https://github.com/libyal/libagdb.git \
    && cd /opt/libagdb \
    && ./synclibs.sh \
    && ./autogen.sh \
    && ./configure --prefix=/usr \
    && make all install \
    && rm -rf /opt/libagdb

cd /opt
git clone --branch="20200709" https://github.com/libyal/libevtx \
    && cd /opt/libevtx \
    && ./synclibs.sh \
    && ./autogen.sh \
    && ./configure --prefix=/usr \
    && make all install \
    && rm -rf /opt/libevtx

cd /opt
git clone --branch="20200926" https://github.com/libyal/libevt \
    && cd /opt/libevt \
    && ./synclibs.sh \
    && ./autogen.sh \
    && ./configure --prefix=/usr \
    && make all install \
    && rm -rf /opt/libevt

cd /opt
git clone --branch="20200717" https://github.com/libyal/libscca \
    && cd /opt/libscca \
    && ./synclibs.sh \
    && ./autogen.sh \
    && ./configure --prefix=/usr \
    && make all install \
    && rm -rf /opt/libscca

cd /opt
git clone --branch="20200418" https://github.com/libyal/libesedb \
    && cd /opt/libesedb \
    && ./synclibs.sh \
    && ./autogen.sh \
    && ./configure --prefix=/usr \
    && make all install \
    && rm -rf /opt/libesedb


# libpff without branch also, because latest release got problems
cd /opt
git clone https://github.com/libyal/libpff \
    && cd /opt/libpff \
    && ./synclibs.sh \
    && ./autogen.sh \
    && ./configure --prefix=/usr \
    && make all install \
    && rm -rf /opt/libpff

cd /opt
git clone --branch="20200710" https://github.com/libyal/libmsiecf \
    && cd /opt/libmsiecf \
    && ./synclibs.sh \
    && ./autogen.sh \
    && ./configure --prefix=/usr \
    && make all install \
    && rm -rf /opt/libmsiecf

cd /opt
git clone --branch="0.7.0" https://github.com/abelcheung/rifiuti2 \
    && cd /opt/rifiuti2 \
    && autoreconf -f -i -v \
    && ./configure --prefix=/usr \
    && make all install \
    && rm -rf /opt/rifiuti2

cd /opt
git clone --branch "7.0.10-61" https://github.com/ImageMagick/ImageMagick \
    && cd /opt/ImageMagick \
    && ./configure --prefix=/usr \
    && make all install \
    && rm -rf /opt/ImageMagick
```

* Configurando as variaveis de ambiente do Java:

```bash
echo "export JAVA_HOME=/usr/lib/jvm/bellsoft-java8-full-amd64/jre/" > /etc/profile.d/java.sh
echo "export JDK_HOME=/usr/lib/jvm/bellsoft-java8-full-amd64/" >> /etc/profile.d/java.sh
echo "export JRE_HOME=/usr/lib/jvm/bellsoft-java8-full-amd64/jre/" >> /etc/profile.d/java.sh
echo "export J2REDIR=/usr/lib/jvm/bellsoft-java8-full-amd64/jre/" >> /etc/profile.d/java.sh
echo "export J2SDKDIR=/usr/lib/jvm/bellsoft-java8-full-amd64" >> /etc/profile.d/java.sh
source /etc/environment
```


* Configurando para abrir Pre visualização no libreoffice e VLC (OPCIONAL este passo):
```bash
apt-get install -y libreoffice libreoffice-java-common \
      libreoffice-gtk2 \      
      xdg-utils \
      libgl1-mesa-dri \
      vlc \
      packagekit-gtk3-module \
      libcanberra-gtk-module
      
      # allows the use of vlc as root
sed -i 's/geteuid/getppid/' /usr/bin/vlc

# For libreoffice java plugin use
echo "SAL_USE_VCLPLUGIN=\"gtk\"" >> /etc/environment
echo "LD_LIBRARY_PATH=\"/usr/local/cuda/lib64\""  >> /etc/environment
```

* Instalando o IPED

Nesta passo criei um usuario comun e estou executando normal (sem ser root)
```bash
useradd usuario
su - usuario

export IPED_VERSION=3.18.4
mkdir ~/IPED/
cd ~/IPED/
wget --progress=bar:force https://github.com/sepinf-inc/IPED/releases/download/$IPED_VERSION/IPED-${IPED_VERSION}_and_extra_tools.zip -O iped.zip && unzip iped.zip && rm iped.zip && ln -s iped-$IPED_VERSION iped

```

* Configurando o IPED

Vale a pena conferir neste link [https://github.com/sepinf-inc/IPED/wiki/User-Manual#Profiles]
Neste link do Youtube se apresenta varias configurações interessantes [https://www.youtube.com/watch?v=A1NDvVtdEJY]

```bash
su - usuario
cd ~/IPED/iped
	sed -i -e "s/#tskJarPath =.*/tskJarPath = \/usr\/share\/java\/sleuthkit-4.6.5.jar/" LocalConfig.txt
	sed -i -e "s/mplayerPath =.*/mplayerPath = \/usr\/bin\/mplayer/" LocalConfig.txt
	sed -i -e "s/numThreads =.*/numThreads = 12/" LocalConfig.txt
	sed -i -e "s/locale =.*/locale = pt-BR/" LocalConfig.txt
```  


* Executando o IPED [https://github.com/sepinf-inc/IPED/wiki/Beginner's-Start-Guide]


Primeiro passo e ter uma imagem do disco ou pendrive e etc (pode ser obter através do comando: dd, ccdc,casesSuites de Hardwares  ou etc);

No meu caso tenho uma imagem do Windows 7 (`IE10WIN7-20180115-181028.img` dentro da pasta `evidencias`) e vai gerar o conteudo indexado na pasta ../evidencias/INDEXIPED
```bash
cd ~/IPED/iped
mkdir -p ../evidencias/INDEXIPED
java -jar iped.jar -d ../evidencias/IE10WIN7-20180115-181028.img  -o ../evidencias/INDEXIPED --nogui
```

Vizualizando o conteudo basta ir na pasta gerada e rodar o comando (Este passo precisar de interface grafica instalada na maquina Linux, ou seja não pode ser via terminal remoto).

```bash
su - usuario
cd ~/IPED/evidencias/INDEXIPED/indexador
java -jar lib/iped-search-app.jar
```

Links de Manuais e Referencis Uteis:

https://servicos.dpf.gov.br/ferramentas/IPED/3.14.5/IPED-Manual_pt-BR.pdf
https://github.com/sepinf-inc/IPED/wiki/Beginner's-Start-Guide
ftp://ftp.registro.br/pub/gts/gts0205/05-tech-tools-forensics.pdf
https://www.tiforense.com.br/iped-indexador-e-processador-de-evidencias-digitais-dpf/



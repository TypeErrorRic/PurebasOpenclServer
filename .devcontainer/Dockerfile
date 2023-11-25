# Utilizar una imagen de ubuntu:
FROM ubuntu:20.04

# Instalar todas las dependencias:
RUN apt-get update && apt-get install -y git\
    zip \
    tar \
    build-essential \
    curl \
    unzip

#Directorio en el que se va atrabajar dentro del contenedor:
WORKDIR /usr/app/opencl

# Clone a sample OpenCL program
RUN git clone --recursive https://github.com/KhronosGroup/OpenCL-SDK.git

# Set noninteractive mode
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get install -y --no-install-recommends \
    cmake \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* cmake

# Instalar los paquetes necesarios:
RUN git clone https://github.com/Microsoft/vcpkg.git && \
    cd vcpkg && \
    ./bootstrap-vcpkg.sh && \
    ./vcpkg integrate install &&\
    ./vcpkg install opencl:x64-linux

# Set vcpkg as an environment variable
ENV PATH=/usr/local/vcpkg:$PATH
ENV PATH=$VCPKG_ROOT:$PATH

WORKDIR /usr/app/opencl/vcpkg

# Define the system name for triplet x64-linux
ENV VCPKG_CMAKE_SYSTEM_NAME=Linux

RUN git pull origin master
RUN ./vcpkg update
RUN ./vcpkg --triplet x64-Linux install tclap glm 

#Cambio de directorio de trabajo:
WORKDIR /usr/app/opencl/

# Instala las bibliotecas de desarrollo de X11, OpenGL y GLU
RUN apt-get update && \
    apt-get install -y \
    libx11-dev \
    libgl1-mesa-dev \
    mesa-common-dev \
    mesa-utils-extra \
    libglu1-mesa-dev \
    libxrandr-dev \
    libudev-dev

# Configura y compila el proyecto con CMake
RUN cmake -G "Unix Makefiles" \
    -D BUILD_TESTING=OFF \
    -D BUILD_DOCS=OFF \
    -D BUILD_EXAMPLES=OFF \
    -D BUILD_TESTS=OFF \
    -D OPENCL_SDK_BUILD_SAMPLES=ON \
    -D OPENCL_SDK_TEST_SAMPLES=OFF \
    -D CMAKE_TOOLCHAIN_FILE=/usr/app/opencl/vcpkg/scripts/buildsystems/vcpkg.cmake \
    -D VCPKG_TARGET_TRIPLET=x64-linux \
    -B ./OpenCL-SDK/build -S ./OpenCL-SDK

RUN cmake --build ./OpenCL-SDK/build --target install

# Set the default command to run when the container starts
CMD ["bash"]

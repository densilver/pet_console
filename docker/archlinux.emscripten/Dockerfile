FROM archlinux/base:latest

ARG SRC_DIR=/usr/src/pet_console

ENV \
	TERM=xterm-color

RUN \
	pacman-db-upgrade && \
	pacman -Syyu --noconfirm

RUN \
	pacman -Syu --noconfirm emscripten && \
	pacman -Syu --noconfirm libxml2

WORKDIR /root

RUN \
	echo "import os" > .emscripten && \
	echo "EMSCRIPTEN_ROOT = '/usr/lib/emscripten'" >> .emscripten && \
	echo "LLVM_ROOT = '/usr/lib/emscripten-llvm'" >> .emscripten && \
	echo "BINARYEN_ROOT = '/usr'" >> .emscripten && \
	echo "NODE_JS = '/usr/bin/node'" >> .emscripten
RUN \
	echo "int main() {}" > binaryen.cpp && \
	/usr/lib/emscripten/em++ -s WASM=1 binaryen.cpp && \
	rm -v binaryen.*

RUN \
	pacman -Syu --noconfirm make cmake

RUN mkdir $SRC_DIR
COPY build.sh $SRC_DIR/build.sh
COPY scripts $SRC_DIR/scripts
COPY CMakeLists.txt $SRC_DIR/CMakeLists.txt
COPY cmake $SRC_DIR/cmake
COPY src $SRC_DIR/src

WORKDIR $SRC_DIR
RUN \
	source /etc/profile.d/emscripten.sh && \
	PET_CONSOLE_TARGET_PLATFORM=emscripten \
	PET_CONSOLE_BUILD_TYPE=Release \
	./build.sh rebuild

WORKDIR $SRC_DIR/.build/emscripten/Release/bin
CMD python -m http.server 8000

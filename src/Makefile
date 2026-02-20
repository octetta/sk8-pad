# defaults for Linux

TARGET = linux
CC = gcc
LIB = -lm -lasound -pthread -lpthread -lrt
COPTS = -D_GNU_SOURCE -Wall -march=native -O3
# COPTS = -D_GNU_SOURCE -Wall -march=native -g # -O3

UNAME_S = $(shell uname -s)

ifeq ($(UNAME_S), Darwin)
	CC = clang
	LIB = -lm -pthread -lpthread -framework CoreAudio -framework CoreFoundation 
	TARGET = darwin
	COPTS = -g -D_GNU_SOURCE -D_IS_OSX_ -Wall -arch arm64  -arch x86_64
endif

OUT = build/$(TARGET)
WINOUT = build/win

SKRED = $(OUT)/skred

all : sk8-pad-$(TARGET)

# Linker Flags
# -s -w strips debug info for smaller binaries
# -H=windowsgui hides the terminal window on Windows launch
# -extldflags '-static...' ensures all C++ dependencies are bundled into the exe
WIN_LDFLAGS="-H=windowsgui -s -w -extldflags '-static -static-libgcc -static-libstdc++'"
LINUX_LDFLAGS="-s -w"

# go install fyne.io/tools/cmd/fyne@latest

GOWINENV = CGO_ENABLED=1 GOOS=windows GOARCH=amd64 CC=x86_64-w64-mingw32-gcc CXX=x86_64-w64-mingw32-g++
GOWINLD = -ldflags="-H=windowsgui -s -w -extldflags '-static -static-libgcc -static-libstdc++'"

sk8-pad-linux:
	mkdir -p $(OUT)
	cd sk8-pad && go build -ldflags="-s -w" -o ../$(OUT)/sk8-pad-linux .

sk8-pad-darwin:
	mkdir -p $(OUT)
	cd sk8-pad && \
	CGO_ENABLED=1 GOOS=darwin GOARCH=amd64 go build -o ../$(OUT)/sk8-pad-darwin-amd64 . && \
	CGO_ENABLED=1 GOOS=darwin GOARCH=arm64 go build -o ../$(OUT)/sk8-pad-darwin-arm64 . && \
	lipo -create -output ../$(OUT)/sk8-pad-darwin-universal ../$(OUT)/sk8-pad-darwin-amd64 ../$(OUT)/sk8-pad-darwin-arm64
	cd sk8-pad && fyne package -exe ../$(OUT)/sk8-pad-darwin-universal -os darwin -icon icon.png -name ../$(OUT)/sk8-pad

sk8-pad-windows:
	mkdir -p $(WINOUT)
	cd sk8-pad && \
    $(GOWINENV) \
    fyne package --release -os windows && \
    cp sk8-pad.exe ../$(WINOUT)

clean :
	rm -rf build
	find . -name '*.exe' -exec rm -f {} \;

#
# 
#
include ../misc/cross_gn.mk
#include ../misc/libwebsocket_cross.mk

#
# h264bitstream & openssl library
#
H264BITSTREAM_LIB = ../lib/h264bitstream/.libs/libh264bitstream.a
#OPENSSL_LIB = -lssl -lcrypto
CCFLAGS +=  -I../lib

#
# Variables for MMAL(raspivid) compilation
#
PI_INCLUDE=$(SYSROOT)/opt/vc/include
MMAL_CFLAGS+=-Wno-multichar -Wno-unused-but-set-variable -fPIC -O3 -DDEBUG -DEGL_SERVER_DISPMANX -DHAVE_CMAKE_CONFIG -DHAVE_VMCS_CONFIG -DOMX_SKIP64BIT -DTV_SUPPORTED_MODE_NO_DEPRECATED -DUSE_VCHIQ_ARM -DVCHI_BULK_ALIGN=1 -DVCHI_BULK_GRANULARITY=1 -D_FILE_OFFSET_BITS=64 -D_HAVE_SBRK -D_LARGEFILE64_SOURCE -D_LARGEFILE_SOURCE -D_REENTRANT -D__VIDEOCORE4__ -D__WEBRTC_DEFAULT__

MMAL_INCLUDES+=-I$(PI_INCLUDE)/interface/vcos/pthreads -I$(PI_INCLUDE)/interface/vmcs_host/linux -I$(PI_INCLUDE)/interface/vmcs_host -I$(PI_INCLUDE)/interface/vmcs_host/khronos -I$(PI_INCLUDE)/interface/khronos/include -I$(PI_INCLUDE)/interface/vchiq_arm -I$(PI_INCLUDE) -I$(PI_INCLUDE)/interface/mmal 

MMAL_LDFLAGS+=-L$(SYSROOT)/opt/vc/lib/ -lmmal_core -lmmal -lmmal_util -lvcos -lcontainers -lbcm_host -lmmal_vc_client -lmmal_components -lvchiq_arm -lvcsm -lvcfiled_check 

#
#
SYS_LIBS += -lpthread -ldl -lrt -lm 

#
# TARGET
#
TARGET = ../webrtc-streamer

#
# source & object list definition
#
#SOURCES.CC = main.cc
#SOURCES.C = 
SOURCES.CC = stream_data_sockets.cc stream_sockets.cc direct_socket.cc streamer_observer.cc streamer.cc streamer_defaults.cc main.cc raspi_encoder.cc mmal_wrapper.cc h264bitstream_parser.cc utils.cc
SOURCES.C = mmal_encoder.c mmal_encoder_reset.c mmal_util.c raspicli.c raspicamcontrol.c raspipreview.c 


OBJECTS.CC = $(SOURCES.CC:.cc=.o) 
OBJECTS.CPP = $(SOURCES.CPP:.cpp=.o) 
OBJECTS.C = $(SOURCES.C:.c=.o)
OBJECTS = $(OBJECTS.CC) $(OBJECTS.C) $(OBJECTS.CPP)

all: $(TARGET)

#
# Makefile rules...
#
.c.o : $(OBJECTS.C) 
	$(CC) -MMD -MF $@.d -I. $(CFLAGS) $(MMAL_CFLAGS) $(MMAL_INCLUDES) -c $< -o $@

.cc.o : $(OBJECTS.CC) 
	$(CXX)  -MMD -MF $@.d -I. $(CFLAGS) $(CCFLAGS) $(CCFLAGS_DEFS) $(INCLUDE_WEBRTC_LIB) $(MMAL_CFLAGS) $(MMAL_INCLUDES)  -c $< -o $@

.cpp.o : $(OBJECTS.CCP) 
	$(CXX) -I. $(WSPP_CCFLAGS) $(WSPP_INCLUDE) -c $< -o $@

$(TARGET): $(OBJECTS) 
	$(CXX) $(LDFLAGS) -o $(TARGET) $(CFLAGS) $(CCFLAGS) -Wl,--start-group $(OBJECTS) $(INCLUDE_WEBRTC_LIB_LIST) $(MMAL_LDFLAGS) $(H264BITSTREAM_LIB) -Wl,--end-group $(OPENSSL_LIB) $(SYS_LIBS) 

clean:
	rm -f *.o  *.o.d $(TARGET)

rcp: $(TARGET)
	rsync -v -u -r --stats $(TARGET) *.cc *.h pi@10.0.0.11:~/Workspace/client


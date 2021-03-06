#
# Copyright (c) 2001, 2002 Swedish Institute of Computer Science.
# Copyright (c) 2011 Gerald Van Baren
# All rights reserved. 
# 
# Redistribution and use in source and binary forms, with or without modification, 
# are permitted provided that the following conditions are met:
#
# 1. Redistributions of source code must retain the above copyright notice,
#    this list of conditions and the following disclaimer.
# 2. Redistributions in binary form must reproduce the above copyright notice,
#    this list of conditions and the following disclaimer in the documentation
#    and/or other materials provided with the distribution.
# 3. The name of the author may not be used to endorse or promote products
#    derived from this software without specific prior written permission. 
#
# THIS SOFTWARE IS PROVIDED BY THE AUTHOR ``AS IS'' AND ANY EXPRESS OR IMPLIED 
# WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF 
# MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT 
# SHALL THE AUTHOR BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, 
# EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT 
# OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS 
# INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN 
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING 
# IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY 
# OF SUCH DAMAGE.
#
# This file is part of the lwIP TCP/IP stack.
# 
# Author: Adam Dunkels <adam@sics.se>
# Secondary author: Gerald Van Baren <gvb@unssw.com>
#

CONTRIBDIR=../..
LWIPARCH=$(CONTRIBDIR)/ports/cross/src

TOP_LEVEL_PROGRAM=../../../quickstart

CROSS_COMPILE = arm-none-eabi-

#Set this to where you have the lwip core module checked out from CVS
#default assumes it's a dir named lwip at the same level as the contrib module
LWIPDIR=$(CONTRIBDIR)/../lwip/src

#Set this to where you have the arch dir for your system for arch/sys_arch.h and arch/cc.h
FREERTOS=../../../FreeRTOS
RTOS_SOURCE_DIR=$(FREERTOS)/Source


# Convention for where the build detritus ends up
BUILD_DIR = obj/

# Quiet vs. verbose: to make things verbose, put a # in front of the @
Q	= @

# Make variables (CC, etc...)

AS	= $(CROSS_COMPILE)as
LD	= $(CROSS_COMPILE)ld
CC	= $(CROSS_COMPILE)gcc
CPP	= $(CC) -E
AR	= $(CROSS_COMPILE)ar
NM	= $(CROSS_COMPILE)nm
STRIP	= $(CROSS_COMPILE)strip
OBJCOPY	= $(CROSS_COMPILE)objcopy
OBJDUMP	= $(CROSS_COMPILE)objdump


#
# The flags passed to the compiler.
#
CFLAGS =	-mthumb \
		-mcpu=cortex-m3 \
		-Os \
		-ffunction-sections \
		-fdata-sections \
		-std=c99 \
		-Wall \
		-pedantic \
		-DPART_$(PART) \
		-c

# Build for debug
CFLAGS +=	-g

CFLAGS += -DIPv4 -DLWIP_DEBUG

CFLAGS	+=\
	-I$(TOP_LEVEL_PROGRAM)/src/app-opts \
	-I$(TOP_LEVEL_PROGRAM)/src/quick-opts \
	-I$(TOP_LEVEL_PROGRAM)/src/quick \
	-I$(LWIPDIR)/include \
	-I$(LWIPARCH)/include \
	-I$(LWIPDIR)/include/ipv4 \
	-I./src/include \
	-I./src/include/LM3S \
	-I$(TOP_LEVEL_PROGRAM)/src \
	-I$(RTOS_SOURCE_DIR)/include \
	-I$(RTOS_SOURCE_DIR)/portable/GCC/ARM_CM3 

# COREFILES, CORE4FILES: The minimum set of files needed for lwIP.
COREFILES=$(LWIPDIR)/core/mem.c $(LWIPDIR)/core/memp.c $(LWIPDIR)/core/netif.c \
	$(LWIPDIR)/core/pbuf.c $(LWIPDIR)/core/stats.c $(LWIPDIR)/core/sys.c \
        $(LWIPDIR)/core/tcp.c $(LWIPDIR)/core/tcp_in.c $(LWIPDIR)/core/raw.c \
        $(LWIPDIR)/core/tcp_out.c $(LWIPDIR)/core/udp.c $(LWIPDIR)/core/init.c \
        $(LWIPDIR)/core/def.c $(LWIPDIR)/core/timers.c $(LWIPDIR)/core/dhcp.c
CORE4FILES=$(LWIPDIR)/core/ipv4/icmp.c $(LWIPDIR)/core/ipv4/ip.c \
	$(LWIPDIR)/core/ipv4/inet.c $(LWIPDIR)/core/ipv4/ip_addr.c \
	$(LWIPDIR)/core/ipv4/inet_chksum.c $(LWIPDIR)/core/ipv4/ip_frag.c


# APIFILES: The files which implement the sequential and socket APIs.
APIFILES=$(LWIPDIR)/api/api_lib.c $(LWIPDIR)/api/api_msg.c $(LWIPDIR)/api/tcpip.c \
	$(LWIPDIR)/api/err.c $(LWIPDIR)/api/sockets.c $(LWIPDIR)/api/netbuf.c $(LWIPDIR)/api/netdb.c

# NETIFFILES: Files implementing various generic network interface functions.'
NETIFFILES=$(LWIPDIR)/netif/etharp.c

# ARCHFILES: Architecture specific files.
ARCHFILES=$(wildcard $(LWIPARCH)/*.c $(LWIPARCH)/netif/tapif.c $(LWIPARCH)/netif/tunif.c $(LWIPARCH)/netif/unixif.c $(LWIPARCH)/netif/list.c $(LWIPARCH)/netif/tcpdump.c)

# LWIPFILES: All the above.
LWIPFILES=$(COREFILES) $(CORE4FILES) $(APIFILES) $(NETIFFILES) $(ARCHFILES)
LWIPFILESW=$(wildcard $(LWIPFILES))
LWIPOBJS=$(addprefix $(BUILD_DIR), $(notdir $(LWIPFILESW:.c=.o)))

LWIPLIB=liblwip.a

VPATH	= $(sort $(dir $(LWIPFILESW)))

$(BUILD_DIR)%.o: %.c
	@echo "  $(CC) $<"
	$(Q)$(CC) $(CFLAGS) -o $@ $<

.PHONY: all clean depend

all: $(LWIPLIB) 

clean:
	rm -f $(BUILD_DIR)*.o $(BUILD_DIR)*.d $(LWIPLIB) *.s  $(BUILD_DIR)depend

depend: $(BUILD_DIR)depend

-include $(BUILD_DIR)depend

$(LWIPLIB): $(LWIPOBJS)
	$(AR) -rc $@ $^

$(BUILD_DIR)depend: $(LWIPFILES)
	@echo "  Building dependancies."
	$(Q)$(CC) $(CFLAGS) -MM $(addprefix -MT, $(LWIPOBJS)) -E $^ > $@ || rm -f $(BUILD_DIR)depend

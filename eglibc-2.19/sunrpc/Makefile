# Copyright (C) 1994-2014 Free Software Foundation, Inc.
# This file is part of the GNU C Library.

# The GNU C Library is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public
# License as published by the Free Software Foundation; either
# version 2.1 of the License, or (at your option) any later version.

# The GNU C Library is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
# Lesser General Public License for more details.

# You should have received a copy of the GNU Lesser General Public
# License along with the GNU C Library; if not, see
# <http://www.gnu.org/licenses/>.

#
#	Sub-makefile for sunrpc portion of the library.
#
include ../option-groups.mak

subdir	:= sunrpc

# The code in this subdirectory is taken from Sun's RPCSRC-4.0
# distribution with some additional changes from the TI-RPC package
# which is also available from Sun.  The files are heavily changed to
# compile cleanly and to fit in the GNU environment.  All the code
# from Sun's rpc, etc, and rpcgen subdirectories is in this directory;
# the rpc subdirectory contains only the header files.  Other than
# that, several files were renamed so as not to exceed 14-character
# file name limits:
#
#	authunix_prot.c -> authuxprot.c
#	bindresvport.c -> bindrsvprt.c
#	clnt_generic.c -> clnt_gen.c
#	clnt_perror.c -> clnt_perr.c
#	clnt_simple.c -> clnt_simp.c
#	get_myaddress.c -> get_myaddr.c
#	pmap_getmaps.c -> pm_getmaps.c
#	pmap_getport.c -> pm_getport.c
#	rpc_callmsg.c -> rpc_cmsg.c
#	rpc_commondata.c -> rpc_common.c
#	rpc_dtablesize.c -> rpc_dtable.c
#	svc_auth_unix.c -> svc_authux.c
#	xdr_reference.c -> xdr_ref.c

rpcsvc = bootparam_prot.x nlm_prot.x rstat.x \
	 yppasswd.x klm_prot.x rex.x sm_inter.x mount.x \
	 rusers.x spray.x nfs_prot.x rquota.x key_prot.x
headers-in-tirpc = $(addprefix rpc/,auth.h auth_unix.h clnt.h pmap_clnt.h \
				    pmap_prot.h pmap_rmt.h rpc.h rpc_msg.h \
				    svc.h svc_auth.h types.h xdr.h auth_des.h \
				    des_crypt.h)
headers-not-in-tirpc = $(addprefix rpc/,key_prot.h rpc_des.h) \
		       $(rpcsvc:%=rpcsvc/%) rpcsvc/bootparam.h
headers = rpc/netdb.h
generated = $(rpcsvc:%.x=rpcsvc/%.h) $(rpcsvc:%.x=x%.c) $(rpcsvc:%.x=x%.stmp) \
	    $(rpcsvc:%.x=rpcsvc/%.stmp) rpcgen
generated-dirs := rpcsvc

include ../Makeconfig

ifeq ($(link-obsolete-rpc),yes)
headers += $(headers-in-tirpc) $(headers-not-in-tirpc)
endif

ifeq ($(build-shared),yes)
need-export-routines-$(OPTION_EGLIBC_SUNRPC) += \
			auth_des auth_unix clnt_gen clnt_perr clnt_tcp \
			clnt_udp get_myaddr key_call netname pm_getport \
			rpc_thread svc svc_tcp svc_udp xdr_array xdr \
			xdr_intXX_t xdr_mem xdr_ref xdr_sizeof xdr_stdio \
			svc_run

need-export-routines-y += xcrypt

need-export-routines := $(need-export-routines-y)

routines-$(OPTION_EGLIBC_SUNRPC) \
	 += auth_none authuxprot bindrsvprt clnt_raw clnt_simp \
	    rpc_dtable getrpcport pmap_clnt pm_getmaps pmap_prot pmap_prot2 \
	    pmap_rmt rpc_prot rpc_common rpc_cmsg svc_auth svc_authux svc_raw \
	    svc_simple xdr_float xdr_rec publickey authdes_prot \
	    key_prot openchild rtime svcauth_des \
	    clnt_unix svc_unix create_xid

# xdecrypt is also used by nss/nss_files/files-key.c.
routines-y += des_crypt des_impl des_soft $(need-export-routines)

ifneq ($(link-obsolete-rpc),yes)
# We only add the RPC for compatibility to libc.so.
shared-only-routines = $(routines)
endif
endif

# We do not build rpcinfo anymore.  It is not needed for a bootstrap
# and not wanted on complete systems.
# others-$(OPTION_EGLIBC_SUNRPC) += rpcinfo
# install-sbin-$(OPTION_EGLIBC_SUNRPC) += rpcinfo
install-bin-$(OPTION_EGLIBC_SUNRPC) += rpcgen
rpcgen-objs = rpc_main.o rpc_hout.o rpc_cout.o rpc_parse.o \
	      rpc_scan.o rpc_util.o rpc_svcout.o rpc_clntout.o \
	      rpc_tblout.o rpc_sample.o
extra-objs-$(OPTION_EGLIBC_SUNRPC) = $(rpcgen-objs) $(addprefix cross-,$(rpcgen-objs))
others-$(OPTION_EGLIBC_SUNRPC) += rpcgen

all: # Make this the default target; it will be defined in Rules.

install-others-$(OPTION_EGLIBC_SUNRPC) += $(inst_sysconfdir)/rpc

tests-$(OPTION_EGLIBC_SUNRPC) = tst-xdrmem tst-xdrmem2
xtests-$(OPTION_EGLIBC_SUNRPC) := tst-getmyaddr

ifeq ($(have-thread-library),yes)
xtests-$(OPTION_EGLIBC_SUNRPC) += thrsvc
endif

headers += $(rpcsvc:%.x=rpcsvc/%.h)
extra-libs-$(OPTION_EGLIBC_SUNRPC) += librpcsvc
# Make it in `others' pass, not `lib' pass.
extra-libs-others-y += $(extra-libs-y)
librpcsvc-routines = $(rpcsvc:%.x=x%)
librpcsvc-inhibit-o = .os # Build no shared rpcsvc library.
omit-deps = $(librpcsvc-routines)

ifeq (yes,$(build-shared))
rpc-compat-routines = $(addprefix compat-,$(need-export-routines))
rpc-compat-routines.os = $(addprefix $(objpfx), \
				     $(addsuffix .os,$(rpc-compat-routines)))
extra-objs += $(addsuffix .os,$(rpc-compat-routines))
endif

include ../Rules

ifeq (yes,$(build-shared))
subdir_lib: $(objpfx)librpc_compat_pic.a
$(objpfx)librpc_compat_pic.a: $(rpc-compat-routines.os)
	$(AR) cr$(verbose) $@ $^
$(rpc-compat-routines.os): $(objpfx)compat-%.os: %.c $(before-compile)
	$(compile-command.c) -DEXPORT_RPC_SYMBOLS
endif

CFLAGS-xbootparam_prot.c = -Wno-unused $(PIC-ccflag)
CFLAGS-xnlm_prot.c = -Wno-unused $(PIC-ccflag)
CFLAGS-xrstat.c = -Wno-unused $(PIC-ccflag)
CFLAGS-xyppasswd.c = -Wno-unused $(PIC-ccflag)
CFLAGS-xklm_prot.c = -Wno-unused $(PIC-ccflag)
CFLAGS-xrex.c = -Wno-unused $(PIC-ccflag)
CFLAGS-xsm_inter.c = -Wno-unused $(PIC-ccflag)
CFLAGS-xmount.c = -Wno-unused $(PIC-ccflag)
CFLAGS-xrusers.c = -Wno-unused $(PIC-ccflag)
CFLAGS-xspray.c = -Wno-unused $(PIC-ccflag)
CFLAGS-xnfs_prot.c = -Wno-unused $(PIC-ccflag)
CFLAGS-xrquota.c = -Wno-unused $(PIC-ccflag)
CFLAGS-xkey_prot.c = -Wno-unused $(PIC-ccflag)
CFLAGS-auth_unix.c = -fexceptions
CFLAGS-key_call.c = -fexceptions
CFLAGS-pmap_rmt.c = -fexceptions
CFLAGS-clnt_perr.c = -fexceptions
CFLAGS-openchild.c = -fexceptions

sunrpc-CPPFLAGS = -D_RPC_THREAD_SAFE_
CPPFLAGS += $(sunrpc-CPPFLAGS)
BUILD_CPPFLAGS += $(sunrpc-CPPFLAGS)

$(objpfx)tst-getmyaddr: $(common-objpfx)linkobj/libc.so
$(objpfx)tst-xdrmem: $(common-objpfx)linkobj/libc.so
$(objpfx)tst-xdrmem2: $(common-objpfx)linkobj/libc.so

$(objpfx)rpcgen: $(addprefix $(objpfx),$(rpcgen-objs))

cross-rpcgen-objs := $(addprefix $(objpfx)cross-,$(rpcgen-objs))

# When generic makefile support for build system programs is
# available, it should replace this code.  See
# <http://sourceware.org/bugzilla/show_bug.cgi?id=14087>.
$(cross-rpcgen-objs): $(objpfx)cross-%.o: %.c $(before-compile)
	$(BUILD_CC) $($(basename $(<F))-CFLAGS) $(ALL_BUILD_CFLAGS) $< \
		$(OUTPUT_OPTION) $(native-compile-mkdep-flags) -c

$(objpfx)cross-rpcgen: $(cross-rpcgen-objs)
	$(BUILD_CC) $^ $(BUILD_LDFLAGS) -o $@

# This makes sure -DNOT_IN_libc is passed for all these modules.
cpp-srcs-left := $(rpcgen-objs:.o=.c)
lib := nonlib
include $(patsubst %,$(..)cppflags-iterator.mk,$(cpp-srcs-left))

# How we run rpcgen to generate sources and headers in the rules below.
# Setting CPP tells it how to run the C preprocessor correctly.  Note
# that $(built-program-file) requires that the just-built cross-rpcgen
# binary be the second dependency listed in each rule using rpcgen-cmd.
#rpcgen-cmd = CPP='$(CC) -E -x c-header' $(built-program-file) -Y ../scripts
# ag-note: use original cross-rpcgen
rpcgen-cmd = CPP='$(CC) -E -x c-header' ../../pre-built/cross-rpcgen -Y ../scripts

# Install the rpc data base file.
$(inst_sysconfdir)/rpc: etc.rpc $(+force)
	$(do-install)

# Generate the rpcsvc headers with rpcgen.
# We use a stamp file to avoid unnessary recompilation each time rpcgen is
# relinked.
$(rpcsvc:%.x=$(objpfx)rpcsvc/%.h): $(objpfx)rpcsvc/%.h: $(objpfx)rpcsvc/%.stmp
	@:
$(objpfx)rpcsvc/%.stmp: rpcsvc/%.x $(objpfx)cross-rpcgen
	$(make-target-directory)
	-@rm -f ${@:stmp=T} $@
	$(rpcgen-cmd) -h $< -o ${@:stmp=T}
	$(move-if-change) $(@:stmp=T) $(@:stmp=h)
	touch $@

# Generate the rpcsvc XDR functions with rpcgen.
$(rpcsvc:%.x=$(objpfx)x%.c): $(objpfx)x%.c: $(objpfx)x%.stmp
	@:
$(objpfx)x%.stmp: rpcsvc/%.x $(objpfx)cross-rpcgen
	-@rm -f ${@:stmp=T} $@
	$(rpcgen-cmd) -c $< -o ${@:stmp=T}
	$(move-if-change) $(@:stmp=T) $(@:stmp=c)
	touch $@

# The generated source files depend on the corresponding generated headers.
# Gratuitous dependency on generated .c file here just gets it mentioned to
# avoid being an intermediate file and getting removed.
define o-iterator-doit
$(rpcsvc:%.x=$(objpfx)x%$o): $(objpfx)x%$o: $(objpfx)x%.c $(objpfx)rpcsvc/%.h
endef
object-suffixes-left = $(filter-out $(librpcsvc-inhibit-o),$(object-suffixes))
include $(o-iterator)

rpcsvc-dt-files := $(foreach o,$(filter-out $(librpcsvc-inhibit-o),\
					    $(object-suffixes)),\
			     $(rpcsvc:%.x=$(objpfx)x%$o.dt))
rpcsvc-depfiles := $(patsubst %.dt,%.d,$(wildcard $(rpcsvc-dt-files))) \
		   $(wildcard $(rpcsvc-dt-files:.dt=.d))
ifdef rpcsvc-depfiles
ifneq ($(no_deps),t)
-include $(rpcsvc-depfiles)
endif
endif

$(objpfx)thrsvc: $(common-objpfx)linkobj/libc.so $(shared-thread-library)

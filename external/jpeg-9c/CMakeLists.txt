cmake_minimum_required(VERSION 3.3.2)
project(jpeg C)

if (NOT MSVC)
	set(JMEM_SRC "jmem-android.c")
else()
	set(JMEM_SRC "jmemnobs.c")
endif()

add_library(jpeg STATIC)
target_sources(jpeg PRIVATE
		jaricom.c jcapimin.c jcapistd.c jcarith.c jccoefct.c jccolor.c
		jcdctmgr.c jchuff.c jcinit.c jcmainct.c jcmarker.c jcmaster.c
		jcomapi.c jcparam.c jcprepct.c jcsample.c jctrans.c jdapimin.c
		jdapistd.c jdarith.c jdatadst.c jdatasrc.c jdcoefct.c jdcolor.c
		jddctmgr.c jdhuff.c jdinput.c jdmainct.c jdmarker.c jdmaster.c
		jdmerge.c jdpostct.c jdsample.c jdtrans.c jerror.c jfdctflt.c
		jfdctfst.c jfdctint.c jidctflt.c jquant1.c
		jquant2.c jutils.c jmemmgr.c ${JMEM_SRC})

target_sources(jpeg PRIVATE jidctint.c jidctfst.c)

# FIXME : include asm for ARM
# target_sources(jpeg PRIVATE jidctint.c jidctfst.S)

target_compile_definitions(jpeg PRIVATE -DAVOID_TABLES)

target_include_directories(jpeg PUBLIC .)

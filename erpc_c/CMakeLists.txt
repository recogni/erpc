if(CONFIG_FILE_SYSTEM_LITTLEFS)
add_library(LITTLEFS INTERFACE)

target_include_directories(LITTLEFS INTERFACE ${CMAKE_CURRENT_SOURCE_DIR})

zephyr_library()
zephyr_library_sources(
    lfs.c
    lfs_util.c
    )
zephyr_library_link_libraries(LITTLEFS)
target_link_libraries(LITTLEFS INTERFACE zephyr_interface)
endif()

####################
    set(ERPC_C "src/services/erpc_c")
#    set(ERPC_C "modules/erpc/erpc_c")   Using modules/erpc is getting ignored ???
    target_include_directories(app PRIVATE ${ERPC_C}/setup)
    target_include_directories(app PRIVATE ${ERPC_C}/infra)
    target_include_directories(app PRIVATE ${ERPC_C}/port)
    target_include_directories(app PRIVATE ${ERPC_C}/config)
    FILE(GLOB erpc_setup_srcs  ${ERPC_C}/setup/*.cpp)
    FILE(GLOB erpc_infra_srcs  ${ERPC_C}/infra/*.cpp)
    FILE(GLOB erpc_port_srcs   ${ERPC_C}/port/*.cpp)
    FILE(GLOB erpc_config_srcs ${ERPC_C}/config/*.cpp)
    target_sources(app PRIVATE ${erpc_setup_srcs} ${erpc_infra_srcs} ${erpc_port_srcs} ${erpc_config_srcs} )

####################


####
# Below is original Makefile
####
.NOTPARALLEL:

this_makefile := $(firstword $(MAKEFILE_LIST))
ERPC_ROOT := $(abspath $(dir $(lastword $(MAKEFILE_LIST)))../)

include $(ERPC_ROOT)/mk/erpc_common.mk

ERPC_C_ROOT = $(ERPC_ROOT)/erpc_c

# TARGET_OUTPUT_ROOT = $(OUTPUT_ROOT)/$(DEBUG_OR_RELEASE)/$(os_name)/$(APP_NAME)

#-----------------------------------------------
# setup variables
# ----------------------------------------------

LIB_NAME = erpc

TARGET_OUTPUT_ROOT = $(OUTPUT_ROOT)/$(DEBUG_OR_RELEASE)/$(os_name)/$(LIB_NAME)

TARGET_LIB = $(LIBS_ROOT)/lib$(LIB_NAME).a

OBJS_ROOT = $(TARGET_OUTPUT_ROOT)/obj

LIBS_ROOT = $(TARGET_OUTPUT_ROOT)/lib

#-----------------------------------------------
# Include path. Add the include paths like this:
# INCLUDES += ./include/
#-----------------------------------------------
INCLUDES += $(ERPC_C_ROOT)/config \
			$(ERPC_C_ROOT)/infra \
			$(ERPC_C_ROOT)/port \
			$(ERPC_C_ROOT)/setup \
			$(ERPC_C_ROOT)/transports

SOURCES += 	$(ERPC_C_ROOT)/infra/erpc_arbitrated_client_manager.cpp \
			$(ERPC_C_ROOT)/infra/erpc_basic_codec.cpp \
			$(ERPC_C_ROOT)/infra/erpc_client_manager.cpp \
			$(ERPC_C_ROOT)/infra/erpc_crc16.cpp \
			$(ERPC_C_ROOT)/infra/erpc_framed_transport.cpp \
			$(ERPC_C_ROOT)/infra/erpc_message_buffer.cpp \
			$(ERPC_C_ROOT)/infra/erpc_message_loggers.cpp \
			$(ERPC_C_ROOT)/infra/erpc_server.cpp \
			$(ERPC_C_ROOT)/infra/erpc_simple_server.cpp \
			$(ERPC_C_ROOT)/infra/erpc_transport_arbitrator.cpp \
			$(ERPC_C_ROOT)/infra/erpc_pre_post_action.cpp \
			$(ERPC_C_ROOT)/port/erpc_port_stdlib.cpp \
			$(ERPC_C_ROOT)/port/erpc_threading_pthreads.cpp \
			$(ERPC_C_ROOT)/port/erpc_serial.cpp \
			$(ERPC_C_ROOT)/setup/erpc_arbitrated_client_setup.cpp \
			$(ERPC_C_ROOT)/setup/erpc_client_setup.cpp \
			$(ERPC_C_ROOT)/setup/erpc_setup_mbf_dynamic.cpp \
			$(ERPC_C_ROOT)/setup/erpc_setup_mbf_static.cpp \
			$(ERPC_C_ROOT)/setup/erpc_server_setup.cpp \
			$(ERPC_C_ROOT)/setup/erpc_setup_serial.cpp \
			$(ERPC_C_ROOT)/setup/erpc_setup_tcp.cpp \
			$(ERPC_C_ROOT)/transports/erpc_inter_thread_buffer_transport.cpp \
			$(ERPC_C_ROOT)/transports/erpc_serial_transport.cpp \
			$(ERPC_C_ROOT)/transports/erpc_tcp_transport.cpp

HEADERS += 	$(ERPC_C_ROOT)/config/erpc_config.h \
			$(ERPC_C_ROOT)/infra/erpc_arbitrated_client_manager.h \
			$(ERPC_C_ROOT)/infra/erpc_basic_codec.h \
			$(ERPC_C_ROOT)/infra/erpc_client_manager.h \
			$(ERPC_C_ROOT)/infra/erpc_codec.h \
			$(ERPC_C_ROOT)/infra/erpc_crc16.h \
			$(ERPC_C_ROOT)/infra/erpc_common.h \
			$(ERPC_C_ROOT)/infra/erpc_version.h \
			$(ERPC_C_ROOT)/infra/erpc_framed_transport.h \
			$(ERPC_C_ROOT)/infra/erpc_manually_constructed.h \
			$(ERPC_C_ROOT)/infra/erpc_message_buffer.h \
			$(ERPC_C_ROOT)/infra/erpc_message_loggers.h \
			$(ERPC_C_ROOT)/infra/erpc_server.h \
			$(ERPC_C_ROOT)/infra/erpc_static_queue.h \
			$(ERPC_C_ROOT)/infra/erpc_transport_arbitrator.h \
			$(ERPC_C_ROOT)/infra/erpc_transport.h \
			$(ERPC_C_ROOT)/infra/erpc_client_server_common.h \
			$(ERPC_C_ROOT)/infra/erpc_pre_post_action.h \
			$(ERPC_C_ROOT)/port/erpc_setup_extensions.h \
			$(ERPC_C_ROOT)/port/erpc_config_internal.h \
			$(ERPC_C_ROOT)/port/erpc_port.h \
			$(ERPC_C_ROOT)/port/erpc_threading.h \
			$(ERPC_C_ROOT)/port/erpc_serial.h \
			$(ERPC_C_ROOT)/setup/erpc_arbitrated_client_setup.h \
			$(ERPC_C_ROOT)/setup/erpc_client_setup.h \
			$(ERPC_C_ROOT)/setup/erpc_mbf_setup.h \
			$(ERPC_C_ROOT)/setup/erpc_server_setup.h \
			$(ERPC_C_ROOT)/setup/erpc_transport_setup.h \
			$(ERPC_C_ROOT)/transports/erpc_inter_thread_buffer_transport.h \
			$(ERPC_C_ROOT)/transports/erpc_serial_transport.h \
			$(ERPC_C_ROOT)/transports/erpc_tcp_transport.h

MAKE_TARGET = $(TARGET_LIB)($(OBJECTS_ALL))

include $(ERPC_ROOT)/mk/targets.mk

$(TARGET_LIB)(%): %
	@$(call printmessage,ar,Archiving, $(?F) in $(@F))
	$(at)mkdir -p $(dir $(@))
	$(AR) $(ARFLAGS) $@ $?

.PHONY: install
install: install_headers install_lib

.PHONY: install_headers
install_headers: $(HEADERS) |  $(INC_INSTALL_DIR)
	@$(call printmessage,c,Installing, headers in $(INC_INSTALL_DIR))
	$(at)mkdir -p $(INC_INSTALL_DIR)
	$(at)install $(?) $(INC_INSTALL_DIR)

$(INC_INSTALL_DIR):
	$(at)mkdir -p $(INC_INSTALL_DIR)

.PHONY: install_lib
install_lib: $(TARGET_LIB)
	@$(call printmessage,c,Installing, $(subst $(LIBS_ROOT)/,,$<) in $(LIB_INSTALL_DIR))
	$(at)mkdir -p $(LIB_INSTALL_DIR)
	$(at)install $(TARGET_LIB) $(LIB_INSTALL_DIR)

clean::
	$(at)rm -rf $(OBJS_ROOT)/*.cpp $(OBJS_ROOT)/*.hpp $(OBJS_ROOT)/*.c

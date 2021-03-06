# synergy -- mouse and keyboard sharing utility
# Copyright (C) 2012-2016 Symless Ltd.
# Copyright (C) 2009 Nick Bolton
# 
# This package is free software; you can redistribute it and/or
# modify it under the terms of the GNU General Public License
# found in the file LICENSE that should have accompanied this file.
# 
# This package is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

set(sources
	synergys.cpp
)

if (WIN32)
	file(GLOB arch_headers "MSWindows*.h")
	file(GLOB arch_sources "MSWindows*.cpp")
	list(APPEND sources
		resource.h
		synergys.ico
		synergys.rc
		tb_error.ico
		tb_idle.ico
		tb_run.ico
		tb_wait.ico
	)
elseif (APPLE)
	file(GLOB arch_headers "OSX*.h")
	file(GLOB arch_sources "OSX*.cpp")
elseif (UNIX)
	file(GLOB arch_headers "XWindows*.h")
	file(GLOB arch_sources "XWindows*.cpp")
endif()

list(APPEND sources ${arch_sources})
list(APPEND headers ${arch_headers})

if (SYNERGY_ADD_HEADERS)
	list(APPEND sources ${headers})
endif()

include_directories(
	../
	../../lib/
)

if (UNIX)
	include_directories(
		../../..
	)
endif()

add_executable(synergys ${sources})
target_link_libraries(synergys
	arch base client common io mt net ipc platform server synergy ${libs} ${OPENSSL_LIBS})

if (WIN32)
	ADD_CUSTOM_COMMAND(
		TARGET synergys
		POST_BUILD
		COMMAND "mt.exe" -manifest \"${CMAKE_SOURCE_DIR}\\res\\dpiaware.manifest\" -inputresource:\"$<TARGET_FILE:synergys>\"\;\#1 -outputresource:\"$<TARGET_FILE:synergys>\"\;\#1
		COMMENT "Adding display aware manifest..."
	)
endif()

if (CONF_CPACK)
	install(TARGETS
		synergys
		COMPONENT core
		DESTINATION bin)
endif()

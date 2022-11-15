cmake_minimum_required (VERSION 3.9)
project (taller)

# Adds the executable from the source files 
add_executable (returntypevec example_uno.cpp)
add_executable (vectorcomplement example_dos.cpp)
add_executable (vectorcartesian example_tres.cpp)
add_executable (vectorsum example_cuatro.cpp)

# Adds the Sources subfolder of the vecCore and Vc
# add_subdirectory (/content/VecCore/install)

#set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR}/cmake/modules)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR})
include(MacroUtilities)

set(RNGTEST_ARCH native sse2 ssse3 sse4.1 sse4.2 aes avx avx2 mic mic_avx512)

enum_option(RNGTEST_VECTOR
      DOC "Vector instruction set to be used"
      TYPE STRING
      VALUES ${RNGTEST_ARCH}
      CASE_INSENSITIVE
    )

if ("${BACKEND}" MATCHES "vc|VC")
  set(BACKEND "Vc")
  message("Vc Backend")
endif()
if ("${BACKEND}" MATCHES "scalar|SCALAR")
  set(BACKEND "Scalar")
endif()

set(RNGTEST_BACKEND Scalar UMESIMD Vc)

enum_option(BACKEND
            DOC "Backend infrastructure to be used"
            TYPE STRING
            VALUES ${RNGTEST_BACKEND}
            CASE_SENSITIVE
           )

# Determine backend

set(${BACKEND} True)
string(TOUPPER "${BACKEND}" _BACKEND_UP)
string(TOLOWER "${BACKEND}" _BACKEND_LO)

if("${BACKEND}" MATCHES "Scalar")
  set(SCALAR True)
  message(STATUS "Configuring with Scalar backend")
elseif ("${BACKEND}" MATCHES "Vc|UMESIMD")
  set(${_BACKEND_UP} True)
  set(BACKEND "Vc")
  set(VecCore_COMPONENTS ${VecCore_COMPONENTS} ${BACKEND})
  message(STATUS "Configuring with ${BACKEND} backend")
else()
  message(FATAL_ERROR "Unknown backend: '${BACKEND}'.")
endif()

find_package(VecCore REQUIRED COMPONENTS ${VecCore_COMPONENTS})
include_directories(SYSTEM ${VecCore_INCLUDE_DIRS})
set(DEFINITIONS "${VecCore_DEFINITIONS}")

set(INCLUDES "${INCLUDES};${VecCore_INCLUDE_DIRS}")
set(LIBRARIES ${LIBRARIES} ${VecCore_LIBRARIES})


# Links the executable 
target_link_libraries (returntypevec ${LIBRARIES})
target_link_libraries (vectorcomplement ${LIBRARIES})
target_link_libraries (vectorcartesian ${LIBRARIES})
target_link_libraries (vectorsum ${LIBRARIES})
PROJECT(colin2)

set( CMAKE_MODULE_PATH    ${CMAKE_MODULE_PATH}
                          ${CMAKE_CURRENT_SOURCE_DIR}
                          ${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules
                          ${CMAKE_CURRENT_SOURCE_DIR}/../cmake/modules)


set(DOXYFILE_TARGET_SUFFIX colin)
include(UseDoxygen OPTIONAL)


#find_package(Clp)
#find_package(CoinUtils)
#find_package(Cbc)
#find_package(Cgl)
#find_package(OSI)


set(CMAKE_CXX_FLAGS "-std=c++11 -Wall ${CMAKE_CXX_FLAGS} -L${CLP_LIB_PATH}")

if(STATIC_COLIN)
set(CMAKE_CXX_LINK_FLAGS "-stdlib=libc++ -static ${CMAKE_CXX_LINK_FLAGS}")
endif(STATIC_COLIN)

include_directories(${val_SOURCE_DIR} ${val_SOURCE_DIR}/parsing
${val_BINARY_DIR} ${CONCERT_INCLUDES}  ${GSL_INCLUDES})

set(colin_build_srcs
    compressionsafescheduler.cpp
    colintotalordertransformer.cpp
    FFSolver.cpp
    FFEvent.cpp
    globals.cpp
    lpscheduler.cpp
    RPGBuilder.cpp
    RPGBuilderAnalysis.cpp
    RPGBuilderEvaluation.cpp
    RPGBuilderNumerics.cpp
    temporalanalysis.cpp
    minimalstate.cpp
    temporalconstraints.cpp
    numericanalysis.cpp
    solver.cpp)

add_library(ColinCommon STATIC ${colin_build_srcs})
target_link_libraries(ColinCommon ParsePDDL Inst Cgl Cbc )
SET_TARGET_PROPERTIES(ColinCommon PROPERTIES COMPILE_FLAGS "-DTOTALORDERSTATES -DPOPF3ANALYSIS")

add_library(colin STATIC  ${colin_build_srcs} solver-clp.cpp drealMain.cpp)
SET_TARGET_PROPERTIES(colin PROPERTIES COMPILE_FLAGS
"-DTOTALORDERSTATES -DPOPF3ANALYSIS")
target_link_libraries(colin  ColinCommon
        bz2 z stdc++ ${COLIN_LIBS} )

ADD_EXECUTABLE(dreal-clp drealMain.cpp solver-clp.cpp)
SET_TARGET_PROPERTIES(dreal-clp PROPERTIES COMPILE_FLAGS
"-DTOTALORDERSTATES -DPOPF3ANALYSIS")
    target_link_libraries(dreal-clp
        ColinCommon
        bz2 z stdc++ Cgl Cbc ClpSolver Clp CoinUtils Osi OsiClp
    )
    SET_TARGET_PROPERTIES(dreal-clp PROPERTIES COMPILE_FLAGS "-DTOTALORDERSTATES -DPOPF3ANALYSIS")


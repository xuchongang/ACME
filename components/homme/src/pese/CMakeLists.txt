# macro for building pese primitive equations executable

macro(pese_setup)

SET(TARGET_DIR        ${HOMME_SOURCE_DIR}/src/pese)
SET(UTILS_SHARE_DIR   ${HOMME_SOURCE_DIR}/utils/csm_share)
SET(SRC_DIR           ${HOMME_SOURCE_DIR}/src)
SET(SRC_SHARE_DIR     ${HOMME_SOURCE_DIR}/src/share)
SET(PHYSICS_DIR       ${HOMME_SOURCE_DIR}/physics)
SET(UTILS_TIMING_DIR  ${CMAKE_BINARY_DIR}/utils/cime/share/timing)

# Make INCLUDE_DIRS global so the tests can access it
SET (EXEC_INCLUDE_DIRS ${PIO_INCLUDE_DIRS} ${UTILS_TIMING_DIR} )

# Find F90 files in target directory
FILE(GLOB TARGET_F90  ${TARGET_DIR}/*.F90)
FILE(GLOB TEST_SRC_F90  ${SRC_DIR}/test_src/*.F90)

SET(PESE_SRCS_F90
  ${TARGET_F90}
  ${TEST_SRC_F90}
  ${PHYSICS_DIR}/column_model_mod.F90 
  ${PHYSICS_DIR}/column_types_mod.F90
  ${PHYSICS_DIR}/heldsuarez/held_suarez_mod.F90 
  ${SRC_DIR}/checksum_mod.F90
  ${SRC_DIR}/common_io_mod.F90
  ${SRC_DIR}/common_movie_mod.F90
  ${SRC_DIR}/forcing_mod.F90
  ${SRC_DIR}/interp_movie_mod.F90
  ${SRC_DIR}/interpolate_driver_mod.F90
  ${SRC_DIR}/netcdf_interp_mod.F90
  ${SRC_DIR}/netcdf_io_mod.F90
  ${SRC_DIR}/pio_io_mod.F90
  ${SRC_DIR}/prim_main.F90 
  ${SRC_DIR}/prim_movie_mod.F90
  ${SRC_DIR}/prim_restart_mod.F90
  ${SRC_DIR}/repro_sum_mod.F90
  ${SRC_DIR}/restart_io_mod.F90
  ${SRC_DIR}/surfaces_mod.F90
  ${SRC_DIR}/test_mod.F90
  ${SRC_SHARE_DIR}/bndry_mod_base.F90
  ${SRC_SHARE_DIR}/cg_mod.F90
  ${SRC_SHARE_DIR}/control_mod.F90
  ${SRC_SHARE_DIR}/coordinate_systems_mod.F90
  ${SRC_SHARE_DIR}/cube_mod.F90
  ${SRC_SHARE_DIR}/derivative_mod_base.F90
  ${SRC_SHARE_DIR}/dimensions_mod.F90
  ${SRC_SHARE_DIR}/dof_mod.F90
  ${SRC_SHARE_DIR}/domain_mod.F90 
  ${SRC_SHARE_DIR}/edge_mod_base.F90
  ${SRC_SHARE_DIR}/edgetype_mod.F90
  ${SRC_SHARE_DIR}/element_mod.F90
  ${SRC_SHARE_DIR}/global_norms_mod.F90
  ${SRC_SHARE_DIR}/gridgraph_mod.F90
  ${SRC_SHARE_DIR}/hybrid_mod.F90
  ${SRC_SHARE_DIR}/hybvcoord_mod.F90
  ${SRC_SHARE_DIR}/interpolate_mod.F90
  ${SRC_SHARE_DIR}/kinds.F90
  ${SRC_SHARE_DIR}/linear_algebra_mod.F90 
  ${SRC_SHARE_DIR}/ll_mod.F90
  ${SRC_SHARE_DIR}/mass_matrix_mod.F90 
  ${SRC_SHARE_DIR}/mesh_mod.F90 
  ${SRC_SHARE_DIR}/metagraph_mod.F90
  ${SRC_SHARE_DIR}/metis_mod.F90
  ${SRC_SHARE_DIR}/namelist_mod.F90
  ${SRC_SHARE_DIR}/parallel_mod.F90
  ${SRC_SHARE_DIR}/params_mod.F90
  ${SRC_SHARE_DIR}/physical_constants.F90
  ${SRC_SHARE_DIR}/physics_mod.F90 
  ${SRC_SHARE_DIR}/prim_advection_base.F90 
  ${SRC_SHARE_DIR}/prim_driver_mod.F90
  ${SRC_SHARE_DIR}/prim_si_mod.F90 
  ${SRC_SHARE_DIR}/prim_si_ref_mod.F90
  ${SRC_SHARE_DIR}/prim_state_mod.F90
  ${SRC_SHARE_DIR}/quadrature_mod.F90
  ${SRC_SHARE_DIR}/reduction_mod.F90
  ${SRC_SHARE_DIR}/schedtype_mod.F90 
  ${SRC_SHARE_DIR}/schedule_mod.F90
  ${SRC_SHARE_DIR}/solver_init_base.F90
  ${SRC_SHARE_DIR}/solver_mod.F90 
  ${SRC_SHARE_DIR}/spacecurve_mod.F90
  ${SRC_SHARE_DIR}/thread_mod.F90
  ${SRC_SHARE_DIR}/time_mod.F90 
  ${SRC_SHARE_DIR}/unit_tests_mod.F90
  ${SRC_SHARE_DIR}/vertical_se.F90
  ${SRC_SHARE_DIR}/vertremap_base.F90
  ${SRC_SHARE_DIR}/viscosity_base.F90
  ${UTILS_SHARE_DIR}/shr_const_mod.F90 
  ${UTILS_SHARE_DIR}/shr_file_mod.F90 
  ${UTILS_SHARE_DIR}/shr_kind_mod.F90
  ${UTILS_SHARE_DIR}/shr_mpi_mod.F90 
  ${UTILS_SHARE_DIR}/shr_spfn_mod.F90
  ${UTILS_SHARE_DIR}/shr_sys_mod.F90 
  ${UTILS_SHARE_DIR}/shr_vmath_mod.F90
)

SET(PESE_SRCS_C
  ${UTILS_SHARE_DIR}/shr_vmath_fwrap.c
)

# If the user specified a file for custom compiler options use those
IF (DEFINED PESE_CUSTOM_FLAGS_FILE)
  setCustomCompilerFlags(PESE_CUSTOM_FLAGS_FILE PESE_SRCS_F90)
ENDIF ()

# Make SRCS global so the tests can access it
SET(EXEC_SOURCES ${PESE_SRCS} ${PESE_SRCS_C} ${PESE_SRCS_F90} )

# Set up defaults
IF (NOT PESE_NP)
  SET (PESE_NP 4) 
ENDIF ()
IF (NOT PESE_PLEV)
  SET (PESE_PLEV 20) 
ENDIF ()
IF (NOT PESE_USE_PIO)
  SET (PESE_USE_PIO FALSE) 
ENDIF ()
IF (NOT PESE_USE_ENERGY)
  SET (PESE_USE_ENERGY FALSE) 
ENDIF ()
IF (NOT PESE_NC)
  SET (PESE_NC 4)
ENDIF ()
IF (NOT QSIZE_D)
  SET (QSIZE_D 4)
ENDIF ()

SET(USE_OPENACC FALSE)
IF (${ENABLE_HORIZ_OPENMP} AND ${ENABLE_COLUMN_OPENMP})
  SET(NESTED_OPENMP TRUE CACHE BOOL "Both horizontal and column threading")
ENDIF ()

endmacro(pese_setup)

pese_setup()
############################################################################
# createTestExec(exec_name exec_type NP PLEV USE_PIO USE_ENERGY)
############################################################################
createTestExec(pese pese ${PESE_NP} ${PESE_NC} ${PESE_PLEV} ${PESE_USE_PIO}  ${PESE_USE_ENERGY} ${QSIZE_D})


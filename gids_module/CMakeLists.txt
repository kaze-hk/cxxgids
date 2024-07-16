CMAKE_MINIMUM_REQUIRED( VERSION 3.3.0 )
PROJECT( cmake-cpp-pybind11 CXX CUDA)

SET( CMAKE_CXX_STANDARD 11 )
SET( CMAKE_CXX_STANDARD_REQUIRED ON )

LIST( APPEND CMAKE_MODULE_PATH
  ${CMAKE_SOURCE_DIR}/cmake
  ${CMAKE_SOURCE_DIR}/cmake/Modules
)


message("Compilation starts")

ADD_LIBRARY(BAM_Feature_Store SHARED gids_nvme.cu )
 
  target_include_directories(BAM_Feature_Store PRIVATE ./include ../bam/include ../bam/include/freestanding/include/)
  target_link_libraries(BAM_Feature_Store PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/../bam/build/lib/libnvm.so)
  # The library must not have any prefix and should be located in
  # a subfolder that includes the package name. The setup will be
  # more complicated otherwise.
  SET_TARGET_PROPERTIES( BAM_Feature_Store
    PROPERTIES
      PREFIX ""
      LIBRARY_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/BAM_Feature_Store"
  )

target_compile_options(BAM_Feature_Store PRIVATE $<$<COMPILE_LANGUAGE:CUDA>:
			-std=c++11;
					-O3;
			--default-stream per-thread;
                      >)

target_compile_options(BAM_Feature_Store PRIVATE
  "$<$<COMPILE_LANGUAGE:CUDA>:SHELL:-gencode arch=compute_70,code=sm_70>"
)
target_compile_options(BAM_Feature_Store PRIVATE
  "$<$<COMPILE_LANGUAGE:CUDA>:SHELL:-gencode arch=compute_80,code=sm_80>"
)


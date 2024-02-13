project(MRMLLogic)

# --------------------------------------------------------------------------
# Dependencies
# --------------------------------------------------------------------------

#
# ITK
#
set(${PROJECT_NAME}_ITK_COMPONENTS
  ITKCommon
  )
find_package(ITK 4.6 COMPONENTS ${${PROJECT_NAME}_ITK_COMPONENTS} REQUIRED)
if(ITK_VERSION VERSION_GREATER_EQUAL "5.3")
  foreach(factory_uc IN ITEMS "IMAGEIO" "MESHIO" "TRANSFORMIO")
    set(ITK_NO_${factory_uc}_FACTORY_REGISTER_MANAGER 1)
  endforeach()
else()
  set(ITK_NO_IO_FACTORY_REGISTER_MANAGER 1) # See Libs/ITKFactoryRegistration/CMakeLists.txt
endif()
list(APPEND ITK_LIBRARIES ITKFactoryRegistration)
list(APPEND ITK_INCLUDE_DIRS ${ITKFactoryRegistration_INCLUDE_DIRS})
include(${ITK_USE_FILE})

# --------------------------------------------------------------------------
# Configure headers
# --------------------------------------------------------------------------
set(configure_header_file vtkMRMLLogicConfigure.h)
configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/${configure_header_file}.in
  ${CMAKE_CURRENT_BINARY_DIR}/${configure_header_file}
  )

set(classes
  vtkMRMLAbstractLogic
  vtkMRMLApplicationLogic
  vtkMRMLColorLogic
  vtkMRMLDisplayableHierarchyLogic
  vtkMRMLRemoteIOLogic
  vtkMRMLLayoutLogic
  vtkMRMLSliceLayerLogic
  vtkMRMLSliceLogic
  vtkMRMLSliceLinkLogic
  vtkMRMLViewLogic
  vtkMRMLViewLinkLogic
  # slicer's vtk extensions (filters)
  vtkImageLabelOutline
  vtkImageNeighborhoodFilter
  )

set(headers ${CMAKE_CURRENT_BINARY_DIR}/${configure_header_file})

vtk_module_add_module(VTK::MRMLLogic
  EXPORT_MACRO_PREFIX VTK_MRML_LOGIC
  CLASSES ${classes}
  HEADERS ${headers}
)

vtk_module_link(VTK::MRMLLogic PUBLIC ${ITK_LIBRARIES})
vtk_module_include(VTK::MRMLLogic PUBLIC $<BUILD_INTERFACE:${ITK_INCLUDE_DIRS}>)

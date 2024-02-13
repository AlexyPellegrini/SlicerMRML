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
set(configure_header_file vtkMRMLDisplayableManagerConfigure.h)
configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/${configure_header_file}.in
  ${CMAKE_CURRENT_BINARY_DIR}/${configure_header_file}
  )

set(classes
  # DisplayableManager associated with ThreeDView
  vtkMRMLCameraDisplayableManager
  vtkMRMLModelDisplayableManager
  vtkMRMLViewDisplayableManager
  vtkMRMLThreeDReformatDisplayableManager

  # DisplayableManager associated with SliceView
  vtkMRMLCrosshairDisplayableManager
  vtkMRMLCrosshairDisplayableManager3D
  vtkMRMLModelSliceDisplayableManager
  vtkMRMLVolumeGlyphSliceDisplayableManager

  # DisplayableManager common between ThreeDView and SliceView
  vtkMRMLOrientationMarkerDisplayableManager
  vtkMRMLRulerDisplayableManager
  vtkMRMLScalarBarDisplayableManager
  vtkMRMLAbstractDisplayableManager
  vtkMRMLDisplayableManagerGroup
  vtkMRMLDisplayableManagerFactory
  vtkMRMLInteractionEventData

  # ThreeDView factory and DisplayableManager
  vtkMRMLAbstractThreeDViewDisplayableManager
  vtkMRMLThreeDViewDisplayableManagerFactory

  # SliceView factory and DisplayableManager
  vtkMRMLAbstractSliceViewDisplayableManager
  vtkMRMLSliceViewDisplayableManagerFactory

  vtkMRMLViewInteractorStyle
  vtkMRMLSliceViewInteractorStyle
  vtkMRMLThreeDViewInteractorStyle

  # Widgets
  vtkMRMLAbstractWidget
  vtkMRMLAbstractWidgetRepresentation
  vtkMRMLCameraWidget
  vtkMRMLSliceIntersectionWidget
  vtkMRMLSliceIntersectionRepresentation2D
  vtkMRMLSliceIntersectionInteractionRepresentation
  vtkMRMLSliceIntersectionInteractionRepresentationHelper
  vtkMRMLRubberBandWidgetRepresentation
  vtkMRMLWindowLevelWidget

  # Proxy classes
  vtkMRMLLightBoxRendererManagerProxy
  
  )

set_source_files_properties(
  vtkMRMLDisplayableManagerGroup.cxx
  vtkMRMLScriptedDisplayableManager.cxx
  WRAP_EXCLUDE
  )

if(VTK_WRAP_PYTHON)
  #list(APPEND classes vtkMRMLScriptedDisplayableManager)
  #set_source_files_properties(
  #  vtkMRMLScriptedDisplayableManager.cxx
  #  WRAP_EXCLUDE
  #  )
endif()

# --------------------------------------------------------------------------
# Create the instantiator for DisplayableManager classes.
# --------------------------------------------------------------------------
#include(SlicerConfigureDisplayableManagerObjectFactory)
#SlicerConfigureDisplayableManagerObjectFactory(
#  TARGET_NAME ${lib_name}
#  SRCS "${DisplayableManager_SRCS}"
#  EXPORT_MACRO "VTK_MRML_DISPLAYABLEMANAGER_EXPORT"
#  EXPORT_HEADER "vtk${PROJECT_NAME}Export.h"
#  OUTPUT_SRCS_VAR DisplayableManagerInstantiator_SRCS
#  )
#set(srcs
#  ${KIT_SRCS}
#  ${DisplayableManagerInstantiator_SRCS}
#  )

# --------------------------------------------------------------------------
# Build library
# --------------------------------------------------------------------------

vtk_module_add_module(VTK::MRMLDisplayableManager
  EXPORT_MACRO_PREFIX VTK_MRML_DISPLAYABLEMANAGER
  CLASSES ${classes}
  HEADERS ${headers}
)

vtk_module_link(VTK::MRMLDisplayableManager PUBLIC ${ITK_LIBRARIES})
vtk_module_include(VTK::MRMLDisplayableManager PUBLIC $<BUILD_INTERFACE:${ITK_INCLUDE_DIRS}>)


#
# ITK
#
set(ITK_IO_MODULES_USED
  ITKIOJPEG
  ITKIOGDCM
  ITKIOBMP
  ITKIOLSM
  ITKIOPNG
  ITKIOTIFF
  ITKIOVTK
  ITKIOStimulate
  ITKIOBioRad
  ITKIOMeta
  ITKIOMRC
  ITKIONIFTI
  ITKIONRRD
  ITKIOGIPL
  ITKIOTransformHDF5
  ITKIOTransformInsightLegacy
  ITKIOTransformMatlab
  MGHIO
  ITKIOMINC
  IOScanco
  )
if(Slicer_BUILD_DICOM_SUPPORT)
  list(APPEND ITK_IO_MODULES_USED ITKIODCMTK)
endif()

set(${PROJECT_NAME}_ITK_COMPONENTS
  ITKIOImageBase
  ITKIOTransformBase
  ${ITK_IO_MODULES_USED}
  )
find_package(ITK 4.6 COMPONENTS ${${PROJECT_NAME}_ITK_COMPONENTS} REQUIRED)

# This is the Slicer library in charge of registering the default ITK factories.
set(ITK_NO_IO_FACTORY_REGISTER_MANAGER 0)

include(${ITK_USE_FILE})

# --------------------------------------------------------------------------
# Configure headers
# --------------------------------------------------------------------------
set(sources
  itkFactoryRegistration.cxx
)

set(headers
  itkFactoryRegistration.h
)

vtk_module_add_module(VTK::ITKFactoryRegistration
  EXPORT_MACRO_PREFIX ITKFactoryRegistration
  SOURCES ${sources}
  HEADERS ${headers}
)

vtk_module_link(VTK::ITKFactoryRegistration PUBLIC ${ITK_LIBRARIES})
vtk_module_include(VTK::ITKFactoryRegistration PUBLIC ${ITK_INCLUDE_DIRS})

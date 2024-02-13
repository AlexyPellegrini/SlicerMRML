set(configure_header_file vtkSegmentationCoreConfigure.h)
configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/${configure_header_file}.in
  ${CMAKE_CURRENT_BINARY_DIR}/${configure_header_file}
  )

set(classes
  vtkOrientedImageData
  vtkOrientedImageDataResample
  vtkSegment
  vtkSegmentation
  vtkSegmentationConversionParameters
  vtkSegmentationConversionPath
  vtkSegmentationConverter
  vtkSegmentationConverterFactory
  vtkSegmentationConverterRule
  vtkSegmentationHistory
  vtkSegmentationModifier
  vtkTopologicalHierarchy
  vtkBinaryLabelmapToClosedSurfaceConversionRule
  vtkClosedSurfaceToBinaryLabelmapConversionRule
  vtkCalculateOversamplingFactor
  vtkClosedSurfaceToFractionalLabelmapConversionRule
  vtkFractionalLabelmapToClosedSurfaceConversionRule
  vtkPolyDataToFractionalLabelmapFilter
  )

set(headers ${CMAKE_CURRENT_BINARY_DIR}/${configure_header_file})

vtk_module_add_module(VTK::SegmentationCore
  EXPORT_MACRO_PREFIX vtkSegmentationCore
  CLASSES ${classes}
  HEADERS ${headers}
)

vtk_module_include(VTK::SegmentationCore PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>)

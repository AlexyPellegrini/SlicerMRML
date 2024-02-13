## Issues

### ITK path lenght

ITK build directory root on Windows needs to be at most 50 chars long. 
Pip uses temporary directories located in `%APPDATA%/Temp/<someid>` or similar which exceed this limit.

Temp fix:
- hardcoded ITK build dir in external projects to `C:/itk-build`

### Mangling

DLL mangling performed by delvewheel uses the package (wheel) name as a part of the hash used to rename the DLLs.
Since the package is name vtk-slicer, the DLLs get mangled with a different name even if their content is actually the same.
This seems to generated runtime errors. These errors are unclear, I don't know what's the real issue.

Temp fix: 
- Hardcode in `delvewheel/_wheel_repair.py` L.274 `"vtk".encode()` instead of `self._distribution_name.encode()`.

### Installation overwrite vtk `__init__.py`

Since vtk-slicer is installed after VTK, it overrides VTK `__init__.py` which makes VTK importation impossible.

Temp fix: 
- In VTK `__init__.py`, add:
    ```py
    libs_dir = os.path.abspath(os.path.join(os.path.dirname(__file__), os.pardir, 'vtk.libs'))
    if os.path.isdir(libs_dir):
        os.add_dll_directory(libs_dir)
    ```
- And replace `__version__ = _vtk_package.__version__` in `vtk.py` with `__version__ = "9.3.0"`

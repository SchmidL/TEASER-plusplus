#### **1. Prerequisites**

Install system dependencies and set up your Conda environment.

```bash
# System dependencies
sudo apt install cmake libeigen3-dev libboost-all-dev

# Create and activate Conda environment (Python 3.12)
conda create -n teaserpp python=3.12
conda activate teaserpp

# Install Python build tools
conda install "numpy<2.0.0" pip setuptools wheel pybind11 open3d
```

### Main branch

```bash
git clone https://github.com/MIT-SPARK/TEASER-plusplus.git
cd TEASER-plusplus
pip install .


# Run a quick test
python -c "import teaserpp_python; print(f'Success! Installed at: {teaserpp_python.__file__}')"

```


You can now run the example script:

```bash
cd examples/teaser_python_ply
python teaser_python_ply.py
```




### ONLY for the developer branch!
-----

#### **2. Clone the Repository**

Clone the specific branch (usually `develop` or `master`).

```bash
cd /scratch
git clone https://github.com/MIT-SPARK/TEASER-plusplus.git
cd TEASER-plusplus
git checkout develop
```

-----

#### **3. [CRITICAL] Patch Source Code**

Your compiler (GCC 11+) requires an explicit include for `std::vector`. You must apply this fix before building, or compilation will crash at 80%.

  * **File:** `teaser/src/graph.cc`
  * **Action:** Open the file and add `#include <vector>` at the top, and change `vector<int>` to `std::vector<int>` at line 12.

**Or just run this command to apply the patch automatically:**

```bash
sed -i '1i #include <vector>' teaser/src/graph.cc
sed -i 's/vector<int> teaser/std::vector<int> teaser/g' teaser/src/graph.cc
```

-----

#### **4. Build the C++ Library & Python Bindings**

We will use CMake to build the shared library manually. This ensures it links to the correct Conda Python version.

```bash
# 1. Create build directory
mkdir -p build && cd build

# 2. Configure CMake
# We explicitly point to the Conda python executable to avoid system conflicts
cmake -DTEASERPP_PYTHON_VERSION=3.12 \
      -DPython3_EXECUTABLE=$(which python) \
      ..

# 3. Compile
# This might take a few minutes. Ensure it reaches [100%]
make teaserpp_python
```

-----

#### **5. Install the Python Package**

Once the build hits `[100%]`, a `setup.py` and the compiled `.so` file are generated inside `build/python`. We install from *there*.

```bash
# Move to the build/python directory (NOT the source python directory)
cd python

# Install the package
# --force-reinstall ensures it overwrites any previous broken attempts
pip install . --force-reinstall
```

-----

#### **6. Verify Installation**

Test that the library can be imported and the bindings are active.

```bash
# Leave the build folder first to avoid local import errors
cd ../..

# Run a quick test
python -c "import teaserpp_python; print(f'Success! Installed at: {teaserpp_python.__file__}')"
```

### **Next Steps**

You can now run the example script:

```bash
cd examples/teaser_python_ply
python teaser_python_ply.py
```
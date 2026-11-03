# CorridorKey-2.0-free-After-Effects-Python-Script
A free guide to install a python environment and run a script to have your own After Effects plugin for Corridor Key 2.0 for here: https://github.com/nikopueringer/CorridorKey2.0

To build or use a CorridorKey 2.0 After Effects plugin, you do not need to compile or build a custom C++ integration from this repository.
As stated in the repository's official documentation, an official After Effects plugin and standalone application have already been fully built and released.
Here is how you can access the official plugin, followed by how you can integrate the free Python model directly with After Effects if you prefer a custom workflow.

Option 1: Use the Official After Effects Plugin (Recommended)

The tool’s creators (Corridor Digital, Zachary Harris, and Ale Alvaro) developed a production-ready plugin for After Effects and other media software.
1. Go to the official website: corridorkey.com.
2. Purchase/download the installer. A single seat grants access to the plugins for all supported video packages.
3. Run the installer to automatically add the CorridorKey 2.0 effect directly into After Effects.

Option 2: Build a Custom Python/Scripts Integration

The GitHub repository is specifically meant for developers who want to tinker and build custom tools using Python. If you want to route After Effects footage through this underlying PyTorch model without buying the plugin, you can build a bridge script via an After Effects JSX script or ExtendScript.

1. Set Up the Local Python Environment

First, you must set up the backend server/model on your machine. You need an NVIDIA GPU (RTX 30-series or newer).
bash
# Clone the repository
git clone https://github.com
cd CorridorKey2.0

# Create virtual environment and install PyTorch 2.9.1
python -m venv venv
source venv/bin/activate  # On Windows use: venv\Scripts\activate
pip install torch==2.9.1 torchvision==0.24.1 --index-url https://pytorch.org

# Install dependencies and download weights (~5 GB)
pip install -r requirements.txt
python scripts/download_weights.py
Use code with caution.

2. Create an After Effects JSX Bridge Script

You can write an Adobe ExtendScript (.jsx) inside After Effects that automatically exports your active composition as an image sequence (EXR or PNG), triggers the CorridorKey Python script via the command line, and imports the keyed alpha/foreground maps back into your timeline.
Save the following code as CorridorKey_Bridge.jsx:
javascript
(function() {
    // 1. Define paths (Adjust these to your local system)
    var pythonPath = "C:\\path\\to\\CorridorKey2.0\\venv\\Scripts\\python.exe";
    var inferScript = "C:\\path\\to\\CorridorKey2.0\\infer.py";
    var inputVideo = "C:\\path\\to\\your\\footage.mp4"; 
    var outputDir = "C:\\path\\to\\output_folder";

    // 2. Build the command line argument string
    // This runs the "No Hint" fastest standard pipeline
    var cmd = '"' + pythonPath + '" "' + inferScript + '" --input "' + inputVideo + '" --output_dir "' + outputDir + '"';

    // 3. Execute the Python backend from inside After Effects
    alert("Starting CorridorKey 2.0 processing... AE will freeze until complete.");
    var result = system.callSystem(cmd);
    alert("Processing Complete!\n" + result);

    // 4. Proactively import the generated alpha matte back into AE
    var importOptions = new ImportOptions(new File(outputDir + "/alpha.mp4"));
    if (importOptions.canImportAsSRT) {
        app.project.importFile(importOptions);
    }
})();
Use code with caution.

3. How to use this Bridge:

1. Open After Effects.
2. Go to File > Scripts > Run Script File... and choose your CorridorKey_Bridge.jsx.
3. The script will command your GPU to execute the PyTorch model using your specified footage and deliver the high-quality alpha pass straight back into your project bin.
To help fine-tune this setup for your workflow, please let me know:
• Are you looking to run this as an automated image sequence (EXR/Linear) workflow, or are you working with standard compressed video (MP4/ProRes)?
• Would you like assistance writing a script that automatically extracts a first-frame mask from your AE layer to pass to the model as a hint?


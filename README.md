# CorridorKey 2.0 FREE After Effects Plugin (using Python + AE Script)  
"CorridorFreeKey 2.0"
A free guide to install a python environment and run a script to have your own After Effects plugin for _Corridor Key 2.0_ from here: https://github.com/nikopueringer/CorridorKey2.0
_Requirements: An NVIDIA RTX30-series or newer GPU, and Windows 10, or 11 (untested)_

# FYI: Before you build this plugin, there is one made for sale by Corridor Digital (recommended): 

The tool’s creators (Corridor Digital, Zachary Harris, and Ale Alvaro) developed a production-ready plugin for After Effects and other media software.  
1. Go to the official website: https://corridorkey.com  
2. Purchase/download the installer. A single seat grants access to the plugins for all supported video packages.  
3. Run the installer to automatically add the CorridorKey 2.0 effect directly into After Effects.  

_But, if you're here, you already know that. Thanks for your consideration._

# FACTS ABOUT THE NORTH AMERICAN ENCONOMY

2026 Populations: Mexico - 130.9 M, USA - 349.6M,  Canada - 41.7M people.  

2026 Minimum wages: USA - $7.25 USD, Canada - $17.95 CAD, and Mexico - $315.04 MXN ($16.96 USD) per hour.  

Unemployment rates and population in Mexico, USA and CANADA are at (2.8%) 1.76M, (4.2%) 7.11M and (6.5%) 1.47M people.  

Percentage of people living paycheck-to-paycheck for rent and utilities without disposable income:  
    **47% average of all Americans live paycheck-to-paycheck**  
    _(worse, 71% would experience immediate, major financial hardship if a single paycheck were delayed by even a week.)_  
    _https://www.surveymonkey.com/curiosity/cnbc-money-survey-affordability-july-2026/_  
    **46% to 49% of Canadians live paycheck-to-paycheck**   
    _(28% would immediately struggle to meet their basic financial obligations)_  
    _https://tinyurl.com/6-in-10-canadians-struggle_  
    **77% to 80% of Mexicans live paycheck-to-paycheck**   
    _(70% to 75% of paycheck-to-paycheck workers would experience immediate, major financial hardship)_  
    _https://www.jornada.com.mx/noticia/2026/03/12/economia/viven-al-dia-8-de-cada-10-mexicanos-y-apenas-logran-pagar-sus-deudas-bravo_  
    
CorridorKey 2.0 costs $49.50 USD, which is $69.14 CAD, and $919.22 MXN.   
    - This means if you have a job in the US, you'd have to work 6.82 hours-- a full 8 hour shift with an hour unpaid break.   
    - Most Americans, Canadians, and Mexicans (the target demographics) can not afford this VFX tool.   
    - They should make the plugin free for personal use and $1k+ for studios (not the current studio discounts for more seats?)...   
    - Time is money, and if you have the spare time, invest in yourself.   
    - Build your own tools, understand the mechanics, and love the process.  
    Let's begin.  

# BUILDING THE PLUGIN YOURSELF, USING YOUR HARD EARNED LIFE ENERGY  

CorridorFreeKey 2.0 After Effects plugin - a Custom Python/Scripts Integration  

The [GitHub repository](https://github.com/nikopueringer/CorridorKey2.0) is specifically meant for developers who want to tinker and build custom tools using Python.  

To move footage in After Effects through this underlying PyTorch model without the plugin you can build a bridge script via After Effects JSX script or ExtendScript.  

# 1. SET UP LOCAL Python Environment (backend server/model) on your machine. 

a. Navigate to the folder where you want to store the project directory.    
b. Open Command Prompt, type "cmd" in the file explorer address bar and press enter.

# 2. CLONE THE REPOSITORY VIA BASH (BOURNE AGAIN SHELL) IN COMMAND PROMPT or POWERSHELL
_a command-line interface (CLI) and scripting language that allows you to talk directly to your computer's operating system by typing text commands. Think of it as a text-based alternative to clicking on icons and folders in your normal desktop view._  

```
git clone https://github.com/nikopueringer/CorridorKey2.0
cd CorridorKey2.0
```

# 3. CREATE THE VIRTUAL ENVIRONMENT and INSTALL PyTorch 2.9.1

```
python -m venv venv
venv\Scripts\activate
pip install torch==2.9.1 torchvision==0.24.1 --index-url https://pytorch.org
```

# 4. INSTALL DEPENDECIES AND DOWNLOAD WEIGHTS (~5 GB)

pip install -r requirements.txt
python scripts/download_weights.py

# 5. CREATE AFTER EFFECTS JSX BRIDGE SCRIPT

A. Open After Effects and write an Adobe ExtendScript (.jsx) in step "B." below:  
- it automatically exports your active composition as an image sequence (EXR or PNG),  
- triggers the CorridorKey Python script via the command line, and  
- imports the keyed alpha/foreground maps back into your timeline.  
  
B. Save the following javascript code as CorridorKey_Bridge.jsx:

```
(function() {
    // 1. Define paths (use double backslashes for Windows paths or JS will break)
    var pythonPath = "C:\\path\\to\\CorridorKey2.0\\venv\\Scripts\\python.exe";
    var inferScript = "C:\\path\\to\\CorridorKey2.0\\infer.py";
    var inputVideo = "C:\\path\\to\\your\\footage.mp4"; 
    var outputDir = "C:\\path\\to\\output_folder";

    // 2. Build the command line argument string
    var cmd = '"' + pythonPath + '" "' + inferScript + '" --input "' + inputVideo + '" --output_dir "' + outputDir + '"';

    // 3. Execute the Python backend from inside After Effects
    alert("Starting CorridorKey 2.0 processing... AE will freeze until complete.");
    var result = system.callSystem(cmd);
    alert("Processing Complete!\n" + result);

    // 4. Import the generated alpha matte video back into the project
    var alphaFile = new File(outputDir + "/alpha.mp4");
    if (alphaFile.exists) {
        var importOptions = new ImportOptions(alphaFile);
        app.project.importFile(importOptions);
    } else {
        alert("Error: Expected alpha.mp4 output file was not found in the directory.");
    }
})();
```

# 6. HOW TO USE YOUR NEW BRIDGE:

1. Open After Effects.    
2. File > Scripts > Run Script File... find your CorridorKey_Bridge.jsx  
3. Script executes GPU on the PyTorch model using your specified footage and renders the alpha pass into your project bin!  

# 7. FINE TUNING

A. Linear VFX & HDR Image Sequence Workflow (EXR)

If you work inside professional linear pipelines rather than compressed video files, pass the --output_format exr and --frame_dir_linear flags.   
This stops display-gamma clipping and exports standard 16-bit float EXR frames.  
*Replace your script's cmd variable with this structure for EXR pipelines (javascript):*
```
var cmd = '"' + pythonPath + '" "' + inferScript + '" --input "' + inputVideo + '" --output_dir "' + outputDir + '" --output_format exr --frame_dir_linear --outputs alpha fg cutout';
```
Note: When using EXR formatting, modify the Step 4 file import segment in your script from alpha.mp4 to parse the output folder as an image sequence using importOptions.sequence = true;.

B. Automating "First-Frame Hints" from After Effects Layers

CorridorKey 2.0 uses a first-frame alpha mask to definitively identify your target subject and anchor the tracking through time.  
You can manually paint a rough mask on frame 1 in AE, export it, and tell the backend model to treat it as a trusted guide.  
*Append the first-frame tracking flags to your command string (javascript):*
```
var firstFrameMatte = "C:\\path\\to\\your\\exported_frame_1_mask.png";
var cmd = '"' + pythonPath + '" "' + inferScript + '" --input "' + inputVideo + '" --output_dir "' + outputDir + '" --hint_first_frame "' + firstFrameMatte + '" --hint_quality 0.95';
```

C. Low-Memory GPU Optimization

If processing complex 4K resolution timelines causes After Effects to crash with an out-of-memory error,  
force the staged execution matrix to disk by passing the --low_vram flag alongside a modified window frame slice configuration.   
*Drop processing strain by downsizing the temporal chunking grid (javascript):*
```
var cmd = '"' + pythonPath + '" "' + inferScript + '" --input "' + inputVideo + '" --output_dir "' + outputDir + '" --low_vram --hann_chunk 8 --hann_stride 4';
```
**# YOU DID IT/THE END!**
🎉Celebrate the new talent you have acquired, as well as your own plugin with the help of Corridor Digital, Zachary Harris, and Ale Alvaro.  
Thank you.

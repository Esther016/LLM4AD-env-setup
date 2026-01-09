# **LLM4AD: Complete Installation & Execution Guide**

This guide provides a step-by-step walkthrough for setting up the **LLM4AD** framework. It is optimized for researchers and beginners, focusing on resolving common Windows-specific path errors and environment conflicts.

---

## **1. Prerequisites & System Requirements**

### **Is Anaconda Necessary?**
While you can use standard Python `venv`, **Anaconda (or Miniconda) is highly recommended** for this project because:
1.  **Dependency Management:** Libraries like `pymoo` often require specific C++ build tools; Conda handles these binary dependencies more reliably on Windows.
2.  **Isolation:** It prevents "polluting" your global Python installation.
3.  **Consistency:** Most research codebases assume a Conda environment.

### **Requirements**
*   **Operating System:** Windows 10/11 (Primary focus) or macOS (Intel/Apple Silicon).
*   **Python:** 3.11 (Required for compatibility with `langchain` and `ollama` versions used).
*   **Disk Space:** ~5GB for dependencies and model caches.

---

## **2. Environment Setup (Cross-Platform)**

### **2.1 Create the Environment**
Open your terminal (Anaconda Prompt or PowerShell on Windows; Terminal on Mac) and run:

```bash
# Create a dedicated environment
conda create -n LLM4AD python=3.11 -y

# Activate the environment
conda activate LLM4AD
```

### **2.2 Install Dependencies**
Navigate to the root directory of the project (`LLM4AD-main`) and run:

```bash
# Install core requirements
pip install -r requirements.txt

# Install specific additional dependencies
pip install pymoo==0.6.0
pip install langchain_ollama ttkbootstrap
```

> **Note (Optional):** We specify `pymoo==0.6.0` to avoid version conflicts with optimization tasks.

---

## **3. Mandatory Windows Configuration**

Windows users **must** perform these steps due to how Windows handles file paths and module searching.

### **3.1 Set Environment Variables**
Windows cannot handle colons (`:`) in folder names, which HuggingFace often uses for datasets. You **must** redirect the cache to a clean path.

Run these commands **every time** you open a new terminal:
```powershell
# 1. Activate environment
conda activate LLM4AD

# 2. Tell Python where the source code is (Replace with YOUR actual path)
set PYTHONPATH=C:\Path\To\Your\LLM4AD-main

# 3. Force HuggingFace to use a safe Windows path
set HF_HOME=C:\LLM4AD_Cache
set HF_HUB_DISABLE_SYMLINKS_WARNING=1
```
 **Some alternative methods** 


* Method 1: The "Conda Way" (Recommended):

    You can bake the environment variables directly into the Conda environment. They will activate automatically whenever you run conda activate LLM4AD.
    Activate your environment:
    ```
    conda activate LLM4AD
    ```
    Run these commands once: (Replace the path with your actual path)
    ```
    conda env config vars set PYTHONPATH="C:\Path\To\Your\LLM4AD-main"
    conda env config vars set HF_HOME="C:\LLM4AD_Cache"
    conda env config vars set HF_HUB_DISABLE_SYMLINKS_WARNING=1
    ```
    Reactivate the environment:
    To make the changes take effect, deactivate and reactivate:
    ```
    conda deactivate
    conda activate LLM4AD
    ```
    Now, every time you activate this environment, the variables are set automatically.

* Method 2: The One-Click Launcher (Easiest for Running GUI)
    Create a "Launcher" script. This is the most robust method for researchers who just want to double-click and start working.
    Open Notepad and paste the following:

    ```
    @echo off
    :: 1. Navigate to the project directory
    cd /d "C:\Path\To\Your\LLM4AD-main"

    :: 2. Set Variables
    set PYTHONPATH=%cd%
    set HF_HOME=C:\LLM4AD_Cache
    set HF_HUB_DISABLE_SYMLINKS_WARNING=1

    :: 3. Run with Conda (assumes conda is in your PATH)
    call conda activate LLM4AD

    :: 4. Launch the GUI
    cd GUI
    python run_gui.py
    pause
    ```

    Save the file as Run_LLM4AD.bat on your Desktop.
    Just double-click this file whenever you want to start the program.



### **3.2 Clean Up Old Cache**
If you tried running the code before and got a `NotADirectoryError`, you must delete the corrupted cache folder:
```powershell
rmdir /s /q %USERPROFILE%\.cache\huggingface\hub
```

---

## **4. Required Source Code Fixes**

There are two known bugs in the current version that require manual editing of the source code.

### **4.1 Fix the GUI "LEFT" Error**
**File:** `GUI/run_gui.py`
**Problem:** `ttkbootstrap` does not always export `LEFT` correctly, causing the GUI to crash on startup.

1.  Open `GUI/run_gui.py` in a text editor (VS Code, Notepad++, etc.).
2.  Add this line at the very top: `from tkinter import LEFT`
3.  Search for all instances of `side=ttk.LEFT` and change them to `side=LEFT`.

`Copy & Past the following to replace the original code:`

```python
from tkinter import LEFT

top_frame = ttk.Frame(root, height=30, bootstyle="info")
top_frame.pack(fill='x')

link_doc = ttk.Button(top_frame, image=photoimage_doc, command=open_doc_link, bootstyle="info")
link_doc.pack(side=LEFT, padx=3)

link_git = ttk.Button(top_frame, image=photoimage_git, command=open_github_link, bootstyle="info")
link_git.pack(side=LEFT, padx=3)

link_web = ttk.Button(top_frame, image=photoimage_web, command=open_website_link, bootstyle="info")
link_web.pack(side=LEFT, padx=3)

link_qq = ttk.Button(top_frame, image=photoimage_qq, command=open_qq_link, bootstyle="info")
link_qq.pack(side=LEFT, padx=3)
```


### **4.2 Resolve Module Conflicts**
If you previously installed `llm4ad` via `pip install`, it will conflict with the local source code.
**Fix:** Run this command:
```bash
pip uninstall llm4ad -y
```
The project will now use the local folder defined in your `PYTHONPATH`.

---

## **5. How to Run LLM4AD**

### **Method A: Using the GUI (Recommended for Beginners)**
1.  Open your terminal.
2.  Navigate to the `GUI` folder: `cd GUI`
3.  Run: `python run_gui.py`

### **Method B: One-Click Launcher (Windows Only)**
To save time, create a file named `start_llm4ad.bat` in the project root with the following content:
```batch
@echo off
call conda activate LLM4AD
set PYTHONPATH=%~dp0
set HF_HOME=%~dp0hf_cache
python GUI/run_gui.py
pause
```
*Double-clicking this file will automatically set the environment and launch the GUI.*

---

## **6. macOS Specific Instructions**

macOS is Unix-based and does not suffer from the `NotADirectoryError` path issue. However, follow these steps:

1.  **Environment:** Conda works exactly the same.
2.  **PYTHONPATH:** Use `export` instead of `set`:
    ```bash
    export PYTHONPATH="/Users/yourname/path/to/LLM4AD-main"
    ```
3.  **GUI:** If the GUI windows appear very small (Retina display issue), you may need to install `python-tk`:
    ```bash
    brew install python-tk
    ```

---

## **7. Troubleshooting Table**

| Error Message | Likely Cause | Solution |
| :--- | :--- | :--- |
| `NotADirectoryError: ...Vehicle routing: period...` | Windows path character limit/illegal characters. | Set `HF_HOME` to a short path (e.g., `C:\hf_cache`) as shown in Section 3.1. |
| `ModuleNotFoundError: No module named 'llm4ad'` | `PYTHONPATH` is not set. | Run `set PYTHONPATH=...` pointing to the project root. |
| `AttributeError: module 'ttkbootstrap' has no attribute 'LEFT'` | Version mismatch in GUI library. | Follow the code fix in Section 4.1. |
| `FileNotFoundError: ../llm4ad/method` | Running the script from the wrong directory. | Always `cd GUI` before running `python run_gui.py`. |
| Program hangs at "Loading Dataset" | Internet connection or cache corruption. | Ensure your `HF_HOME` is set and you have an active internet connection for the first run. |

---

## **8. Verification of Success**
You will know the system is working correctly if:
1.  The GUI window opens without any red text in the terminal.
2.  A folder named `hf_cache` (or whatever you named your `HF_HOME`) appears and begins filling with files.
3.  The terminal displays `Log: Starting Optimization...` when you click "Run" in the GUI.

## **9. Fill out the GUI blank**

host: api.deepseek.com

model: deepseek-chat

![](https://drive.google.com/uc?export=view&id=1R-L7nJTfMqId4qWUEHu5BC8j6LWv1PLs)
![](https://drive.google.com/file/d/1BQCSSKSoTEs6RGNB-aQeZNNUIZFOAKR0)
# ⚡ Resonator Q-Factor & Background Analysis

A fast, interactive Streamlit web application designed for the bulk analysis of microwave resonator S-parameter sweeps (S12/S21) across multiple temperatures. This tool automates the extraction of loaded ($Q_L$), external ($Q_{ex}$), and internal ($Q_{in}$) quality factors using non-linear curve fitting, and provides interactive, GPU-accelerated visualizations of the results.


* **Appriciate the help from Gemini** 

* **Blazing Fast Multi-core Processing:** Utilizes `concurrent.futures.ProcessPoolExecutor` to distribute independent curve-fitting tasks across all available CPU cores.
* **Interactive Dashboard:** Features WebGL-accelerated Plotly graphs (`Scattergl`) for smooth rendering of high-density frequency sweeps. 
* **Dynamic Background Subtraction:** Optionally interpolates and subtracts the background from the previous temperature step sequentially.
* **Robust Error Propagation:** Calculates rigorous error bounds for the internal quality factor ($Q_{in}$)[cite: 1]. It includes programmatic safeguards to prevent `ZeroDivisionError` during these calculations[cite: 1].
* **One-Click Export:** Download extracted Q-factors, resonance frequencies, and combined background datasets directly to CSV.



## 🧮 Mathematical Model

The application fits the magnitude of the transmission data using a modified Lorentzian resonance model with a real background offset ($A$):

$$\vert{}S_{21}\vert{} = \left\vert{} \frac{Q_L}{Q_e} \frac{1}{1 + 2j Q_L \delta} \right\vert{} + A$$

Where:
* $\delta = (f - f_0) / f_0$ is the fractional frequency detuning.
* $f_0$ is the resonance frequency.
* $Q_L$ is the loaded quality factor.
* $Q_e$ is the external (coupling) quality factor.
* $A$ is the real background offset magnitude.

The internal quality factor ($Q_{in}$) is subsequently derived using the standard relationship:

$$\frac{1}{Q_{in}} = \frac{1}{Q_L} - \frac{1}{Q_{ex}}$$

## 🛠️ Installation & Setup

### Prerequisites
Ensure you have Python 3.8+ installed. 

### Dependencies
Install the required packages using `pip`:

```bash
pip install streamlit numpy pandas scipy plotly
```

### Running the App
Navigate to the directory containing your script (e.g., `app.py`) and run:

```bash
streamlit run app.py
```

## 📂 Data Format Requirements

The application expects experimental sweep files in `.txt` or `.csv` format. 
* **Naming Convention:** Files should follow a specific naming convention containing the temperature string, such as `S12_..._T_2.5.txt` or `S12_..._T_2.5_K.csv`. The app parses the file name to extract the temperature (`T`).
* **Column Structure:** The parser expects the frequency data to be in the **1st column** (index 0) and the real magnitude raw data to be in the **4th column** (index 3). 
* **Header:** The script skips the first row (`skiprows=1`), assuming it contains column headers.

## 🖥️ Usage Guide

1. **Upload Data:** Use the sidebar to upload multiple sweep files at once.
2. **Set Filters:** 
   * Define the Temperature range ($T_{min}$ and $T_{max}$) to analyze.
   * Specify a file prefix if your directory contains mixed data.
   * (Optional) Restrict the row range if your files contain extraneous header/footer data or out-of-band noise.
3. **Configure Fit Settings:** Expand the **⚙️ Advanced Fit Settings** tab to adjust parameter bounds, the number of trial guesses (lower is faster), and the relaxation range.
4. **Run Analysis:** Click the **🚀 Run Analysis** button.
5. **Explore Results:** Navigate through the three main tabs:
   * **📈 Global Trends:** View how $Q_{in}$, $Q_L$, $Q_{ex}$, and $f_0$ evolve with temperature.
   * **🔍 Individual Scans:** Select specific temperatures from a dropdown to inspect the quality of the individual curve fits and backgrounds.
   * **📊 Data Table & Export:** Review the raw extracted metrics in a tabular format and download the `.csv` files for your records.

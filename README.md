# SPI Image Creation

## Overview
The `spi_image_creation` project uses a Generative Adversarial Network (GAN) to generate Standardized Precipitation Index (SPI) images for drought analysis. It processes historical and future climate data (e.g., SPI12, SPEI12, temperature, precipitation) and historical SPI raster images (TIFF) to create new images for future climate scenarios. The generated images are denoised using wavelet transforms, masked with a geographical boundary (e.g., Iran's map), and saved as TIFF and colored PNG files for visualization.

This tool is designed for researchers and meteorologists studying drought patterns and spatial climate data.

## Features
- **SPI Image Generation**: Generates SPI images for historical and future periods using a GAN model.
- **Conditional Inputs**: Uses climate variables (e.g., SPI12, SPEI12, temperature) as conditioning data.
- **Wavelet Denoising**: Applies wavelet-based noise reduction to enhance image quality.
- **Geographical Masking**: Applies a mask (e.g., Iran’s map) to focus on specific regions.
- **Evaluation Metrics**: Calculates SSIM, PSNR, R², and RMSE to evaluate generated images.
- **Visualization**: Saves images as TIFFs and colored PNGs with a custom colormap (`brown`, `red`, `orange`, `yellow`, `green`, `blue`).

## Prerequisites
- **Hardware**: A CUDA-enabled GPU is recommended for faster training and inference (falls back to CPU if unavailable).
- **Software**:
  - Python 3.6+
  - Required libraries:
    ```bash
    pip install torch pandas numpy rasterio geopandas matplotlib scikit-learn scikit-image pywavelets opencv-python
    ```
- **Data**:
  - Historical climate data in Excel format (e.g., `spi(1981-2024).xlsx`) with columns: `SPI12`, `SPEI12`, `tavg-monthy`, `thorn-monthy`, `p-pet-monthy`.
  - Future climate projections in Excel format (e.g., `120_months.xlsx`) with the same columns.
  - Historical SPI images in TIFF format, stored in a folder (e.g., `/1981-2024-IMG/`).
  - A grayscale mask image (e.g., `iran.jpg`) for geographical filtering.

## Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/Pouya-Azizzadh/spi_image_creation.git
   ```
2. Navigate to the project directory:
   ```bash
   cd spi_image_creation
   ```
3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
4. Place your data files (Excel, TIFFs, and mask image) in the appropriate directories (e.g., update paths in the code if not using Google Drive).

## Usage
The project involves two main workflows: **training the GAN model** and **generating future SPI images**.

### 1. Data Preparation
- **Historical Data**: Load climate data from `spi(1981-2024).xlsx`. The data should include columns like `SPI12`, `SPEI12`, `tavg-monthy`, `thorn-monthy`, and `p-pet-monthy`.
- **TIFF Images**: Store historical SPI images (TIFF format) in a folder (e.g., `/1981-2024-IMG/`). Images are resized to 200x200 pixels if necessary.
- **Future Data**: Provide future climate projections in `120_months.xlsx` with the same column structure.
- **Mask**: Use a grayscale image (e.g., `iran.jpg`) to mask non-relevant areas in the output.

### 2. Training the GAN
The GAN consists of:
- **Generator**: Generates SPI images from random noise and climate conditions.
- **Discriminator**: Distinguishes between real and generated images.
- **Training Details**:
  - Data is split into 80% training and 20% validation sets.
  - The model trains for up to 50 epochs, stopping early if R² ≥ 0.39 or after 500 epochs without improvement.
  - The best generator model is saved as `best_generator.pth`.

Run the training script:
```bash
python train.py
```
*Note*: Update file paths in the script to match your data locations.

### 3. Generating Future SPI Images
- Loads the trained generator (`best_generator.pth`).
- Uses future climate data (`120_months.xlsx`) to generate 120 months of SPI images.
- Applies wavelet denoising to improve image quality.
- Saves images as:
  - TIFF files in `/future_images/`.
  - Colored PNG visualizations in `/2024_10_to_234_9_spi_image/` with titles including the date and SPI value.

Run the inference script:
```bash
python generate_future_images.py
```

### 4. Evaluation
Generated images are evaluated using:
- **SSIM**: Measures structural similarity.
- **PSNR**: Measures peak signal-to-noise ratio.
- **R²**: Measures the coefficient of determination.
- **RMSE**: Measures root mean square error.
Metrics are computed with the mask applied to focus on the region of interest.

### 5. Visualization
- Images are saved as TIFFs for raw data and PNGs for visualization.
- PNGs use a custom colormap (`brown`, `red`, `orange`, `yellow`, `green`, `blue`) and include a colorbar.
- Each PNG is named with the date (e.g., `2024_10_spi_image.png`) and SPI value.

## Project Structure
```
spi_image_creation/
├── scripts/
│   ├── train.py                # Script for training the GAN
│   ├── generate_future_images.py # Script for generating future SPI images
├── data/
│   ├── spi(1981-2024).xlsx     # Historical climate data
│   ├── 120_months.xlsx         # Future climate projections
│   ├── 1981-2024-IMG/          # Historical SPI TIFF images
│   ├── iran.jpg                # Geographical mask
├── output/
│   ├── future_images/          # Generated TIFF images
│   ├── 2024_10_to_234_9_spi_image/ # Colored PNG visualizations
├── best_generator.pth          # Saved generator model
├── requirements.txt            # List of dependencies
└── README.md                   # This file
```

## Example
To train and generate images:
1. Prepare data files and update paths in the scripts.
2. Train the model:
   ```bash
   python train.py
   ```
3. Generate future images:
   ```bash
   python generate_future_images.py
   ```
4. Check `/future_images/` for TIFFs and `/2024_10_to_234_9_spi_image/` for PNGs.

## Notes
- **File Paths**: Update paths in the scripts to match your environment (e.g., local paths instead of Google Drive).
- **Performance**: A GPU is recommended for faster training.
- **Customization**: Adjust parameters like `latent_dim` (120), `condition_length` (7), or `num_epochs` (50) as needed.
- **Denoising**: The `pywt` library is used for wavelet denoising; modify `wavelet`, `level`, or `threshold` for different effects.

## Contributing
1. Fork the repository.
2. Create a branch (`git checkout -b feature-branch`).
3. Commit changes (`git commit -m "Add feature"`).
4. Push to your branch (`git push origin feature-branch`).
5. Open a pull request.

## Contact
For questions or support, open an issue on GitHub or contact the repository owner.
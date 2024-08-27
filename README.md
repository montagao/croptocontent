# Crop To Content

Crop To Content is a web-based tool that automatically removes excess whitespace or transparent areas around images, leaving only the essential content. This tool is particularly useful for processing logos or preparing images for use with other tools.

## Features

- Drag and drop or select image files
- Supports common image formats (PNG, JPEG, GIF, etc.)
- Automatically trims excess space around the image content
- Preview of original and trimmed images
- Download option for trimmed images
- Responsive design for various screen sizes

## Technologies Used

- [Svelte](https://svelte.dev/) - Frontend framework
- [ImageMagick](https://github.com/dlemstra/magick-wasm) - Image processing library (WebAssembly version)
- HTML5 File API
- CSS3 for styling

## Getting Started

### Prerequisites

- Node.js (version 12 or later)
- npm (comes with Node.js)

### Installation

1. Clone the repository:
   ```
   git clone https://github.com/yourusername/crop-to-content.git
   ```

2. Navigate to the project directory:
   ```
   cd crop-to-content
   ```

3. Install dependencies:
   ```
   npm install
   ```

4. Start the development server:
   ```
   npm run dev
   ```

5. Open your browser and visit `http://localhost:5000` (or the port shown in your terminal).

## Usage

1. Drag and drop an image onto the designated area, or click to select a file.
2. Once an image is selected, click the "Trim Image" button.
3. The tool will process the image and display the trimmed version.
4. Click the "Download" button to save the trimmed image.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the [MIT License](LICENSE).

## Contact

Created by [@montakaoh](https://twitter.com/montakaoh) - feel free to contact me!


<script>
    import { onMount } from "svelte";
    import {
        MagickFormat,
        ImageMagick,
        initializeImageMagick,
    } from "@imagemagick/magick-wasm";
    import magickWasm from "@imagemagick/magick-wasm/magick.wasm?url";
    import { createEventDispatcher } from "svelte";

    const dispatch = createEventDispatcher();

    onMount(async () => {
        if (typeof window !== "undefined") {
            await initializeImageMagick(new URL(magickWasm, import.meta.url));
        }
    });

    function showExample() {
        dispatch("showExample", (image) => {
            image.trim();
            image.repage();
        });
    }

    let selectedFile = null;
    let trimmedImageUrl = null;
    let originalImageUrl = null;
    let errorMessage = null;
    function handleFileInput(event) {
        const file = event.target.files?.[0];
        if (file) {
            if (file.type.startsWith("image/")) {
                selectedFile = file;
                originalImageUrl = URL.createObjectURL(file);
                trimmedImageUrl = null;
                errorMessage = null;
            } else {
                errorMessage = "Please select a valid image file.";
            }
        }
    }

    function handleDrop(event) {
        event.preventDefault();
        const file = event.dataTransfer?.files[0];
        if (file) {
            if (file.type.startsWith("image/")) {
                selectedFile = file;
                originalImageUrl = URL.createObjectURL(file);
                trimmedImageUrl = null;
                errorMessage = null;
            } else {
                errorMessage = "Please drop a valid image file.";
            }
        }
    }

    function handleDragOver(event) {
        event.preventDefault();
    }

    async function trimImage() {
        if (!selectedFile || typeof window === "undefined") return;

        try {
            const arrayBuffer = await selectedFile.arrayBuffer();
            const uint8Array = new Uint8Array(arrayBuffer);

            ImageMagick.read(uint8Array, (image) => {
                image.trim();
                image.write(MagickFormat.Png, (data) => {
                    const blob = new Blob([data], { type: "image/png" });
                    trimmedImageUrl = URL.createObjectURL(blob);
                    errorMessage = null;
                });
            });
        } catch (error) {
            console.error("Error trimming image:", error);
            errorMessage =
                "An error occurred while trimming the image. Please try again.";
        }
    }

    function downloadTrimmedImage() {
        if (!trimmedImageUrl) return;

        const downloadLink = document.createElement("a");
        downloadLink.href = trimmedImageUrl;
        downloadLink.download = "trimmed_image.png";
        document.body.appendChild(downloadLink);
        downloadLink.click();
        document.body.removeChild(downloadLink);
    }
</script>

<main>
    <h1>Crop To Content</h1>
    <div class="flex flex-col">
        {#if typeof window === "undefined"}
            <div class="loading-spinner">
                <div class="spinner"></div>
                <p>Loading...</p>
            </div>
        {:else}
            <div
                class="drop-zone"
                class:has-file={selectedFile}
                on:dragover={handleDragOver}
                on:drop={handleDrop}
            >
                {#if selectedFile}
                    <p>{selectedFile.name}</p>
                    {#if originalImageUrl}
                        <div class="preview">
                            <img src={originalImageUrl} alt="Original image" />
                        </div>
                    {/if}

                    <button on:click={trimImage}>Trim Image</button>
                {:else}
                    <p>
                        Drag and drop an image here, or click to select a file
                    </p>
                    <input
                        type="file"
                        accept="image/*"
                        on:change={handleFileInput}
                    />
                {/if}
            </div>
            {#if errorMessage}
                <p class="error-message">{errorMessage}</p>
            {/if}

            {#if trimmedImageUrl}
                <div class="preview flex flex-col">
                    <img src={trimmedImageUrl} alt="Trimmed image" />
                    <div>
                        <button
                            class="download-button"
                            on:click={downloadTrimmedImage}>Download</button
                        >
                    </div>
                </div>
            {/if}
        {/if}
    </div>
</main>
<div class="faq">
    <h2>FAQ</h2>
    <dl>
        <dt>What does this tool do?</dt>
        <dd>
            Crop To Content automatically removes excess whitespace or
            transparent areas around your images, leaving only the essential
            content.
        </dd>
        <dd>This might be useful for logos or use with other tools</dd>

        <dt>How do I use it?</dt>
        <dd>
            Simply drag and drop an image onto the designated area, or click to
            select a file. Then, click the "Trim Image" button to crop your
            image to its content.
        </dd>

        <dt>What file types are supported?</dt>
        <dd>
            This tool supports common image formats such as PNG, JPEG, GIF, and
            more.
        </dd>

        <dt>Is my data safe and private?</dt>
        <dd>
            All image processing is done entirely on your device (client-side).
            We don't store or transmit your images to any server. Your files
            never leave your browser, ensuring complete privacy and security for
            your data.
        </dd>
    </dl>
</div>

<footer>
    Made with love by <a
        href="https://twitter.com/montakaoh"
        target="_blank"
        rel="noopener noreferrer">@montakaoh</a
    >
</footer>

<style>
    :global(body) {
        font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
        margin: 0;
        padding: 0;
        background-color: #f5f5f5;
    }

    main {
        max-width: 800px;
        margin: 0 auto;
        padding: 20px;
    }

    h1 {
        color: #ff3e00;
        text-align: center;
        margin-bottom: 30px;
        font-weight: 300;
    }

    .drop-zone {
        border: 3px dashed #ff3e00;
        border-radius: 10px;
        padding: 30px;
        text-align: center;
        cursor: pointer;
        transition: all 0.3s ease;
        background-color: white;
    }

    .drop-zone:hover {
        background-color: #fff5f2;
    }

    .drop-zone.has-file {
        background-color: #e6f7ff;
        border-color: #1890ff;
    }

    .preview {
        margin-top: 20px;
        text-align: center;
        vertical-align: top;
        margin-right: 20px;
    }

    .preview img {
        max-width: 200px;
        max-height: 200px;
        object-fit: contain;
        border-radius: 5px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    button {
        background-color: #ff3e00;
        color: white;
        border: none;
        padding: 10px 20px;
        border-radius: 5px;
        cursor: pointer;
        transition: all 0.3s ease;
        font-size: 16px;
        margin-top: 10px;
        display: inline-block;
    }

    button:hover {
        background-color: #ff6240;
        transform: translateY(-2px);
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
    }

    .download-button {
        background-color: #4caf50;
        margin-left: 10px;
    }

    .download-button:hover {
        background-color: #45a049;
    }

    input[type="file"] {
        display: none;
    }

    .loading-spinner {
        text-align: center;
        color: #ff3e00;
    }

    .spinner {
        width: 50px;
        height: 50px;
        border: 5px solid #f3f3f3;
        border-top: 5px solid #ff3e00;
        border-radius: 50%;
        animation: spin 1s linear infinite;
        margin: 20px auto;
    }

    @keyframes spin {
        0% {
            transform: rotate(0deg);
        }
        100% {
            transform: rotate(360deg);
        }
    }

    .faq {
        margin-top: 40px;
        background-color: #fff;
        padding: 20px;
        border-radius: 10px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        max-width: 800px;
        margin-left: auto;
        margin-right: auto;
    }

    .faq h2 {
        color: #ff3e00;
        margin-bottom: 20px;
    }

    .faq dt {
        font-weight: bold;
        color: #333;
        margin-top: 15px;
    }

    .faq dd {
        margin-left: 0;
        margin-bottom: 15px;
        color: #666;
    }

    footer {
        margin-top: 40px;
        text-align: center;
        font-size: 14px;
        color: #666;
    }

    footer a {
        color: #ff3e00;
        text-decoration: none;
    }

    footer a:hover {
        text-decoration: underline;
    }

    .error-message {
        color: #ff0000;
        text-align: center;
        margin-top: 10px;
    }

    @media (max-width: 600px) {
        main {
            padding: 10px;
        }

        .drop-zone {
            padding: 20px;
        }

        h1 {
            font-size: 24px;
        }

        button {
            font-size: 14px;
            padding: 8px 16px;
        }
    }

    @media (max-width: 840px) {
        .faq {
            max-width: 95%;
            padding: 15px;
        }
    }
</style>
